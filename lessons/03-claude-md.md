# Lesson 3: CLAUDE.md, the rules every chat reads

## The idea

`CLAUDE.md` sits in your project folder, and Claude Code loads it into every chat automatically. It's the place for rules that should **always** apply in this project:

- **Start of every chat:** pull the latest code, check that Studio and Rojo are ready, then say in one line what you still need to do.
- **Before each task:** recommend a model and effort, and search the notes for past work.
- **How to work:** build only what I ask, keep it simple, and be honest about what was actually checked.
- **Code facts:** where scripts live, how they get into Studio, and the checks to run.
- **After each task:** write a task log, update the notes, commit and push, and remind me to save in Studio.

## Why

Without it you'd repeat the same instructions every chat, forget half of them, and get a different Claude each time. Your partner's Claude reads the same file, so you both get the same behavior.

**Keep it short.** Everything in it costs context in every chat. Put rules here and details in notes. Ours is about 60 lines.

## Good rules vs bad rules

- Bad: "Be careful with the code." (Claude can't act on that.)
- Good: "Run `selene src` and `stylua --check src` on changed code."
- Bad: "Don't do too much."
- Good: "Build only what I ask. No extra features, redesigns or polish I didn't ask for."

The best rules come from mistakes. When something goes wrong twice, write the rule that would have prevented it.

## From Battle for Pets

Our real CLAUDE.md, with notes on why each line exists, is in `examples/battle-for-pets-CLAUDE.md`. Some lines and the problems they prevent:

- "Studio session IDs change every time Studio restarts, so never reuse an old one." Otherwise Claude talks to a Studio window that's gone.
- "`map-scripts/` holds old one-time map-building passes. Never sync or rerun them." Rerunning one would rebuild the map over our hand-made changes.
- "Only make the changes the user asks for." It came from Claude repeatedly adding decorations and "improvements" we then had to undo.

## Exercise

Show them their CLAUDE.md (or `templates/CLAUDE.md` if they don't have one) and ask: "What's one thing you've told Claude three times or more?" Help them turn it into one clear rule and add it, with their OK.

## Apply it now

No CLAUDE.md yet → suggest `setup`. They already have one → suggest `checkup`.
