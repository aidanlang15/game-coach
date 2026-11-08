# Live events and updates: planning, sessions and release

How we plan and ship big updates and timed events in Battle for Pets: the Kraken world event, the Halloween event, the Starfall update and an "admin abuse" hour. It also covers our roadmaps, the release checklist and how we write announcements.

## What we built

**The Kraken (a world event, every 30 minutes):**

- Every half hour on the **real clock** (:00 and :30, the same in every server) the sky at one area darkens and a giant boss rises out at sea. Players who unlocked that area tap JOIN and are teleported to the beach.
- The fight has three phases: each player fights the boss's tentacles with the normal battle controls; ink-covered minions guard a shield; then **everyone fights the whole boss together** with one shared health bar. Dodge breaks sit between phases and a healing tide restores everyone's team. It can be **lost**: if time runs out it sinks.
- Rewards: materials that craft useful things (a heal boost and a capsule), a badge, an exclusive pet chance and a new mutation for that pet.
- Rules for the schedule: never in a server's first five minutes, never with nobody eligible, never overlapping (one event lasts about 12 minutes). A countdown badge under the Index button shows the time to the next one.
- An **admin EVENT tab** to start it now, skip a phase, set the boss health, add bots that fight and test every piece. Studio checks use debug commands.

**The Halloween event (about two weeks, one release):**

