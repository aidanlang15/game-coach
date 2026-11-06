# Example: lessons from our other games

Besides Battle for Pets, the same person built three more Roblox games with Claude Code:

- **Axel**, a racing and car game, solo
- **+1 Mecha Cyborg**, a "+1" genre game, solo
- **+1 TikTok Fame**, a friend's game they help build

These are real excerpts with names, IDs and repos taken out.

## "Other games" section (from +1 Mecha Cyborg's CLAUDE.md)

```markdown
## My other games

This project can read them (added to `additionalDirectories` in `.claude/settings.json`):

- `<path>/battle-for-pets`: my first game. The source of this game's UI style and training system.
- `<path>/Axel`: my racing game.
- `<path>/plus1-tiktok-fame`: my friend's +1 game. The same genre as this one, so check it for how +1 loops, rebirths and areas were done.

Rules for them:

- Use their notes and task logs for lessons and for how similar things were built, and reuse their code by copying it into this repo.
- Read only: never edit, commit, pull or run Rojo or scripts in those folders. Their CLAUDE.md doesn't apply here.
- The memories were copied from those games. They may name those games' files.
```

And in "Before each task": "When this project has nothing on it yet and one of the other games built something similar, check its notes the same way."

## Guest section (from +1 TikTok Fame's CLAUDE.md)

```markdown
## It's [owner]'s game

- Follow how their game already works: their scripts, naming and style. Don't restructure, rename or reformat their code, and don't "clean up" things nobody asked about.
- They may be editing in Team Create at the same time. Before changing a script, model or area, ask me whether they're working on it, and re-read it fresh right before editing.
- Never publish the game, change game settings, monetization or permissions, or delete their work. Those are their calls; tell me what needs their OK.
- Animations and audio must be owned by the game's owner to play.

## Code

- The code lives only in the Studio place (no Rojo). Read and edit scripts through the Studio MCP.
- Before editing a script, save its current Source to `snapshots/<full instance path>.luau` and commit, so the edit can be undone. After the edit, save the new Source over it. Commit both.
- Run `selene` on the edited snapshot files. Don't run stylua (it would reformat their code).
```

## Rejected / learned, from their task logs

**Looks and design**

- *Axel:* "Does the car model look anything like the reference photo? Genuinely look at both." The car Claude built was low, boxy and flat next to a tall, curved concept, and several rounds were wasted before anyone compared them properly. Now Claude puts its work next to the reference from the same angles before showing it.
- *Axel:* a phone controls mock "looks very AI made". It had flat circles with words in them and plain gray squares. The version that passed had drawn icons, depth (a rim, gloss, a shadow) and color by job.
- *Axel:* "the first finishes all kind of look the same". Under the game's lighting, Roblox's built-in materials barely changed anything. Judge looks in the game's real lighting.
- *+1 Mecha Cyborg:* the first mecha was "fine for the first one", but the next ones should be "really really cool and mechy". When something is only "fine", ask what would make it great.
- *+1 Mecha Cyborg:* the user picked none of the proposed design twists. They wanted the mechas themselves to carry the game. Don't push extra systems into a genre game.

**Building and testing**

- *Axel:* the whole track model got dragged about 22 studs out of place, most likely by accident, and lifted the land over the road. Now every finished track part is Locked.
- *+1 Mecha Cyborg:* faces that sit almost on top of each other flicker, even 0.02-0.06 studs apart. Keep them about 0.15 apart.
- *+1 Mecha Cyborg:* a knee pad looked fine from the front but like a loose loop from behind. Check every view, not just the front.
- *+1 Mecha Cyborg:* two parts with the same name made a script find the wrong one. Give parts unique names.
- *+1 TikTok Fame:* the screen recording captured the browser instead of Roblox, because Windows' recorder records whichever window is active. Click into the game first.
