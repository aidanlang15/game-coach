# Game Coach

A coach for building Roblox games with Claude Code. It trains you to get great results from Claude: better prompts, a CLAUDE.md that sets the rules, memories that teach Claude your taste, and notes that keep every chat up to date. It's built from four Roblox games we made with Claude Code (**Battle for Pets**, **Axel**, **+1 Mecha Cyborg** and **+1 TikTok Fame**), with real examples from them.

It's a Claude Code **skill**: your own Claude turns into the coach when you type `/game-coach`. It coaches, it doesn't build your game, and it asks before writing any file.

## Install

Paste this into Claude Code:

```text
Install the Game Coach skill: git clone https://github.com/aidanlang15/game-coach into my personal skills folder (~/.claude/skills/game-coach). Then tell me to start a new chat.
```

Or run it yourself (Git Bash, macOS or Linux):

```bash
git clone https://github.com/aidanlang15/game-coach ~/.claude/skills/game-coach
```

Windows PowerShell:

```powershell
git clone https://github.com/aidanlang15/game-coach "$env:USERPROFILE\.claude\skills\game-coach"
```

Start a new chat in your game's folder and type `/game-coach`.

**Update:** ask Claude to "git pull in ~/.claude/skills/game-coach".

## Use

| Command | What it does |
| --- | --- |
| `/game-coach` | Asks 4 quick questions the first time, then suggests what to do next |
| `/game-coach setup` | Sets Claude up for your game: CLAUDE.md, a notes folder, a session start prompt |
| `/game-coach review <your prompt>` | Grades a prompt before you send it, rewrites it, and teaches one habit |
| `/game-coach lesson` | The next lesson (or `lesson 4`, `lesson memory`), each with a short exercise |
| `/game-coach checkup` | Looks over your CLAUDE.md, memory and notes and suggests the top 3 fixes |

Good order: `setup`, then lessons 1, 2, 3 and 5, then `review` before your real prompts for a few days.

## The lessons

1. How an agent works, and why Claude forgets
2. Writing a task prompt
3. CLAUDE.md: rules every chat reads
4. Memory: teaching Claude your taste
5. Notes and task logs: the project's brain
6. Picking the model and effort
7. Design questions, options side by side, mocks
8. Checking the work, git and saving
9. Making your usage last
10. Skills: turning a routine into a command
11. Starting your next game: carry the lessons over
12. Helping on a friend's game

Your progress is saved in `notes/Coach Progress.md` in your project.
