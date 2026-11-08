# Visual effects (VFX)

How we made attack effects, boss effects, mount effects and mutation looks with Claude Code. Comes mostly from Battle for Pets (dozens of pets, a dozen bosses, a dozen mounts, seven or more mutation looks), with a few lines from +1 Mecha Cyborg (punch effects).

## What we built

- **A hero attack for every pet.** Each pet has a Fast move and a Charged move, and each has its own effect. A star bear's charged move draws the seven Big Dipper stars over the foe and joins them with gold lines. A black-hole pet opens a small black hole on the foe and tears rocks into it. Nobody shares a "default finisher".
- **A stylized library** (`FXKit`) that every area uses: animated 8x8 sprite sheets for bursts and smoke, slashes swept by a trail, banded energy meshes (dome, ring, orb, swirl, crescent, spike), streaks, cross flares, speed lines and an anime "impact frame". Later we added fire, electric and glitter orbs.
- **Area kits.** Each area has its own small kit on top (frost, crystal, ember, cloud, starfall) with its own textures and meshes. They share one rule: the area's colors decide the effect's colors, so it reads on that map.
- **Boss effects.** Each boss move has a warning sigil on the ground that closes exactly when the hit lands, a lane from the part that attacks, a contact, an impact and an aftermath. A dodged hit breaks the sigil.
- **Mount effects.** A dozen rideable mounts. Each has persistent effects (dust, glints, trails, breath), motion effects (footprints, skid sparks, speed streaks), and a summon and dismiss show. A mount's module has a small budget of instances.
- **Mutation looks.** Seven looks that change how a pet looks (golden, frozen, rainbow, inferno, galaxy, celestial, abyssal). Each has custom skins, custom meshes and its own motion.
- **Phone and accessibility versions** of everything: half the particles on phones, and no flashes or hit-stop with Reduced Motion.

## How we did it

