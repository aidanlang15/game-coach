# Setup mode: setting up Claude for their game

Goal: by the end, every new chat in their project starts already knowing the game, the rules and what happened last time. Templates are in `templates/`. Real examples are in `examples/`.

## 1. Look before asking

Check, without changing anything:

- Is there a `CLAUDE.md` in the project folder already? A `notes/` folder?
- Is it a git repo (`git status`)? Is there a remote (`git remote -v`)?
- Is there a `default.project.json` (Rojo)? A `src/` folder?
- Are the Roblox Studio MCP tools available? If so, call `list_roblox_studios` to see which place is open.
- Is there a memory directory mentioned in your own instructions (Claude Code's auto memory)?

If CLAUDE.md or notes already exist, **don't overwrite them**. Propose additions instead, and show exactly what you'd add.

## 2. Ask (with defaults; "d" takes the default)

1. The game in 2-3 sentences: genre, what players do, what makes it fun.
2. Reference games and the look you want (default: none yet).
3. Who builds what? (default: from the progress file)
4. What's built so far? (default: "just started")
5. The Studio place's name. (Default: the one `list_roblox_studios` shows.)
6. How does code get into Studio? (Default: whatever you found in step 1: Rojo if there's a `default.project.json`, otherwise Claude edits scripts in Studio through the MCP.)
7. Anything Claude does that annoys you? (Default: "adding things I didn't ask for", the most common one.)
8. Is this your game or a friend's? (default: mine) If it's a friend's, add a guest section like the one in `examples/other-games.md`, and suggest lesson 12.
9. Do you have another game already set up with Claude? (default: no) If so, offer to copy its memories, and to add a read-only "other games" section (lesson 11).

If there's no Rojo, the template's "Without Rojo" line replaces the Rojo one. A git repo still helps here: it's where the script snapshots go, so every edit can be undone.

## 3. Show the plan, wait for yes

List the files you'll create, one line each. Wait for the student's OK.

## 4. Create the files

Fill each template with their answers and delete the lines that don't fit them (no Rojo means no Rojo lines, no partner means no "Working as a team" section):

- `CLAUDE.md` ← `templates/CLAUDE.md`
- `notes/Home.md` ← `templates/notes/Home.md`
- `notes/Project Overview.md` ← `templates/notes/Project Overview.md`
- `notes/Working Preferences.md` ← `templates/notes/Working Preferences.md`
- `notes/Session Start Prompt.md` ← `templates/notes/Session Start Prompt.md`
- `notes/Task Log/<today> Claude setup.md` ← `templates/notes/Task Log entry.md`. This is the first log, describing the setup itself.

Keep CLAUDE.md short (under about 100 lines). Long details go in notes, which Claude reads only when it needs them.

**Memory:** if your instructions include a memory directory, save one `user` memory: their name, their level, who they build with, and the game in one line. Explain that from now on they can say "remember that ..." whenever they correct Claude (lesson 4).

**Obsidian (optional):** the notes are plain Markdown, so any editor works. To get clickable `[[links]]` and a graph view, install Obsidian, choose "Open folder as vault" and pick the `notes` folder.

## 5. Finish

- If they use git, offer to commit ("Set up Claude notes") and push. Only do it on a yes.
- Tell them the two new habits:
  1. **Start every new chat** by pasting the prompt in `notes/Session Start Prompt.md`.
  2. **After each task**, Claude writes a task log and updates the notes. CLAUDE.md now tells it to.
- Update the progress file (setup done) and suggest lesson 2.
