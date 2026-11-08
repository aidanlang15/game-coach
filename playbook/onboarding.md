# Onboarding: the first minutes of play

How we built the first minutes of Battle for Pets: the opening cutscene and guide, the nine-step tutorial, the quiet HUD, the task list that follows it, tips, badges, the What's New pop-up, and the analytics funnels that told us where players were lost.

## What we built

**The first minute (for a brand-new player only):**

- **An opening scene at spawn.** The camera glides over the plaza while the game logo pops in. A guide (a Roblox avatar dressed as a pet trainer, using Roblox's own walk, run and wave animations) runs up with a starter pet, points at the gifts and waves. The whole scene is about 12 seconds, with no skip.
- **The handover.** The guide gives a starter pet, three capsules and a starter mount (a skateboard). The server checks and grants all of it, once.
- **A first battle that can't realistically be lost.** The tutorial's own wild pet has much lower health and attack than normal while the guaranteed first catch is unused. The fight lasts a few seconds.
- **A guaranteed first catch.** Only on the player's designated tutorial pet, only once.
- **A quiet HUD.** Until the tutorial ends, buttons the tutorial doesn't use (shop, index, inventory, daily rewards, boost tray, event badges, offers) wait out of sight and pop in when the "You're ready" banner shows. Money, Pets, Mount and the team bar stay.

**The tutorial card** (nine steps): walk to the first area, get the pet, walk to a wild pet, start a battle, attack, dodge, throw a capsule, take the catch home and place it, add it to the team, stand on the training pad. A small card shows "STEP n / 9" with progress pips, one hint line, a pointer and:

- **World guide:** glowing arrows on the ground along the walking route, a pulsing ring, a light beam and a floating arrow with the distance. One module that can point at any world position or model.
- **Button highlight:** a cyan glow and a bobbing pointer that follow any GUI button (Pets, then EQUIP BEST inside it).
- **A "You're ready!" banner at the end.** Big title, rays, and a ribbon with the goal ("TRAIN TO LV 10, BEAT THE FIRST BOSS, OPEN THE NEXT AREA"). No button; it fades after about four seconds.
- **A resume checkpoint.** Leaving after placing the catch and rejoining starts at step 8, not step 1.
- **HELP replay.** A blue HELP button replays the tutorial (without gifts) for anyone who wants it.

**The task list (after the tutorial):** one task on screen at a time, in chapters, one chapter per area, each ending with that area's boss. The card hangs off the HELP button and shows the chapter and count, the task, its progress, the reward and one button: SHOW ME while it's open, a green CLAIM when it's done. SHOW ME runs the same world guide and button highlight as the tutorial. Rewards are items the game already has (capsules, boosts, materials), never cash. Bigger purchases (a pen upgrade, a better training dummy) cut in the first time the player can afford them, so saving up never blocks the list.

**Boost tips:** a small side note appears at the moment an item would help (a capture escapes, the team is knocked out in a boss fight, training for a while), once per player, with the nearest way to get it. A red count bubble on INDEX shows how many rewards wait.

**A boss unlock card:** after a boss's first win, one card shows everything the win unlocked (new area, level cap, capsules, training gear, recipes, title) with a LET'S GO that starts the guide to the new area.

**Badges:** five Roblox badges for early milestones (finish the tutorial, first catch, two early bosses and the first event win), awarded by the server.

**A What's New pop-up:** every big update gets a card that shows once per player on join, with picture tiles and the update's redeem code.

**Analytics:** a one-time Onboarding funnel (Joined, Met Guide, First Battle, First Win, First Catch, Catch Placed, Team Of Two, Trained, Finished), a First 30 Minutes funnel by time played, an After Tutorial funnel (Tutorial Done to first boss and the next area), a Tasks funnel, and custom events for early leaves, load time and tip use.

## How we did it

1. **Build the first version, then read the funnel.** The first tutorial was a guided walk, a battle, a catch and a "you're ready". We added the analytics funnel in the second tutorial pass (nine server-side steps), published, and let real players show us where they stopped.
2. **Write the problem down from the data.** After a few weeks we asked Claude for an honest review of the whole game using the funnels, the notes and the code, and ranked the problems. The first 60 seconds came first.
3. **Brainstorm approaches with mockups.** For "what should a new player learn next" we had Claude write four approaches (a short guided tail, just-in-time tip cards, a Next Step chip, a HELP topics list) with mock images, each with the scene, effort, risks and what it reuses. We picked parts of three and decided what went into stage 1. The decisions went into the note.
4. **Keep it short.** We asked for a short tutorial and taught the rest when it matters (tips and tasks), instead of one long guided stretch and a checklist.
5. **Ask the design questions in one round.** Defaults for each question, a recommendation, and "d" meant "your default". The answers went into the note as "Decisions".
6. **Write the build prompt into the note.** The stage 1 prompt had the goal, the exact steps, the files to read, the rules (server decides, skip always works, no uploads without asking), how to verify, and the settings. A new chat built it.
7. **Server first for anything that grants.** The task list was built as three sessions: server and data with a 200-check script, then the card, then checks before release. Tutorial gifts, the checkpoint and claims are all server actions.
8. **Test as a brand-new player.** A throwaway fresh profile that lives in memory only (no real save is touched) walks through the whole run. Then desktop, phone sideways, phone upright, gamepad. The MCP can't tap an emulated phone, so we do phone and controller by hand.
9. **Publish, put a marker on the dashboard, and watch** the funnel steps, early-leave activity and next-day return for the next change.

**How we worked with Claude:**

- Every onboarding change started with "read the funnel first". Claude pulled live numbers through the analytics API (see the launch guide), then wrote what the data said and what it didn't.
- We asked Claude to be critical. It found its own mistakes in the review (a log field that hid mid-battle quits as "Exploring") and fixed the plan.
- Mocks first for every new card, with real phone screenshots as backgrounds.
- A "release together" rule: the new guide, the cutscene and the code shipped in one update, so one funnel comparison measures one change.

## Rules we follow now

1. **Keep the tutorial short and teach the rest when it matters.** A long guided tail and a goals checklist were both set aside. Tips and a one-task-at-a-time list took their place. Reason: the first session is where players leave.
2. **Show what players are about to get in the first line.** Step 1 used to say only "Walk into the first area, 162 studs away". It now sells the gifts ("Your guide has a pet, 3 capsules and a skateboard for you. Follow the arrows!").
3. **No Skip on a first run.** Most new players pressed SKIP right after meeting the guide. They then missed the guaranteed first catch and the guided steps, so they never learned to battle or place a pet. We made SKIP appear only on HELP replays. Skip still works on the server for replays.
4. **The first fight can't be lost.** Losing the first battle sends a new player home to heal their only pet. We weaken the tutorial pet on the server, only while the first catch is unused.
5. **The first thing a player sees is the guide, not the full HUD.** A quiet HUD until the tutorial ends. Check each hidden item against every tutorial step: our quiet HUD once hid the hotbar that the "place your catch" step needs.
6. **The server owns every grant.** Starter gifts, task rewards, badges and the checkpoint are server actions. The client can only ask. A forged request can at worst skip teaching.
7. **Tips show once per player, never in the way.** One tip at a time. Never during a battle, cutscene, tutorial or over an open window. At least three minutes apart. X closes one for good. Skip doesn't have to switch them off.
8. **Tips teach what they point at.** A tip that points at something the player can't use yet teaches nothing. If the boost is locked, show nothing. (We tried a Robux pop-up for locked boosts instead. On phones it covered the HUD, players found it annoying and it sold almost nothing, so we deleted it.)
9. **Task rewards are items, not cash, and each is worth minutes of play, not hours.** We checked every reward against typical income at that point and against the price of the same item in the shop, with a small script. A reward bigger than the thing it tells you to buy teaches the wrong lesson.
10. **One task on screen.** The task card shows one task, SHOW ME and CLAIM. No checklist. No skip button. No luck-based tasks. No task that needs Robux.
11. **Big purchases cut in, they don't queue.** "Upgrade your pen" appears the first time the player has the cash, then the next purchase waits for the next time. A saved-up-for task at the head of the list blocks everything behind it.
12. **Never point back at an area the player has left.** Beat a boss early and the list jumps to that boss's task. Veterans start at the chapter for the furthest area they have opened, and earlier chapters give nothing.
13. **Badges for early milestones, free ones first.** Roblox lets a game create five badges per 24 hours for free, then charges. Icons are 512 by 512 and shown cropped to a circle, so keep everything that matters inside it. We gave a Ranch Hand badge (finish the tutorial) only to players who played it through, not skippers.
14. **A new redeem code and a What's New card with every big update.** The card shows once per version per player. Closing it saves the version.
15. **Analytics belong in the first version of the tutorial.** Log each step once, from the server, only for players who actually start the tutorial (not veterans, not HELP replays). Wrap every analytics call in `pcall`.
16. **Measure what you changed.** We put a dashboard marker at each release and compared the funnel steps, not just the headline number.

## Mistakes and gotchas

**The biggest leak was step 1, and we guessed wrong at first.** Players never met the guide. The walk was only a few seconds, so it was loading and first impressions: a new player loaded in at the plaza with no pet and the entire HUD. The fix was the cutscene, the guide meeting you at spawn and the quiet HUD, not a shorter walk.

**Skip looked like quitting.** The funnel's biggest drop between "Joined" and "Met Guide" was players pressing Skip. Log skips as their own event (with the furthest step reached) so they aren't mistaken for leaving.

**An early-leave field hid mid-battle quits.** Our early-leave event took its "what were they doing" field when the player left, but the battle service had already ended the fight on leave, so everyone counted as "Exploring". The review wrongly claimed players almost never quit in battle. Fix: read the activity from the last five-second snapshot.

**Loading counts as a step.** Add a `Loaded` signal from the client (seconds since joining) and an "activity: Loading" value so players who quit before the HUD appeared are visible.

**The Analytics query API doesn't have every metric.** There was no "average of a custom field", so average minutes per leave couldn't be computed from the API. Look in the dashboard page instead, or log a second event.

**Tutorial card too big on phones.** The card's scale was clamped to at least 1 so a landscape phone drew it at desktop size and covered half the screen. Scale it by the screen (0.7 on a phone), keep touch targets at least 40 px, and on a first run put the card in the free left column, not in the middle.

**Step 6's card looked empty.** During the capture the card shrinks to a chip, but the chip was too narrow for its pips and hid the instruction. A "Step 6/9" with an empty box is worse than no card.

**The quiet HUD hid something a later step needs.** The hotbar (step 7 places the catch from it). Walk the tutorial's steps against the hidden list every time you change either.

**A battle during the training steps pulled the tutorial back.** Any battle start switched to the battle steps. Decide which steps a battle can interrupt and make the others wait.

**Roblox only loads an image while it's drawn.** Hiding the logo until `IsLoaded` kept it unloaded forever. Let it draw blank (or show a text stand-in) and fade in.

**The camera is sometimes about 1 by 1 pixel right at join.** Size on-screen pieces of the first scene with scale and constraints, not pixels, or wait for a real camera size.

**There is no PlayerModule in our game.** `GetControls():Disable()` silently does nothing. To hold the player still for a cutscene: block mounts and dismount, set Humanoid speed and jump to zero, turn off auto-rotate and proximity prompts, and restore everything afterwards. See snippet 4.

**Avatars through the Studio MCP.** `generate_avatar` timed out and once dropped Studio's connection, but still dropped a model in Workspace later (named after its prompt). We built the guide from catalog items and a `HumanoidDescription` instead. `SetAccessories(list, true)` also wipes accessories you set another way, so hats must be in the list. Hair hid the faces under hats, so the guide has none.

**A brand-new upload can show in Edit but not load in Play.** Test with an image that has had time to process.

**Studio's Edit `require` keeps old module copies.** A check that requires a changed module needs its own fresh clone. From the MCP, `require` of a server module in a playtest returns a separate, unstarted copy: drive the live module from a Script inside the game.

**Studio awards nothing.** Badges, funnel steps and custom events only record in the published game. Print "would award X" in Studio instead, and confirm with a real account after publishing.

**A window that opens by itself eats your test.** Our daily-rewards window opens at join, so a test that pressed SHOW ME right after joining hit it. Close it first, and find buttons by walking the visible GUI instead of guessing paths.

**A button's `Visible` isn't enough for a highlight.** The training panel's EQUIPMENT button reported `Visible` while its panel was hidden, so the highlight drew in empty space. Check every ancestor.

**The map streams.** Far-away markers aren't on the client, so a world guide to another area needs the server's positions.

**A saved field that old servers drop.** The What's New record is a new saved field. A server still running older code doesn't know it and drops it, so the card shows again there. Shut down old servers after publishing, or keep the field in a version-safe record.

**Pick task rewards and task types carefully.** "Gifts" can't be a task (they aren't saved and stop after the first hour, so the task could stall). Supplies make a poor late reward because their price grows faster than a late player's income.

