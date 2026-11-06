# Lesson 11: Starting your next game

## The idea

Your second game shouldn't start from zero. Everything Claude learned on the first one can come along:

1. **Copy your memories over.** The lessons about your taste, your tools and Roblox's quirks still apply. Give the new game's Claude a copy of the old game's memory files on day one.
2. **Let it read the old game, but never touch it.** Give the new project read access to the old game's folder (Claude Code's `additionalDirectories` setting) and add a section to the new CLAUDE.md: "Use its notes and task logs for lessons. Reuse its code by copying it into this project. Never edit, commit or run anything in that folder."
3. **One Rojo port per game.** Each project gets its own port (for example 34872, 34880 and 34890), and CLAUDE.md says which. Never connect one game's Rojo to another game's place: it overwrites or deletes that game's scripts.
4. **Study the genre first.** Before designing, look closely at the top games in your genre. Record a few minutes of play, have Claude pull frames from the video and describe the layouts, loops and screens.
5. **Say what your words mean.** "Areas" can mean whole worlds or single stages. "Make it cooler" means nothing to Claude. Pin it down.

## Why

Without the copied memories, the new game's Claude makes every mistake the old one already learned from. Without the read-only rule, a chat in one project can "helpfully" change the other game. And a genre game whose screens don't look like the genre's hits feels wrong to players, who already know where everything should be.

## From our games

- **+1 Mecha Cyborg** started with about 60 memories copied from our other games, and a snapshot of Battle for Pets' UI kit and training system to build on.
- Its first three HUD mocks, in Battle for Pets' style, were all turned down. The user sent a screenshot of a top game in the genre: "make it exactly like this". For standard screens (HUD, rebirth, shop) in a genre game, match the layout the hits use. Keep your own art, icons and names, never another game's assets.
- Its design round offered 5 areas. The user wanted far more, and asked whether "areas" meant arenas or worlds. Now Claude says "worlds" or "stages" explicitly.
- **+1 TikTok Fame** began with a genre study: a recording of a top +1 game, cut into frames every 2 seconds, plus close-ups of every menu.

## Exercise

Ask the student: "If you started a new game tomorrow, which 3 things from your current project's memory or notes should come with it, and which should stay behind?" Good answer: taste and tool lessons come along. Game-specific facts stay behind, like this game's pets, prices and file names.

## Apply it now

If they already have a second project, offer to add the "other games" section to its CLAUDE.md (example in `examples/other-games.md`).