1. **Show the references first.** The first attack effects were built from stock Roblox particles and looked cheap. We started again from three references the user picked: an anime VFX animation, a short film with ink-black tendrils, and a Blender stylized-energy tutorial. Claude pulled frames from the clips with FFmpeg and wrote down what they share: hard-edged shapes, a white-hot core, one hue, dark accents, clear timing.
2. **Write the style guide before building.** We saved the findings as a portable `VFX Style Guide` note (look, timing, tiers, budgets, colors, recipes, gotchas). Every later session reads it first. It also holds the pre-ship checklist.
3. **Make assets, not code, first.** Textures are drawn by a Python script (numpy, run through Blender's Python). Meshes come from a Blender script. Claude previews each texture as a contact sheet on the real ground color before anything is uploaded. That takes seconds. An upload and a Studio test take minutes.
4. **Upload and import.** Textures go up through the Studio MCP image upload. Meshes go in as one FBX the human imports with Import 3D (Claude can't click that menu). Claude then dresses each mesh in Studio: material, color, a `SurfaceAppearance` skin.
5. **Calibrate on two pets.** Before a whole area gets effects, we build two calibration pets (one flowing and light, one heavy and big), the human reviews, then Claude builds the rest in the approved style. For area-wide batches the human typically answered the design questions with "d" (use your default).
6. **Preview with a test panel.** A Studio-only VFX TEST button stages an attacker and a target (small to huge), a mutation and a camera, and plays Fast, Charged or both. Claude and the human use the same panel.
7. **Film it.** One short reel per batch from the real battle camera, in 16:9, at full speed and again at half speed, next to a reference frame. The human says "looks good" or lists changes. Then Claude runs a few real fights.
8. **Measure.** Count live effect instances at the peak of every move (Fast about 60, Charged at most about 144), check 0 are left after, and test the phone and Reduced Motion versions.
9. **Split into sessions.** Planning (the audit and storyboards, waits for the human's OK), then assets (textures and meshes), then Studio work (install, tune, film), then review. Big batches ran on a separate git branch with the code served into Studio over a local web server, so other sessions could keep using the main folder.

**What we asked Claude for first, in each area:** an audit of the existing effects, a storyboard per move (wind-up, release, impact, aftermath, with colors), and only then the build. The plan note waits for the human's OK. Storyboards are cheap to change. Built effects are not.

## Rules we follow now

- **No primitive parts.** Never build visible effects from Roblox's Block, Ball, Cylinder or Wedge parts. Use custom meshes made in Blender, particles, beams, trails, textures and lights. An invisible part that only carries attachments is fine. *Why:* the user ruled this after effects kept coming out as primitive shapes, which read as cheap next to custom pets.
- **A recolor is not a new effect.** Reusing another character's effect in a new color gets rejected. Give each character its own signature piece built from its theme.
- **No shared finisher.** Ground cracks, flame-ring shockwaves and twisting vortexes after every big hit made all pets look the same. The user's words: they all had "the quake effect on the ground, and the vertical twirling effect". *Why:* sameness is what players notice first.
- **Hard-edged, not soft.** A hot core, one main hue, dark accents. Soft generic glows look cheap.
- **Anime timing.** Charge, release, impact, aftermath. Never everything at once. The charge happens where the character charges: one clear thing growing there (an orb), not effects spread around the body. The user turned down a spin-and-dive for a charged move: charge in place, then release.
- **3D pieces that move, not flat sprites.** Big camera-facing sprites (a skull, grins, sigils, flat rings) read as "images on the screen", even with custom textures. Build hits from moving 3D pieces: ribbons, rings that turn, meshes that blast out, debris. Make big hits land hard: a longer hit-stop, a stronger flash, shockwaves, thrown debris.
- **Throw big hits out the far side of the target.** Attackers stand close (about 3 studs in our game), so a hit centered on the target hides behind it.
- **Tint the core toward the hue.** Near-white cores, rings glowing in the core color and full-brightness additive bursts washed out to white on pale ground. Give rings their own color at glow 1.4 to 2.2. Give bursts partial brightness (light emission 0.75).
- **Deep tints, never pale Neon.** Roblox's bloom turns pale Neon white. Dark Glass parts vanish. On a pale map, drop additive light to about 0.3 and keep each piece's own deep color.
- **Test on the brightest ground in the game.** An effect that works on a dark stage can vanish on a pale map.
- **Compare side by side before showing anything.** For a reference clip, crop the same moments from the reference and from ours at the same on-screen size, and list every difference. The user said "look at the difference" after we shipped a version that looked right at a glance and was twice the size of the reference up close.
- **Many small crisp pieces, dark gaps between them, round a small hot core.** One or two big soft sprites, or several glow layers stacked in the middle, add up and bloom into a flat blob. Emit through a ball volume so pieces sit in front of and behind the core: that gives depth.
- **Copy the shape language.** The reference's lightning was angular, kinked wire. Smooth curls read as tentacles. Its sparkle was fine round grains. Four-point "+" stars read as cartoon.
- **Match the size on screen.** Wrong size alone makes an effect look crude.
- **Body skins stay simple.** Busy patterned overlays on a pet's body were rejected for rainbow (tie-dye, opal flecks, foil lines). The user picked a bold, clean spectrum with glints and put the detail in effects around the pet (shards, ribbons, a halo).
- **Keep the pet's detail under a look.** The approved golden concept's hammered texture washed out in game and hid the markings. We tinted each part by its tone against the pet's main body so spots and dark hooves still show.
- **Rider on the hero object.** For a mount that is an object (a comet), the rider sits ON it. A small head on a neck read as "a mace on a stick".
- **Each effect gets its own budget and cleans up.** Fast moves about 60 live instances, Charged at most about 144, Mythic-and-up about 180. Nothing left behind when it ends.
- **Scale by tier.** Common to Rare: 2 to 4 layers, simple. Epic and Legendary: layered charge and impact, speed lines. Mythic and up: fully layered, impact frame, hit-stop, the largest throw.
- **Phones ("lite"):** half the particles, about 60% of the spikes, no extras. **Reduced Motion:** no impact frame, flash or hit-stop, and spinning at 30% speed.
- **Effects are client-side and cosmetic only.** They never change damage, timing or saving.
- **Anchor on rest frames.** Read anchor points (a fist, a horn) from the rig's bones or attachments, using a bone's rest `CFrame`, not its animated `WorldCFrame`.
- **Update the guide when a rule changes.** The portable style guide is the one source. We copied it into our other game and linked it from that game's CLAUDE.md.

## Mistakes and gotchas

- **Fresh uploads draw flat or blank** ("being generated") for 20 to 40 minutes. A thumbnail API saying "Completed" is not proof. Check with an on-screen `ImageLabel`'s `IsLoaded` (off-screen ones never load). Plan the uploads first, and work on other things meanwhile.
- **First use of a skinned mesh draws white.** The first shield of a session stood as an opaque white dome for a frame. Preload the templates with `ContentProvider:PreloadAsync` when the fight starts. Test in a fresh Play, since the cache hides it later.
- **ForceField on a textured mesh shows only its outline; Neon ignores texture alpha.** Use a `SurfaceAppearance` with the texture as color map and glow mask, tint through its `Color` and `EmissiveTint`, and set it once on the template. Never change a Texture at runtime.
- **Particle squash stretches.** A squash of 2.8 draws a particle about 3.8 times its size, so the first streaks came out as 20-stud white bars. Size them as length divided by 3.8.
- **Don't name an effect's field after one of its methods.** `fx.collapse` shadowed `Effect:collapse` and silently broke a move. It became `fx.horizonClosed`.
- **Broken effect steps hide as warnings.** A per-frame step that errors is dropped with a warning, not an error. Count those warnings in your checks. One wrong argument stopped every bomb explosion for a boss.
- **Bone.WorldCFrame is animated.** An offset measured mid-animation put a boss's fire back at the static spot. Build rest frames from `Bone.CFrame` and the parent chain.
- **A Bone is an Attachment.** Counting attachments under a rig also counts bones (we saw 50). Count `ClassName == "Attachment"`. Never add Bones to a mount's rig for effects: the animation binder takes every Bone and the mount stops animating. Add Attachments under the existing bones.
- **Import 3D quirks.** It turns meshes 180 degrees about Y, keeps each authored pivot in `PivotOffset`, and puts skinned bones on a separate root part with mirrored X and Z. Move the bones onto the mesh and measure pivots from the imported bones, not from your Blender manifest. A raw import left in Workspace blocks a publish.
- **Skinned strips need a tip bone.** Vertices past the last bone get zero weight and stretch into long spikes. Also, the stretch past the last bone is rigid, so a strip posed along a path must be about that long.
- **Textures with 1-3 px detail vanish at the game camera.** A pet shows about one tile across. Make key features several pixels wide.
- **Beams twist into bow-ties** when large and close to the camera. Use particles on carriers for big flat shapes. For lightning, see the snippet below.
- **Glass takes the color behind it** at partial transparency, and dark Glass vanishes. A black obsidian mesh in SmoothPlastic with a little reflectance reads as glossy glass.
- **A PointLight next to a pale body washes it out.** A 30-stud light beside a pale boss turned the whole frame white. Keep lights off effects that sit against a big pale body, and keep ground lights soft on pale ground.
- **Textured skins multiply by the area's light.** Gold went olive under a blue night and brown in a cave. A Neon base showing through a 20% see-through skin kept it gold everywhere.
- **Fire built from separate flame licks reads as floating teardrops.** Fluid fire needs connected, continuously moving flame (overlapping licks forming one body, or a scrolling flame texture on a mesh). We accepted "not fluid" for now and wrote it down.
- **Mutation effects cost per frame.** Our first celestial look cost about 430 microseconds per pet per frame (80 ellipsoid parts for wings, 52 parts for a ground seal). Eight of them cost several milliseconds. The redesign used real meshes and about a dozen effect parts, and got the cost down to roughly 60 microseconds per pet. Each part moved per frame costs a few microseconds, and welds don't save that.
- **The battle's own hit flash hid fast effects.** A white flash on the foe for 0.18 s hid a rake effect. Put fast impacts in front of and around the foe, with a dark layer, and make the best moment last past 0.2 s.
- **Studio quirks when filming.** Studio runs at 15 fps when it isn't the focused window. Hide the cursor, play a warm-up pass before filming (each fresh Play logs "content representation is being generated"), and expect the MCP to reset the camera after every call. Do camera moves inside one call.
- **Blender renders in parallel go black.** Five at once wrote blank frames with no error. Run at most two or three.

## Code snippets

Names are generic. The ideas are the ones we use in every effect.

### 1. An animated burst from a sprite sheet

One-shot sheets (8x8, 64 frames) play once over the particle's life. Loop sheets play forever. Splat sheets show one random still per particle.

```lua
-- sheet: a white or grey texture, tinted by the emitter's Color.
local function burst(parent: Instance, sheet: string, color: Color3, size: number, life: number)
	local e = Instance.new("ParticleEmitter")
	e.Texture = sheet
	e.FlipbookLayout = Enum.ParticleFlipbookLayout.Grid8x8
	e.FlipbookMode = Enum.ParticleFlipbookMode.OneShot
	e.FlipbookFramerate = NumberRange.new(64 / life) -- all 64 frames in one life
	e.Lifetime = NumberRange.new(life)
	e.Rotation = NumberRange.new(0, 360)
	e.Speed = NumberRange.new(0)
	e.LightInfluence = 0
	e.LightEmission = 0.75 -- partly lit by the scene, so it doesn't blow out to white
	e.Color = ColorSequence.new(color)
	e.Size = NumberSequence.new({
		NumberSequenceKeypoint.new(0, size),
		NumberSequenceKeypoint.new(1, size * 1.1), -- grows about 10% over its life
	})
	e.Enabled = false
	e.Parent = parent
	e:Emit(1)
	game:GetService("Debris"):AddItem(e, life + 0.1) -- never leave it behind
	return e
end
```

### 2. A slash swept by a trail

An invisible holder with two attachments. Each frame the attachments move along an arc, and the trail's texture (a hard white leading edge breaking into streaks) draws the slash.

```lua
local function slash(cf: CFrame, radius: number, color: Color3, slashTexture: string)
	local holder = Instance.new("Part") -- invisible carrier only
	holder.Anchored, holder.CanCollide, holder.CanQuery = true, false, false
	holder.Transparency, holder.Size, holder.CFrame = 1, Vector3.one * 0.1, cf
	holder.Parent = workspace
	local a0, a1 = Instance.new("Attachment", holder), Instance.new("Attachment", holder)

	local trail = Instance.new("Trail")
	trail.Attachment0, trail.Attachment1 = a0, a1
	trail.Texture = slashTexture
	trail.TextureMode = Enum.TextureMode.Stretch
	trail.Color = ColorSequence.new(Color3.new(1, 1, 1), color)
	trail.LightEmission = 1
	trail.Lifetime = 0.16
	trail.MinLength = 0.01
	trail.FaceCamera = false
	trail.Parent = holder

	local t0, seconds = os.clock(), 0.1
	local conn
	conn = game:GetService("RunService").Heartbeat:Connect(function()
		local k = math.clamp((os.clock() - t0) / seconds, 0, 1)
		k = 1 - (1 - k) ^ 2 -- ease out
		local angle = 2.2 + (-0.6 - 2.2) * k -- sweep from 2.2 to -0.6 rad
		local dir = cf.RightVector * math.cos(angle) + cf.UpVector * math.sin(angle)
		a0.WorldPosition = cf.Position + dir * radius
		a1.WorldPosition = cf.Position + dir * radius * 0.7
		if k >= 1 then
			conn:Disconnect()
			task.delay(0.2, function() holder:Destroy() end)
		end
	end)
end
```

### 3. A banded energy ring or dome that grows, turns and fades

The mesh wears a hard-edged band texture. We tint it and make it glow through its own `SurfaceAppearance`, set up once on the template.

```lua
-- template: a MeshPart with proper UVs and a SurfaceAppearance (ColorMap = bands texture).
local function growMesh(template: MeshPart, color: Color3, frame: CFrame, r0: number, r1: number, life: number)
	local part = template:Clone()
	part.Anchored, part.CanCollide, part.CanQuery, part.CanTouch, part.CastShadow = true, false, false, false, false
	local skin = part:FindFirstChildOfClass("SurfaceAppearance")
	if skin then
		skin.Color = color -- the tint
		skin.EmissiveTint = color -- glow in its OWN color, not the core color
		skin.EmissiveStrength = 1.8
	end
	local base = template.Size
	part.Parent = workspace
	local t0 = os.clock()
	local conn
	conn = game:GetService("RunService").Heartbeat:Connect(function()
		local k = math.clamp((os.clock() - t0) / life, 0, 1)
		local e = 1 - (1 - k) ^ 3 -- eased out
		local r = r0 + (r1 - r0) * e
		part.Size = Vector3.new(base.X * r, base.Y * r * (1 - 0.55 * k), base.Z * r) -- a dome flattens
		part.CFrame = frame * CFrame.Angles(0, (os.clock() - t0) * 2.8, 0) -- turns on its own axis
		part.Transparency = k ^ 1.6 -- fades late
		if k >= 1 then
			conn:Disconnect()
			part:Destroy()
		end
	end)
end
```

### 4. An impact frame (about 0.12 seconds)

A black full-screen frame with a hard white splat on the hit point, then a tint, then gone. Use it on the biggest hits only, and skip it with Reduced Motion.

```lua
local function impactFrame(worldPoint: Vector3, tint: Color3, splatImage: string)
	if game:GetService("GuiService").ReducedMotionEnabled then return end
	local camera = workspace.CurrentCamera
	local gui = Instance.new("ScreenGui")
	gui.IgnoreGuiInset, gui.DisplayOrder, gui.ResetOnSpawn = true, 50, false
	local black = Instance.new("Frame")
	black.Size, black.BackgroundColor3, black.BorderSizePixel = UDim2.fromScale(1, 1), Color3.new(0, 0, 0), 0
	black.Parent = gui
	local p = camera:WorldToViewportPoint(worldPoint)
	local size = math.max(camera.ViewportSize.X, camera.ViewportSize.Y) * 0.9
	local splat = Instance.new("ImageLabel")
	splat.BackgroundTransparency, splat.Image = 1, splatImage
	splat.AnchorPoint, splat.Size = Vector2.new(0.5, 0.5), UDim2.fromOffset(size, size)
	splat.Position, splat.Rotation = UDim2.fromOffset(p.X, p.Y), math.random() * 360
	splat.Parent = black
	gui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
	task.delay(0.06, function()
		black.BackgroundColor3 = Color3.new(1, 1, 1):Lerp(tint, 0.25)
		splat.ImageColor3 = tint
	end)
	task.delay(0.12, function() gui:Destroy() end)
end
```

### 5. Lightning from Beams that actually zigzags

Geometry makes the zigzag, not the texture. Kinks go in the camera's screen plane, segments are much longer than they are wide, and the halo is alpha-blended on pale ground.

```lua
-- points: build a jagged chain of Attachments from `from` to `to`.
local function boltPoints(from: Vector3, to: Vector3, segments: number, kink: number)
	local camera = workspace.CurrentCamera
	local dir = (to - from).Unit
	-- screen-plane sideways direction: camera right, made square to the bolt
	local side = (camera.CFrame.RightVector - dir * camera.CFrame.RightVector:Dot(dir)).Unit
	local pts = { from }
	for i = 1, segments - 1 do
		local base = from:Lerp(to, i / segments)
		pts[#pts + 1] = base + side * (math.random() * 2 - 1) * kink
	end
	pts[#pts + 1] = to
	return pts
end
-- One Beam per segment: a thin pale core (width about 0.2-0.3 x the effect scale) plus
-- a wider saturated halo with LightEmission about 0.15, so it still shows on pastel ground.
-- Keep halo width under about 0.6 x segment length, or the joints turn into blocky tiles.
```

## Prompts that worked

**1. Real prompt, cleaned (the rebuild).** After the first effects looked alike, we sent three reference clips and said, roughly: our attack effects aren't as good as these references and the pets all look the same. We asked Claude to study the references frame by frame, tell us why ours fell short, and propose a rebuild. Claude's answer listed causes (one shared kit, still textures, no real swirls, too much at once, judged from far away in a cramped view). We approved a new library and a single showcase pet first.

**2. Real prompt, cleaned (matching a clip).** We sent a short clip of three looping orbs (fire, electric, glitter) on a black grid and asked whether we could make effects like it. We chose "library plus a test reel", so the orbs went into the shared library with no pet using them yet. The first version looked like it at a glance and was twice the size. We replied, "look at the difference", and Claude built a side-by-side crop grid and rebuilt the orbs.

**3. Example (our style, a new area's attacks).**

```text
First read notes/VFX Style Guide.md and the task logs for the last area we did.
Then audit the attack effects for the 10 pets in [area]. Write a storyboard for each
pet's Fast and Charged move (wind-up, release, impact, aftermath, colors from the
pet's own model, one signature piece per pet, no shared finisher). Don't build yet.
Pick two calibration pets (one light, one heavy) and say why. List the textures and
meshes you'd make. Stop and wait for my OK.
```

## Do it in your game

1. **Collect two or three references** you love (clips, GIFs, tutorials). Ask Claude to pull frames and list what they share. Save that as a short style guide note in your project, and add one line to CLAUDE.md: "Attack effects follow `notes/VFX Style Guide.md`."
2. **Decide the rules before the first effect:** no primitive parts, each character gets its own signature piece, a budget of live instances per move, a phone version and a Reduced Motion version.
3. **Make a tiny asset set first.** A burst sheet, a smoke sheet, a slash texture, a band texture and four meshes (ring, dome, orb, spike). Draw them with a Python script, preview them on your real ground color, upload them, and import the meshes.
4. **Build a test panel** that stages an attacker, a target and a camera so you can replay any effect in one click.
5. **Calibrate on two characters** (one small, one big), review a short reel from your real game camera, then roll out the rest.
6. **Check before you ship:** peak instance counts, 0 left afterward, the brightest map, a phone-sized view, and a few real fights. Write what was rejected into the task log so the next session doesn't repeat it.

```text
First read ~/.claude/skills/game-coach/playbook/vfx.md.

I want to add attack effects to my Roblox game. My game is [one sentence]. My
characters are [list, with rarity or size]. My references are [links or clips].
Please:
1. Study the references and write a short VFX style guide note for my project.
2. Audit what effects I have now.
3. Propose a small shared library (textures, meshes, helper functions) and two
   calibration characters. Don't build until I say OK.

What questions do you have for me so I can help you implement this feature as best as possible?
```