**Players who already finished never see the new badge.** The save couldn't tell a skipper from a finisher, so we gave Ranch Hand to no one retroactively. Players can earn it by replaying from HELP.

## Code snippets

### 1. A quiet HUD that releases when the tutorial ends

```lua
-- Hidden pieces live in an invisible frame so their layout still works.
local quiet = Instance.new("Frame")
quiet.Name = "QuietHold"
quiet.BackgroundTransparency = 1
quiet.Visible = false -- children draw nothing while the holder is hidden
quiet.Size = UDim2.fromScale(1, 1)
quiet.Parent = hud

local held = {} -- piece -> its real parent

local function hold(piece: GuiObject)
	held[piece] = piece.Parent
	piece.Parent = quiet
end

local function release()
	for piece, parent in held do
		piece.Parent = parent
		-- pop in, not just appear
		local scale = piece:FindFirstChild("Squish") or Instance.new("UIScale", piece)
		scale.Name, scale.Scale = "Squish", 0.6
		TweenService:Create(scale, TweenInfo.new(0.35, Enum.EasingStyle.Back), { Scale = 1 }):Play()
	end
	table.clear(held)
end

-- A returning player (tutorial already finished) never gets held at all.
if profile.tutorial.finished then
	release()
else
	for _, piece in { shopButton, indexButton, inventoryButton, hotbar, boostTray } do hold(piece) end
	profile.tutorialFinished:Connect(release)
end
```

