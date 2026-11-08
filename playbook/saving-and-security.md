# Saving and security

How to save player data safely and keep a Roblox game honest (server authority, remote checks, rate limits, admin tools, luck events). Comes from Battle for Pets, with notes from Axel and +1 Mecha Cyborg, which reuse the same save design.

## What we built

### The save

- One service owns all player data. It loads a **profile** when a player joins, keeps it in memory, changes it only through functions, and writes it with `UpdateAsync`.
- The profile is a plain table. Ours holds: the pets (species, mutation, level, XP, when caught), the team, cash, capsule counts, boss wins, materials, boosts, pen level, tutorial progress, settings and playtime. Ids are strings, because DataStore tables need string keys.
- Each game system saves into its own sub-table (`boss wins`, `training`, `events`, `tasks`). A system's loader **validates its own part** and drops anything malformed.
- **Health is not saved** (it lasts for the session). Save only what the player would be angry to lose.
- Studio playtests use a **separate store** (the live name plus `_Studio`), so a test can never touch live data. Studio needs "Enable Studio Access to API Services" turned on in Game Settings. Without it, progress lasts for that playtest only and the output says so.

### Session locks

- The profile records which server is using it (`lock = { session, at }`). A second server that finds a fresh lock **waits and retries** (8 tries, 5 s apart). A lock not refreshed for 5 minutes belongs to a crashed server and is taken over.
- The autosave (every 90 s) also refreshes the lock. Leaving and server shutdown save **and release** the lock.
- If a save finds another server holding the lock, this server **stops using the profile and kicks the player** ("Your pets were loaded on another server. Please rejoin."). It never overwrites.
- A failed load **kicks or retries; it never starts a fresh profile**, because a fresh profile could overwrite a real one.
- Shutdown (`BindToClose`) saves everyone and waits up to 25 s for saves in flight, including saves started by players leaving.
- Because the lock already exists, we planned our teleport to a second place (the Arena) on top of it with no new save system. It still needs a test of the handoff.

### Versioning

- **A new top-level field** only needs a default in the profile template. Old saves get it when they load. **No version bump.**
- **Changing the shape of an existing field** bumps the version and adds a migration function.
- Fields the loader doesn't know are not copied forward, so new fields must be added to both the loader and the writer. (Axel's version also saves unknown fields back untouched, and refuses to load a record saved by a newer version. That protects players while old servers still run after a publish.)
- **After publishing, migrate every live server to the new version right away.** A server on older code drops fields it doesn't know when it saves. We wrote this rule in our release notes for an event update, where old servers would have dropped the event data and a crafted title.
- A change that touches the save shape is **released with every server shut down**, not just migrated.

### Server authority

- **Cash, income, rewards, capsule counts, boss wins and XP are all computed on the server** from the server's own profile. The client sends a kind or a key, never a number.
- The client sees state as **attributes** (`Beat_<Boss>`, `Level`, `Wallet`) and **pushed updates**. It never owns the truth.
- Every purchase and craft checks and spends **in one step with no yield**, so two quick requests can't both pay.
- The first-boss-win, first-catch, and claim flags are set by server code only.
- Distance checks: battles, gates and pads are all judged by the server from the character's real position.
- Gates: a client wall for looks, a server check that puts the player back.

### Remotes: validate, limit, never grant

- All remotes are created in one module and every handler follows the same shape: **rate-limit, check types, check state, act.**
- A **token bucket** per player: bursts up to N, refilling at N a second. We pick N per remote (battle actions 15, starting a battle 2, shop 2 to 4).
- **Validate arguments strictly.** Types, whole numbers (`arg % 1 == 0`), table fields, a battle id that matches the current battle, an attempt number that matches the next expected one.
- **Make requests idempotent.** Repeating the last capture attempt number returns the old answer instead of rolling again.
- Never let a remote say "give me". A remote asks; the server checks and decides.

### Robux purchases

- The shop remote **opens the purchase prompt but never grants.** The receipt callback is the only place that grants.
- A receipt id is saved **in the same profile write as the grant**. A repeat finds the id and grants nothing. Only a successful save returns "granted" to Roblox, so a crash retries.
- Each grant checks caps first (so a paid item is never lost to a full inventory).
- Studio test purchases go through the real receipt path.

### Admin tools

- **Who is an admin is decided on the server only**: a list of user ids, and everyone in a Studio playtest. The server marks admins with a player attribute so their HUD shows the admin button. **The button is a convenience. The server re-checks on every single admin request.**
- The admin tab has pages: spawn a pet, unlock areas, reset boss progress, run an event, gift, film mode. Every page's request goes through one `AdminAction` remote with the check at the top.
- Admin accounts are kept off the public leaderboards.
- Test hooks like a debug `BindableFunction` live in `ServerStorage` and **only exist in Studio**. Clients can't reach them.
- Typed admin messages to other players were **left out**: typed text shown to players must go through Roblox's text filter. We use fixed preset messages instead, since we write the text.

