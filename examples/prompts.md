# Example prompts, before and after

1 and 5 come from Battle for Pets. 2-4 are examples written in the same style.

## 1. A small feature

> **Before:** add leaderboards
>
> **After:** Add a leaderboard of the 10 richest players in this server, shown on a board in the hub plaza and updated every minute. Don't change the HUD. Done = I can see the board in the plaza with my name and money on it during a playtest.

What changed: which leaderboard, where, how often, what to leave alone, and what done looks like.

## 2. A bug report (an example)

> **Before:** the shop is broken
>
> **After:** When I buy the Advanced capsule in the Gear shop, my money goes down but no capsule shows in my inventory. Expected: one Advanced capsule in the hotbar. Output: `ServerScriptService.Services.Shop:88: attempt to index nil with 'capsules'`. It works for the Starter capsule. Find the cause before changing anything.

What changed: what I did, what I expected, what happened instead, the exact error, what still works, and "find the cause first" so Claude doesn't guess at a fix.

## 3. A new system: design questions first

> **Before:** make a trading system
>
> **After:** I want players to be able to trade pets. Before building anything, read notes/Pets.md and notes/Wild Pets, Battles and Saving.md, then ask me 3-5 design questions with options and a default for each: how a trade starts, what can be traded, how to stop scams, and anything else that matters. The server must check every trade. Don't build anything until I've answered.

What changed: it points at the notes, asks for questions before code, names the risky part (scams, server checks) and holds off building.

## 4. Picking a look: options side by side

> **Before:** make the boss arena look cooler
>
> **After:** Make 3 lighting versions for the Frostpeak boss arena (cold blue night, stormy, bright icy day) and show me a screenshot of each from the battle camera, side by side. Don't change the arena's models. I'll pick one.

## 5. A prompt for another chat (a session prompt)

Trimmed from one of our real ones. Big features get split into sessions, and each one gets a prompt that stands alone, because the new chat knows nothing else.

```text
Session 9 of the Kraken event: the Kraken Hatchling pet and the Abyssal mutation. Sessions 1-8 are done and pushed. Do only session 9.

Settings: Opus, xhigh (saved pets and mutation rules).

Read first:
- notes/Kraken Event Plan.md: the Rewards section and the session table.
- notes/Task Log/2026-10-12 Kraken rewards, audio and release.md: what's built and what's open.
- notes/Pets.md: the catalog, rarities, sizes, art style and mutations.

Step 1, before building anything: a plan for my OK.
- The Hatchling: three looks (a baby kraken in the big one's colors), its size next to a 5-stud player, its moves and stats.
- Where it must stay out of: wild spawns, the Index completion, starter packs.
- How a won Hatchling is delivered, exactly once. If a new saved field is needed, tell me first.

Rules: build only session 9. The server decides who gets a pet. Back up a test save before changing saving.

Check: win the event in a playtest, get the Hatchling once, rejoin and it's still there.

What questions do you have for me so I can help you implement this feature as best as possible?
```

What makes it work: the scope (only session 9), the settings, exactly which notes to read, a plan before building, the risky parts called out (saving, delivered exactly once), how to check it, and the closing question so the new chat asks before it guesses.