### 2. An onboarding funnel step, once, from the server

```lua
local AnalyticsService = game:GetService("AnalyticsService")

local STEPS = { "Joined", "MetGuide", "FirstBattle", "FirstWin", "FirstCatch", "CatchPlaced", "TeamOfTwo", "Trained", "Finished" }
local sent = {} -- [player] = { [step] = true } for this session

local function funnel(player: Player, step: number)
	local profile = Profiles[player]
	if not profile or profile.tutorial.finished then
		return -- veterans and HELP replays never log onboarding steps
	end
	sent[player] = sent[player] or {}
	if sent[player][step] then
		return
	end
	sent[player][step] = true
	-- Roblox counts only a step's first log per player, so nothing else needs saving.
	pcall(function()
		AnalyticsService:LogOnboardingFunnelStepEvent(player, step, STEPS[step])
	end)
end

-- Call it where the thing really happens, on the server:
-- funnel(player, 2) in the handover action
-- funnel(player, 4) when the first wild battle is won
-- A skip logs a custom event instead: its value is the furthest step reached.
```

### 3. A tutorial action the client asks for and the server checks

```lua
-- The client sends "checkpoint" once the catch is placed. The server decides if it counts.
local function tutorialAction(player: Player, action: string)
	local profile = Profiles[player]
	if not profile then return end
	local t = profile.tutorial

	if action == "checkpoint" then
		-- Only between the handover and the end, and it can only ever store 8.
		if t.starterPending or t.finished then return end
		t.checkpoint = 8
	elseif action == "finish" then
		t.finished, t.checkpoint = true, nil -- finish clears it
		funnel(player, 9)
		Badges.award(player, "RanchHand")
	end
	-- Nothing here pays cash. A forged request can only skip teaching.
end
remote.OnServerEvent:Connect(tutorialAction)

-- On load, keep the checkpoint only for an unfinished, non-pending record.
local function cleanTutorial(saved)
	local t = { starterPending = saved.starterPending == true, finished = saved.finished == true }
	if saved.checkpoint == 8 and not t.finished and not t.starterPending then
		t.checkpoint = 8
	end
	return t
end
```

