# Lesson 8: Checking the work, git and saving

## The idea

"Done" means **checked**, not "Claude said it's done". After each task:

1. **Playtest it**, yourself or by asking Claude to playtest through the Studio MCP (start Play, trigger the feature, take screenshots, read the Output).
2. **Ask what was actually checked.** Claude should separate *planned*, *built* and *verified*. "I wrote the code" is not "I saw it work".
3. **Commit and push** (if you use git), so the work is saved and your partner gets it.
4. **Save the place in Studio (Ctrl+S).** Studio changes only stick after saving, and players only see them after you **publish**.

## Why

Code can look right and still not work: wrong script location, a typo in a name, something that only breaks on phones. Finding it now costs one message. Finding it after publishing costs players.

Git is your undo button. If a task goes wrong, you can go back to the last commit. Without commits, a bad change can wipe hours of work.

## Safety habits for Roblox

- **The server decides anything that matters:** money, pets, battle results, saving. The client only *asks*, so exploiters can't just give themselves pets.
- **Back up player data before changing how saving works,** and test with a copy, never with real players' saves.
- **Check phones.** Most Roblox players are on phones. Use the device emulator in Studio.
- **Check the map before publishing.** A test model left in Workspace goes live.
- **Lock finished builds.** On Axel, the whole race track got dragged 22 studs out of place by accident. Locked parts can't be dragged.
- **Check every angle.** A mecha's knee pad looked fine from the front and like a loose loop from behind.

## From Battle for Pets

A raw 3D import of a broom was left in Workspace, and the next publish put a giant grey broom into the live game. Now our last step before every publish is "re-scan Workspace for leftovers" (`examples/task-log.md`).

## Exercise

Ask: "Claude says 'Done! The shop now shows 4 eggs.' What three things do you check before you believe it?" Good answer: playtest and see the 4 eggs (on a phone too), check the Output for errors, then commit and push and save in Studio.

## Apply it now

Offer to add an "After each task" section to their CLAUDE.md (see `templates/CLAUDE.md`) if it's missing.
