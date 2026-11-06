# Lesson 7: Design questions, options side by side, mocks

## The idea

Three moves that stop Claude building the wrong thing:

1. **Design questions first, for anything new.** "Before building, ask me 3-5 design questions, each with clear options and a default." You answer each one with a pick or "d" for the default, and only then does building start.
2. **Options side by side, for anything visual.** "Make 3 versions and show me screenshots side by side." Pick one and make it the default.
3. **Mock before building UI.** Ask for 2-4 mockups of a new screen over a real screenshot of your game (in Figma or just as images) before any UI code gets written.

## Why

Building is the expensive part. Changing an answer to a question costs one message, and rebuilding a finished system costs a whole session. And for looks, you often don't know what you want until you see options. "Make it look better" goes nowhere; "I like B's colors with A's layout" gets you there in one step.

## From Battle for Pets

- Battles, catching, wild pets and progression all started as question rounds. Each question had options and a default, and we answered most with "d".
- Pets, the camera and the lighting were each picked from versions shown side by side.
- New UI gets 2-4 Figma mocks over a real game screenshot first. We pick one, then Claude builds it.
- For big features, Claude writes a **plan** first, split into sessions (one chat each). Our Halloween update was planned as 13 sessions, each with its own prompt.

## Ask for the honest view

Some other cheap questions that save rework:

- "What's the simplest way to do this?"
- "What are the risks?"
- "Is this worth building?"
- "Explain how capture works and which files are involved." (Ask this before changing a system you don't understand.)

## Exercise

Ask the student for a feature they want next. Have them write the design-questions prompt for it, then review it (`reference/prompt-review.md`).

## Apply it now

Offer to add to their CLAUDE.md: "New systems: ask me 3-5 design questions first, each with options and a default. 'd' means use your default."