### 4. Freeze the player for a cutscene when there is no PlayerModule

```lua
local ProximityPromptService = game:GetService("ProximityPromptService")

local function freezePlayer(character: Model)
	local humanoid = character:WaitForChild("Humanoid") :: Humanoid
	local saved = {
		speed = humanoid.WalkSpeed,
		jump = humanoid.JumpPower,
		height = humanoid.JumpHeight,
		rotate = humanoid.AutoRotate,
	}
	Mounts.dismountNow() -- your own systems that move the player
	Mounts.setBlocked(true)
	humanoid.WalkSpeed, humanoid.JumpPower, humanoid.JumpHeight = 0, 0, 0
	humanoid.AutoRotate = false
	ProximityPromptService.Enabled = false

	return function() -- call it when the scene ends, even if the scene errors
		humanoid.WalkSpeed, humanoid.JumpPower, humanoid.JumpHeight = saved.speed, saved.jump, saved.height
		humanoid.AutoRotate = saved.rotate
		ProximityPromptService.Enabled = true
		Mounts.setBlocked(false)
	end
end
```

### 5. A task reward the server pays once

```lua
-- No arguments from the client: the server looks at the player's own current task.
local function claimTask(player: Player)
	local profile = Profiles[player]
	if not profile or not profile.loaded or not profile.tutorial.finished then return end
	local task = TaskRules.current(profile) -- the one task now on screen
	if not task or not TaskRules.isDone(profile, task) then return end
	if not Grants.fits(profile, task.grant) then return end -- full inventory: refuse, keep the task

	-- Nothing yields between the check and the move, so a double press pays once.
	TaskRules.advance(profile) -- the task is now claimed
	Grants.apply(profile, task.grant) -- items only, never cash
	Analytics.taskClaimed(player, task.key) -- custom event
end
claimRemote.OnServerEvent:Connect(claimTask) -- rate limit this remote too
```

