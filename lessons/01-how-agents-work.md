# Lesson 1: How an agent works, and why Claude forgets

## The idea

A chatbot reads your message and writes text back. An **agent** is the same model plus **tools** and a **loop**:

1. Claude reads everything in its **context**, the text it can see right now: its instructions, your `CLAUDE.md`, its memory index, your message and every tool result so far.
2. It decides the next step: answer you, or call a **tool** (read a file, run a command, edit a script, take a Studio screenshot).
3. Claude Code runs the tool and adds the result to the context.
4. Back to step 1, until the task is done.

So when Claude "builds a pet", it's really looping: read the pet scripts → edit one → check Studio → see an error → fix it → check again.

**MCP** is how tools plug in. The Roblox Studio MCP is what lets Claude look inside Studio, run Luau, start a playtest and take screenshots.

**Subagents** are helper agents Claude can start with an empty context. They search or research something and hand back only a summary, which keeps the main chat's context clean.

## Why it matters: Claude forgets

Claude's "brain" (the model) **never changes from your chats**. You can't train it the way you'd train a pet. Every new chat starts knowing nothing about your game except what gets loaded into its context.

The context also has a size limit. Very long chats get summarized, details get lost, and every message costs more because Claude rereads the whole chat each time.

So "training Claude" really means **writing files it reads**:

| Layer | What it's for | Loaded |
| --- | --- | --- |
| `CLAUDE.md` | Rules for the whole project | Every chat, automatically |
| Memory | Your taste and the lessons learned | The index every chat, full memories when relevant |
| Notes and task logs | What the game is, what's built, what you approved or turned down | When Claude searches them before a task |
| Skills | Step-by-step guides for routines | When you type the command, or the task matches |

## From Battle for Pets

We have about 60 memories, 65 topic notes and over 400 task logs. A brand-new chat reads CLAUDE.md, pulls the latest code, finds the notes for the task and knows what we turned down three weeks ago, without us explaining anything. That is the whole trick.

## Exercise

Ask the student: "You spent an hour in a chat teaching Claude that you hate cute chibi pets. Tomorrow you open a new chat and ask for a new pet. What happens, and what should you have done?"

Good answer: the new chat doesn't know, so it may make a chibi pet again. They should have saved it, as a memory ("remember that my pets are never chibi, because...") or in their notes or CLAUDE.md.

## Apply it now

If they haven't run `setup`, suggest it next: that's where these files get made.