### Server luck and admin abuse events

- **Server Luck** is a paid boost that raises rare-pet and mutation odds for everyone in a server for 15 minutes. The server owns the tiers and timers and shares them as attributes. Tiers stack: the highest with time left applies.
- **Exotic and rarer pets get at most x2 on any tier.** We built that as a cap on the *chance per spawn*, not on the weight (see mistakes). The shop text doesn't mention the cap and stays true because it names no per-rarity numbers.
- Roblox's rules on paid random items: luck changes the odds of *free* spawns, so it isn't a paid random item. Check the current policy for your own design.
- **Admin Abuse** is a set hour where the developers use admin powers for the players: free x8 luck in every server and "pet showers" sent to every server at once. The time window is a config (start, end), so luck turns on and off by itself in every server, even if admins are late.
- Cross-server buttons use **MessagingService**. The shower has a shared cooldown, enforced by the sender and by every receiver. MessagingService is best effort, so a server that misses a message misses that shower.
- **Studio safety:** Studio can reach live servers through MessagingService, and everyone in Studio counts as an admin. So Studio publishes only on its own separate topic. Otherwise a test could shower real public servers.
- A code that only works during the event hour carries a start time as well as an end time, so a leak before the hour is useless.

### Leaderboards and cheat checks

- Boards copy values from the profile into OrderedDataStores. We made fresh versioned boards for the launch, and admin accounts never write to them.
- When we suspected a top player of cheating, Claude checked the live data read-only: when they joined, when each boss was beaten, whether the pace fit the economy curve, and the Robux receipts. It found a clean explanation and changed nothing. The lesson: **check the data against your own economy math before accusing anyone.**

## How we did it

1. **Start from a proven design.** The first save service came with session locks, retries and a separate Studio store. Axel and Mecha Cyborg copied it, so we never rebuilt it.
2. **One save record per player**, with each system adding its own sub-table. Each task that added data said in its log whether the version was bumped.
3. **Write the loader to be forgiving.** Missing field? default. Wrong type? default. Number too big? clamp. A bad record should become a safe profile, not an error.
4. **Check scripts with an in-memory store.** For the capture and shop paths we tested lock loss, repeated receipts and "two copies arrive together" on mocks, then confirmed one real rejoin in Studio.
5. **Rate limits and checks as a habit, not a task.** Every new remote in a plan note lists its limit and its checks.
6. **Release in order.** Save changes release with a publish, then migrate every server, then watch the first minutes. For the update, we planned who presses what and when.
7. **Test the save in Studio** (below), always with a backup.
8. **Log the lesson.** Every surprise became a line in a task log's "Rejected / learned" section, and the big ones became memory notes.

### Testing saves in Studio

