# Lesson 10: Skills, turning a routine into a command

## The idea

A **skill** is a folder with a `SKILL.md` file: step-by-step instructions for one kind of job. You run it by typing `/skill-name`, and Claude can also load it on its own when a task matches the skill's description. This coach is a skill.

- **Project skills** live in your project: `.claude/skills/<name>/SKILL.md`. They're shared with your partner through git.
- **Personal skills** live in your user folder: `~/.claude/skills/<name>/SKILL.md`. They work in every project.

A SKILL.md starts with a short header:

```
---
name: pet-import
description: Import a new pet model from Blender into Studio and set it up. Use when the user says they have a new pet model to import.
---
```

Then come the steps, the rules and the lessons learned. Extra files (examples, scripts) can sit next to it, and the skill tells Claude when to read them.

## Why

Some jobs repeat with the same steps every time: importing a pet, making a TikTok, releasing an update. Without a skill you re-explain the steps each time and Claude forgets one. With a skill it's one command, and every lesson learned gets written into it so the next run is better.

## When to make one

Make a skill when you've done the same kind of task **three times** and keep giving the same instructions. Not before, because skills for one-off jobs are wasted effort.

## From Battle for Pets

Our `/social-video` skill makes a TikTok from start to finish: it plans the script with us, films in Studio, cuts the video, adds captions and a voice. After the first TikToks' feedback ("open on the best moment", "punchy camera, not floaty") we wrote those lessons into the skill, so every video after that started from them.

## Exercise

Ask: "What do you ask Claude to do over and over?" Help them write the `name` and `description` lines for that skill. The description should say what it does and when to use it.

## Apply it now

If they have a real routine, offer to draft the skill with them. Keep it short (under 100 lines) and add to it as they learn.
