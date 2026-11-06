# Example: a Battle for Pets task log

A real task log, lightly trimmed. A raw 3D import of the Halloween broom mount (grey, no colors, at the importer's giant scale) was left in Workspace when the game was published, so a giant broom stood in the live game. The fix was a careful check before publishing again, and the `Rejected / learned` line turned the mistake into a rule every later chat follows.

```markdown
---
date: 2026-10-30
tags: task-log
---

# Giant broom in the live game

The user found a giant grey Witch's Broom standing in the live game about 9 hours before the Halloween start. It was a raw Import 3D of the broom model: uncolored, at import scale, in Workspace when the last publish went out. That session deleted it after the publish, so Studio was already clean; the user picked "check and publish now" over waiting for the 6 pm release.

## Pre-publish check

- Git up to date, no uncommitted code, no other session running, Rojo listening.
- All 422 scripts in `src` match Studio.
- 13 new modules have an identical second copy in Studio. Harmless now, but a later edit would only reach one copy. A Rojo Disconnect and Connect deletes the extras.
- Workspace holds only Camera, Terrain, the map and the pet showcase: no bones, no uncolored meshes, no test models.
- Studio Play: the event is hidden before its start time, as it should be.

## What ships with this publish

Everything since the last publish, all hidden until Friday's start: the Pumpkin King fight and cutscene, the pet attack effects, the Robux items and the spooky night.

## Rejected / learned

- Re-scan Workspace (bones, uncolored meshes, unknown top-level models) as the very last step before telling the user to publish. Other sessions import models in the middle of a check, and the first pass was clean.
```

## What makes it good

- **What happened and why**, in a few lines, with no blame.
- **What was checked**, with real numbers (422 scripts), so the next chat knows what's been verified.
- **What's left** (the duplicate modules) is written down, not forgotten.
- **`Rejected / learned`** gives one clear rule with its reason. The next time someone says "publish", Claude searches the logs, finds it, and re-scans Workspace last.
