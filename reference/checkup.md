# Checkup mode: how is their setup working?

Look over how the student's Claude setup is doing and suggest the three fixes that would help most. Don't change anything without a yes.

## Read (keep it small)

- `CLAUDE.md`
- `notes/Home.md` and `notes/Working Preferences.md`
- The 5 newest task logs (follow the Task log list in Home, newest first)
- Their memory index (`MEMORY.md` in the memory directory named in your instructions), plus any memory file it points to that looks off
- `notes/Coach Progress.md`
- **Optional, ask first:** their recent prompts. Claude Code keeps chat history in `~/.claude/projects/<project folder name>/*.jsonl`. Read only the user's messages from the 3 newest files, at most 10 messages each. Grade the patterns, not every prompt.

## What to look for

**CLAUDE.md**
- Missing basics: the session start steps, "build only what I ask", what to do after each task, how code gets into Studio.
- Too long (over about 150 lines). Move details into notes and keep the rules.
- Out-of-date facts, rules that contradict each other, or vague rules ("be careful") that Claude can't act on.

**Memory**
- Corrections the student keeps repeating in chats that were never saved.
- Memories missing their **Why** (Claude can't judge the edge cases without it).
- Duplicates, or facts that changed since.
- Rules for the whole project that belong in CLAUDE.md instead (so their partner's Claude gets them too).

**Notes**
- Task logs stopped being written, or are missing `## Rejected / learned` when something was turned down.
- Notes that Home doesn't link to.
- Topic notes that disagree with the newest task logs.

**Habits** (from the progress file and their prompts)
- The same miss three or more times → suggest a CLAUDE.md line that covers it.

## What to send back

```
**Working well:** 1-2 lines.
**Top 3 fixes:**
1. <fix> — <why> — <the exact text to add or change>
2. ...
3. ...
```

Ask which ones to apply, apply only those, and record the checkup date in the progress file.
