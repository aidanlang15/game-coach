# Sound: music, ambience and sound effects

How we gave a silent Roblox game a full sound pass with Claude Code: a mixer, a Settings window, music for every area, battle and capture sounds, UI sounds and cartoon pet cries. It comes from Battle for Pets, with one lesson from +1 Mecha Cyborg.

## What we built

- **A sound slot table.** One data file lists every sound the game plays as a "slot" with a name like `ui.click`, `hit.slam` or `music.meadow`. A slot holds the sound ids, volume, group, cooldown, a cap on how many can play at once, a little random pitch and volume change, and when it is loaded. Changing how a sound plays is a one-line edit.
- **A small player module** (`Sfx`) with `play`, `at` (3D, at a point or following a part), `loop`, `stop` and `preload`. It reuses a pool of Sound instances per slot instead of making a new one every call.
- **A mixer.** Four sound groups under one master: Music, Sound Effects, Ambience and Interface. Players control them with sliders.
- **A Settings window** opened from a gear in Roblox's top bar: a slider per group and a Mute All switch. The settings are saved in the player's profile with the normal autosave, and are never written on every drag.
- **Music.** Each area has a short playlist. A track plays through, then 30 to 60 seconds of ambience only, then the next track, so nothing loops for hours. Crossing into another area crossfades over 2.5 seconds. Battles fade the area music out and a battle theme in. Boss fights have their own themes. Short stings play for a win, a loss, a catch and a boss win.
- **Ambience.** Each area has a loop (wind, waves, frogs, cave hum). The cave area also gets a reverb. Lava gets 3D emitters on the nearest lava parts.
- **Battle sounds.** Every attack move belongs to one of 13 categories (bite, slash, pierce, slam, wind, fire, ice, water, lightning, earth, crystal, magic, sonic). Each category has two hit sounds that take turns and a wind-up sound for charged moves. Light moves play higher and softer. Heavy moves play lower and louder with a shared low impact layer under them.
- **Capture sounds.** The capsule throw has one sound per beat: throw, impact, beam, lid shut, bounce, each wobble (rising pitch), the final click, then a sparkle. Rare catches add a glow.
- **Cries.** Ten cartoon voices (squeak, chirp, yip, mew, bloop, buzz, honk, rumble, a cute rawr, chime). Each species is assigned a voice, and its size sets the pitch, so a tiny pet and a giant never sound alike. Cries play when a battle starts (normal), when a pet faints (lower and slower) and when it is caught (higher and happy).
- **UI and economy sounds.** A shared button click for every button, plus distinct sounds for open, close, tab, deny, buy, cash gain, discovery, level-up, reward claim and so on.
- **World cues.** Training pad hits, a gentle hint when a rare pet is near, and quiet hums near special mutated pets.

## How we did it

1. **Audit first.** Claude listed every sound the game already played (a handful of hand-wired ones) and every place that needed one. It also found cheap hook points: all battle beats arrive on one remote, so a battle audio module could listen on its own with no edits to the battle code. One listener on the screen gave every button a click without touching 130 buttons.
2. **Write the plan and wait.** The Sound Pass Plan covered the audit, the module layout, the slot list, music direction per area, the attack categories, a phone memory budget and five stages. It waited for the human's OK. The human answered with decisions (a Pokemon-style music direction, cries that are never real animals, one shared click, hover on desktop only).
3. **Five stages, each with the same loop:**
   - 2a: the system, mixer, Settings window and a Studio test panel. No new sounds yet. The old sounds were routed through the mixer so they sound the same at default volumes.
   - 2b: UI and economy sounds.
   - 3: music and ambience.
   - 4: battle, capture and cries.
   - 5: world cues, then a mix pass.
