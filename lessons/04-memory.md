# Lesson 4: Memory, teaching Claude your taste

## The idea

Claude Code can keep **memories**: small files it writes itself, one fact each, in a memory folder for your project. A short index (`MEMORY.md`) loads into every chat. The full memory loads when it's relevant.

You make one by telling Claude: **"Remember that ..."**. It's best done right after you correct it.

There are four kinds:

- **user:** who you are. ("I'm 15, I script in Luau, my partner does the models.")
- **feedback:** how you want things done, including corrections. ("Never make pets chibi.")
- **project:** what's going on. ("Halloween update ships Oct 30.")
- **reference:** where things are. ("Our analytics are at ...")

## Memory vs CLAUDE.md vs notes

- **CLAUDE.md:** project rules for everyone, including your partner's Claude. Memory is only on your computer.
- **Memory:** your taste and lessons learned, Claude's own notebook.
- **Notes:** the game itself: what's built and why.

Rule of thumb: if your partner's Claude should follow it too, put it in CLAUDE.md or notes, not memory.

## Why

A correction you don't save is a correction you'll make again tomorrow. The best memories include **why**, because then Claude can handle the cases you didn't think of.

## From Battle for Pets

> **Pet art style:** sturdy, not chibi, confident eyes, no blush or cute accessories.
> **Why:** the user rejected a chibi pass as "too cute" and "looks much smaller".
> **How to apply:** build new pets this way. Exception: the Shark keeps its toothy chibi grin, which the user liked.

> **"d" means default:** when Claude asks several questions with a proposed default for each, the answer "d" means use the default.
> **Why:** fast answers without retyping.

More in `examples/memories.md`. Notice that each one has a **Why** and a **How to apply**.

## Exercise

Ask: "What's something you corrected Claude on recently?" Help them phrase it as a memory with a Why. If your instructions include a memory folder, offer to save it, with their OK.

## Apply it now

Habit: every time you say "no, not like that", follow it with "remember that ...". And once a month, ask Claude to tidy the memories: merge duplicates and delete the outdated ones.
