# Example: Battle for Pets' CLAUDE.md

This is our real CLAUDE.md (Nov 2026), lightly trimmed, with the place ID and repo name taken out. After it are the stories behind the rules. Use it as an example, not something to copy word for word: your game needs your own rules.

```markdown
# Battle for Pets

Roblox pet collection and battle game. Project state, the user's decisions and scope rules live in the Obsidian vault notes under `notes/`: start at `notes/Home.md`, which links every topic note and the task log. Read the notes relevant to the task (Home, Project Overview and Working Preferences always). Only make the changes the user asks for.

## Start of every session

Do these before the first task, without being asked:

1. Run `git pull` to get the collaborator's latest changes.
2. Check whether Rojo is already listening on port 34872. If not, ask the user to run `rojo serve default.project.json` from this folder in their own Terminal panel (assistant background jobs get killed after 30 minutes to 2 hours).
3. Call `list_roblox_studios`. The expected place is **Battle For Eggs**. Studio session IDs change every time Studio restarts, so never reuse an old one.
4. Tell the user in one short line what is ready and what they still need to do (open Studio, start Rojo, or click Plugins → Rojo → Connect).

## Before each task

Recommend both a model and a reasoning effort before each new task, with a brief reason based on complexity and the consequences of mistakes. Tell the user which settings to select and wait if a switch is needed. Do not interrupt routine follow-ups within the same task to repeat the recommendation.

Effort levels: low/medium for tiny tweaks and questions; high for map building, Blender models, visual polish and most scripting; xhigh/max for complex rigging/animation integration, battles, capture, DataStore saving, per-player boss unlocks and server security.

Check past work before planning. Search `notes/` (topic notes and `notes/Task Log/`) for the task's key names: the pet, boss, mount, area, system or screen. Read the topic note and the 2–3 newest matching task logs, and follow what the user approved, turned down or asked to change there, including each log's `## Rejected / learned` section. One search per task; skip it for routine follow-ups within the same task.

## Keep work simple and efficient

The user has limited usage and explicitly wants focused work without overcomplication. Apply this to every task:

- Build only the requested scope. Choose the simplest suitable approach and reuse existing systems and assets. Add abstractions, pipelines, dependencies or extra polish only when the task needs them.
- Keep planning, file reads and tool calls proportionate to the change. Reuse information already gathered in the session; batch independent checks and avoid repeating unchanged results.
- Run the required checks and meaningful verification for the change, then finish. Repeat or broaden checks only for failures, new edits or unresolved risks. Keep necessary combat, saving and security validation.
- Avoid extra design reviews and permission rounds when the user has already approved the direction or action. Resolve routine choices yourself.
- Flag unexpected time or usage costs early. Keep updates brief and explain concrete progress or blockers. Stop polishing once the requested result works and visible issues are fixed.

## Code

- Game code syncs through Rojo: `src/client` → StarterPlayerScripts, `src/server` → ServerScriptService, `src/shared` → ReplicatedStorage.Shared. Edit the files, not the scripts in Studio.
- Rojo manages code only. The map lives in the Studio place and is edited there.
- `map-scripts/` holds old one-time map-building passes. Never sync or rerun them on the current place.
- Zones 2–8 are generated: `tools/zonegen` (Python layouts) → built in Studio by a map builder. Edit the Python layout and rebuild the zone instead of hand-editing generated zone models.
- Run `selene src` and `stylua --check src` on changed code.
- Commit after each finished task. Remind the user to save in Studio (Ctrl+S), since only saving keeps changes in the place.

## Notes (Obsidian vault)

The project folder is an Obsidian vault; notes live in `notes/` and sync through git with the code. After each finished task, before committing: add `notes/Task Log/YYYY-MM-DD Title.md` (frontmatter `date` and `tags: task-log`), add it to the top of the task log list in `notes/Home.md`, and update the topic notes the task changed. When the user turned something down or a lesson came up, end the log with `## Rejected / learned`: what, and why, in a line or two. Link notes with `[[Note name]]`.

## Working as a team

Two people build this game. Code and project files are shared through a private GitHub repo; the map is shared through Roblox Team Create.

- Push after each commit (`git push`) so the other person gets it.
- Both people can work at the same time if they don't touch the same things: different models or areas in Team Create, different files in code.
- Only one person should run Rojo against the Team Create place at a time.
- To test code without being the Rojo person: File → Save to File for a personal copy of the place, open it, and run Rojo against that copy.
```

## Why each rule is there

- **"Only make the changes the user asks for."** Early on, Claude kept adding props, decorations and "improvements" that had to be removed again. The clearest version we ever gave it: "That's it, don't do anything I'm not telling you to do."
- **"Studio session IDs change every time Studio restarts."** Without it, Claude reuses an old ID and talks to a Studio window that's gone.
- **"In their own Terminal panel."** Claude's background jobs get stopped after 30 minutes to 2 hours. If Rojo runs there, code quietly stops syncing.
- **"Never rerun `map-scripts/`."** Those scripts built the first map. Rerunning one would rebuild over everything changed by hand since.
- **"Edit the Python layout and rebuild."** Hand edits to a generated zone are lost the next time it's rebuilt.
- **"Check past work before planning."** Without it, a new chat suggests ideas we already turned down. The `Rejected / learned` sections are what it searches for.
- **"Keep work simple and efficient."** Usage is limited. Re-checking things and polish nobody asked for eat most of it.
- **"Only one person runs Rojo."** Two Rojo servers syncing into one place overwrite each other's scripts.
- **"Remind the user to save (Ctrl+S)."** Changes in the place are only kept after a save, and Claude can't press it for you.

One more from our Working Preferences: a deleted old script once came back as an untracked file, Rojo synced it in, and the game had two wild-pet spawners. Now Claude checks `git status` for unexpected files before syncing.