4. **A shortlist note per stage.** For each slot, Claude found 2 to 4 candidates in Roblox's licensed libraries, marked its recommended pick with a star, and listed a store link next to each. The human listened (store page, or the test panel) and answered "approve all stars", or picked per slot ("click: 2nd, deny: 3rd, rest stars"), or said "none fit" with a description so Claude searched again.
5. **A Studio-only SOUND TEST panel.** It has a PLAY button for every slot and every candidate, STOP, and CHECK ALL, which preloads every id and turns rows green or red. That catches sounds that don't load before the human ever listens.
6. **Wire the picks.** The picks go into the slot table. Hooks in existing files are one line each. Each stage ended with a Studio playtest where a recorder logged every sound as it started, so Claude could verify the right sound played at the right moment even though it can't hear.
7. **The mix pass.** Claude measured every sound's loudness by playing it in a muted sound group and sampling `PlaybackLoudness` every frame. It set a target loudness per category (area music, battle music, boss themes, stings, ambience, hits, cries, UI) and recalculated each slot's volume. The biggest fixes were beds that were 2 to 9 times louder than the rest and one boss theme that was far too quiet.
8. **Memory and instance checks** on desktop and an emulated phone: sound memory stayed under a 25 MB target, with about 30 sounds at once at most.

## Rules we follow now

- **Claude can't hear audio.** Every shortlist says so, and picks come from title, album, description and length only. The human always listens before approving. Never claim a sound "sounds good".
- **Licensed library audio only.** Roblox's licensed libraries (Pro Sound Effects and APM Music) so ownership and moderation are never a problem.
- **Cries are never real animal recordings.** The human wanted cute and cartoon-like. We used toy squeaks, bike horns, synth tones, bubble bloops and the library's cartoon creature sounds.
- **Kid-friendly.** Skip gore, knife and flesh sounds. We used a toy suction-cup dart for piercing attacks and a slide whistle for a faint.
- **One shared click plus distinct special sounds.** The shared click plays after a button's own handlers, but only if none of them played a more specific sound that frame. A button can name its own slot or opt out.
- **Hover sounds on desktop only,** very quiet, throttled.
- **Don't write the save on every slider drag.** Send it one second after the last change and let the autosave carry it.
- **At the default slider positions, every group is 1.0.** A group's volume is its slider divided by its default, so old sounds keep their loudness and the top of the slider is slightly louder.
- **Pool Sound instances,** cap how many play per slot and per group, and give repeats a bit of pitch and volume variation so they don't sound robotic.
- **Load by scope,** never everything: UI sounds on join, an area's music when you enter it (and the next area's near its edge), battle sounds on the first battle, capture sounds when a capture starts. Destroy a music track that has not played for two minutes.
- **No per-frame work.** Area checks run twice a second. Throttles and caps are timestamp checks.
- **Level-up and other frequent sounds are short and quiet,** with a cooldown. In +1 Mecha Cyborg the level-up was a 3-second musical hit and played almost nonstop early on. The user called it annoying. We replaced it with a 0.4 second synth arpeggio at low volume, no music dip and a 1.5 second cooldown.
- **Start calm for a hub.** The first hub tracks were bouncy and annoyed the human. We swapped them for one calm ukulele track at a lower volume, and tuned its volume by measuring loudness again.
- **Stings duck the music.** Musical UI stings (celebrate, level-up, discovery) dip the music group to about 35% for a few seconds. Battles pause the area music and halve ambience. Cutscene scores pause everything else.

## Mistakes and gotchas

