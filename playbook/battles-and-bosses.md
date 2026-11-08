# Battles and bosses

How to build wild-pet fights, capture, bosses, a co-op event boss, duels and training in a Roblox game with Claude Code. Comes from Battle for Pets (the deep source), with a short look at combat in +1 Mecha Cyborg.

## What we built

### Wild pets and the encounter

- Wild pets live in invisible **habitat markers** placed on flat ground, grouped by area. A spawn service picks a species by rarity weight, rolls a mutation, rolls a level from the area's range, and drops the pet at a random marker.
- The **server** picks every walk path and publishes it as attributes on the model (`MoveFrom`, `MoveTo`, `MoveStart`, `MoveDuration`). The **client** only animates it. No physics replication of 12 wandering pets per area.
- Each area keeps about 12 pets. A pet stays 3 to 4 minutes, then a replacement spawns a few seconds later. (We first had 9. We raised it because playtests felt empty.)
- Walking up shows a card with a BATTLE! button on the pet (a `BillboardGui` plus a prompt). The card must be `Active` and fire on touch-down, or phone taps do nothing. We shipped that bug once.
- All spawn odds live in one shared table. A check script simulates a million spawns and prints the odds of every pet, so the numbers we tell players are the numbers the server rolls.

### The battle itself

- It is **real time**, like Pokemon GO, not strict turns. You get a fast attack (builds energy), a charged attack (spends energy, big hit), a dodge (about 0.7 s of only 25% damage), a pet switch and a run button. A team is 3 pets and one fights at a time.
- The wild pet attacks on its own. Its charged attacks are **announced first** (a "warn" event with a server time) so the player can dodge. Rarer wild pets give less warning (1.6 s for Common down to 0.7 s for Secret).
- **The server runs the whole fight.** One service owns each battle record and steps it every Heartbeat. The client sends only button presses (`"attack"`, `"charged"`, `"dodge"`, `"switch"`, `"run"`). It shows whatever the server says happened.
- The client draws everything else: a cinematic entrance, a three-quarter camera that pulls back until both pets fit the screen (giants on portrait phones), hit effects, damage numbers.
- A battle ends on a win, a loss, a run, a 180 s time limit, or the player walking too far away (a "leash").
- **Health is session-only.** Pets keep damage after a fight, hurt pets slowly heal, fainted pets heal only at the player's ranch. A team with no healthy pet can't start a battle. That makes the ranch matter.

### Stats and damage

- Health and attack come from **rarity base x area step x species shape x mutation x level**. Each species has its own fast and charged move. Rarity steps are big on purpose.
- The level multiplier is `1 + 0.08 x (level - 1)`.
- Wild pets get a level from their area. A higher level also makes them harder to catch.
- We added a tiny wild-only scale table for one species that one-shot every Mythic in its area. Caught pets keep their normal stats.

### Capture

- Beating a wild pet opens a **capsule** screen. A capsule tier has a catch chance per rarity and a cooldown. Up to 3 throws. Failing all three frees the pet for others, but blocks that one player on that one spawn.
- The catch chance is multiplied by a level penalty: `max(0.40, 1 - 0.015 x (level - 1))`.
- Capsules are bought or crafted, and each tier unlocks after a boss. That ties capture, bosses and the economy together.
- The tutorial's first throw always catches. The server decides that from its own saved flag. The client cannot ask for it.

### XP

- Pad training came first (stand on your pad and the pet levels). Later we found battling, the main activity, gave no XP. We added **XP per wild win**: one level's worth at the wild pet's level, times a rarity multiplier (x1 Common up to x3 Mythic and rarer).
- Pets that fought get all of it. The bench gets half.
- Bosses, wardens and event bosses give no XP. Catches arrive at half the wild level.
- It shows as a designed **XP card**, not a plain toast.

### Bosses and locked areas

