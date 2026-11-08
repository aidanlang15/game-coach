# World building: maps, areas, gates and lighting

How we built the maps in our games: the hand-built hub and first area and the generated zones in Battle for Pets, plus the Axel track and hub, the +1 Mecha Cyborg world and the +1 TikTok Fame studs. Everything here comes from Battle for Pets unless it says otherwise.

## What we built

**Battle for Pets, the starting area (hand-built, in Studio):**

- **A pen hub.** Every player owns a pen where their pets live. The hub changed shape three times:
  - first, eight plots in a grid with gates facing inward;
  - then six pens (three down each side);
  - finally a **ring**: six pens facing a round tan plaza, the stations (Fusion, Gear, Crafting and so on) in a horseshoe on the plaza, the spawn on a medallion in the middle, and a ring wall around it all with one wide opening toward the first area.
- **Clover Meadow, the first area.** A broad lawn about 424 studs wide and 630 long, running in the -Z direction so "forward" always means -Z. It has encounter lawns, a berry grove, a brook with a bridge, a lily pond, a boss clearing and one AI-generated landmark tree. We wrote down approximate coordinates for each feature so later edits could target them.
- **A floor system.** The first floors were big polygon faces. They flickered where they overlapped, and the studs pointed in different directions on neighbouring pieces. The fix was thousands of small axis-aligned rectangles on a shared **one-stud grid** with an **eight-stud checker pattern**. Flat floors show studs; water and special motifs stay smooth. It cost a lot of parts, but it never flickered and the studs always lined up.
- **Camera and lighting presets.** We tested several camera heights and light moods and kept them as named presets, so "go back to the old one" is one setting, not a rebuild.
- **Scene folders.** The hand-built map lives under one model (`BattleForPetsMap`) with folders for the hub, the zones, the floor tiles, invisible gameplay markers and borders. A second folder in `ServerStorage` holds reusable templates and **backups** of earlier map states.

**Battle for Pets, zones 2 to 9 (generated):**

- Each area is its own **floating island**, 448 by 600 studs, in a straight line toward -Z, joined by 48-stud bridges. One landmark per area makes it readable from far away:

| Area | Landmark |
| --- | --- |
| Seashell Coast | giant spiral conch on a rocky point |
| Sunstone Desert | sandstone arch with a smiling sun |
| Toadstool Grove | grand toadstool beside a fairy ring |
| Frostpeak Valley | frozen falls off a tall peak |
| Crystal Hollows | glowing split geode over the boss floor |
| Ember Highlands | volcano with a lava flow, two lava rivers with one bridge each |
| Cloud Gardens | windmill on the top of four floating terraces |
| Starfall Frontier | giant ringed planet behind the boss arena |

- Every island has three **habitats** (invisible marker areas where wild pets spawn), a boss arena, a boss material area, an exit gate at x = 0, and tall invisible **border walls** (200 studs) so nobody can climb or jump out.
- Later we added a **Halloween area** (three tiers far down the line, reached only through a portal in the hub) and **Starfall Frontier** (zone 9). Both used the same generator.

**Area gates (locked areas):**

- Each gate between two areas opens **per player** after that player beats the boss before it. Areas that are not built yet show "COMING SOON" for everyone.
- The client puts an invisible wall across each gate that is locked for that player, with a sign: LOCKED ("Defeat <boss> to enter <area>!") or COMING SOON. The **server checks too**: a player found past a locked gate is put back in front of it with the message.
- Beating the boss opens it at once and the wall and sign vanish. An admin button can unlock all areas for testing.

**Area looks (lighting and air):**

- Every area has its own **look**: sky, light, haze, clouds, bloom, sun rays, a colour grade and one signature particle effect (desert dust, grove spores, cave glints, fireflies, star glints). Looks blend over about 1.5 seconds as you cross between areas, and the place's own values come back exactly when no look applies.
- The home area keeps the place's own warm light. Neighbours contrast: bright midday coast, hot sandy desert, lavender dusk grove, real night in the frost valley. Only one area is real night so night stays special.
- Boss cutscenes can cover the area's light, but only through the look system (see the rules).

