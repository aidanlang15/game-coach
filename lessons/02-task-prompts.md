# Lesson 2: Writing a task prompt

## The idea

Claude does what you say, not what you meant. A good prompt answers the questions Claude would otherwise guess at. The full checklist is in `reference/prompt-review.md`. The five that matter most:

1. **One task per message.** It's easier to check, and a mistake only breaks one thing.
2. **Say exactly what you want:** what, where, and who sees it.
3. **Say what done looks like:** what you'll see when it works.
4. **Say what to leave alone.**
5. **Point to the right note, and attach pictures** for anything visual.

## Why

Every gap gets filled with a guess, and guesses cost you a redo. "Add leaderboards" could mean a global board, a server board, a GUI, a 3D board, or richest or strongest players. Claude picks one, builds it, and you spend your next prompt explaining what you actually meant.

## From Battle for Pets

> Weak: "add leaderboards"
>
> Strong: "Add a leaderboard of the 10 richest players in the server, shown on a board in the hub plaza. Don't change the HUD."

A bug report works the same way (this one is an example):

> Weak: "the battle is broken"
>
> Strong: "After I win a wild battle on a phone, the capsule throw button is hidden behind the team bar. Expected: the button shows above the team bar like on PC. No errors in Output. Screenshot attached."

For new systems we never just say "build it". We say: "Before building, ask me 3-5 design questions, each with options and a default." Battles, catching and progression were all designed that way, and that's lesson 7.

More real before-and-after prompts are in `examples/prompts.md`.

## Exercise

Give the student this weak prompt and ask them to rewrite it. Use a version from their own game if you know it:

> "make the shop better"

Then grade their rewrite with the review checklist. A good rewrite names one shop, one change ("the egg cards are too small on phones; make them fill the width"), what done looks like ("4 cards fit on an iPhone screen without scrolling sideways") and what to leave alone ("don't change prices").

## Apply it now

Tell them: before each real task for the next few days, run `/game-coach review` on the prompt first. It takes a minute and the habit sticks fast.
