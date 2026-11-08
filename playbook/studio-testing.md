# Testing and tooling with Claude in Roblox Studio

How we test a Roblox game with Claude Code through the Studio MCP, how two people share one game with Rojo and Team Create, and the Windows and tooling traps that cost us time. It comes from all our games, mostly Battle for Pets, where two of us worked in the same place at the same time.

## What we built

- **A Claude-driven test loop.** Claude edits code files, syncs them into Studio, starts a playtest, drives the game (teleports, clicks, key presses, remote calls), reads the console and takes screenshots, then reports. We use it for combat, capture, boss fights, economy, UI and performance.
- **Studio-only test panels.** A VFX TEST button, a SOUND TEST panel and an admin tab, all hidden outside Studio. They stage pets, spawn wild enemies, grant items and replay effects in one click.
- **Check scripts** in a `tools/` folder: small Luau files that run a set of checks in one call (training, shop, passes, boss fights, pen cutscenes). They print counts like "42 checks passed".
- **Debug commands** (`BFP_Debug` in our game): a server remote that only exists in Studio. It gives pets, spawns wild pets, heals a team, sets health, resets a profile, and so on. Claude uses it instead of editing saves by hand.
- **A save backup and restore routine** so tests never ruin the real Studio profile.
- **Performance sampling** with script timing, frame counters and instance counts. We compare "before" and "after" in alternating rounds.
- **Rojo for code, Team Create for the map** and later **TeamSync**, a small sync tool of our own, so two people can work in the same place at once.
- **A pre-publish checklist** that ends with a re-scan of the place.

## How we did it

1. **Start of every session (CLAUDE.md does this).** `git pull` to get the partner's changes. Check Rojo is listening on its port. Ask Claude to list the open Studios and find the right place. Studio session ids change every time Studio restarts, so Claude never reuses an old one. Claude says in one line what is ready and what you still need to do (open Studio, start Rojo, click Connect).
2. **Edit files, not scripts in Studio.** Code lives in the project folder and syncs through Rojo: client scripts to StarterPlayerScripts, server scripts to ServerScriptService, shared modules to ReplicatedStorage. The map lives only in the place. Rojo manages code only.
3. **Before a playtest, check the code that is actually in Studio.** Claude reads a changed script's `Source` in Studio and compares it with the file. Silent stale code was our most common false result (see mistakes).
4. **Back up the save, test, restore.** Our Studio profile is a real DataStore key. Claude backs it up before any test that writes, runs the test, restores it, deep-compares and deletes the backup.
5. **Drive the game from `execute_luau`.** In Play there are separate server and client views. Claude runs one in the view it needs. Typical test calls: teleport next to a pet, trigger a prompt, send battle actions through the real remotes, count instances, read the output.
6. **Look at it.** `screen_capture` for stills. For fast things (effects) a burst tool that takes a series of window screenshots. For anything that needs eyes on a real device (phones, gamepads, two players) we leave it for the human and say so.
7. **Check for leftovers.** After every run Claude counts instances the feature made (we want 0 left), checks the camera is back to normal, and reads the console.
8. **Record it.** The task log lists what was checked and, importantly, what was NOT checked ("not tested: a real phone, two players").

**What Claude can do through the Studio MCP:** list and pick Studio windows, read and search the game tree and scripts, read the console output, start and stop Play, run Luau in the edit, server or client view (`execute_luau`), take screenshots, press keys and click the mouse (including by instance path), insert assets, upload images and generate assets. It can't click Studio's own menus (Import 3D, device emulator) and it can't hear audio.

