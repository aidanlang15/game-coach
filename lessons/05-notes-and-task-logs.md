# Lesson 5: Notes and task logs, the project's brain

## The idea

A `notes/` folder of plain Markdown files that Claude reads and writes:

- **Home.md:** the index. It links every note, plus the task log list (newest first).
- **Project Overview.md:** what the game is, who builds it, the look you want, what's in scope.
- **Working Preferences.md:** how you like work done.
- **Topic notes:** one per system (Pets, Battles, Shop, Map). What it does, the files involved, the decisions made.
- **Task Log/:** one short note per finished task: what was built, how it was checked, what's left. When you turned something down or learned something, it ends with **`## Rejected / learned`**.

CLAUDE.md tells Claude to write the task log, update the topic notes and link it all in Home after every task.

## Why

Chats end, but notes stay. Before each task, Claude searches the notes for the pet, system or area involved, so it knows what you already approved and, even more important, **what you turned down**. Without that, a new chat cheerfully suggests the same idea you rejected last week.

Notes are also how two people stay in sync. Your partner pulls, and their Claude reads your task logs.

## From Battle for Pets

A real task log is in `examples/task-log.md`. A raw 3D import was left in the map, a giant grey broom shipped to the live game, and the log's `Rejected / learned` turned that into a rule: re-scan the map as the very last step before publishing.

We used Obsidian to read the notes. "Open folder as vault" on `notes/` gives you clickable `[[links]]` and a graph of how everything connects. It's optional: Claude only needs the files.

## Exercise

Ask: "Think of the last thing you built with Claude. Write the `## Rejected / learned` line for it. What did you turn down, or what went wrong, and why?" If they say "nothing", ask what they had to ask Claude to change. There's almost always something.

## Apply it now

If they don't have notes yet, suggest `setup`. If they do, check that the last 3 tasks have logs. If they don't, help write them from what the student remembers.
