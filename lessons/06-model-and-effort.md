# Lesson 6: Picking the model and effort

## The idea

Claude Code lets you pick a **model** (bigger models are smarter but use your limit faster) and a **reasoning effort** (how long it thinks before acting). Matching them to the task saves usage on easy jobs and avoids costly mistakes on hard ones.

Our table (the names in your model menu may differ; the idea is what matters):

| Task | Model | Effort |
| --- | --- | --- |
| Questions, ideas, names, descriptions | smallest or middle | low |
| Writing prompts, notes, docs | middle | low–medium |
| Small exact edits (a price, a label) | middle | low–medium |
| Everyday Luau features, UI screens | middle or biggest | high |
| Map building, 3D models | biggest | high |
| Rigging and animation in Studio | biggest | xhigh |
| Battles, capture rules, saving data, security, Robux | biggest | xhigh–max |
| Hard bugs across several systems | biggest | xhigh |

**Go up a level** when a mistake could lose players' saves, let people cheat, or break work you already approved. **Go down** for quick questions and small follow-ups.

## Why

The biggest model at max effort on "change this label" burns your limit for nothing. The smallest model on DataStore saving can wipe players' progress. Saving and security mistakes are the expensive ones, because players notice and they don't come back.

## From Battle for Pets

Our CLAUDE.md tells Claude to recommend a model and effort before each new task and wait if we need to switch. It takes two seconds, and we stopped running everything at max.

## Exercise

Ask the student to pick a model and effort for each task, then check their answers against the table:

1. "Rename the Fire Dog to Blaze Pup."
2. "Save each player's pets so they're still there when they rejoin."
3. "Give me 10 name ideas for a desert boss."
4. "Make a new shop window for eggs."

Answers: 1 small/middle, low. 2 biggest, xhigh–max (saving data). 3 small, low. 4 middle or biggest, high.

## Apply it now

Offer to add this line to their CLAUDE.md: "Before each new task, recommend a model and effort with a short reason, and wait if I need to switch."