- Each area ends with a boss. **The first win is saved on the server and opens the next area.** Later wins are for fun.
- Boss fights are **private**. The fighter's screen hides the real boss and fights a copy, so any number of players can fight the same boss at once.
- A boss has a confirmation window first (a CHALLENGE card, then the boss's moves with tips and a FIGHT button), then a cutscene with a SKIP button.
- Every boss has 3 moves on top of its fast attack. They are built from a small set of **special kinds**, all server-owned:
  - a warned charged hit (dodge it),
  - a **storm** (several hits, each announced on its own),
  - a **heal** you can break (a charged hit during the warning stops it),
  - a **bask** (heals, but fast hits cut the heal),
  - a **mirror** shield (charged hits bounce back),
  - an **armor** you crack with enough charged damage, and a **whiteout** enrage at low health,
  - a ticking **stack** (Frostbite) that clears when you switch the pet out.
- Gates are walls on the client plus a check on the server. The client draws a LOCKED sign and a wall across the gate. The server puts back anyone found past a locked gate. **Never trust the client wall alone.**
- Boss numbers (health, attack, tips, gates) sit in one config. A balance script runs the real battle step for hundreds of seeded fights per team and prints win rates.

### The Kraken: a co-op event boss

- Every 30 minutes on the real clock (the same in every server), the Coast darkens and a giant boss rises. Players tap JOIN and are teleported to the beach.
- Phase 1: each player fights tentacles with the normal battle controls. Phase 2: roaming ink minions guard a shield. Phase 3: everyone fights the whole boss with **one shared health bar**.
- Between phases are two **dodge breaks** in the open world (hazards judged on the server).
- A design choice we liked: tentacle and minion health **scales to each player's own team**, but the whole-boss health grows only slowly with team strength. Strong teams finish it faster and earn the top-damage reward.
- Architecture, a "hybrid": each player's battle is a normal battle on the existing engine, in a **slot** on the beach. The event service owns the shared state and broadcasts key moments with server times, so every screen animates every slot together. The boss model is built on each client from templates.
- Rewards are granted once per event id. A player who disconnects at the very end gets a **pending reward** in a separate store, claimed once on rejoin.

### Arena duels (first slice built)

- The plan is a PvP Arena with a Classic league (every pet evened out) and a Master league (real stats, one set level). So far we built the **duel core**: a service with two equal **seats**, each a player or a bot, on the same battle rules.
- A charged attack is warned to the other side about 1 s ahead. Each side has 2 **Shields** that fully block a charged hit. A shield is spent only when that hit lands, so a dodged or cancelled hit can't bait one out.
- 180 s matches, most health left wins. Duel pets are fresh copies, so real saved health is never touched.
- A bot seat presses the same buttons through the same checks as a player, so duels can be tested alone in Studio.

### Training

- Pets level on the player's own pad. The server checks ranch ownership and position every 0.25 s. XP only counts while the player is held on the pad. No offline XP. No catch-up burst after a lag spike.
- Levels cap by boss: beating a boss raises the cap. No banking XP at the cap.
- Nine training dummies unlock one per area. Each gives a bigger XP rate for a bigger price.

### Combat in +1 Mecha Cyborg (short)

- A different genre, the same rule: **the server owns combat**. A punch is a remote with a 4-per-second token bucket. The server picks the nearest live enemy within reach.
- Enemy numbers are written **relative to the stage's recommended Power**, so one table scales to any stage.
- Enemies **take turns**: one attack may start per player every 0.6 s, shared by the wave. A bigger wave keeps pressure up longer instead of hitting harder.
- Boss attacks have a telegraph, a damage check on the server, a roar that cancels attacks still on their way, and knockback on only some moves.

## How we did it

1. **Read the notes first.** Before each combat task Claude searched the notes and task logs for the boss or system name and read what was approved or turned down.
2. **Design questions first.** For anything new (a boss, the Kraken, the Arena) Claude asked 5 to 10 questions with a default each. We answered "d" for "use your default". Then it wrote a plan note with the decisions in a table.
3. **Server first, art later.** Every big feature was split into sessions:
   - Event server: rules, config, state machine, check script, placeholder visuals.
   - Event client: HUD, battle mode, hazards. The first playable fight.
   - Model, rig, animations, cutscene, effects, rewards: one session each.
   - The Kraken was planned as 9 sessions. The Arena is planned as 11.
4. **One config file per system** holds every number. Pure rules live in their own module so a check script can run them without Studio.
5. **Check scripts before playtests.** We wrote scripts that run the real battle step on seeded fights: win rates by team, fight lengths, and "boss fights unchanged" comparisons (344,135 identical events across 1,600 runs after the Kraken change).
6. **The Rojo plus Studio MCP loop.** Edit files, Rojo syncs, Claude drives a Play session through the Studio MCP: spawn a wild pet, hold the prompt, send `BattleAction` remotes, read the log. Then screenshots from the battle camera.
7. **Playtest tuning with the owner.** The first Kraken round changed the multiplier, the hazards and the minions. Tuning came from real play, not from the plan.
8. **Multi-client tests last.** Studio's Clients and Servers test needs the owner present. We used one real player plus simulated bots until the end of the session.
9. **A task log after each task**, with a "Rejected / learned" section.

## Rules we follow now

- **The server decides every outcome.** The client sends a button name. It never sends damage, a result or a "caught it" flag. Reason: anything the client says can be faked.
- **Check every action against a timer on the server.** Cooldowns, energy, "is a warn open right now". Give a small lag tolerance (0.1 s) so honest players on bad ping aren't punished.
- **Rate-limit every battle remote.** We allow 15 actions a second in combat and 2 a second for starting battles.
- **One config per system, one check script per rule.** If the numbers are scattered, balance changes break things silently.
- **Boss fights are private copies.** Reason: any number of players fight at once, and nobody's fight changes another's.
- **Save the first boss win only on the server** and drive gates from it.
- **Every dodge needs a warning the player can read.** We announce charged moves with a server time, and we checked that a human can react at normal ping.
- **Boss animation must be big and readable.** The first Crownjaw clips were rejected: "tail doesn't even move", "barely any movement at all". For Kingcroak: "frog doesn't hop or stick its tongue out". Now we author whole-body wind-ups and signature actions.
- **Skip review renders and videos for new boss rigs.** We said "Scratch the contact sheet and review video". Check motion with bake checks and Studio stills from the battle camera. Reason: limited usage.
- **Rewards on the win screen get a designed card.** About the first XP toast we said "make the ui look better, it's not great". Use the same style as the other reward pop-ups.
- **No new progression layers.** We turned down pet star upgrades. Reason: levels already exist and the game should stay simple. Prefer consumables that plug into levels, capsules and battles.
- **Pets keep real stats in PvP.** Evening out every pet as the only rule was turned down. So was raw stats with only power matching. Two leagues cover both.
- **Don't build what wasn't asked.** About PvP in Mecha Cyborg we said "i didnt mean pvp". The ideas went into a plan note and stayed unbuilt.
- **Ask when a game-structure answer can be read two ways.** "Boss every other round" meant no boss until Stage 3, not a boss in every stage.
- **Make the first minute forgiving.** The tutorial's wild pet is weaker while the guaranteed catch is unused. Before that, too many first battles were lost.
- **Search for what already exists before planning.** Claude once told us saves weren't locked to one server. They were. Search the code for the word first.

## Mistakes and gotchas

- **Phone taps did nothing on the BATTLE card.** The BillboardGui wasn't `Active` and the button fired on release. Fix: `Active = true`, fire on touch-down.
- **Cramped spawns refused battles** ("There's no room to battle here"). A pet on a tiny islet couldn't fit a 3-pet team plus the player. Fix: a placement service searches nearby spots in the same area, reserves the pet's home, and sends it back after.
- **A balance script that never applied player attacks.** After we added attack wind-ups, our balance check showed 0 wins for every team. It had never called the step that lands player hits. Fix: run the real per-frame step.
- **The rarest pet was caught in five minutes** in the first playtest. We asked for a harder balance, and wild stats now scale with rarity and area.
- **Bosses need a "who should win" table.** For example: a level-30 team with a Mythic wins most runs, Epic teams win none. That is the design target, not a feeling. Run it after every balance change.
- **Event hazards drew on every screen.** Any server-wide hazard signal needs a per-player check on the client before drawing screen-space effects.
- **Knockback threw the player out of the arena** (Mecha Cyborg boss). Now only a few moves push, and slower.
- **A roar didn't cancel an attack already on its way.** Attacks started just before a phase change still landed. Fix: a fight token on the server and an epoch on the client.
- **Enemies hit harder as waves grew.** Every enemy in reach attacked on its own timer. Fix: a shared attack gap per player.
- **The shared-health path can break the single-player path.** Event battles use a shared boss health. Test both after any change to hit handling.

### Studio MCP testing gotchas

- `require` of a server module from `execute_luau` gives a **separate, unstarted copy**. For live state, create a `Script` from the call, let it write JSON into a `StringValue`, poll it, then delete it. Or use a debug command (below).
- Each `execute_luau` call times out around 60 to 90 s, and threads started inside it die when it ends. Keep each test under about 50 s.
- The MCP resets the camera to Custom after every call. Set the camera inside the same call as the thing you're photographing.
- The BATTLE prompt only works once it has been shown. Teleport next to the pet, wait about 1 s, then `InputHoldBegin`. Don't move during the hold.
- Simulated key presses arrive late and unevenly (14 s late and 0.2 s apart in our run). They can't hit a 4-second timing window. Hold the window open on the server for that test.
- A teleport into a locked area is sent back by the server. Set the test's `UnlockAll` attribute first.
- Screenshots don't draw `BillboardGui` cards in `PlayerGui`. Use a real window capture or read the GUI.
- Changing a module's table through the MCP doesn't change the running game. Edit the file, let Rojo sync, restart Play.
- Boss fixture: start the first fight, then wait for the area to stream in before judging visuals.
- A won test event gave the test account real rewards. Read the reward text, then restore cash, materials and records.
- **Back up the test account's save before battle tests and restore it after** (see the saving guide).
- Studio caps Play at 15 FPS when its window isn't focused. Compare script time, not FPS.
- **Debug commands:** a `BindableFunction` in `ServerStorage` that exists only in Studio gives fixed commands (`give`, `spawn`, `reset`, `health`, `beatBoss`, `kraken`, `duel`). Clients can't reach it. This is how Claude spawns, heals and sets up cases.

## Code snippets

### 1. A battle action handler: limit, check, then act

```lua
-- The client only sends a button name. Every check happens here.
local canAct = Remotes.limiter(15) -- token bucket, see the security guide
local TOLERANCE = 0.1              -- seconds of lag we forgive

actionRemote.OnServerEvent:Connect(function(player, action, arg)
	if type(action) ~= "string" or not canAct(player) then
		return
	end
	local battle = battles[player]
	if not battle or battle.ended then
		return
	end
	local now = os.clock()
	if action == "attack" then
		-- ready? (a little early is fine; it doesn't shorten the next cooldown)
		if battle.playerAttack or now + TOLERANCE < battle.readyAt then
			return
		end
		battle.readyAt = math.max(now, battle.readyAt) + fighter.stats.fast.cooldown
		battle.playerAttack = { at = now + windup, kind = "fast" } -- damage lands later, on the step
	elseif action == "switch" then
		if type(arg) ~= "number" or arg % 1 ~= 0 then
			return
		end
		local target = battle.team[arg]
		if not target or target.health <= 0 or now < battle.switchReadyAt then
			return
		end
		switchTo(battle, arg)
	end
end)
```

### 2. Stats from rarity, area, species shape, mutation and level

```lua
local function statsFor(species, mutation, level)
	local base = RarityBase[species.rarity]            -- e.g. Common 100/10 ... Mythic 1000/100
	local shape = Shapes[species.key] or { health = 1, attack = 1 }
	local bonus = (MutationBonus[mutation] or 1)
		* (AreaStep ^ (species.area - 1))              -- each area is 1.5x the one before
		* (1 + 0.08 * (level - 1))
	return {
		maxHealth = math.round(base.health * shape.health * bonus),
		attack = math.max(1, math.round(base.attack * shape.attack * bonus)),
	}
end

local function damage(attack, moveMultiplier)
	return math.max(1, math.round(attack * moveMultiplier))
end
```

### 3. A capture throw the client can't cheat

```lua
-- Validate the ordinal, spend the capsule and roll with NO yield in between.
local function tryCapture(battle, arg)
	if type(arg) ~= "table" or arg.battleId ~= battle.id then return end
	-- a repeated attempt number gets the same answer back, never a second roll
	if battle.lastResponse and arg.attempt == battle.lastResponse.attempt then
		send(battle, "captureResult", battle.lastResponse)
		return
	end
	local tier = CaptureConfig.tier(arg.capsuleKey)
	if not tier or not PlayerData.canUseCapsule(player, arg.capsuleKey) then return end

	local level = battle.wild.level
	local chance = tier.chance[battle.wild.rarity] * math.max(0.40, 1 - 0.015 * (level - 1))
	local guaranteed = battle.guaranteed and PlayerData.firstCatchReady(player) -- server-side flag only
	local result = CaptureRules.attempt(battle.capture, arg.attempt, if guaranteed then 1 else chance, rng:NextNumber())
	assert(PlayerData.useCapsule(player, arg.capsuleKey))
	if result == "success" then
		PlayerData.addPet(player, battle.wild.species, battle.wild.mutation)
	end
end
```

### 4. A warned charged attack the player can dodge

```lua
-- Server: announce first, judge later, using server time.
local function startWildCharged(battle, now)
	local warning = ChargeWarning[battle.wild.rarity] -- 1.6 s Common ... 0.7 s Secret
	battle.pendingHit = { at = now + warning, multiplier = 3.5 }
	send(battle, "warn", { at = serverTimeIn(warning), seconds = warning })
end

local function stepPendingHit(battle, now)
	local hit = battle.pendingHit
	if hit and now >= hit.at then
		battle.pendingHit = nil
		local dodged = now < battle.dodgeUntil -- set by the "dodge" action
		local dmg = damage(battle.wild.attack, hit.multiplier)
		if dodged then dmg = math.round(dmg * 0.25) end
		applyDamageToActivePet(battle, dmg)
	end
end
```

### 5. Enemies take turns (a shared attack gap)

```lua
-- One attack may start per player every GAP seconds, shared by the whole wave.
-- Without this, damage grew with the number of enemies in reach.
local GAP = 0.6
local function tryEnemyAttack(run, enemy, now)
	if now < run.nextAttackAt then
		enemy.retryAt = now + 0.2 + math.random() * 0.4 -- wait your turn
		return
	end
	run.nextAttackAt = now + GAP
	beginWindup(enemy)
end
```

## Prompts that worked

**Real (cleaned): design a big feature before building.**

```text
I want a server-wide event boss. Before you build anything, read the notes on bosses and battles, then ask me design questions with a default for each. Give me 2 or 3 architecture options side by side with the one you recommend. Don't write code yet.
```

**Real (cleaned): boss animation feedback.**

```text
The tail doesn't even move and there is barely any movement at all. Keep the same rig and timings, but make every clip big and readable from the battle camera: whole-body wind-ups, a tail that whips with each joint lagging the one before. Check stills from the battle camera side.
```

**Example (our style): test a fight through the Studio MCP.**

```text
Spawn a level 5 wild Cat near me, start the battle through the real prompt, send attack and dodge actions, and read the log. Back up my test save first and restore it after. Tell me the fight length and whether anything errored.
```

## Do it in your game

1. **Write the loop on paper first.** What does one fight look like, in how many seconds, with what buttons? Decide real-time or turn-based now.
2. **Put every number in one shared config** (health, attack, cooldowns, warnings, rarity steps). Add a tiny check script that simulates fights.
3. **Make the server own the battle.** One service, one battle record per player, a Heartbeat step. The client only sends button names.
4. **Start with one wild enemy and one area.** Get fight, win, reward and save working. Then add capture, then XP, then a boss.
5. **Add the first boss with a gate.** One warned charged move, one storm. Save the first win on the server and open the next area from it.
6. **Add a debug hook for Studio only** (spawn, heal, give, reset) so Claude can test through the MCP without clicking.

```text
I'm building a Roblox battle system for my game. [fill in: your game in one sentence, your fight style (real-time or turn-based), what you can fight (wild creatures, bosses, players)]. I want the server to own the fight, the client to only send button presses, every number in one config, and a check script that simulates fights. Please read my CLAUDE.md and notes first, then ask me design questions with a default for each before you plan the sessions. What questions do you have for me so I can help you implement this feature as best as possible?
```
