# Economy and Robux items

How we built the money, upgrades and Robux shop in Battle for Pets, and the rules we learned on the way. Everything here comes from Battle for Pets unless it says otherwise.

## What we built

**The money side (cash earned by playing):**

- **Passive income.** Every pet you own and place in your pen earns cash per second. A pet's rate is `rarity base x area factor x mutation multiplier`. Online time only, no offline earnings.
- **The pet Index.** Every species has a card. Unknown species show as a black silhouette and "???" until you catch one. Each discovery pays a small reward (30 seconds of that species' income). Finishing every species in an area pays a bigger area reward and unlocks that area's mount. A Claim All button pays everything waiting. A red bubble on the Index button shows how many rewards are waiting.
- **Cash sinks (things to spend on):**
  - Capsules and supplies (Potion, Revive, Training Treat) at the Gear shop.
  - Training dummies (better ones train faster).
  - Pen levels 2 to 5 (more room for pets).
  - Fusion (three pets in, one better pet out, costs cash).
  - Crafting (cash plus materials).
  - Cosmetics: name colors, titles, capsule throw trails, pen themes.
  - A Sell station that turns a pet into cash.
- **Materials and crafting.** Every area has one material in three grades plus one boss material. Wild wins and boss wins drop them. Crafting turns them into special capsules and boosts. Higher pen levels also need materials, so growth follows how far you have played, not only how rich you are.
- **Boosts.** Crafted or bought consumables that sit in an Inventory with a USE button: Steady Hand (a 4th capsule throw), Lucky Clasp (a missed throw may give the capsule back), Rare Incense (extra rare wild pets spawn near you, only you can battle them), Fusion Catalyst, EXP Share, Pet Radar and more.
- **Server Luck.** A Robux purchase that raises the odds of rare pets and mutations for everyone in that server for 15 minutes. The whole server sees who bought it.

**The Robux side (about 40 items by now):**

- **Game passes (once):** a permanent 4th capsule throw, VIP (tag, gold chat name, +25% training XP), Extra Pen Slots (+4 pets).
- **Developer products (bought again and again):**
  - Boosts: 2x Money and 2x Training XP in singles and packs of 5, a Second Try Clock, crafted-boost bundles.
  - A **Money ladder**: four stackable permanent 2x steps, 6 / 49 / 129 / 299 R$, up to x16.
  - **Cash packs**: Small, Medium, Large (25 / 99 / 299 R$). The amount scales with the buyer's own income.
  - Capsule packs (9 and 29 R$), a once-per-player Starter Pack (79 R$), a limited-time Featured Pets lineup (49 / 129 / 399 R$ for 1 / 3 / 10 random pets with the odds shown), Server Luck x2 / x4 / x8 (29 / 79 / 199 R$), and event items (an event mount, event currency packs, a "summon a meteor shower" product).
- **Where players meet an offer:**
  - The Robux Shop window (Featured, Boosts, Cash, Passes, Packs, Codes tabs).
  - A small chip on the cash counter ("2x MONEY", R$ 6).
  - A pop-up at the moment of need (full pen, a capture that got away, out of capsules).
  - A speech bubble in the Gear shop.
  - A rainbow "x2 Luck" button on the HUD.
  - When you press buy without enough cash, Roblox's purchase prompt for the smallest cash pack that covers the gap opens straight away.
- **Sales banners.** The Featured tab has a banner card like other games' egg banners: a limited-time pet lineup with the odds, a ribbon, art and a countdown.

## How we did it

Order matters. We did it in this order and would again.

1. **Cash only, no Robux.** We built income, the Index, selling, pens and fusion first, all server-owned. Only when money felt right did we touch Robux.
2. **Materials and crafting as a second loop.** We wrote a plan note first (decisions, tables of drops and costs, what we were NOT doing) and the user approved it before any code. Sources of materials: wild wins and boss wins only. No shops that sell materials, no breaking pets down for parts.
3. **A data-driven Robux Shop with fake items.** One config file lists every item (id, section, name, price, grant, icon). The window, the server prompt and the receipt handler were built and tested with placeholder items and a Studio-only "test purchase" command before any real product existed. Adding an item later is one table entry.
4. **Choose the real item list in one question round.** We asked the user for the items, then prices, then rules. We wrote the answers into a plan note with a "Rules (decided)" section.
5. **Build each item's perk before its product goes on sale.** A pass that does nothing is a refund request. Items carry a `ready` flag in the config, set only when every part is built.
6. **Create the products on the Creator Dashboard.** Managed pricing off at first. Store images for every pass and product (we generated them with a small script so they all match). We read product IDs back with `MarketplaceService:GetDeveloperProductsAsync()` instead of copying them by hand, and compared the dashboard's prices with the config.
7. **Show Roblox's live price.** The shop cards ask Roblox for the player's own price, so regional pricing works. The price in the config is only a fallback, and we keep it equal to the dashboard.
8. **Economy update (a big rebalance).** After a few weeks a playtester was sitting on a huge pile of cash with nothing to buy, and nobody was spending Robux. We wrote a plan note, built it in two sessions, ran a read-only dry run on every live save, and released it with "Shut Down All Servers". Details in the rules below.
9. **Watch the data.** Every shop opening, offer pop-up, button press and purchase is logged as a funnel, and every cash gain or spend is logged as an economy event. We read it after each change (see the launch and growth guide).

**How we worked with Claude on this:**

- The first message for any money change was: "plan it, don't build it". Claude wrote the plan note with tables of today's numbers and the new numbers. The user answered, Claude updated the note, and only then came the build.
- Server work (receipts, saving, income) always ran on the strongest model and the highest effort. Look-only work ran lower.
- UI for offers (the chip, the speech bubble, the banner) was mocked in Figma over a real screenshot first, 2 to 3 options, and the user picked one.
- Claude ran check scripts after each change (see "Mistakes"): a script that simulates 100 repeated claims, a receipt arriving twice, a cap being hit, and so on.

## Rules we follow now

**Pricing and tiers**

- **Paying should make progress faster.** The user's words: that is "the whole point of spending". We do not apologize for pay-to-progress-faster items like cash, training and capsules.
- **Never sell damage.** "It would make progression very skewed." No 2x Damage product, ever. Reason: battles and boss gates are the skill part of the game, and a damage purchase skips them.
- **Cheapest worthwhile item first.** Nobody was spending, so the first rung is a permanent 2x Money for 6 R$. Then a few tiers up to x16. The user found "very high multipliers absurd", so the ladder stops at x16.
- **Few tiers.** Four rungs, not ten. Each extra tier is more to explain on a phone screen.
- **No progress locks on Robux items.** "If someone is willing to buy something, give it to them." A capsule pack sells before its boss is beaten.
- **Do not sell for cash what is sold for Robux.** Otherwise the Robux item loses its point.
- **Do not name per-rarity numbers in shop text** when a hidden cap exists. Server Luck caps the top three rarities at x2 on every tier, and the card just says "rare pets and mutations show up far more often". The text stays true.
- **Prices from the config are a fallback.** The card shows Roblox's price for that player.

**Economy design**

- **Do not add new upgrade layers.** We turned down pet "star upgrades" because pet levels already exist and the game "should remain somewhat simple". We turned down rebirth/prestige too. Reuse levels, capsules, battles and fusion instead; sell things that plug into them.
- **Prices follow income, by area.** Every price that belongs to an area is multiplied by that area's factor. Income grows x5 per area, prices x2.5 per area, and the one-time conversion for existing players used the price factor so they could buy exactly what they could before. The first version of the curve had prices as hand-typed numbers; they fell behind income and a player ended up rich with nothing to buy.
- **One rarity step is worth about one area** (above Legendary). A top-rarity pet in a middle area earns what a lower-rarity pet in the next area does. Players can reason about it.
- **Rewards paid in "seconds of the player's income" scale on their own.** Cash packs, playtime gifts, discovery rewards and codes all use this, so we never had to re-price them. Show the player a plain cash amount, never "minutes of income".
- **Consumables that skip training must cost at least a couple of minutes of income.** Training Treats (a level for the whole team) were cheap for rich players and made training pointless. Now the price is the bigger of the area price and 2 minutes of the pets' own income. We offered three options; the user chose the one that "tracks how rich a player is".
- **Supplies are a poor late-game reward.** Their price grows x10 per boss while income grows about x5 per area, so one Revive can be worth many minutes of a late player's income. Do not use them as big rewards.
- **Stacking rules:** the same boost doesn't stack with itself (extra ones wait in the Inventory). Different multipliers do stack (ladder x boost x friend bonus), and we calculated the maximum (x48) and checked it was OK.
- **Bought pets should not trivialize the start.** A featured lineup of late-area pets would give a new player a huge head start, so lineups use earlier areas' pets, and the rare top prize stays small (0.5%).
- **Lineup odds follow power.** Each pet in a lineup must be stronger than every more common pet in it. Check the real base powers before a lineup goes on sale.

**Offers and popups**

- **Offers must not nag.** Our limits: after the tutorial, after the player's first 20 minutes, at most once per 10 minutes per moment, never in a battle or cutscene, never for items they own.
- **Remove an offer if it annoys.** A locked-boost offer covered the phone HUD and sold very little, so we deleted it. The tips still tell the player how to get the boost by playing.
- **If the player pressed buy, don't put a card in the way.** When a buy is short of cash, open the Roblox prompt for the right cash pack at once.
- **Keep the HUD clean.** Our luck ad pill had empty space inside it and the screen was crowded; we shrank it and planned "one pop-up at a time".
- **A Starter Pack chip was turned down.** The user wanted the full pack pop-up with the featured pet showing, which the player can close.

**Safety and policy**

- **The server owns everything.** Money, ownership, boost timers, grants. The client only asks to open a purchase prompt.
- **Receipts are recorded in the same save as the grant** and the receipt handler only says "granted" after that save wrote.
- **Paid random items.** Roblox requires showing odds before purchase and blocking paid random items where a player's policy restricts them. We check the policy on join. Blocked players don't see the Starter Pack or Featured Pets and their prompts are refused by the server. Cash packs are blocked too because cash pays for fusion, whose results are random. Items that only change the odds of free spawns (Server Luck, incense packs) are not paid random items.
- **Show the odds on the card** for any random product, and keep the odds in the config so the card and the roll can't drift.
- **Gift buttons must check the receiver's policy too** if gifting is ever built.

## Mistakes and gotchas

- **Prices drifted from income.** Hand-typed prices fell behind within three weeks. Fix: a formula (area factor) plus a check script that prints "time to afford this item" at normal and boosted income and flags outliers. Run it whenever income changes.
- **A price table was missed.** In the economy update, the Gear shop supplies still grew x4 per boss. We found it by asking "does everything match the new curve?" the day of release and fixed it before publishing. Lesson: grep for every place a price or reward lives (shop, crafting, pen, rewards, cosmetics, cash packs, codes) and list them in the plan.
- **Old servers keep old code.** A migration that multiplies cash must not run twice. We put the version bump in the same saved record as the cash, and old servers keep the stored version when they save. Old servers would also drop new fields, so the release shut down every server. No rolling back afterwards, fix forward. The save keeps the before-values (`preUpdate`) so any player can be checked or corrected later.
- **A dry run saved us from surprises.** A read-only command (Studio only) read every live save and printed each player's cash and income before and after, plus anyone who would hit the cap. We reviewed it before publishing.
- **Capping a weight is not capping a chance.** For Server Luck we first capped the top rarities' weight. In a weighted roll, other weights growing made those pets rarer than with no luck. Cap the chance per spawn instead.
- **x8 weight is not x8 per spawn** when some rarities keep their weight. x8 weight gives rare pets about 2.5x as often. Only mutations scale fully. Explain odds with real simulated numbers, not the multiplier.
- **Cash stored as a double.** Above about 9 quadrillion the last digits round. We set a cap (1e18), added suffixes up to quintillion everywhere money is shortened, and showed the player list's money as text.
- **A leaderboard store mixed converted and unconverted cash.** We made a new leaderboard store at the switch.
- **A stale test fixture.** After rules changed, two old checks failed because the "bounds" were too tight. Update checks with rules.
- **Studio test purchases still grant.** Never click near Buy while closing a test purchase sheet. The sheet doesn't show in screenshots; read its item name and close it via its close button.
- **Cloud shop products weren't re-scaled.** One capsule and one dummy kept old prices after a new area opened, and the next area ended up 15x more expensive. Put "re-scale when the area ships" on the area's release checklist.
- **Store images for lineups.** The images for a limited-time lineup go up when the lineup takes over, not at the update release, because an earlier event's lineup may still be on sale.
- **Dashboard prices vs config.** The user changed some prices on the dashboard; the config fallback was stale. We now compare them with a Studio read.
- **Claude's first answer was wrong about saves.** It said saves weren't locked to one server. They were. Search the code before planning anything cross-server.

## Code snippets

### 1. ProcessReceipt that grants once

```lua
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")

local inFlight = {} -- purchase ids being handled right now

local function processReceipt(receipt)
	local player = Players:GetPlayerByUserId(receipt.PlayerId)
	local item = Config.byProduct(receipt.ProductId)
	if not player or not item then
		return Enum.ProductPurchaseDecision.NotProcessedYet -- Roblox retries later
	end

	local id = tostring(receipt.PurchaseId)
	if inFlight[id] then
		return Enum.ProductPurchaseDecision.NotProcessedYet -- two copies arriving at once
	end
	inFlight[id] = true

	local ok, decision = pcall(function()
		PlayerData.waitUntilLoaded(player)
		-- grant + record the receipt in the SAME profile table
		local result = PlayerData.grantReceipt(player, id, item.grant)
		-- "new" = granted now, "known" = already granted before
		if not result or not PlayerData.saveNow(player) then
			return Enum.ProductPurchaseDecision.NotProcessedYet -- save failed: retry
		end
		return Enum.ProductPurchaseDecision.PurchaseGranted
	end)
	inFlight[id] = nil
	if not ok then
		warn("receipt failed:", decision)
		return Enum.ProductPurchaseDecision.NotProcessedYet
	end
	return decision
end

MarketplaceService.ProcessReceipt = processReceipt
```

Why: Roblox calls this again until you return `PurchaseGranted`. Return it only after the save wrote. Return `NotProcessedYet` for anything unknown (absent buyer, failed save, profile still loading) so the player is not charged for nothing.

### 2. The grant itself: record the receipt in the same save

```lua
function PlayerData.grantReceipt(player, purchaseId, grant)
	local session = sessions[player]
	if not session or not session.canSave then
		return nil -- profile not loaded or locked: don't grant
	end
	local profile = session.profile
	if profile.receipts[purchaseId] then
		return "known" -- repeat, grant nothing
	end
	-- validate EVERYTHING before changing anything
	if grant.boost and not BoostConfig.get(grant.boost) then
		return nil
	end
	applyGrant(profile, grant) -- cash, boosts, capsules, tier ...
	profile.receipts[purchaseId] = os.time()
	pruneOldest(profile.receipts, 100) -- keep the newest 100 only
	return "new"
end
```

### 3. Prices that follow the area, not hand-typed numbers

```lua
-- Income grows x5 per area, prices x2.5 per area.
local INCOME_GROWTH, PRICE_GROWTH = 5, 2.5

local function incomePerSecond(rarityBase, area, mutationMult)
	return rarityBase * INCOME_GROWTH ^ (area - 1) * mutationMult
end

local function areaPrice(meadowPrice, area)
	local raw = meadowPrice * PRICE_GROWTH ^ (area - 1)
	-- round to 2 significant digits so the number looks designed
	local digits = math.floor(math.log10(raw))
	local step = 10 ^ math.max(digits - 1, 0)
	return math.floor(raw / step + 0.5) * step
end

-- A consumable that must not replace training:
local function treatPrice(areaPriceValue, pets_incomePerSecond)
	return math.max(areaPriceValue, pets_incomePerSecond * 120) -- at least 2 minutes of income
end
```

### 4. A boost that only counts down while the player is online

```lua
-- boost = { endsIn = seconds_left } saved in the profile, never an os.time() end date
RunService.Heartbeat:Connect(function(dt)
	for _, session in sessions do
		local b = session.profile.boosts.doubleMoney
		if b and b.endsIn > 0 then
			b.endsIn = math.max(0, b.endsIn - dt)
		end
	end
end)

local function moneyMultiplier(profile)
	local m = 2 ^ profile.economy.moneyTier -- the permanent ladder: x1, x2, x4 ... x16
	local boost = profile.boosts.doubleMoney
	if boost and boost.endsIn > 0 then
		m *= 2 -- the temporary boost doubles whatever the player has
	end
	return m
end
```

Why: a saved seconds-left value is simple to pause, and "online time only" is fair to players who log off.

### 5. Server-wide luck with stacking tiers

```lua
local TIME_PER_PURCHASE = 15 * 60
local timeLeft = { [2] = 0, [4] = 0, [8] = 0 } -- seconds per tier, server memory only

function ServerLuck.add(tier)
	if timeLeft[tier] == nil then return false end
	timeLeft[tier] += TIME_PER_PURCHASE -- buying a running tier adds time
	return true
end

function ServerLuck.current()
	local best -- the highest tier with time left runs; lower tiers wait
	for tier, seconds in timeLeft do
		if seconds > 0 and (best == nil or tier > best) then
			best = tier
		end
	end
	return best -- the spawner asks this for every spawn
end
-- every second: subtract only from the tier that is running
-- share the running tier + end time with clients as attributes on ReplicatedStorage
```

### 6. Block paid random items where the player's policy says so

```lua
local PolicyService = game:GetService("PolicyService")

Players.PlayerAdded:Connect(function(player)
	local ok, policy = pcall(PolicyService.GetPolicyInfoForPlayerAsync, PolicyService, player)
	-- a failed lookup counts as restricted
	player:SetAttribute("PaidRandomItems", ok and not policy.ArePaidRandomItemsRestricted)
end)

function Config.randomBlocked(player, item)
	local g = item.grant
	if not g or not (g.pet or g.lineup or g.cash or g.cashSeconds) then
		return false
	end
	return player:GetAttribute("PaidRandomItems") ~= true
end
-- the server checks this before PromptProductPurchase; the shop shows "Unavailable"
```

## Prompts that worked

**1. Plan first (cleaned from our economy update):**

```text
Money should grow more steeply through the game so better pets feel grand, and
I want cheap stackable Robux purchases since nobody is spending yet. Don't
build anything. Write a plan note with: today's income and price tables next
to proposed ones, what happens to current players' saves, how we release it
safely, and what we are NOT doing. Then ask me the decisions you need.
```

**2. Review what exists, then fix the gap:**

```text
Check that every price, reward and cost in the game matches the new income
curve. List every place a price lives (shops, crafting, pen, rewards,
cosmetics, packs, codes), and show anything that doesn't follow the rule.
```

**3. Example (our style, for a new consumable):**

```text
Add a Robux boost called "Double Coins" (30 minutes, 2x coin income, counts
down only while online). Use the existing shop config, ProcessReceipt and boost
inventory. Server-side only. A second purchase while it's running waits in the
inventory. Run selene and stylua, then test a receipt arriving twice and tell
me what you saw. What questions do you have for me before you start?
```

## Do it in your game

1. **Write down your loop first.** Earn, spend, upgrade. One paragraph. Ask Claude to critique it and to point out where players will run out of things to spend on.
2. **Build cash and one sink on the server** (income, one shop or upgrade) before you think about Robux. Keep money in the player's saved profile and change it only from server code.
3. **Put prices in one config with a formula** (area factor or level factor), plus a check script that prints "how long to afford this".
4. **Build a data-driven shop with fake items** and a Studio-only test purchase. Then write the ProcessReceipt handler as in snippet 1 and test: a receipt twice, two at once, the buyer leaving, a failed save.
5. **Pick a short, cheap item list** (a permanent small multiplier, a few boosts, one starter pack, one pass). Create the products on the Creator Dashboard, give each an image, read the IDs back by script, and put the real prices in the config as a fallback.
6. **Add offers last and sparingly.** One moment of need, one limit rule, one way to close. Log shop opens, presses and purchases as a funnel so you can see which offers work and delete the ones that don't.

Starter prompt:

```text
I'm building a Roblox game: [fill in: one sentence about your game and what players collect or upgrade].
I want to add an economy and a Robux shop the way Battle for Pets did it:
server-owned money, prices that follow a formula, a data-driven shop config,
a ProcessReceipt handler that grants once and saves the receipt in the same
save, and a few cheap Robux items. Don't build yet. First read my project
notes, then write a plan note with the currencies, the sinks, the first five
Robux items and prices, what we will NOT do, and how we test purchases safely.
What questions do you have for me so I can help you implement this feature as best as possible?
```