- **Back up before any test that changes data.** Debug commands that set materials or boosts **replace the whole table**.
- Edit-mode DataStore calls returned "Access forbidden" for us (a permission the assistant doesn't have). A **Play server** could still read and write the Studio store. So we did the backup from the Play server:
  1. At the start of Play, copy the Studio record to a backup key (set `lock = nil` on the copy).
  2. Run the tests.
  3. `Kick` the player from the server, wait about 8 s for the leave-save to clear the lock.
  4. Write the backup back, compare it field by field, delete the backup key, then stop Play.
- **Never write the live store from Studio.** To test on a copy of a real save, read the live record from a Play server (reads worked) and write it into the Studio store, never the other way round.
- A backup taken during Play contains that session's `lock`. Restoring it left a stale lock and the next Play never loaded the player's data. Always clear `lock` on the copy.
- Stopping Play from outside skips the restore. Restore right after the risky part, not only at the end.
- `require` of a server module from the MCP gives a fresh copy with no player sessions. Drive real saves through debug commands or a runtime `Script` that shares the live module cache.
- Don't test with the account's real rewards on. Read what a won test gave, then restore it.

## Rules we follow now

- **The client only asks.** If a remote can make a number go up, the number must come from the server.
- **Never grant from a remote that opens a purchase.** Grant in the receipt callback only.
- **Save the receipt id in the same write as the grant.** Reason: a crash between "granted" and "saved" would double-grant or lose it.
- **A failed load never creates a fresh profile.** Retry, then kick with a message.
- **Never write to a record you don't hold the lock for.**
- **Separate store for Studio, always.** Reason: tests must never touch live data.
- **Cap everything.** Pets (500), capsules (9,999), boost counts (999), cash. A hostile or buggy record can't grow forever.
- **New field? Default in the template, no version bump. New shape? Bump and migrate.**
- **Migrate every live server right after a publish.** Reason: old servers drop new fields.
- **Admin checks live on the server.** The client button is just a button.
- **A server-wide effect needs a per-effect cap on the chance per spawn**, not on a weight.
- **Don't promise what the text can't keep.** Shop text names no multipliers we cap.
- **Typed text shown to other players goes through Roblox's filter**, or we use fixed presets.
- **Rare rewards stay rare.** Our owner's own shower odds were turned down for a middle ground: with a small crowd they would have handed out a big share of the game's rarest pets in one hour and cheapened the paid top prizes.
- **Check `git status` before connecting Rojo ahead of a publish.** Rojo syncs the whole working tree, including another session's uncommitted files.
- **Search for `lock` in the save code before planning anything cross-server.** We almost planned a second save system for something that already existed.
- **We have a to-do to audit every remote handler** (rate limits, input checks, movement trust). Do yours early, not late.

## Mistakes and gotchas

- **A cap on a rarity's weight made it rarer.** Capping Exotic's *weight* in a shared roll made it *less* common when every other rarity's weight grew. Fix: cap the chance per spawn.
- **"x8 luck" isn't x8 per spawn.** Common and Uncommon keep their weight, so x8 made Mythic about 2.5x as common. Mutations are the part that scales fully. Tell players what you mean.
- **Studio's MessagingService reaches live servers.** A Studio test would have hit real players. Fix: a separate Studio topic and a publish check on live admin ids.
- **Old servers drop new data.** Event data and a crafted title were at risk when older servers saved. Fix: migrate every server at publish time.
- **Rojo syncs the whole working tree**, including another session's uncommitted files, so a publish could ship them. Fix: check `git status` first.
- **The Edit tool and Write tool crash a running Rojo** on our Windows setup. While Rojo serves, edit `src/` in place with a script, then check that the source in Studio changed.
- **A stale lock after a restore** blocked the next Play from loading data. Fix: clear `lock` on every restored copy.
- **A checker that reads a stale fixture.** Our old check scripts expected an old save version and failed. Update checks when the shape changes, or mark them stale in the notes.
- **Showers outside a player's streaming radius don't reach that client.** Count shower pets on the server, not the client.
- **Free codes can turn a locked prize into a weaker one.** A code granted a locked capsule tier as the starter tier. Fix: a flag on the code saying "any tier".
- **Studio playtests count as owning VIP**, so test numbers had a +25% bonus (60 showed as 75).
- **Reading live saves from Studio failed** with "Access forbidden" while ordered leaderboards still read. We used the boards as a stand-in and did not work around the permission.

## Code snippets

### 1. Load and lock a profile with UpdateAsync and retries

```lua
local LOAD_ATTEMPTS, LOCK_STALE = 8, 300
local SESSION = if game.JobId ~= "" then game.JobId else "studio-" .. HttpService:GenerateGUID(false)

local function load(player)
	for attempt = 1, LOAD_ATTEMPTS do
		local result, lockedElsewhere = nil, false
		local ok, err = pcall(function()
			store:UpdateAsync(keyFor(player), function(old)
				result, lockedElsewhere = nil, false -- the callback can run again; reset state
				local lock = type(old) == "table" and old.lock or nil
				if lock and lock.session ~= SESSION and os.time() - (lock.at or 0) < LOCK_STALE then
					lockedElsewhere = true
					return nil -- cancel: another server still holds it
				end
				local profile = if old == nil then newProfile() else validate(old) -- forgiving loader
				result = profile
				local saved = table.clone(profile)
				saved.lock = { session = SESSION, at = os.time() }
				return saved
			end)
		end)
		if ok and result then return result end
		if not ok then warn("load failed:", err) end
		if attempt < LOAD_ATTEMPTS then
			task.wait(if lockedElsewhere then 5 else math.min(2 ^ attempt, 10))
		end
	end
	return nil -- the caller kicks the player; it never creates a fresh profile
end
```

### 2. Save only while you hold the lock

```lua
-- release = true when the player leaves or the server shuts down.
local function save(player, session, release)
	local stolen = false
	local ok, err = pcall(function()
		store:UpdateAsync(keyFor(player), function(old)
			local lock = type(old) == "table" and old.lock or nil
			if not lock or lock.session ~= SESSION then
				stolen = true
				return nil -- not ours any more: write nothing
			end
			local p = session.profile
			return {
				version = p.version,
				pets = p.pets,
				cash = p.cash,
				boss = p.boss,
				lock = if release then nil else { session = SESSION, at = os.time() },
			}
		end)
	end)
	if stolen then player:Kick("Your data was loaded on another server. Please rejoin.") end
	return ok and not stolen
end

game:BindToClose(function()
	for player, session in sessions do task.spawn(save, player, session, true) end
	local deadline = os.clock() + 25
	while savesInFlight > 0 and os.clock() < deadline do task.wait(0.1) end
end)
```

### 3. A per-player token bucket for remotes

```lua
-- Allows bursts up to `perSecond`, refilling at that rate.
function Remotes.limiter(perSecond)
	local buckets = setmetatable({}, { __mode = "k" }) -- weak keys: leavers are collected
	return function(player)
		local now = os.clock()
		local b = buckets[player]
		if not b then
			b = { tokens = perSecond, at = now }
			buckets[player] = b
		end
		b.tokens = math.min(perSecond, b.tokens + (now - b.at) * perSecond)
		b.at = now
		if b.tokens < 1 then return false end
		b.tokens -= 1
		return true
	end
end

-- use: a remote handler that checks everything before it acts
local canBuy = Remotes.limiter(2)
buyRemote.OnServerInvoke = function(player, itemKey)
	if not canBuy(player) then return false, "Please wait a moment." end
	if type(itemKey) ~= "string" or not Items[itemKey] then return false, "Unknown item." end
	return PlayerData.buy(player, itemKey) -- checks cash and spends it in one step, no yield
end
```

### 4. Grant a Robux purchase exactly once

```lua
MarketplaceService.ProcessReceipt = function(info)
	local player = Players:GetPlayerByUserId(info.PlayerId)
	if not player or not PlayerData.loaded(player) then
		return Enum.ProductPurchaseDecision.NotProcessedYet -- Roblox will retry
	end
	local grant = Products[info.ProductId]
	if not grant then return Enum.ProductPurchaseDecision.NotProcessedYet end

	-- no yield between the "seen" check and the write
	if not PlayerData.hasReceipt(player, info.PurchaseId) then
		PlayerData.applyGrant(player, grant)               -- checks caps first
		PlayerData.recordReceipt(player, info.PurchaseId)  -- same profile, same save
	end
	if PlayerData.saveNow(player) then                    -- only a written save confirms
		return Enum.ProductPurchaseDecision.PurchaseGranted
	end
	return Enum.ProductPurchaseDecision.NotProcessedYet
end
```

### 5. An admin check and a safe cross-server button

```lua
local ADMIN_IDS = { --[[ your user ids here ]] }
local function isAdmin(player)
	return RunService:IsStudio() or table.find(ADMIN_IDS, player.UserId) ~= nil
end

local canAdmin = Remotes.limiter(4)
adminRemote.OnServerInvoke = function(player, action, scope)
	if not isAdmin(player) or not canAdmin(player) then return end -- re-check every time
	if action == "shower" then
		if os.time() - lastShowerAt < 120 then return false, "Cooldown." end
		lastShowerAt = os.time()
		if scope == "all" then
			-- Studio publishes on its own topic so a test never reaches live servers
			local topic = if RunService:IsStudio() then "AdminAbuseStudio" else "AdminAbuse"
			MessagingService:PublishAsync(topic, { cmd = "shower", at = os.time() })
		else
			runShower()
		end
	end
end
-- receivers also enforce the cooldown, and only live servers listen to the live topic
```

## Prompts that worked

**Real (cleaned): plan before building a cross-server feature.**

```text
Before planning the Arena, search the save code for how profiles are locked and loaded. Tell me what already protects a player moving between servers, and only then list what's missing.
```

**Real (cleaned): the safety review.**

```text
Check this player's data read-only against our economy math. When did they join, did the boss wins come at a believable pace, and do the cash numbers match their pets? Don't change anything. Tell me if it looks like cheating or not, and why.
```

**Example (our style): audit one system.**

```text
Audit every remote in the shop service. For each one, list its rate limit, its type checks, and what the server verifies before it changes data. Show me a table, then fix only the gaps, and tell me how you tested each fix.
```

## Do it in your game

1. **Make one data service.** Start it first. One profile table, loaded on join, written only through functions. Use `UpdateAsync`, not `SetAsync`.
2. **Add the session lock, retries, autosave and `BindToClose` at the start.** They are much harder to add after players have data.
3. **Use a separate store in Studio**, and turn on Studio API access. Write a short note on how to back up and restore your test save.
4. **Create a remotes module** with a token-bucket limiter. Make every handler: limit, check types, check state, act.
5. **Keep a rule list**: the client sends a key, the server computes the result; purchases grant only in the receipt callback; admin checks live on the server.
6. **Before each publish**, check `git status`, then publish, then migrate every server. Keep a task log of what changed in the save.

```text
I'm adding saving and security to my Roblox game. [fill in: your game in one sentence, what players own that must be saved (pets, cash, items), whether you sell anything for Robux]. I want one data service with session locks, UpdateAsync with retries, autosave, a separate Studio store, and forgiving loading. I want every remote to be rate-limited and validated, the server to decide every number, and Robux grants only in the receipt callback. Please read my CLAUDE.md and notes first, then ask me design questions with a default for each before you plan the sessions. What questions do you have for me so I can help you implement this feature as best as possible?
```