- **A save backup taken during Play held that session's lock.** Restoring it left a stale lock, so the next Play never loaded the player's data and the music never started. Take backups in Edit mode, or clear the lock after restoring.
- **The catch sting played at the throw, not the click.** The server's "battle ended" message arrives at the throw. We moved the sting to the capsule's final click and delayed the area music's return.
- **A training-pad sound played once after the player left.** Scheduling hit to hit missed the leave. We added a distance check before each hit.
- **Some map features did not exist.** The map had no named shoreline, running waterfall or hot spring, so those emitters weren't placed. Check what is in your map before promising emitters.
- **Studio plays no sound in Edit mode and none while its window is unfocused,** and it reports loudness 0 in those cases. Measure in Play with Studio in front.
- **The mix pass found ten sounds that were silent or nearly inaudible** (the water and pierce hits, the revive and a few more). We replaced them with new library picks. Loading fine does not mean audible.
- **A boss theme at a quiet volume** was 2.9 times louder than its first guess once balanced. First-guess volumes are always wrong. Measure.
- **Top-bar gear placement** was wrong on phones (it hid under Roblox's microphone button because the safe area shifts). Check the placement on a phone-sized view.
- **Library search needs the right method.** See "Do it in your game": Studio's own asset search returns mostly user uploads.

## Code snippets

### 1. A slot table (data, not code)

```lua
-- SoundConfig: every sound the game plays is one slot. Tuning is a one-line data change.
local Slots = {
	["ui.click"] = {
		ids = { "rbxassetid://YOUR_ID" }, -- the pick first, alternatives after
		names = { "Switch Click Toggle Button" }, -- library title, for notes and your test panel
		group = "UI", -- which slider controls it
		volume = 0.25,
		cooldown = 0.05,
		max = 2, -- at most 2 playing at once
		vary = 0.04, -- random pitch +-0.04 and volume +-0.02
	},
	["hit.slam"] = {
		ids = { "rbxassetid://YOUR_ID", "rbxassetid://YOUR_ID" }, -- they take turns, never the same twice
		group = "SFX",
		volume = 0.3,
		vary = 0.08,
		scope = "battle", -- preloaded on the first battle, not at join
	},
	["music.meadow"] = {
		ids = { "rbxassetid://YOUR_ID", "rbxassetid://YOUR_ID" }, -- the area's two-track playlist
		group = "Music",
		volume = 0.26,
		scope = "meadow",
	},
}
```

### 2. Mixer groups and a group's volume from its slider

Nested groups multiply, so a sound already in a child group keeps its own level.

```lua
local SoundService = game:GetService("SoundService")
local DEFAULTS = { Music = 0.5, SFX = 0.8, Ambience = 0.6, UI = 0.7 }

local master = Instance.new("SoundGroup")
master.Name = "Master"
master.Parent = SoundService

local groups = {}
for name in DEFAULTS do
	local g = Instance.new("SoundGroup")
	g.Name = name
	g.Parent = master
	groups[name] = g
end

-- settings = { music = 0..1, sfx = 0..1, ambience = 0..1, ui = 0..1, muted = false }
local function apply(settings)
	master.Volume = if settings.muted then 0 else 1
	for name, g in groups do
		-- at the default slider position this is exactly 1.0, so old sounds keep their loudness
		g.Volume = settings[string.lower(name)] / DEFAULTS[name]
	end
end
```

### 3. A pooled one-shot player with variation

```lua
local pools: { [string]: { Sound } } = {}

local function play(key: string, opts)
	local slot = Slots[key]
	if not slot then return end
	pools[key] = pools[key] or {}
	local pool = pools[key]
	local sound
	for _, s in pool do
		if not s.IsPlaying then sound = s break end -- reuse a free one
	end
	if not sound then
		if #pool >= (slot.max or 3) then
			sound = pool[1] -- restart the oldest instead of making more
			table.remove(pool, 1)
			table.insert(pool, sound)
		else
			sound = Instance.new("Sound")
			sound.Parent = game:GetService("SoundService")
			table.insert(pool, sound)
		end
	end
	sound.SoundId = slot.ids[math.random(#slot.ids)]
	sound.SoundGroup = groups[slot.group]
	local vary = slot.vary or 0
	sound.PlaybackSpeed = (slot.pitch or 1) * (1 + (math.random() * 2 - 1) * vary) * ((opts and opts.pitch) or 1)
	sound.Volume = slot.volume * (1 + (math.random() * 2 - 1) * vary / 2)
	sound.TimePosition = 0
	sound:Play()
	return sound
end
```

### 4. Listening to the battle without editing it

```lua
-- One module listens to the battle's own event remote and picks sounds by event.
-- No edits to the battle code are needed for hits, wind-ups, warnings or faints.
BattleEvent.OnClientEvent:Connect(function(kind, data)
	if kind == "hit" then
		local category = MoveCategory[data.move] or "slam"
		play("hit." .. category, { pitch = if data.weight == "light" then 1.1 else 0.9 })
		if data.weight == "heavy" then play("battle.heavy") end -- shared low layer under big hits
	elseif kind == "warn" then
		play("battle.warn")
	elseif kind == "faint" then
		play("cry." .. CryVoice[data.species], { pitch = 0.7 }) -- lower and slower
	end
end)
```

### 5. Measuring loudness silently (for the mix pass)

```lua
-- Play a sound in a muted group and sample how loud it is each frame.
local muted = Instance.new("SoundGroup")
muted.Volume = 0
muted.Parent = game:GetService("SoundService")
local s = Instance.new("Sound")
s.SoundId = "rbxassetid://YOUR_ID"
s.SoundGroup = muted -- PlaybackLoudness still reads normally, nothing plays out loud
s.Parent = workspace
s:Play()
local peak, sum, n = 0, 0, 0
local conn = game:GetService("RunService").Heartbeat:Connect(function()
	local l = s.PlaybackLoudness
	peak = math.max(peak, l)
	if l > 10 then sum += l n += 1 end
end)
task.wait(2.5)
conn:Disconnect()
print("peak", peak, "mean while sounding", if n > 0 then sum / n else 0)
-- Then volume = targetLoudness / mean, and clamp to something sane.
```

## Prompts that worked

**1. Real prompt, cleaned.** We asked for a whole-game sound pass and gave a few rules: use only Roblox's licensed libraries, give us shortlists and let us pick, add a Settings window with sliders, and keep pet cries cartoon-like rather than real animals. Claude wrote the audit and plan first, answered our questions with defaults, and then did the stages one at a time.

**2. Real follow-up, cleaned.** After the hub music got annoying we said so and asked Claude to find three calmer candidates, one per mood. Claude gave links, we listened on the store pages, picked one, and Claude set its volume by measuring loudness against the area-music target.

**3. Example (our style).**

```text
First read notes/Sound Pass Plan.md (or write one with me if it doesn't exist).
Audit every sound the game plays and every place that should have one. Then build
stage [N] only: [UI and economy | music and ambience | battle and capture | cries].
For each slot, find 2 to 4 candidates from Roblox's licensed libraries, mark your
recommended pick, add store links, and make sure every candidate loads in the SOUND
TEST panel. Don't wire anything until I pick. Remember you can't hear audio, so
don't tell me what anything sounds like.
```

## Do it in your game

1. **Audit what you have.** Ask Claude to list every sound and every silent moment: buttons, windows, rewards, cash, battles or combat, pickups, area changes, menus.
2. **Build the system before the sounds.** A slot table, a pooled `Sfx` player, four sound groups (Music, Effects, Ambience, Interface), a Settings window with sliders and Mute All, and a Studio-only test panel. Route anything that already makes sound through the groups.
3. **Make shortlists, not decisions.** Claude finds licensed library sounds in batches (UI, then music, then battle, then cries) with a recommended pick and a store link each. You listen and answer "approve all", per slot, or "none fit, I want [description]".
4. **Wire by listening to events,** not by editing old code. One listener on your battle or combat remote, one on button presses, one on your data signals (cash, discoveries, level-ups).
5. **Do a mix pass.** Measure each sound's loudness, set a target per category, and fix the outliers. Then check memory and how many sounds play at once on a phone-sized view.
6. **Listen yourself,** on desktop and phone, and keep every change a one-line volume or id edit.

**Finding licensed library audio.** Studio's built-in asset search can't filter by uploader, so it returns mostly user uploads. What works: open any page on Roblox's toolbox web service in a browser (the Claude in Chrome tool or the built-in browser) and use `fetch` from that page. Search audio (category 3) with a keyword and a creator filter, then fetch item details for names, descriptions with duration and category, and album and artist for music. The creator filter needs the numeric user id of the library uploader. Find each id by opening the uploader's Creator Store profile ("Pro Sound Effects" for effects, "APM Music" for music) and reading the id from its URL. Don't paste ids into shared notes. Pro Sound Effects leans sci-fi for UI, so softer UI sounds came from searches like temple blocks, switches, suction pops and magic poofs. Verify every pick loads with your test panel's CHECK ALL.

```text
First read ~/.claude/skills/game-coach/playbook/sound.md.

I want to add sound to my Roblox game. My game is [one sentence]. Right now it has
[no sound / a few hand-wired sounds]. Please:
1. Audit every sound and every silent moment in the game.
2. Write a short sound plan: the module layout (slot table, Sfx player, mixer groups,
   Settings window), the slot list, music direction per area, and stages.
3. Don't build yet. Show me the plan and wait for my OK.

What questions do you have for me so I can help you implement this feature as best as possible?
```
