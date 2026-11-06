# [Game name]

[One line: what the game is.] Project state, decisions and scope rules live in the notes under `notes/`. Start at `notes/Home.md`, which links every topic note and the task log. Read Home, Project Overview and Working Preferences before the first task, and the notes relevant to each task.

## Start of every chat

Do these before the first task, without being asked:

1. Run `git pull` to get [partner]'s latest changes.
2. Check whether Rojo is running (`rojo serve` listens on port 34872). If not, ask me to start it in my own terminal.
3. Call `list_roblox_studios` and use the place **[place name]**. Studio IDs change every time Studio restarts, so never reuse an old one.
4. Tell me in one short line what's ready and what I still need to do.

## Before each task

- Recommend a model and reasoning effort with a short reason: low/medium for small tweaks and questions, high for most scripting, UI and building, the highest for saving data, security, battles and anything with Robux. Wait if I need to switch.
- Search `notes/` (topic notes and `notes/Task Log/`) for past work on the same thing. Follow what I approved, turned down or asked to change there, including each log's `## Rejected / learned`.
- New systems: ask me 3-5 design questions first, each with options and a default. "d" means use your default.

## How to work

- Build only what I ask. No extra features, redesigns or polish I didn't ask for.
- Choose the simplest approach that works and reuse what already exists.
- Keep approved work as it is unless I ask to change it.
- Be clear about status: what's planned, what's built, and what you actually checked.
- Keep updates short. Flag early if something will take a lot of usage.

## Code

- Code syncs through Rojo: `src/client` → StarterPlayerScripts, `src/server` → ServerScriptService, `src/shared` → ReplicatedStorage.Shared. Edit the files, not the scripts in Studio. The map lives only in the place.
- The server decides anything that matters (money, pets, battles, saving). The client only asks.
- [If set up:] Run `selene src` and `stylua --check src` on changed code.

## After each task

1. Add `notes/Task Log/YYYY-MM-DD Title.md` (frontmatter `date` and `tags: task-log`): what was built, how it was checked, what's left. When I turned something down or a lesson came up, end with `## Rejected / learned` (what, and why, in a line or two).
2. Link it at the top of the Task log list in `notes/Home.md`, and update the topic notes the task changed.
3. Commit and push.
4. Remind me to save in Studio (Ctrl+S).

## Working as a team

- Pull before starting, and commit and push when a piece of work is done.
- Prefer different files from [partner]. In Team Create, don't edit the same model or area at the same time.
- Only one person runs Rojo against the Team Create place at a time.