- A **Hallowvale area** behind a portal in the hub, in three tiers for early, mid and late players, with a boss at the top.
- Eight new pets, a boss, an event mount (a witch's broom, crafted with event candy and also sold for Robux), an event mutation (Haunted), event candy and a candy shop, the hub turned spooky, and event Robux items that are only for sale while the event runs.
- A **Soul Lantern**: wins in a tier fill a lantern; a full lantern lets you challenge a gate warden; beating it opens the gate for you.
- Everything switches on one config with dates and one `active()` check. Before the start nothing shows (the Index tab, titles, material rows, the portal and the mount card are hidden); after the end the crafts stay open a few days so leftover candy can be spent.

**The Starfall update (a permanent update, one release):**

- A new top area (zone 9) with nine new pets, a boss, a mount, a new mutation and a new crafting tab.
- **Meteor showers in every area** twice an hour (at :15 and :45, halfway between Krakens): a 60-second warning, streaks across the sky, craters with one special wild pet each for five minutes, and a currency drop that buys things in the new tab. Plus a Robux product that starts a shower for the whole server.
- Why both: very few players reach the top areas, so the new area alone would be seen by almost nobody. The meteors are what most players play, in the areas they are already in. The area is the headline for the thumbnail; the meteors are for everyone.

**An admin abuse hour (a one-hour community event):**

- A set, announced hour when we join the game and use admin powers *for* players: free luck in every server, plus an "all servers" **pet shower** button that drops one rare pet in every area of every server at once, and preset messages that show a banner and chat line to every server. A countdown pill shows on the HUD from a few days before, then "LIVE" during the hour.
- The start and end times are in a config, so everything turns on and off by itself in every server, even ones that open mid-event or if we are late.
- A code that only works while the hour is live.

**The release machinery we reuse every time:**

- A **What's New card** on join, once per player per update.
- A **redeem code** for each update.
- A **pre-publish Workspace scan**.
- An update **config** with dates and phases, and admin buttons to force a phase for tests.
- A short **Discord teaser** and an event page.

## How we did it

**1. Plan first, in a note, with choices.** Every event started as an idea table with an effort size on each row (S = an hour or two, M = a session, L = several). We picked a core plus one headline: for Halloween, candy, crafts, the new mutation, spooky night and the event wrapper, plus one big thing (the area behind a portal). Then the user's choices went into a plan note: tiers, rosters, levels, odds, what is saved, what is paid, balance rules.

**2. Balance rules written down before building.** Examples we wrote first: the event never opens main areas, never raises level caps, and never unlocks anything a boss gates; candy only buys things that help in proportion (capsules, odds, the mount, titles), never flat cash; any cash prize is minutes of the player's own income. Why: a limited event must not hand early players an unfair jump, and the top of the event should still be a real chase for late players.

**3. Split into sessions, each ending with a working game.** The event plan has a table of sessions. Each row has a lane (Python, Blender, Studio), what it needs from earlier rows and which model and effort to use. Example from Halloween (13 sessions):

| Session | What | Lane |
| --- | --- | --- |
| H1 | Event core: config, candy, save record, the mutation's data, craft tab, admin toggle | Studio |
| H2 | Concept sheet for pets, boss and mount (we pick), then models | Blender |
| H3 | Map layout of the three tiers, plaza portal | Python, then Studio |
| H4 | Rigs and animations for the pets | Blender, then Studio |
| H5 | Gameplay: tiers, spawns, portal, lantern, wardens, gates, Index tab | Studio |
| H6 | The mutation's look | Blender, then Studio |
| H7 and H8 | Boss rig, then the boss fight | Blender, then Studio |
| H9 | Mount model and effects | Blender, then Studio |
| H10 and H11 | Effects for the pets, then the boss | Studio |
| H12 | Robux items, spooky night, wrapper, music and icons | Studio |
| H13 | Full playtest with a copy of our save, release, migrate servers | Studio |

- **The Studio lane is the bottleneck.** Only one session should use the shared Studio at a time. Sessions that need no Studio (concept sheets, layouts, Blender models) run alongside.
- Starfall had 14 sessions (concepts, map, models, rig install, meteor server, meteor visuals, mutation look, boss, mount, effects, crafting and Robux, then the playtest and release). Each session had one clear outcome, a "needs" column and a model and effort.
- A **fallback** was written into each plan: if the boss or the effects slip, ship without them (a plain look, and the arena shows the boss "stirring" like a coming-soon area). The core ships on time either way.

**4. A real multi-session prompt.** The Kraken's last build session was started by a prompt written at the end of the session before. It names the settings, lists what to read first (with the sections of the plan note), asks for a **plan for approval before building**, then lists numbered build steps, rules for this session, what to verify and testing lessons from earlier sessions. See the prompt below.

**5. Playtest the whole thing before release.** Before Halloween opened, we forced the event on with an admin toggle and played the whole event on a **copy of our real save**: early, mid and late test teams, every gate and warden, a real boss fight, crafting, the code, Robux test purchases, the before and after phases. We restored the save afterwards and compared it deeply.

**6. Release steps, in order.** See the checklist rules below. The short version: freeze, scan, save, publish, migrate servers, post.

**7. Roadmaps and what's next.** Two notes: a roadmap of everything planned, and a release roadmap with a section "must do before release", "should add", "can wait for updates", plus decisions for us. After the first launch the roadmap became "one headline per update, every one to two weeks" with the headline in the title and thumbnail.

## Rules we follow now

- **Every big update gets a What's New pop-up on join.** Once per player per update, a new version key shows it again. Why: the user asked for it at the Halloween release ("add a whats new pop up when people join the game") so players see what is new without reading Discord. Small fix publishes do not get one.
- **Every update gets a new redeem code,** proposed in the announcement; we then add it to the code list in a separate task, and it must be in the published game before the post goes out. Rewards are helpful, not progress-breaking (boosts, capsules, minutes of income). Event codes can be locked to the event's dates. Why: codes bring players back and push them to the Discord.
- **Announcements are short teasers.** About five short lines that hint at what is new, the exact code line and the game link. No feature lists, odds, prices or mechanics. Why: we wrote a detailed post and the user rejected it: "make the announcement less descriptive, let the players explore the update themselves."
- **Re-scan the place as the very last step before saying "you can publish".** Check top-level Workspace children, models with bones, uncoloured grey meshes, test leftovers and duplicate scripts. Why: a raw model from another session's import landed mid-check, went live as a giant grey broom and needed a fix publish.
- **Everything event-only hides before the start.** The Index tab, titles, material rows, the portal and the mount card wait for the event. Why: the place can then be published days ahead without leaking the event.
- **Event things are switched by one config with dates and phases** (before, on, after, off), and admins can force a phase for tests. Why: no code change at release time, and tests do not depend on the clock.
- **After publishing, migrate every server.** Why: a server running older code drops saved fields it does not know, and would not run the event.
- **Check the publish in version history,** not the server page. Saving in Team Create makes a version but does not publish. Why: we once thought we had published when we had only saved.
- **Do not publish other sessions' half-finished files.** Before connecting the code sync ahead of a publish, look at which source files have uncommitted changes. Put unfinished ones back to their committed version in Studio. Why: the sync serves the whole working folder.
- **Ask before stopping a playtest you did not start.** Several sessions share one Studio. A playtest that restarts by itself belongs to another session; message it, and take save backups only after its lock clears. Why: stopping it breaks its test.
- **Events should sit on existing systems.** Halloween candy reused the material drops; the Haunted mutation reused capsule and fusion rules; the Kraken's side badge was reused for Halloween's countdown. Why: the Kraken took about three days of sessions; a second event cannot.
- **Big balance numbers are tested on a copy of a real save.** Why: test teams hide real progression problems.
- **Studio safety for cross-server buttons.** In Studio everyone counts as an admin and Studio's cross-server messaging can reach live servers. So Studio uses its own separate topic and only the real admin list can publish on the live one. Why: a test would otherwise shower real public servers.
- **Sound, thumbnails and posts are part of the update,** not afterthoughts: a music and effects set, an event page text, a thumbnail prompt and a short video all sit in the session table.
- **Keep Studio-only and phone checks for the end.** Multi-client tests, phone and controller checks need us at the computer; do them last and test with bots until then.
- **Time zones in config.** Write the dates as UTC timestamps with a comment for the local time you meant. Why: daylight saving had just ended when we planned one event, so the times were written for the new offset and the comment says which.

## Mistakes and gotchas

- **The giant grey broom.** See rules: an Import 3D leftover shipped. Fix: move it to `ServerStorage`, scan last.
- **Duplicate scripts after a code sync.** Several modules from different sessions existed twice. A disconnect and reconnect of the sync cleared them. Scan for duplicates in every pre-publish check.
- **A zone nobody could walk into.** The Starfall area was reachable by teleport but not on foot (a 30-stud height gap and a leftover wall). Tests teleported. Fix: lift the zone and cut an opening. Lesson: walk across every opened gate after opening it.
- **Another session's playtest.** Studio went back into Play right after we stopped it, again and again. It was another session's playtest. Ask before stopping.
- **Unfinished files reaching a publish.** Another session's half-done effects file was synced into Studio right before a publish. We put the old version back by hand and checked it by checksum.
- **Edit-mode save reads are blocked.** We could not read live saves from Edit mode. A Play server can read them (read only), which is enough to copy a real save into the test slot. Never write to the live save.
- **Debug setters replace whole tables.** A debug command that sets materials or boosts overwrote the real save. Back up the Studio save first and restore it after.
- **A code's grant at an item cap** shows "you got 5 capsules" while adding nothing. We left it (real players are far from the cap) but noted it.
- **Product images and lineups.** A new lineup's store images go up when it actually takes over, not at the update's release. Two events overlapping share products.
- **Event overlap.** Halloween and the new update's crafting tab overlapped for days. The new tab had to show beside the old one instead of waiting for the old crafts to close.
- **The game description went stale.** After Starfall, the game page still said "7 areas". Add the description and thumbnail to the release checklist.
- **Server restart permission.** An automatic server restart through the web API was refused (no write permission), so the user restarted outdated servers by hand in Creator Hub.
- **Mock before UI.** A new countdown pill needed a Figma mock first and the user picked one of three looks. Building the pill straight in would have cost a round.
- **A claim about saves was wrong once.** While planning a cross-server arena we first said saves were not locked to one server. They are. Search the code before planning anything cross-server.

## Code snippets

**1. One config with dates and phases (every event uses this shape).**

```lua
-- SeasonConfig.luau: the one place that says when the event runs and what phase it is in.
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local SeasonConfig = {}

-- UTC timestamps; the comment says the local time we meant
SeasonConfig.Start = 1793397600 -- Fri 6 pm Eastern
SeasonConfig.End = 1794200400   -- end of Sunday, Eastern
SeasonConfig.CraftDays = 3      -- crafts stay open after the end so leftovers can be spent
SeasonConfig.ForceAttribute = "SeasonForce"  -- admins set "on" | "after" | "off" for tests

function SeasonConfig.phase(): string
	local forced = ReplicatedStorage:GetAttribute(SeasonConfig.ForceAttribute)
	if forced == "on" or forced == "after" or forced == "off" then return forced end
	local now = workspace:GetServerTimeNow()
	if now < SeasonConfig.Start then return "before" end
	if now < SeasonConfig.End then return "on" end
	if now < SeasonConfig.End + SeasonConfig.CraftDays * 86400 then return "after" end
	return "off"
end

function SeasonConfig.active(): boolean return SeasonConfig.phase() == "on" end
function SeasonConfig.revealed(): boolean   -- UI hides event things before the start
	local p = SeasonConfig.phase()
	return p == "on" or p == "after"
end
return SeasonConfig
```

**2. A What's New version key (save the last one a player closed).**

```lua
-- WhatsNewConfig.luau: a new Version shows the card to everyone again.
local WhatsNewConfig = {}
WhatsNewConfig.Version = "update2026b"
WhatsNewConfig.Ends = 1795824000 -- stop showing it two weeks after release

function WhatsNewConfig.live(): boolean
	return workspace:GetServerTimeNow() < WhatsNewConfig.Ends
end
return WhatsNewConfig
-- client: show the card once the HUD has been free for 2 s (no battle, cutscene or open window)
-- when player data's seenWhatsNew ~= WhatsNewConfig.Version. On close, fire a remote with the
-- Version; the SERVER saves it in the profile. New saved field: migrate servers after publishing.
```

**3. A cross-server button with a Studio-safe topic.**

```lua
local MessagingService = game:GetService("MessagingService")
local RunService = game:GetService("RunService")

-- Studio gets its own topic, so a test can never reach live servers.
local TOPIC = if RunService:IsStudio() then "EventAdminStudio" else "EventAdmin"

function publishShower(adminName: string)
	if not isAdmin(player) then return end                       -- the server checks, not the client
	if os.time() - lastShower < COOLDOWN then return end         -- also checked on each receiving server
	MessagingService:PublishAsync(TOPIC, { cmd = "shower", by = adminName, at = os.time() })
end

MessagingService:SubscribeAsync(TOPIC, function(message)
	if message.Data.cmd == "shower" then runShower(message.Data.by) end
end)
-- best effort: a server that misses the message just misses that shower
```

**4. A timed event on the real clock (same slot in every server).**

```lua
-- slots at :00 and :30 of the clock, so every server agrees without talking to each other
local function nextSlot(now: number): number
	return (math.floor(now / 1800) + 1) * 1800
end

while true do
	local slot = nextSlot(os.time())
	task.wait(slot - os.time() - ANNOUNCE_LEAD)       -- announce 90 s early
	if serverAgeSeconds() >= 300 and anyEligiblePlayer() then
		startEvent(slot)                               -- skip young servers and empty ones
	end                                                -- otherwise the countdown moves to the next slot
end
```

## Prompts that worked

**A multi-session prompt (the Kraken's session 8, cleaned and shortened).**

```text
Session 8 of the Kraken event: rewards and release. Sessions 1 to 7 are done and pushed. Do only session 8. The pet and the new mutation are session 9.

Settings: [fill in: model and effort].

Read first:
- notes/Kraken Event Plan.md: Rewards, Schedule, Anti-cheat, Analytics, Saved event history, and the session table.
- the task logs for the icon pipeline and the boost and capsule pipelines.
- the last two Kraken task logs (what was built, what is still open).

Step 1, before building anything: a plan for my OK. The icons (one line each), the boost and capsule (one line each), the capsule's options until the mutation exists, the badge, the audio (who hears what, and how the shortlist works), the release checklist and a playtest plan. If this is too much for one session, propose a split.

Then build, in this order: (1) icons, (2) the heal boost, (3) the capsule, (4) the badge, (5) audio, (6) release switches, (7) tuning with real players, (8) open items from the last session.

Rules: build only this scope. Do not change timings, fight rules or reward numbers without my OK. Rewards stay server-side: one grant per player per event. No uploads without asking. New effects use the event effects module.

Verify: icons on every page that shows them, crafting both items, the badge awarded once, every sound on its beat, the schedule on, check scripts and the linters.

At the end: write the task log, update the notes, commit and push, and remind me to save in Studio.

Lessons for testing (from earlier sessions): [fill in: your own list, such as "only I can switch the phone emulator", "restore test rewards from the save backup", "do the multi-player test last"].

What questions do you have for me so I can help you implement this feature as best as possible?
```

**An update-wrapper prompt (example, in our style).**

```text
We are releasing the [fill in: update name] on [fill in: date]. Before I publish, do the wrapper:
1. Add a new What's New entry (new version key, 3 to 5 highlight tiles with pictures, the code chip).
2. Propose a redeem code name and reward (helpful, not progress-breaking). I will ask you to add it to the code list separately.
3. Write a Discord teaser: about five short lines, the exact code line, the game link. No feature list.
4. Run the pre-publish check: top-level Workspace, models with bones, grey meshes, test leftovers, duplicate scripts, forced test attributes, and which source files have uncommitted changes. Do the Workspace scan last.
Tell me what you found before I publish.

What questions do you have for me so I can help you implement this feature as best as possible?
```

**An event idea round (example).**

```text
I want a limited-time [fill in: season] event for [fill in: dates]. Give me a table of 10 ideas with a rough effort size (S = an hour or two, M = a session, L = several sessions). Suggest a core of the cheapest ideas that reuse systems we already have, plus one headline. Then list what you would NOT do and why. Do not build anything yet.

What questions do you have for me so I can help you implement this feature as best as possible?
```

## Do it in your game

1. **Make an idea table.** Ask Claude for ten ideas with effort sizes. Pick a cheap core that reuses what you have, plus one headline. Write the choices into a plan note.
2. **Write balance rules first.** What can the event never give? (Main-area unlocks, level skips, flat cash.) Who is it for (early, mid, late players)?
3. **Build one config with dates and phases,** and an admin switch to force it. Everything event-only reads that one check and stays hidden before the start.
4. **Split the work into sessions** with a table: the lane, what each needs, the model and effort. Run non-Studio sessions in parallel. Write a fallback (what ships if a piece slips).
5. **Do the wrapper:** a What's New pop-up with a version key, a redeem code, a short teaser post, an event page, a thumbnail line.
6. **Test on a copy of a real save,** with the event forced on, then forced off and "after". Scan the place last, publish, check the version history, and migrate every server.

Starter prompt for a new chat:

```text
First read ~/.claude/skills/game-coach/playbook/live-events.md.

I am building a Roblox game called [fill in: your game]. I want to ship [fill in: an event or an update] on [fill in: a date]. My game already has [fill in: systems like crafting, capsules, bosses, a currency]. Players range from [fill in: new to end-game].

Before you build anything: give me an idea table with effort sizes, the balance rules the event must follow, a session table (lane, needs, model and effort, fallback), and the release checklist (What's New, a code, a pre-publish scan, migrating servers). Use one config with dates for everything event-only. Keep it as small as possible and reuse what the game already has.

What questions do you have for me so I can help you implement this feature as best as possible?
```