**Event zones:**

- **Halloween (Hallowvale):** three tiers (a small pumpkin patch for new players, a foggy wood, a manor with a boss arena), each darker and more purple. Each tier gate ends in a ring where a **warden** fight happens at a soul altar. A portal frame stands in the hub only while the event runs (it is stored out of the map the rest of the time).
- **Starfall Frontier:** periwinkle star lawns, a gold path, glowing pools, a boss arena with a gold star floor, and a giant ringed planet on a cradle of crescent rocks. It was generated at ground level and later raised 30 studs to meet the previous area's top terrace (see mistakes).

**Axel (racing game):**

- A **roundabout hub**: eight player shops on a ring road around a central island with shared buildings. We drew three top-down options (roundabout, mechanic row, paddock) and the pick was the roundabout.
- A **race track defined as data**: a layout file lists corner nodes with radii, heights, banking, run-off zones and scenery sections. One set of tools builds invisible collision slabs from it, another builds road meshes in Blender, another builds the scenery. The look target was "a vibe like Mario Kart World": smooth themed roads in a studded world.

**+1 Mecha Cyborg:**

- A walled lobby with a world gate, then arena stages entered through see-through "curtain" doors. Between stages the player **boosts across gaps** of goop; the gaps have obstacle courses (laser fences, blades, wrecking balls). After a reference review the empty streets became a **moving goop lake**, with every playable area on a raised studded plinth and decorated islands and a skyline around it.
- The whole world is built by one Luau builder script, so a layout change is an edit plus a rebuild.