**How we split work into sessions.** One task per session with a plan first. Big batches (a whole area's effects) ran on a git branch in a separate worktree so other sessions could keep using the main folder. Multi-client tests were saved for the end of a session, when the human was there.

## Rules we follow now

- **The MCP has time limits.** An `execute_luau` call times out somewhere between 60 and 90 seconds, so keep each measurement under about 50 seconds. Threads started with `task.spawn` or `task.delay` inside a call die when it ends. A `RenderStepped` connection made in a client call does survive it.
- **The MCP resets the camera after every call.** It sets the camera type back to Custom, which hides battle prompts. Set the camera and run the prompt hold inside the same call. For filmed camera moves, do the whole move inside one call.
- **`require` from `execute_luau` gives a separate copy of a module.** It has no live state. Test live code through remotes, attributes or debug commands. If you must call a live server module, make a Script in ServerScriptService with the test as its `Source`, have it write JSON into a StringValue, poll for that, and delete the Script. The MCP also caches modules between calls, so clone test modules into a fresh folder each time.
- **Studio stops rendering when its window isn't focused or visible.** Unfocused it runs Play at about 15 fps and a minimized or locked window renders nothing. Check `RenderStepped` frames per second before any visual check. When it's unfocused, compare script time per call, not FPS.
- **Never stop a Play you didn't start.** Several sessions can share one Studio. If a Play appears, another session's test may be running. Ask that session before stopping it, and take save backups only after its lock clears.
- **Back up in Edit mode, restore from a Play server.** A backup taken during Play includes that session's profile lock. Restoring it left a stale lock and the next Play never loaded the player's data. If Edit-mode DataStore calls are forbidden, take the backup from the Play server at the start, kick the player at the end so the leave-save runs, write the old value back, deep-compare, and delete the backup key.
- **Debug commands that "set" can replace a whole table.** Back up first.
- **Multi-client tests need the human.** Test, Clients and Servers opens extra Studios, can be flaky, and the MCP can only start a normal one-player Play. Test with one real player plus bots, and ask the human to run the multi-client test at the end. Save first (Ctrl+S): that test runs the last saved version.
- **Use `instance_path` for clicks.** `user_mouse_input` coordinates are in `AbsolutePosition` space (below Roblox's top bar), not screenshot space. Screenshots can be scaled about 1.5 times. Look the position up with `execute_luau`, or click by instance path.
- **A key press needs a down and an up.** A plain key press is too short to trigger a hold prompt. Send `keyDown`, wait 150 ms, then `keyUp`.
- **Phone emulation ignores MCP clicks.** Select the button with `GuiService.SelectedObject`, send Return (down, wait, up), then clear it. This also exercises the gamepad path.
- **Count warnings as failures.** Some systems drop a broken step with a warning, not an error. Count those too.
- **Record what you did not check.** "Not tested: a real phone, two players, a gamepad" is a normal line in every task log.
- **Rojo: one person per place.** Only one person runs Rojo against the shared Team Create place. If you aren't sure the other person stopped, ask. To test without being the Rojo person: File, Save to File for a personal copy, open it, run Rojo against that copy. The copy does not get later map changes.
- **Map work overlaps fine in Team Create** as long as you aren't in the same model or area. **Code conflicts** only happen when two people edit the same file, so split work by file or system.
- **Run Rojo in your own terminal,** not as an assistant background job. Assistant background jobs get killed after 30 minutes to 2 hours, which dropped our sync silently twice.
- **Before connecting Rojo for a publish, check `git status` on `src/`.** Rojo ships the whole folder, including other sessions' half-finished files. Put Studio's copy back to the committed version or ask.
- **Commit after each finished task and push,** so your partner gets it.

## Mistakes and gotchas

- **Editing files under a running Rojo can crash it.** `sed -i`, the editor tool, and deleting a synced folder all crashed our Rojo (it watches for a temp file that vanishes). Studio then kept stale code with no warning. We edit files in place with a small script that reads and writes the file bytes, and after any edit check that Rojo's port still listens and a `Source` in Studio changed. Delete single files, not folders.
- **Another session's Rojo overwrote the code.** Studio's Rojo plugin connects to one Rojo, and that Rojo's copy of every file replaces what was synced earlier. Before a playtest, check that Studio's `Source` has the new code. If another session owns Studio's Rojo, test visuals by hand and list the in-game code check as open.
- **Duplicates after a Disconnect and Connect.** Duplicate scripts sat under the Rojo roots until a reconnect removed them. A rescan for twins is part of every pre-publish check.
- **A raw Import 3D left in Workspace went live.** A giant grey broom stood next to the spawn after a publish, because another session imported a model mid-check. The fix: re-scan the whole place as the very last step before "you can publish".
- **CRLF line endings.** Windows Python's `write_text` turns `\n` into `\r\n`, and `stylua --check` then flags the whole file. Write with bytes. Git Bash `grep` can hide carriage returns, so use `file <path>` to check. New git worktrees check out CRLF because of `core.autocrlf`. Create them with autocrlf off.
- **Backslashes collapse in heredocs.** A pasted patch script lost its double backslashes, wrote a real newline into a file and broke it. Use the editor tool for edits with escapes, or check the lines afterward.
- **Parallel Blender renders go black.** Five at once gave blank frames with no error. Keep it to two or three, and scan frames for blanks.
- **Headless browser renders can be stale.** Rendering images one after another through one page saved the previous job's picture. Use a fresh page and profile per image, delete the old file first, and check the bounds.
- **`screen_capture` returns black** when the 3D view is hidden behind a script tab or the window is minimized. It also can't draw BillboardGuis that are always on top. Check those with a real window screenshot or read the GUI.
- **Stale screenshots in Edit.** Right after rebuilding a lineup, a capture can show the previous frame. Capture twice or wait a second.
- **Start Play can wedge.** `start_stop_play` sometimes says started while Studio stays in Edit. Check the state and try again, or have the human press Play.
- **The emulator menu is disabled during Play.** Stop, pick the device, then start again. Studio's device emulator can't be switched from a script.
- **A wild pet walked out of range between calls.** Teleport next to the target and hold the prompt in the same call, wait for the prompt to show, and don't move the character during the hold or the battle cancels as "moved away".
- **Spawn and give debug commands silently ignore some args.** One ignored the level unless a mutation was passed. Read what the command returned.
- **Test-data side effects.** A won test fight granted real rewards to the Studio profile, a capture added a pet, and a heal was needed between fights. Put them back.
- **Timing a burst.** The burst tool took 5 to 20 seconds to start. Start it first, then trigger the move from a call that waits a few seconds, and log events with Unix millisecond timestamps to line frames up.
- **Sampling frame rate on a shared PC.** Other sessions running Blender or Studio swing FPS. Alternate cases in rounds and compare medians. Each moved part costs a few microseconds per frame, and welds don't save that.
- **Filming Studio footage.** Roblox's top menu pill can't be hidden in Studio, so we moved Studio's window up and recorded below it. A floating plugin widget can sit over the view and show in recordings. When the screen is covered by another app, Studio stops drawing and the recording freezes.
- **Our own mistake with TeamSync's first fetch.** Fetching into `origin/master` every few seconds locked the ref and broke a `git pull`. It now fetches into its own private ref.

## Code snippets

### 1. Backing up and restoring a test profile (concept, from a Play server)

```lua
-- Run in the SERVER view at the start of a test session. Names are examples.
local DataStoreService = game:GetService("DataStoreService")
local store = DataStoreService:GetDataStore("PlayerData_Studio") -- your Studio-only store
local key = "u_" .. YOUR_USER_ID
local backupKey = key .. "_bak_mytask"

local value = store:GetAsync(key)
value.lock = nil -- a live session's lock must not be copied
store:SetAsync(backupKey, value)

-- ... run the tests ...

-- At the end: make the leave-save run, wait for it, then put the old value back.
game.Players:GetPlayers()[1]:Kick("test over")
task.wait(8)
store:SetAsync(key, store:GetAsync(backupKey))
-- Deep-compare as tables (JSON strings differ by key order), then:
store:RemoveAsync(backupKey)
```

### 2. Holding a prompt from the client, in one call

The camera and the hold must be in the same call, because the MCP resets the camera afterward.

```lua
-- Run in the CLIENT view.
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local root = player.Character.HumanoidRootPart
local targetAttachment = workspace.WildPet.BattlePromptAttachment
local prompt = targetAttachment:FindFirstChildOfClass("ProximityPrompt")

camera.CameraType = Enum.CameraType.Custom
camera.CameraSubject = player.Character.Humanoid
root.CFrame = targetAttachment.WorldCFrame * CFrame.new(0, 0, 4) -- about 4 studs away
task.wait(1) -- the prompt must be shown before a hold counts
prompt:InputHoldBegin()
task.wait(prompt.HoldDuration + 0.2)
prompt:InputHoldEnd() -- don't move the character during the hold
return player:GetAttribute("InBattle")
```

### 3. Driving a battle through the real remotes and logging events

```lua
-- CLIENT view. Events are logged into _G so later short calls can read them back.
_G.BattleLog = {}
local remotes = game.ReplicatedStorage.Remotes
remotes.BattleEvent.OnClientEvent:Connect(function(kind, data)
	table.insert(_G.BattleLog, { os.clock(), kind })
end)

-- Fire actions from a loop. A RenderStepped connection survives the call; task.spawn does not.
local frames = 0
local conn
conn = game:GetService("RunService").RenderStepped:Connect(function()
	frames += 1
	if frames % 40 == 0 then remotes.BattleAction:FireServer("attack") end
	if frames > 1200 then conn:Disconnect() end
end)
return "driving"
```

### 4. Counting leftovers and frame rate in one short call

```lua
-- CLIENT view. Count every folder with the effects' name: a test copy can make a second one.
local function count(name)
	local n = 0
	for _, child in workspace:GetChildren() do
		if child.Name == name then n += #child:GetDescendants() end
	end
	return n
end

local frames, t0 = 0, os.clock()
local conn = game:GetService("RunService").RenderStepped:Connect(function() frames += 1 end)
task.wait(4.5) -- stay under about 50 seconds per call
conn:Disconnect()
return {
	fps = frames / (os.clock() - t0), -- if this reads ~15, Studio isn't focused
	liveEffects = count("MyEffects"),
}
```

### 5. A pre-publish scan (run as the very last step)

```lua
-- Run in the EDIT view right before saying "ready to publish".
local report = {}
for _, child in workspace:GetChildren() do
	if not child:IsA("Terrain") and not child:IsA("Camera") then
		local bones = child:FindFirstChildWhichIsA("Bone", true)
		local grey = false
		for _, d in child:GetDescendants() do
			if d:IsA("MeshPart") and d.Color == Color3.fromRGB(163, 162, 165) then grey = true break end
		end
		table.insert(report, `{child.Name} ({child.ClassName}) bones={bones ~= nil} greyMesh={grey}`)
	end
end
return report -- read every line: unknown top-level models, Import 3D leftovers, test fixtures
```

## Prompts that worked

**1. Real prompt, cleaned (two people, one place).** We asked for our own version of Rojo that makes it easier for two people to work at the same time. Claude proposed TeamSync (below), we approved it, and it was built and tested against scratch git repos before it touched the live place.

**2. Real follow-up, cleaned.** After the first version we asked for a "git pull" button and a "disconnect" button like Rojo's. Claude added both and tested pull in four cases: already up to date, a new commit, uncommitted changes (refused), and a conflict (undone).

**3. Example (our style, a testing task).**

```text
First read notes/Studio Testing Tips.md and the last two task logs that mention tests.
Then run a real test of [feature] in Studio Play: back up my Studio save first (in the
way the notes describe), drive it through the real remotes, count leftovers and check
the console. At the end restore the save and tell me exactly what you checked and
what you did NOT check (phones, gamepad, two players).
```

## TeamSync: our sync tool for two people

Rojo is one-way (files to Studio) and only one person can run it against a shared place. We wanted both of us syncing at once, so we built TeamSync:

- Each person runs their own small server from their own clone of the repo. Each Studio has a small plugin connected to it.
- **Pushed code** goes into the place automatically, written by one live person, the "leader" (the lowest user id).
- **Your unpushed files** go into the place right away and are claimed in your name (an attribute on the script). Nobody else's sync overwrites a claimed script while you are connected. A claim from someone who has gone quiet for 45 seconds can be taken over.
- When you push, your claim drops and the pushed version takes over.
- If you edit a file your partner changed in a push since your last pull, the sync holds it back and says "Commit, then git pull".
- Deletes and renames wait for a push. It never removes anything it didn't create, and asks before removing more than 40 scripts.
- Presence is shared through a folder in the place that Team Create syncs.
- It fetches the remote into its own private ref so it never locks your `git pull`.
- Status: tested by one person with simulated second users in a sandbox, plus a dry run against the real place (every script identical, nothing to remove). The test with both of us connected was still to do, so the one-Rojo-person rule stayed until then. Rojo stays in the project for personal copies and for building the plugin.

You probably don't need this. Start with the Rojo rules above and add a tool like this only if you keep hitting the same collision.

## Do it in your game

1. **Set up the loop.** Install Rojo (through Rokit), the Rojo plugin, and the Studio MCP. In CLAUDE.md, add a start-of-session routine: `git pull`, check Rojo's port, list open Studios, say what is ready.
2. **Write your save-safety rule** into CLAUDE.md or a note: back up the Studio profile before tests, restore after, in Edit mode or from a Play server, never during someone else's Play.
3. **Add a debug remote and a test panel** that only exist in Studio: give items, spawn enemies, set health, reset a profile, replay an effect.
4. **Teach Claude your testing tricks** by saving each gotcha as a memory the moment it costs you time (module copies, camera resets, key holds, clicks by path). Read the ones above first.
5. **Agree on a team rule** with your partner: one Rojo person per place (or a personal copy), split work by file or system, commit and push after each task.
6. **Make a pre-publish checklist** that includes the final place scan, the duplicates check, `git status` on your source folder, and a list of what was NOT checked.

```text
First read ~/.claude/skills/game-coach/playbook/studio-testing.md.

I want to set up reliable testing for my Roblox game with the Studio MCP. My game is
[one sentence]. I work [alone / with a friend]. Please:
1. Check my CLAUDE.md for a start-of-session routine and a save-safety rule, and
   propose what's missing.
2. Propose a small Studio-only debug remote and test panel for my game's main systems.
3. Draft a pre-publish checklist for me.
Don't build anything yet. Show me the proposals and wait for my OK.

What questions do you have for me so I can help you implement this feature as best as possible?
```