## Prompts that worked

**1. Review before you fix (based on how we started the first-minute work).**

```text
Give me an honest, critical review of the game for new players: what to add,
change or remove, and how to keep players. Use the notes, the code, a recent
screenshot and the analytics funnels. Don't change anything. Rank the problems,
say what the data does and doesn't show, and tell me which one to fix first.
```

**2. Approaches with mocks (our style).**

```text
Players don't know what to do after the tutorial. Don't build. Write 3 or 4
approaches (for example a short guided tail, just-in-time tip cards, a next
step chip), each with: a scene of what the player sees, how long it takes, what
it reuses, where it can go wrong, and effort. Mock them as images over real
screenshots, desktop and phone. Recommend one and ask me the decisions.
```

**3. A stage 1 build prompt (example, in our style).**

```text
Build stage 1 only of the approved tutorial plan in notes/Tutorial Brainstorm.md.
Read that note first. The server decides every grant. Skip must always work.
Reuse the card, the guide and the highlight. Log the onboarding funnel from the
server, once per step, only for players who start the tutorial. Test with a
brand-new fresh profile (memory only) on desktop, phone sideways and upright,
and tell me what you couldn't check. Recommend a model and effort.
What questions do you have for me before you start?
```

## Do it in your game

1. **Write the first minute on one page** before you build: what the player sees at 0 seconds, what they do by 30 seconds, when they get their first reward, when they first win. Show it to a friend who has never played.
2. **Build a tiny tutorial first** (a few steps, a pointer, one card) and a server-side funnel at the same time. Log each step once. Publish it and look at where players stop before you design more.
3. **Make the opening an event.** A short scene (a guide, a logo, a gift) works better than a loading screen and an empty HUD. Hide buttons the first minute doesn't use until it's over.
4. **Guarantee the first win.** Make the first fight and the first catch safe. Let experienced players replay it from a HELP button.
5. **Teach the rest as tips and tasks.** One tip at a time, once per player, at the moment it helps. One task on screen with a SHOW ME that reuses your tutorial pointer. Rewards are items you already have.
6. **Add small proof of progress**: a few badges for early milestones, a What's New pop-up and a code with every update. Measure each change with a dashboard marker.

Starter prompt:

```text
I'm building a Roblox game: [fill in: one sentence about your game].
I want the first minutes to work the way Battle for Pets does it: an opening
scene with a guide, a short tutorial card with a pointer and a world guide
(arrows and a ring), a first win the player can't lose, a quiet HUD until the
tutorial ends, a server-side onboarding funnel that logs each step once, and
later a one-task-at-a-time list with item rewards. Don't build yet. First read
my project notes, then write a one-page "first minute plan" with the timeline,
the tutorial steps, what the server checks, which funnel steps to log, and what
we will NOT do. Give me 2 or 3 options for the opening scene.
What questions do you have for me so I can help you implement this feature as best as possible?
```