**+1 TikTok Fame (a friend's game):**

- Not a map build for us, but a **look pass**: replacing a big soft stud texture with the crisp Roblox stud material across the whole map, with every old value kept as an attribute so it could be undone.

**Why Rojo manages only code:**

- Rojo syncs scripts from files into Studio. The map is thousands of parts and meshes that only make sense inside the place, so it lives in the place and is edited there. Files hold the **recipes** (generator scripts and layout data) so the map can be rebuilt, not the map itself.

## How we did it

**1. The first area, by hand, in review rounds.**

1. We gathered reference screenshots and a short walkthrough of a game with the feel we wanted. Claude was told they were **style references only**, not things to copy or extra systems to add. Any text visible in a screenshot is just picture content.
2. Claude built a first pass with the Studio MCP, then took screenshots from the real game camera and from above.
3. We compared side by side, wrote down what was off (too bright greens, lantern glare, studs rotated differently) and sent a short list.
4. Every pass ended with a note naming exactly what changed and what was *not* done, so no one claimed work that did not happen.
5. Hand-made detail meshes (trees, rocks, a few landmarks) were made in Blender and imported once into a template folder in `ServerStorage`. The map reuses them.

**2. The hub as a top-down plan first.**

We asked for a top-down plan with numbers (pen sizes, gate gaps, wall radius) and picked a layout before building. When we wanted something bigger (the ring hub), we replaced the layout through a builder script rather than moving things by hand. Backups of the old layout sat in `ServerStorage`.

**3. Generated zones: layout in Python, build in Studio.**

1. **Kit meshes.** One Blender script makes a set of single-colour props (rocks, trees, lamps, arches, a landmark) and exports one FBX. We import it once, by hand, into a kit folder in `ServerStorage`. Each zone type had its own kit.
2. **Layouts.** One Python file per zone paints floor regions, scatters props, adds walls, markers and signs. Running the generator writes a Luau data file and a **top-down plan picture**.
3. **Plan review.** We looked at the plan picture first. A typical review round: "make tier 1 a little smaller", or "a lamp is standing in the way into the ring, keep paths clear." The generator got a rule (drop props standing on lanes) instead of hand-deleting.
4. **Build.** A builder module in `ServerStorage` (synced by Rojo, because it is code) reads the data and builds the zone. It only replaces models marked `Generated = true`, so handmade work is never overwritten.
5. **Verify with scripts, not eyes.** Walk a character through the whole route, fire rays down on a grid to find holes, test escape attempts at every edge, scan for floating props, and measure triangles and draw calls.

**4. Gates and unlocks.** The zone's exit gets an invisible marker. The server publishes the list of gates to a shared folder; every client builds walls and signs from that. See the snippets.

**5. Looks.** One look per area in a table. Claude applied values in Play, took screenshots, and toned things down (the desert and coast were too bright and washed out the sand at first). We previewed in Edit mode by applying values for one screenshot and restoring them.

**6. Event zones.** The plan note came first (three tiers, gates inside the area, where the portal stands). The layout session had no gameplay at all. A later session added portal teleport, souls, wardens and gates. Decorators (my partner, in Team Create) work in a **separate decor model**, because a rebuild replaces the generated zone model.

**How we split work into sessions:** kit meshes and layout (no Studio needed, can run while someone else uses Studio), then one Studio session to import and build, then gameplay in another session. Studio is shared and only one session should use it at a time.

## Rules we follow now

- **Code in Rojo, map in the place.** The map is edited in Studio, saved with Ctrl+S. Why: syncing a huge map from files is slow and fragile, and Team Create already shares the map between both of us.
- **Generated content is rebuilt, never hand-edited.** Edit the Python layout and rebuild. Why: a hand fix is lost on the next rebuild. The builder only touches models marked `Generated = true`, and refuses to replace anything else.
- **Handmade things live outside generated models.** A separate decor model next to the zone. Why: same reason.
- **Old map-building scripts never rerun on the current place.** We keep one-time scripts in a folder labeled as history. Why: they assume an older hierarchy and would damage newer work.
- **Make a backup before any big map change.** A model in `ServerStorage` named for the change and date, kept out of Workspace. Why: it is the only undo for a rebuild.
- **Never delete hidden objects in bulk.** Old floors may be invisible but still hold collision or be referenced. Why: we once removed one and opened holes.
- **Keep every floor on the same grid and every flat part at least a few tenths of a stud apart from coplanar neighbours.** Why: overlapping faces at the same height flicker (we saw it at 0.02 to 0.06 studs). Run a scan for it after every build.
- **Studs belong on windows, floors and walls, not on art banners.** Use Roblox's real stud material, not a home-made texture. Why: the user said our drawn stud tile "looked very bad" next to the real stud texture; and sales banners with studs read as flat cards.
- **Gates are checked on the server per player.** The client wall is only for feel. Why: a shared gate that opens for everyone after one player wins is a bug, and a client-only wall can be bypassed.
- **Walk across every gate after opening it.** Why: see the Starfall height mistake below.
- **Looks go through the look system.** A cutscene must never write lighting directly in an area that has a look. Why: a fight that started straight from a teleport begins before the area's look switches on, and the look then saved the cutscene's dusk as "the place's own light" and "restored" it later.
- **Keep a path clear.** After a lamp stood in the way into a boss ring, the generator got a rule to drop props on lanes and entries. Why: players walk the route constantly.
- **Reference means style, not copy.** We note where a reference came from and what we took (timing, feel) and what we did not.
- **Zone layouts get a top-down picture review before any Studio build.** Why: a plan costs a minute; a rebuild costs a session.
- **Do the map work with a model that matches the risk.** We used a high-effort setting for map building and layouts and went higher for gates, saving and security.
- **Team Create map rules:** two people can edit the map at once as long as they are not in the same model or area; never run two code syncs against the same place at once; playtests run locally so either person can test any time. Why: both of us once found scripts silently replaced by the other's sync.

## Mistakes and gotchas

- **A whole map was in the place twice.** Two identical map models were stacked (probably an accidental copy-paste), which doubled the part count. Fix: find by comparing counts and positions, delete the selected copy as one undo step. Prevention: a scan for duplicate top-level models before every publish.
- **A zone built at the wrong height.** Starfall Frontier was generated at ground level, but the area before it climbs to a top terrace 30 studs up. The bridge floor was 30 studs lower than the gate, and an invisible edge wall still crossed the gate opening (it had been made when the gate was closed). Nobody could walk through. Tests teleported into the area, so they never caught it. Fix: a `lift` option on the layout that adds a height to everything at export, and a gap in the previous zone's edge wall. Lesson: **walk through every gate on foot**; a teleport hides a height mismatch and a blocking wall.
- **Rojo crashes when files vanish.** Editing or deleting files under the synced folder with some tools (sed -i, deleting a folder, even an editor's save) crashed Rojo, and Studio silently kept old code. Fix: edit in place, check that the sync port still answers after edits, and check a script's Source in Studio. Delete synced folders only after stopping Rojo.
- **Big temporary scripts.** We synced a temporary install script into Studio, ran it, and deleted the folder; the delete crashed the sync. Keep temporary modules as a single file in an existing folder.
- **Other sessions' Rojo overwrites yours.** Studio's plugin talks to one Rojo at a time, and that one pushes its own copies of every file. Check that Studio's script has your code before testing.
- **Imported leftovers shipped.** An Import 3D of a mount (100x scale) landed in Workspace in the middle of a check and went live as a giant grey object. Fix: move it to `ServerStorage`. Rule: re-scan Workspace as the very last step before publishing.
- **Kit imports came in at odd scales.** The importer fit a big landmark under a size cap, so the whole kit arrived at about 17x. Check each mesh against its manifest size and uniformly rescale, rather than guessing.
- **Axes differ.** Blender's axes map to Studio mesh axes differently. We checked with a small axis-probe mesh once and applied a half-turn in the builder so a model's front faces +Z at yaw 0.
- **Slabs can't follow a twisting road.** On the race track, flat slabs every 4 studs leave steps where banking changes, and the car's body catches on them. Fix: cut twisting slabs into strips and measure the collision with rays along the whole lap (worst step 0.28 studs after the fix).
- **A whole track model got dragged.** A track moved about 22 studs up and 47 north between a rebuild and an install, probably while pieces were selected. Fix: rebuild, and make every track part `Locked`.
- **Textures that render blank.** A SurfaceAppearance made while its image was still processing stays blank; recreate it later. Imported meshes with UVs far from zero can render one flat colour. Uploaded images can take around 40 minutes to show.
- **Studs on meshes need no UVs.** A stud material variant sizes itself in the world. Textures that must follow a road (planks, tiles) go on a SurfaceAppearance.
- **Parts max out at 2,048 studs a side.** A big sea is a grid of tiles.
- **A skyline too close feels like a canyon.** In +1 Mecha Cyborg, 150 studs out with tall towers read as boxed in; 205 out and shorter worked.
- **The builder is not always the place.** A stage had been moved by hand and nothing recorded why. Before a full rebuild, compare the place's positions with the builder's.
- **Overlapping plinths flicker.** Platforms that touch must meet face to face, not overlap.
- **Boundaries need a second look.** The meadow's back edge beside the ring wall had no boundary and players could jump off. Check island edges from each side and where two walls meet.
- **Streaming hides far markers.** With StreamingEnabled, far gameplay markers do not exist on the client. A guide arrow to another area needs positions from the server.
- **Saving the place is not publishing.** In Team Create, Ctrl+S makes a version but does not publish. Check "published only" in version history.

## Code snippets

**1. A zone layout in Python (core idea: paint, scatter, mark, export).** Generic names; one file per zone.

```python
# zone_example.py: layouts work in (x, v) with v = distance down the island; export maps v to world Z.
from .core import ZonePlan, Style, Scatter, path, blob, circle, rect

LAWN = Style('Lawn', (132, 142, 226), 0.04)           # name, colour, floor height
TRAIL = Style('Trail', (250, 212, 150), 0.28)
POOL = Style('Pool', (70, 130, 226), -0.06, studs=False, material='Neon', checker=False)

def build():
    # lift raises everything at export, so layouts can be written from zero
    plan = ZonePlan(9, 'Zone09_Example', 'Example Area', z_start=-5488, lift=30.0)
    trail = path([(0, -4), (0, 120), (30, 240), (0, 360), (0, 604)], width=20)
    habitat = blob(-96, 110, 40, 34, seed=1)           # a wild-pet area

    plan.paint(plan.island_rect(), LAWN)               # later paints cover earlier ones
    plan.paint(trail, TRAIL)

    scatter = Scatter(seed=7)
    scatter.reserve(-96, 110, 44)                      # keep props out of the habitat
    plan.marker('ExamplePets_Meadow', -96, 110, (80, 1, 68), 'habitat')
    plan.marker('Zone09_ExitSocket', 0, 598, (42, 1, 8), 'gate')   # the gate opening
    plan.wall(-224, 0, -224, 600, height=200)          # tall invisible border, no climbing
    return plan                                        # build.py exports data + a top-down plan picture
```

**2. The Studio builder replaces only generated models (Luau).** This is what keeps handmade work safe.

```lua
return function(data: any)
	local map = workspace:FindFirstChild("WorldMap")
	assert(map, "map not found")
	local zones = map:FindFirstChild("Zones")
	local old = zones:FindFirstChild(data.key)
	if old then
		-- never overwrite something a person made by hand
		assert(old:GetAttribute("Generated") == true, "Refusing to replace a zone that was not generated: " .. data.key)
		old:Destroy()
	end
	local zone = Instance.new("Model")
	zone.Name = data.key
	zone:SetAttribute("Generated", true)
	buildFloor(data, zone)   -- merged rectangles, one stud grid
	buildProps(data, zone)   -- kit meshes cloned from ServerStorage
	buildWalls(data, zone)
	zone.Parent = zones
	buildMarkers(data)       -- habitat and gate markers go in a shared markers folder
end
-- run from the command bar, cloning the builder first so fresh synced edits are used:
-- local b = game.ServerStorage.MapBuilder:Clone() b.Parent = game.ServerStorage
-- require(b.BuildZone)(require(b.Data.Zone02_Example)) b:Destroy()
```

**3. A per-player gate on the client (the wall is only for feel).**

```lua
local function isOpen(entry: Instance): boolean
	if player:GetAttribute("UnlockAll") == true then return true end  -- admin test switch
	if entry:GetAttribute("ComingSoon") == true then return false end
	local boss = entry:GetAttribute("Boss")
	return type(boss) ~= "string" or player:GetAttribute("Beat_" .. boss) == true
end

local function build(entry: Instance)
	local wall = Instance.new("Part")
	wall.Anchored, wall.CanCollide, wall.CanQuery = true, true, false
	wall.Transparency = 1                      -- a part only THIS client has
	wall.Size = Vector3.new(entry:GetAttribute("Width"), 220, entry:GetAttribute("Depth"))
	wall.CFrame = CFrame.new(entry:GetAttribute("Centre") + Vector3.new(0, 108, 0))
	wall.Parent = gateFolder
end
-- rebuild when the player's "Beat_<boss>" attribute changes: open gate = wall destroyed
```

**4. The same gate on the server (the real check).**

```lua
-- every half second: anyone found past a gate that is locked for them goes back in front of it
local info = firstLocked(player)                 -- the nearest locked gate on the route
if info and root.Position.Z < info.centre.Z - info.depth / 2 - PAST then
	local spot = info.centre + Vector3.new(0, 0, info.depth / 2 + BACK_OFF)
	local at = Vector3.new(spot.X, floorAt(spot) + 3, spot.Z)   -- raycast down for the floor, stand on it
	character:PivotTo(CFrame.lookAt(at, at + Vector3.new(0, 0, 1)))
	notice:FireClient(player, "Defeat the boss to enter this area!")
end
```

**5. An area look entry (a table, one per area).**

```lua
AreaEnvironment.Looks.Grove = {
	lighting   = { ClockTime = 18.1, Brightness = 1.9, Ambient = RGB(120, 110, 150) },  -- misty lavender dusk
	atmosphere = { Density = 0.32, Offset = 0.2, Color = RGB(190, 170, 230), Haze = 1.4 },
	clouds     = { Cover = 0.4, Density = 0.35 },
	bloom      = { Intensity = 0.5, Threshold = 1.6 },   -- glowing mushrooms bloom
	particles  = "Spores",                               -- one signature effect per area
	keepInBattle = true,                                 -- battles leave this look alone
}
-- cutscenes must go through AreaEnvironment.cover(values, seconds), never write Lighting directly.
```

## Prompts that worked

**A layout session (adapted from our zone work).**

```text
First read the notes for the map pipeline. Then lay out the next area, Desert Mesas, as a floating island 448 x 600 studs, joined to the previous one by a 48-stud bridge.

What I want: a readable main trail from the bridge to the exit gate, three wild-pet habitats (one lawn each, about 70 x 60 studs), a boss arena before the gate, one landmark you can see from the entry (a sandstone arch with a smiling sun), and tall invisible border walls so nobody can climb out. Style: bright, clean, studded floors like our first area.

Do the layout in the Python generator only (no Studio yet) and give me the top-down plan picture. Do not edit hand-built map pieces. Keep props off the main trail and the habitats. I will pick changes from the plan before you build anything in Studio.
```

**A review round (real shape).**

```text
Here is the plan. Tier 1 is too big for new players: make it about 20% smaller. A lamp stands on the way into the boss ring: keep every path clear, and fix it in the generator rather than deleting props by hand. Everything else is approved. Rebuild the data and show me the new plan.
```

**A map-quality complaint with a reference (Mecha Cyborg).**

```text
Here is a screenshot of another game: stages on raised studded platforms over a textured green goop lake. Ours looks like a blank street around the stage. Make ours feel like that: one lake of moving goop, everything playable on a raised plinth, islands around it. Keep the studs. Show me a screenshot of the lobby before you do the other stages.
```

## Do it in your game

1. **Pick a direction and a unit.** Decide which way the world grows (we always used -Z) and the size of one area. Write the numbers in a map note before building anything.
2. **Hand-build only the first area.** Get reference screenshots, ask for a style match (not a copy), and review from the real game camera. Save a backup of the map state before each big change.
3. **Write the recipe, not just the map.** From area two onward, ask Claude for a layout generator (a file per area plus a builder script) that only replaces models it created. Review the top-down plan picture before building.
4. **Add gates early.** Even before bosses exist, give each exit an invisible marker. Have the server decide, per player, who may pass, and have the client draw a wall and sign.
5. **Give each area a look.** One table of lighting, haze and one signature particle effect per area, blended over about 1.5 seconds. Keep one area as "home" so the others contrast.
6. **Test by walking.** Walk a character through every gate and bridge, drop rays on a grid to find holes, and try to escape at every edge. Scan Workspace for leftovers before every publish.

Starter prompt for a new chat:

```text
First read ~/.claude/skills/game-coach/playbook/world-building.md.

I am building a Roblox game called [fill in: your game]. I want [fill in: a hub, a first area, or a set of areas] and the world should grow toward [fill in: a direction]. The look I want is [fill in: references or a mood]. The areas will be [fill in: how many, and what makes each one different].

Before you build anything: tell me which parts should be hand-built in Studio and which should be generated from a layout file, how gates and boundaries will work per player, and what you will back up first. Use Rojo for code only and keep the map in the place. Do not copy any reference exactly.

What questions do you have for me so I can help you implement this feature as best as possible?
```
