# Lesson 12: Helping on a friend's game

## The idea

When you help build a game someone else owns, your Claude has to act like a guest:

1. **Follow how their game already works.** Their scripts, their names, their style. No restructuring, renaming, reformatting or "cleaning up" things nobody asked about.
2. **Ask before touching something.** In Team Create they may be editing the same script or area right now. Ask first, and have Claude re-read it fresh right before editing.
3. **The owner's calls stay the owner's.** Your Claude never publishes, changes game settings, monetization or permissions, or deletes their work. It tells you what needs their OK.
4. **Make every edit undoable.** If their game has no Rojo and no repo, Claude edits scripts in Studio through the MCP. Before each edit, it saves the script's current code to a `snapshots/` folder in a small git repo of your own and commits. If something breaks, the old version is right there.
5. **Check who owns the assets.** Roblox only plays an animation (and some audio) in a game when the game's owner owns it. An animation you upload to your own account won't play in your friend's game: they, or their group, have to upload it.

## Why

It's their game. A "small improvement" to their code can break something they understand and you don't, and reformatting their scripts makes every one of them look changed, so they can't see what you actually did. And without snapshots, there's no undo for an edit made straight in Studio.

## From our games

**+1 TikTok Fame** is a friend's game that one of us helps build. Its CLAUDE.md has a section called "It's [owner]'s game" with the rules above. Some details from it:

- No stylua on the owner's code, because it would reformat everything. Only `selene` runs, to catch real mistakes.
- Team Create's Collaborative Editing can turn an edit into a draft. The first time, check that the change actually reached the place.
- Every script edit gets a snapshot before and after, both committed, so the history shows exactly what changed.

## Exercise

Ask: "Your friend's shop script is messy, and you're adding one new item to it. Your Claude offers to tidy the whole script while it's there. What do you say, and why?" Good answer: no. Add the item only. Tidying their script is their call, and it hides the real change.

## Apply it now

If the student helps on someone else's game, offer to add a guest section to that project's CLAUDE.md, using `examples/other-games.md` as the starting point.
