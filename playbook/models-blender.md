# 3D models with Blender, Python and Claude

How we made pets, mounts, bosses, a car and mech parts in Blender (driven by Python scripts Claude writes and runs headless) and got them into Roblox Studio. From Battle for Pets, Axel and +1 Mecha Cyborg.

## What we built

**Battle for Pets** has dozens of pet models, about ten bosses and about a dozen mounts. Almost every one comes from a Python script, not from hand modelling in Blender's window.

- **Pets.** One builder script per game area (`tools/build_area_pets.py`, nearly 10,000 lines by now). Each pet is a Python function that stacks shapes: ellipsoid "blobs" for body masses, tapered tubes for legs and tails, feathers, scales, glowing cracks, floating motes. Areas so far: a meadow, a coast, a desert, a mushroom grove, a frozen valley, a crystal cave, a volcano, sky gardens, a Halloween event area and a space area. Each area has 8-10 pets from Common to Secret.
- **Pet detail levels.** Common and Uncommon about 5k-22k triangles. Legendary and Mythic up to about 38k. Exotic and Divine up to about 48k. A pet is 9-26 MeshParts, and one MeshPart is one colour key (one colour, one material). Extra detail adds triangles, not draw calls.
- **Pet remakes.** Five early pets (a unicorn, a griffin, a phoenix, an owl, a sea turtle) did not stand out at their rarity, so we rebuilt them. The Mythic unicorn went to 37k triangles: pearl body, gold spiral horn with a glowing groove, floating four-leaf clovers, a flowing mane in three colours.
- **Upgrade pass.** 18 fire and cloud pets were modelled twice. The first pass was 2.6k-14.5k triangles with column legs and tube bodies. We wrote an audit of every defect, then rebuilt each to the standard of three approved reference pets.
- **Mounts.** About a dozen rideable mounts: a bison, a sabre-tooth, a nautilus, a roadrunner, a nine-tail fox, a crystal horse, a lava drake, a feathered sky serpent, plus object mounts (a witch's broom, a flying saucer, a blazing comet). Built in `tools/build_mounts.py`, with saddle, stirrups and a seat point for the rider.
- **Bosses.** Colossal one-off models: a 33-stud crocodile pharaoh, a toad king, a sea Kraken (186 studs across, 58k triangles in 30 parts), a lamp djinn, a cloud monkey king, a pumpkin king, a ringed-planet boss.
- **Axel (car game).** A voxel R34-style coupe with heavy cel outlines, 5 engines, 3 turbos, 14 body-kit pieces and 6 rim styles, all matched to AI-made concept art.
- **+1 Mecha Cyborg.** A pilot-seat mech made of 78 rigid pieces (about 41k triangles), then heavier "Pacific Rim" mechs, hover-bots and a crab boss. Each has four colour slots (Paint, Metal, Trim, Glow) so a reskin is only a recolour.

## How we did it

### The loop for one batch of models

1. **Concepts first, in words or quick blockouts.** Claude writes 2-3 options per slot (or builds ugly coloured blockouts and puts them on one labelled sheet). The user picks. Halloween pets: round 1 was rejected as "reskins of pets we already have", so round 2 was monsters and cryptids that were not a species already in the game.
2. **Check past work before building.** Claude reads one or two approved builders of the same rarity and the list of past change requests. This is now a memory rule (see Rules).
3. **Write the builder.** Claude writes one Python function per pet in a shared builder script. Helper functions (`blob`, `smooth_limb`, `feather`, `cool_eye`, `crack`, ...) live at the top and get reused. New shapes get added to the shared kit when a pet needs one.
4. **Run Blender headless.** `blender --background --python tools/build_area_pets.py -- <area> [PetKey ...]`. The script builds every pet, prints triangles, mesh count and size, renders review images and writes one FBX plus a JSON manifest.
5. **Review renders.** Per pet: front, side, back, top, a head close-up, and a lineup beside a 5-stud player figure. Plus a sheet with the new pet beside an approved pet of the same rarity.
6. **Review rounds with the user.** They give notes in chat ("the wings need work", "make the mouth look better"). Claude fixes all notes in one pass and re-renders. Usually 1-3 rounds.
7. **Import 3D by the user.** Studio's File > Import 3D is a manual step. Claude gives the exact FBX path to paste.
8. **A generated Luau setup script** (Studio MCP `execute_luau`) fixes up the import from the manifest: sizes, names, colours, materials, attributes, pivot. See the snippets.
9. **Checks in Studio.** Sizes match the manifest. Eyes face -Z. Colours look right in the area's real lighting (screenshot it). Then catalog, stats, animation, mutation hooks and so on are separate tasks.

### Why a manifest

Studio's importer drops material colours and renames parts. So the Blender script writes `manifest.json` with colour, material, transparency, light, size and model attributes for every part. The Studio fix-up script reads it. To change a colour later, you edit the manifest and rerun the script. No re-import.

### Session split

Big model jobs were split into sessions with a hand-over note at the end of each one:

- Models only (Blender, no Studio, no game code). Ends with renders and an FBX.
- Rigs, animation clips, Studio install (see the rigging guide).
- Effects and polish.

For six mounts, the user asked for all six built together in one session, shown together, one review round for all, then the same for rigs, then the same for effects. One round per stage saved their usage.

### Reviewing honestly against a reference

In Axel the user supplied a concept image of the car. Our first part-built car was low, boxy and flat. The user asked: "does the car model look anything like the reference photo? Genuinely look at both". After that Claude measured the concept first (belt height, cabin size, wheel size), rendered the same views, and listed the real differences before showing anything. The rejected car had its belt line at 2.85 studs against the concept's 3.9.

For pets the reference is an approved pet of the same rarity. Claude renders them side by side and fixes the gap before the user sees the model.

## Rules we follow now

Each rule exists because something was rejected.

- **Default pet look: sturdy, not chibi.** Faceted low-poly, near-natural proportions but thicker legs and medium heads, small glossy eyes with a confident upper lid, no blush, no bells, no cute accessories. Each pet gets its own main colour. Reason: a chibi pass (big heads, stubby legs, huge eyes) was rejected as "too cute" and "looks much smaller". The one exception is a shark whose chibi toothy grin the user liked.
- **Legs are smooth tapered tubes that bend through the joints.** Reason: stacked limb segments were called "blocky".
- **Every pet in an area gets its own main colour, not only its own gimmick.** Reason: a navy-black Mythic bear and a navy-black Divine beast in one area read as the same pet.
- **Study the approved builders before the first pass.** Reason: Halloween round 2 first attempts were 4k-11k triangles with one gimmick each, against 30k-48k and many layers on the approved top-rarity pets. The user called one dragon "bland and undetailed, the wings need work" and another "lacking basically everything".
- **Mythic and above need layers.** Anatomy masses, then surface detail (scales, plates, runes, cracks), then flowing glass or glow accents, then floating effects (halo, orbiting motes, ribbons). Wings are layered shaped membranes with ribs and edge detail, never one flat polygon.
- **A theme runs over the whole body.** "Patchwork" with one sewn-on arm was rejected as not very patchwork.
- **A biting mouth needs real jaws.** Upper and lower jaw, hinged open, a visible dark inside, fang rows on both, and the bitten thing clearly between them.
- **Pet size is by what the creature is relative to a ~5-stud player, never by rarity.** Bigger does not mean stronger in our game. Tiny creatures are scaled up though: the user found small Commons made areas look empty, and picked the larger of two options shown in a lineup next to a 5-stud marker.
- **A new pet is about 4 studs tall or more** unless it is a big showpiece.
- **Show size changes as a lineup next to a 5-stud marker** before applying them.
- **Object mounts: the rider sits ON the main object.** The first comet had a small head in front and the rider on a neck behind it. The user said "That does not look good" (it read as a mace on a stick) and asked for it bigger with the rider on the comet.
- **Surface detail sells a hero object.** A plain glass ball with a star stuck on was rejected. About 18 craters with raised rims, rubble and glowing cracks got it approved.
- **Colours are checked in game, not only in Blender.** Roblox bloom turns pale Neon white, so glows need deeper tints (cyan 70,185,255 beat pale cyan). The area's lighting changes base colours too: an oxblood boss (150,40,50) read bright red in the desert and (122,34,44) fixed it.
- **Check a model from every side before showing it.** A knee pad's rim looked like a loose blue loop from behind. A first crab boss read as a spider with a red ball on its back. Fix the silhouette (wide flat body, short thick legs, huge claws) from front and side first.
- **A first pass should survive a "does this look like the reference" check.** Compare, then show.
- **Keep Studio parts out of detailed models.** A part-built car (blocks with stud textures) was rejected as "not good at all". Boxes can't do curves. Use meshes. Parts only suit blocky subjects like a train.
- **One MeshPart per colour key.** Colours stay changeable from Studio, and draw calls stay low.
- **Transparent parts are the costly ones on phones.** At most 3-4 see-through colour keys per pet, one PointLight at most and only on parts away from the body (a light inside the body tints the whole model green).
- **Group-owned game, group-owned animations.** If your game belongs to a group, anything uploaded for it as an animation must have the group as creator (details in the rigging guide). Images uploaded by the MCP under a personal account still show in a group game.
- **Do a last Workspace scan before publishing.** Models imported mid-task get left in Workspace (once a giant broom shipped). Scan for leftovers, bones, uncoloured grey meshes and duplicate scripts as the very last step.

## Mistakes and gotchas

**Import 3D**

- **Scale.** Default import of an FBX written in centimetres comes in at 100x. Either scale every MeshPart by 0.01 in the fix-up script, or (better) export at 1 stud per unit (`global_scale=0.01` in the FBX export) and import at Scale Factor 1. We switched to 1 stud per unit and kept it.
- **2048-stud limit at 100x.** Any mesh part longer than 2048 studs at import scale (a pet part over ~20 studs) makes the importer shrink the whole import (our bosses came in at 0.889x). Rigged imports get hit worst: the meshes shrink but the bones do not, so the skin ends up on a skeleton that is too big. At 1 stud per unit the limit never triggers.
- **Split hides break mutations.** Our colour-mutation effects only treat a part named `Fur` as the body. When giants had their hide split across several meshes for the limit, a gold mutation gave a white body with a gold neck. At 1 stud per unit the hide is one `Fur` mesh again.
- **Names.** The importer adds Blender-style `.001` suffixes. The fix-up script strips them. Other code looks parts up by name, so names matter.
- **Rotation.** Blender meshes exported with axis forward -Z, up Y come in turned 180 degrees about Y in one project. Test with an asymmetric shape and turn them back in the install script.
- **Colours and materials.** The importer drops them. Apply from the manifest.
- **Import 3D sometimes inserts nothing.** Confirm the model landed in Workspace before running a setup script.
- **Wrong file imported.** The user once picked the old static FBX instead of the rigged one. Give the full path to paste, and have the setup script check for the rig (a root part, expected mesh names) before it runs.

**Materials**

- **Colour-key names choose materials.** Our builder maps names to Studio materials: keys like `wing`, `veil`, `ice`, `water` become Glass and keys like `glow`, `core`, `gem` become Neon. Two pets with feathered wings were built with the key `wing` and came in see-through at 30% transparency. Renamed to `leaf` and rebuilt, re-imported. Check the Glass/Neon list before asking the user for an import.
- **Neon shifts hue.** A lime (170,255,90) reads yellow in game. Use (80,255,110) for green glows.
- **Dark Glass vanishes.** A black obsidian effect in Glass disappeared against the scene. Use SmoothPlastic with Reflectance around 0.25.
- **Dark Neon is not dark.** A dark Neon face still glows because the flesh behind it shows through the holes.
- **Pale models wash out.** A PointLight next to a pale body (a sand-coloured planet boss) turned the whole frame white-violet. Keep lights soft and away from big pale bodies.
- **CorrodedMetal on small pieces** washed rust paint to pale blotchy grey. Plain Metal kept the look. Check a material next to the scene before keeping it.
- **Textured effect meshes.** A MeshPart with a texture and ForceField material only shows the outline. Use a `SurfaceAppearance` (colour map plus emissive mask) instead. Uploaded textures took up to about 40 minutes to load in game.

**Blender**

- **Loose vertices.** Decimation leaves stray vertices that Roblox drops, so part bounds in the manifest were off by up to a stud. Delete loose verts and degenerate faces before measuring.
- **Inside-out halves.** Never recalculate normals on an OPEN mesh (a half-tube, a cup). It can turn it inside out. Roblox draws one side only, and Blender renders hide the problem. Keep the built winding and check which way it faces.
- **Render-only effects don't travel.** Compositor lines, the bevel shader node and bloom do not carry into Roblox. In Axel we rebuilt cel lines as geometry (see "Axel's voxel look" below).
- **Five Blender renders at once** silently wrote black PNGs for one pet. Run at most two, and scan for blank frames.
- **Python on Windows.** `Path.write_text` writes CRLF and cp1252 by default. Pass `encoding="utf-8"` and `newline="\n"`, or the file fails linters or garbles symbols.
- **Parts that must be rigged later** need to be separate pieces: limbs, heads, tails, wings and fins as their own meshes (or tagged), so the rig script can find them.
- **Blender 5.2 renamed a function:** `Mesh.set_sharp_from_angle` marks hard edges after `shade_smooth()`.

**Sizing and measuring**

- **Eyes and faces pointing wrong.** After a review the pets' pupils pointed straight out sideways. They had to be turned forward and the lids raised. Add an eye-direction check to the review sheet.
- **Fast path for sizes.** A sheet showing every pet's height against a 5-stud marker catches size mistakes before import.

**Axel's voxel look**

- Voxels with no outline were rejected as "too pixely". The same voxel car with heavy cel lines was picked over a smooth bevelled car.
- Roblox has no equivalent of render outlines. Outlines were built as geometry: a copy of the model pushed out about 0.06 studs with faces flipped (an inverted hull), plus thin dark strips on the surface where two line groups meet.
- Cutting every face edge into voxel-long segments found all the boundaries. Using shared mesh edges missed T-junctions.
- Front and rear detail needs limits (nose and tail only), or "paint every face that points forward" also hits wheel arches.
- A range check like `(X > 40.5) & (X < 41.5)` holds no voxel centre when centres sit on .5. Use whole-number bounds around a .5 centre.

**Mecha parts**

- Heavy armoured mechs came from the user's note on the first one: "later ones lets make them look really really cool and mechy, like some pacific rim type stuff". The first mech stays simple on purpose.
- Limbs are chains of short rigid pieces on Motor6Ds, not skinned bones. Each band plus the ball joint at its start is one rig group. This was cheaper and read well on a mech.
- A big model streams in a few parts at a time with StreamingEnabled. Make display copies `Atomic` and wait for the parts to arrive before attaching effects.
- A chest light washed out a white mech. Keep lights weak on pale models.

## Code snippets

### 1. Headless build and export (Blender 5.2)

```python
# Run: <your Blender.exe> --background --python build_pets.py -- MyArea [PetKey ...]
import bpy, sys, json, pathlib

argv = sys.argv[sys.argv.index('--') + 1:] if '--' in sys.argv else []
OUT = pathlib.Path('assets/pets-myarea')
OUT.mkdir(parents=True, exist_ok=True)

# ... build every pet here; one root empty per pet, children = one mesh per colour key ...

bpy.ops.object.select_all(action='DESELECT')
for root in roots:                      # roots: the pet root empties you built
    root.select_set(True)
    for child in root.children:
        child.select_set(True)

# 1 stud per unit: with global_scale=0.01 the FBX imports at Scale Factor 1 at the exact size
bpy.ops.export_scene.fbx(filepath=str(OUT / 'MyAreaPets.fbx'), use_selection=True,
                         object_types={'MESH', 'EMPTY'}, axis_forward='-Z', axis_up='Y',
                         apply_unit_scale=True, bake_anim=False, global_scale=0.01,
                         add_leaf_bones=False, use_mesh_modifiers=True, use_triangles=True)

manifest = {'units': 'studs', 'pets': {}}   # colours, materials, sizes, attributes per part
(OUT / 'manifest.json').write_text(json.dumps(manifest, indent=1), encoding='utf-8', newline='\n')
```

### 2. A smooth tapered limb: one tube through the joints

```python
import bpy, bmesh, math
from mathutils import Vector

def smooth_path(pts, per=3):
    """Catmull-Rom curve through the joints, `per` samples per segment."""
    P = [Vector(pts[0])] + [Vector(p) for p in pts] + [Vector(pts[-1])]
    out = []
    for i in range(1, len(P) - 2):
        p0, p1, p2, p3 = P[i-1], P[i], P[i+1], P[i+2]
        for k in range(per):
            t = k / per
            out.append(0.5 * (2*p1 + (p2 - p0)*t + (2*p0 - 5*p1 + 4*p2 - p3)*t*t
                              + (3*p1 - p0 - 3*p2 + p3)*t**3))
    out.append(P[-2])
    return out

def tube(name, joints, radii, sides=12):
    path = smooth_path(joints)
    n = len(path)
    rad = [radii[0] + (radii[-1] - radii[0]) * i / (n - 1) for i in range(n)]  # taper
    bm = bmesh.new()
    rings = []
    for i, c in enumerate(path):
        t = (path[min(i+1, n-1)] - path[max(i-1, 0)]).normalized()
        side = t.cross(Vector((0, 0, 1)))
        if side.length < 1e-4:
            side = t.cross(Vector((1, 0, 0)))
        side.normalize(); up = side.cross(t)
        rings.append([bm.verts.new(c + (side * math.cos(a) + up * math.sin(a)) * rad[i])
                      for a in (2 * math.pi * k / sides for k in range(sides))])
    for r0, r1 in zip(rings, rings[1:]):
        for k in range(sides):
            bm.faces.new((r0[k], r0[(k+1) % sides], r1[(k+1) % sides], r1[k]))
    bm.faces.new(rings[0][::-1]); bm.faces.new(rings[-1])      # caps
    mesh = bpy.data.meshes.new(name); bm.to_mesh(mesh); bm.free()
    obj = bpy.data.objects.new(name, mesh); bpy.context.collection.objects.link(obj)
    return obj

# a back leg: hip, knee, hock, paw, with radii that shrink toward the foot
tube('Leg', [(0.3, 0, 2.0), (0.4, 0.5, 1.1), (0.35, 0.2, 0.5), (0.35, 0.5, 0.05)], [0.34, 0.2, 0.14, 0.18])
```

### 3. Clean a mesh before measuring it for the manifest

```python
import bmesh

def clean_for_manifest(obj):
    bm = bmesh.new(); bm.from_mesh(obj.data)
    loose = [v for v in bm.verts if not v.link_faces]         # decimation leaves these
    bmesh.ops.delete(bm, geom=loose, context='VERTS')
    bmesh.ops.dissolve_degenerate(bm, dist=1e-5, edges=bm.edges)
    # NOTE: do NOT call recalc_face_normals on open meshes (half-tubes, cups): can flip them inside out
    bm.to_mesh(obj.data); bm.free()
    xs = [v.co for v in obj.data.vertices]
    return [round(max(c[i] for c in xs) - min(c[i] for c in xs), 3) for i in range(3)]   # size
```

### 4. Studio fix-up from the manifest (Luau, run with the Studio MCP in Edit mode)

```lua
-- manifest was written by the Blender script and embedded or fetched; one entry per pet
local manifest = game:GetService("HttpService"):JSONDecode(MANIFEST_JSON)

local function fixPet(model: Model, info)
	for _, part in model:GetDescendants() do
		if part:IsA("MeshPart") then
			part.Name = string.gsub(part.Name, "%.%d+$", "")      -- strip Blender ".001"
			local spec = info.parts[part.Name]
			if spec then
				part.Color = Color3.fromRGB(table.unpack(spec.color))
				part.Material = Enum.Material[spec.material or "SmoothPlastic"]
				part.Transparency = spec.transparency or 0
			end
			part.Anchored, part.CanCollide, part.CanTouch, part.CanQuery = true, false, false, false
			part.CollisionFidelity = Enum.CollisionFidelity.Box
		end
	end
	-- feet at Y = 0, centred on X/Z, pivot at the origin
	local cf, size = model:GetBoundingBox()
	model:PivotTo(model:GetPivot() + Vector3.new(-cf.X, -(cf.Y - size.Y / 2), -cf.Z))
	model.WorldPivot = CFrame.new(0, 0, 0)
	for k, v in info.attributes do model:SetAttribute(k, v) end   -- Rarity, Zone, PetName ...
	-- verify: the built size must match the manifest
	assert((size - Vector3.new(table.unpack(info.size))).Magnitude < 0.05, model.Name .. " size is off")
end
```

### 5. Scale up small creatures (Luau)

```lua
-- Anything under 6.5 studs grows by (6.5 / size)^0.7; the smallest grow most, the order stays the same.
-- size = max(height, 0.6 * length). Keep a backup folder first.
for _, pet in library:GetChildren() do
	local _, s = pet:GetBoundingBox()
	local size = math.max(s.Y, 0.6 * math.max(s.X, s.Z))
	if size < 6.5 then
		local factor = (6.5 / size) ^ 0.7
		if math.abs(factor - 1) > 0.03 then
			pet:ScaleTo(pet:GetScale() * factor)    -- scales from the feet; the pivot stays put
		end
	end
end
```

## Prompts that worked

**1. A model batch, with the standard stated up front (adapted from our fire and cloud pet upgrade, Oct 12):**

```text
Bring the 18 Ember Highlands and Cloud Gardens pet models up to the standard of the Lotus Koi Spirit, Aurora Narwhal and Sea Dragon. Models only: names, species, rarity, zone, keys and heights stay. Lengths and widths can change. If a pet would work better as a different animal, tell me what you change it to. Work in batches with renders and I approve each batch before importing. First write an audit of the current 18 (what is too simple, what works) and a plan per pet, then build a calibration batch of three.
```

What made it work: a named standard, a clear "what stays", permission to change a species, and batches with approval.

**2. A review note that goes straight to the builder (Halloween pets):**

```text
The Bone Dragon is bland and undetailed, the wings need work: match the Divines and Secrets. The Mothman is lacking, swap it for a different idea. The Stitchbeast is not very patchwork, it has one sewn-on arm. Before modelling next time, study the approved builders and my past change requests so the first pass is already good enough.
```

The last sentence became a permanent rule, saved as a memory.

**3. Example (our style, not a real log): a mount with a hard constraint.**

```text
Make a Blazing Comet mount. You ride ON the comet itself, not on a neck behind it. Make the head big and craggy, with craters that have raised rims, rubble and a few glowing cracks. Show me three views and one in-game-style render with bloom. Use deep Neon tints so bloom does not turn the tail white.
```

## Do it in your game

1. **Pick a look and write it down.** Write 3-5 lines of art style (proportions, eyes, colours, polygon feel). Put it in a memory or notes file so every new chat builds in the same style. Add what you reject as you go.
2. **Build one calibration model first.** One pet or prop with the detail level you want everyone to match. Render it, approve it, and keep it as the reference. Then batch the rest.
3. **Use one builder script per area, with helpers.** Ask Claude to put shared shapes at the top of one Python file and to run Blender headless. Make it write an FBX at 1 stud per unit and a JSON manifest with colours, materials and sizes.
4. **Make review renders part of the script.** Front, side, back and a lineup beside a 5-stud figure. Always add a side-by-side with your approved reference.
5. **Import and fix up by manifest.** You run File > Import 3D with default settings. Claude then writes a Luau script (Studio MCP) that strips names, applies colours, sets sizes and pivots, and checks the size against the manifest. Screenshot colours in the real lighting.
6. **Finish with notes.** Write a task log: what you approved, what you rejected and why. Next time Claude reads it first.

Starter prompt:

```text
I'm building a Roblox game and I want Claude to model my [pets / vehicles / props] in Blender with Python and import them into Studio. Here is the style I want: [2-3 sentences, plus any concept images]. Please: (1) propose a simple builder script layout (one Python file, shared helper shapes, one function per model); (2) build ONE calibration model first and render front, side, back and a lineup next to a 5-stud player; (3) export an FBX at 1 stud per unit plus a JSON manifest with colours, materials, transparency and sizes; (4) write a Luau script that fixes up the import from the manifest (names, colours, sizes, pivot, attributes) and checks the size. Before you build, read my notes for past rejected looks. What questions do you have for me so I can help you implement this feature as best as possible?
```
