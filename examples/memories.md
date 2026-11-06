# Example: Battle for Pets memories

Five real memories from our project, lightly trimmed. Each is one small file in Claude Code's memory folder, and a one-line pointer to it sits in `MEMORY.md`. Notice that every one says **what**, **why** and **how to apply it**. The why is what lets Claude handle the cases nobody wrote down.

---

```markdown
---
name: pet-art-style
description: Approved art style for new pet models (not chibi; sturdy, confident, "cool")
type: feedback
---

New pet models: faceted low-poly, near-natural animal proportions but sturdier than realistic (thicker legs, medium heads), smaller glossy eyes with a confident upper lid, no blush or cute accessories (bells, daisies), each pet with its own main color and a "cool" accent (bandana, crest, side streak). Normal pets are 3–5 studs tall. Legs are smooth tapered tubes, not blocky segments.

**Why:** the user rejected a chibi pass (big heads, stubby legs, huge eyes) as "too cute" and "looks much smaller", then approved this version and asked for it to be the default.

**How to apply:** build every new pet this way. Exception: the Shark keeps its chibi toothy grin, which the user liked.
```

---

```markdown
---
name: d-means-default
description: When I ask the user questions with defaults, "d" means use the proposed default
type: feedback
---

When I ask the user several questions and propose a default for each, an answer of "d" (alone or per question number) means "use the default you proposed".

**Why:** the user wants fast answers to question rounds without retyping my recommendations.

**How to apply:** always propose a clear default for each question, so "d" has something to point to.
```

---

```markdown
---
name: keep-systems-simple
description: User rejects new progression layers (e.g. pet star upgrades); prefers shop items that reuse current systems
type: feedback
---

The user turned down pet star upgrades because pet levels already exist and the game "should remain somewhat simple". They approved shop consumables instead (capsules, battle potions, training treats) and cosmetics.

**Why:** another stat layer is one more thing players must learn; the user wants a simple game.

**How to apply:** for economy and progression ideas, prefer things that plug into existing systems (levels, capsules, battles) over new upgrade layers.
```

---

```markdown
---
name: time-estimates
description: My time estimates run too high; give smaller, realistic numbers or none
type: feedback
---

When flagging how long a step will take, I overestimate. The user said so directly: "definitely not an hour, you always overestimate the time it takes to do things".

**Why:** inflated estimates make routine steps sound like big costs and push the user to skip work that should just be done.

**How to apply:** flag real costs by what they involve (uploads, many tool calls), not inflated minutes.
```

---

```markdown
---
name: prompts-end-with-questions
description: Prompts I write for other chats end with a question asking what that chat needs from the user
type: feedback
---

Every prompt I write for another chat ends with: "What questions do you have for me so I can help you implement this feature as best as possible?"

**Why:** the new chat raises open decisions before building instead of guessing.

**How to apply:** make it the last line inside the prompt's code block.
```

---

## What makes these good

- **One fact each.** Easy to find, easy to update or delete.
- **The user's own words** where possible ("too cute", "you always overestimate"). They carry the feeling as well as the rule.
- **Exceptions written down** (the Shark). Otherwise Claude "fixes" the one thing you liked.
- **A short description line.** Claude decides from it whether the memory is relevant to the task, so make it specific.
