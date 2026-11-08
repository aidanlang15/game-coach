# Rigging, animation and cutscenes with Claude

How we rigged pets, bosses, a Kraken and mounts with Python in Blender, played the clips in Roblox, and built boss entrance cutscenes. From Battle for Pets (Axel and Mecha Cyborg only added small pieces).

## What we built

- **Pet rigs.** Every collectible pet has a skinned rig with Idle, Walk, Attack and (for the big ones) a Charged attack. The first test was one dog (27 bones). The user picked "bold and energetic" motion and no references. After approval, 16 more species were done in four batches, then each new area's pets (Frostpeak, Crystal Hollows, Ember, Cloud, Halloween, Starfall).
- **Pet flourishes.** Nine-tail fans, horn thrusts, wing and plume motion, a travelling body wave on the Sea Dragon, a two-fist slam on the Golem, a Turtle whose crystals and seaweed follow through. Fliers hover on beats, swimmers do three paced strokes per loop.
- **Boss rigs, 30-146 bones each.** Crownjaw (a crocodile pharaoh, 30 bones), Kingcroak (a toad, 39), Rimecoil (a three-headed frost hydra, 73), Ashwhirl (a djinn on a whirlwind, 86), Galestaff (a monkey king with a telescoping staff, 87), a pumpkin king (cape, lantern staff), a ringed-planet boss, and the Kraken (146 bones in the body, 25 per tentacle).
- **Boss clip sets.** Idle (a long loop), Entrance, one clip per attack, Hit (an additive flinch), Defeat (held at the end), plus boss-specific clips such as "Wish" and "Denied" on the djinn. Each clip has "contact" times: the exact moment a hit lands.
- **Kraken.** A 186-stud boss built from a model the user supplied, with 25 body clips (Emerge, Roar, Arm Lash 1-6, Beak Crush 1-6, Ink Barrage, Tidal Wave, Defeat, Retreat) and 13 tentacle clips (Slam, Sweep, three Coil sizes chosen by the pet's size, Reel, Down).
- **Mount rigs.** Each mount has Idle, Walk, Run (a gallop with the spine flexing), Summon and Dismiss. Rider poses (straddle, forward crouch) are published animations on the avatar.
- **Boss entrance cutscenes.** Each boss has its own 7-20 second cinematic before the fight: camera moves, the boss's Entrance clip, particle bursts, a name card, and a SKIP button. Three examples: the crocodile rears up and stomps then drinks in sunlight through its crown; the monkey king loops across a golden sky on its cloud and dives in; the Kraken cutscene is 20 seconds, every shot keeping its subject in the middle third so a 9:16 crop works for social video.
- **Avatar NPCs.** A tutorial guide built from Roblox avatar data (a dressed humanoid with a smile), cloned on the client.
- **Mecha Cyborg** moves its mech by posing rigid segments with spring physics each frame (no clips): a walk that eases into a run, whip-jab punches, flight poses.

## How we did it

### Blender builds the rig, bakes the clips, writes a data file

1. **The model is already built from separate, tagged pieces** (see the models guide). The rig script runs the model builder with "capturing" parts that remember which region, helper call and hinge each piece used. The captured mesh must equal the approved FBX vertex for vertex (we checked worst error: about 0.00001 studs). This means rigging never changes the approved look.
2. **Bones are placed from the builder's own points** (leg joints, hoof contacts, crest, horn tip), not by eye. Builders write named points (`rig_points`) for this.
3. **Skin weights come from tags and islands.** A tag like `neck` or `plume3` maps to soft weights over a chain. Feathers, stars and spots are one rigid weight set per island, taken where they meet the wing arm. Armour, gems and bands stay rigid on one bone.
4. **Clips are authored in Python** as poses at key times. Arm poses are solved from hand targets (inverse kinematics). Feet are planted with leg IK so they do not slide. Tails and plumes lag their parents ("follow-through").
5. **Bake.** Python bakes poses at a frame rate chosen so every contact time lands on an exact frame (50 fps for most bosses, 25 or 20 fps for the Kraken). It runs checks: feet above the floor, planted feet not sliding, IK reach, loops closing, and "the baked Blender actions equal the exported tracks" (tens of thousands of poses compared).
6. **Output.** An FBX with the skeleton (1 stud per unit), a manifest (part list, effect points, contact checks) and a Luau data module with the clip tracks. Early clips were JSON floats; the Kraken format stores rotations as int16 in base64 and is about half the size. Each clip decodes the first time it plays. Nothing is uploaded to Roblox: our pet and boss clips are data played by our own runtime, so there are no animation IDs or ownership problems for them.
7. **Install in Studio.** The user runs Import 3D (rig type Custom, Keep Zero Influence Bones, pivot at scene origin, Anchored, Scale Factor 1). A generated Luau script then stages the import: it makes a hidden `RootPart`, adds a Motor6D from the root to each skinned mesh (shared-bone skin deformation needs these), checks every part and bone against the manifest, keeps the old static model in a backup folder and installs the rigged one.
8. **Check in Studio.** A Luau check script plays every clip on copies at five scales and under all mutations, comparing each bone pose to the baked data (we ran hundreds of thousands of pose checks). Then a real Play test with disposable fixtures through the real battle functions, with the player's save snapshotted first and restored after.

### The game runtime

A small client module (`BossAnimation`, `PetAnimation`) sets each Bone's `Transform` every frame from the decoded clip. Blends between clips are done in code. Attack clips are timed from the game's config so a clip's contact lands exactly when the damage lands. We never change combat timings to fit an animation. We made the animation fit the config.

### Boss intro cutscenes

1. **Storyboard first.** Claude writes a shot-by-shot table (time, camera, what happens). The user approves it before any building. Choices are asked with defaults ("the big beat: somersault slam, or ...").
2. **A scene module per boss** plays on the fighter's own screen before their team comes in: camera moves, the boss's Entrance clip, effects timed to cue marks stored in the clip data (`introCues`: stomp 1.1, charge 1.45, roar 3.55), a name card, letterbox bars.
3. **SKIP** appears after about 1.5 seconds. On skip, the server moves the fight start and the first attacks earlier by the same amount (never later).
4. **Shared fallback.** Bosses without their own scene use a generic one: a low camera at the boss's feet craning up, its Entrance clip, shockwave rings and a name card.
5. **Preview in the admin panel** (INTRO and PROMO buttons) so cutscenes can be filmed without fighting.

### Splitting the work into sessions

- **Model, then rig, then fight, then cutscene, then attack effects**, one boss per session. The Kraken event was planned as nine numbered sessions (design, server, client, model, rig and animations, cutscene, attack effects, rewards, pet). Later sessions started from a prompt the previous session wrote and saved in notes.
- Each session prompt names the notes to read first, asks for a plan before building, and lists the model and effort to pick.

### Skipping review renders for boss animation

For the first boss clips we made contact sheets and review videos. The user said "Scratch the contact sheet and review video, we can split the two bosses up as well" (limited usage). We verify with the Blender bake checks and Studio screenshots from the battle camera's side instead. The one exception was the djinn, where one low-resolution preview clip was approved before the import.

## Rules we follow now

- **Motion must be big and readable.** The user's words on the first crocodile clips: "very poor", "tail doesn't even move", "barely any movement at all" (the idle swayed about 1 degree and the roar opened the jaw 9). On the toad: "frog doesn't hop or stick its tongue out". Now: whole-body wind-ups, whipping chains with per-joint lag, and a signature action per boss.
- **Check stills from the battle camera's side.** The battle camera sees bosses roughly in profile from one side. Plan sideways tail whips toward that side. For pets it sees the rig's `L` side, so put one-limbed strikes there.
- **One boss per session.** Reason: usage limits, and a clean hand-over.
- **Every clip keeps the config's timings.** Contact markers land on exact frames. A clip never moves a warning or a hit time.
- **Chained clips start where the last one ended.** Three tail sweeps in a row each start on the previous sweep's contact pose, so there is no jump.
- **Aim strikes with the camera in mind.** Strikes aimed straight at your pet foreshorten toward the battle camera, so we changed a lamp throw to an overhand hurl and had fist hooks cross the body.
- **Hold ends side-on.** A skinned cape cannot crumple. The defeat pose was posed by hand, and checking only the floor scan missed that the cape stuck out like a board. Check held end poses from the side.
- **Rest frames come from rest CFrames.** A bone's `WorldCFrame` while animated is not its rest frame. Effects anchored to bones must use the rest frame (snippet 3).
- **Accept both joint types on avatars.** Roblox avatars now use `AnimationConstraint` for LowerTorso.Root. A check for `Motor6D` alone fails silently.
- **Never write Lighting directly from a cutscene** in an area that has its own look. Go through the area's environment system (our `AreaEnvironment.cover`). Reason: a fight that starts right after a teleport begins before the area's look switches on. Our first djinn cutscene wrote its dusk straight to Lighting, the area look saved that as the place's own light, and leaving the area later "restored" the dusk.
- **Freeze players through Humanoid and prompts,** not through a controls module (our game has none, so `GetControls():Disable()` silently does nothing). Snippet 5.
- **Group-owned games need group-owned animations.** A published animation only plays in the game if the game's owner owns it. We saved the first rider pose under a personal account and it did not load in game. Re-saving the same animation kept the personal owner, so we saved a new copy with a new name and the group as creator. Rule: in the Save to Roblox window, set Creator to the group before the first save. Image uploads through the MCP stay personal and still show in a group game. How to check the owner: `MarketplaceService:GetProductInfo(id).Creator`.
- **Storyboard before cutscene build, with defaults.** The user can answer "d" to take every default.
- **A cutscene keeps the subject in the middle third** so a vertical (9:16) crop works for social video.
- **Never use Roblox's basic Block/Ball/Cylinder parts for cutscene effects.** Use custom meshes, particles, beams, trails (see the VFX guide if you have it).

## Mistakes and gotchas

**Rig and skin**

- **Bone names must be unique across a batch.** Two pets in one FBX with a bone both called `Spine` get merged into one skeleton. Prefix every bone with the species key (`Dog_Spine`).
- **The 2048-stud shrink scales meshes but not bones.** Export rigged models at 1 stud per unit and import at Scale Factor 1.
- **Skinned import quirks.** Import 3D puts bones under a separate `RootPart` with Motor6Ds and an `InitialPoses` folder, and X and Z can come in mirrored. Measure pivots from the imported bones, not from the Blender manifest.
- **A tip segment has no bone of its own.** Moving bones apart stretches the mesh between them, but a chain's last segment keeps its rest length, so tips land short. Model that stretch in your forward kinematics. For skinned strips, put a bone at the tip and give every vertex weights that sum to 1. Unweighted vertices stay at rest and stretch into long spikes.
- **Sharply bent limbs flip at the knee.** Projecting a very bent tube onto its bones flips the inside of the knee between bones (squash down to 0.11). Weight each vertex by the tube ring it was built on. A helper knee bone made it worse.
- **Thick arms crease under IK.** Raising a thick arm toward the shoulder folds the elbow. Carry the arm with the rearing body, or swing it nearly straight around an arc.
- **Hard floor limits snap arms.** A solver that hard-stops at the floor moves an arm 20 degrees in one frame. A soft floor that eases in fixed it.
- **Never recalculate normals on open pieces** (half-tubes, cups). It can turn them inside out.
- **A mesh loop with an unbroken part** (a ring that spins) must turn by a step that looks the same each loop. We turn a clover ring a seventh of a turn per loop. Swim and flight Idle and Walk share one length so their blend never fights.
- **Drift bones.** Gems and rings that spin in Idle jumped at every attack start. We added an opt-in `drift` list so those bones keep their endless spins under attack clips.
- **Stale rig results.** A checker once read skin weights from an old single-pet build. Delete single-pet build outputs before the final check.
- **The checker reports the first failing spot, not the worst.** Read the full report before setting an allowance.

**Studio and runtime**

- **Edit-mode `require` serves old code** after the file syncs. Clone the module and require the clone. A client module required through the MCP is a separate copy, so UI functions on it fail.
- **Animation module data vanishes after a Studio restart** if you wrote it in by hand. Use the sync tool.
- **Only animate what the camera sees.** Pets far from the camera skip frames. Check a rig with the camera next to the pet, or it looks frozen. A pet animated every frame cost about 0.3 ms per frame on a PC, and a full-server test dropped from 14-17 ms to 3.5-5 ms a frame after we animated only nearby pets every frame.
- **A script that moves a boss must go through the boss FX placement.** The battle HUD re-places the boss copy each frame from its base. Moving it directly gets overwritten (the monkey king's flight).
- **Entrance hand-off.** An Entrance that hands off to Idle must end where Idle will be at that time (Entrance length mod Idle length). A tail looking back 0.25 s showed a pop until we picked a length where Idle had no gesture in the quarter-second before.
- **A merged colour region cannot be hidden alone.** Builders merge pieces by colour: three peaches were one mesh. Tuck the bone inside the body instead.
- **A loop or a flight reads only face-on.** Put the camera beside the plane of motion.
- **A big dark scene needs lighting care.** In dusk scenes the clock past about 17.6 already shows a starry night. Use a dense grey Atmosphere with a high offset for a hazy dusk instead of lowering everything.
- **Fire rings and big lights flood floors.** A fire-ring mesh scaled to 80 studs fills the floor because the band grows with it, and a 60-stud PointLight floods it more.
- **A head turned fully round dips its jaw into the collar behind it.** Lift it first.
- **A Lua field named like a method shadows the method.** (`self.patch` vs `:patch`.)

**Testing and tooling**

- **Studio's `screen_capture` times out** while Studio is minimized or behind other windows, and may reset the camera. For cutscenes we used a PowerShell script that grabs a burst of frames with Studio in front.
- **Unfocused Studio runs at about 15 fps.** Bring it to the front for perf checks and filming.
- **A locked screen stops rendering entirely.** Offline checks only.
- **Parallel tool calls run one after another.** To watch something during real clicks, leave a listener counting into a Player attribute, click, then read it.
- **MCP mouse and key input arrives seconds late.** It cannot hit a 4-second timed input. Hold the window open on the server for a test.
- **Start a fight from a `task.delay`** inside `execute_luau`, or the camera resets to Custom when the call returns.
- **Fixture teleports into locked areas get sent back.** Set the session-only unlock attribute first.
- **Back up and restore the player's save** around any test that can win rewards.
- **`generate_avatar` (Studio MCP) timed out every time** and dropped a prompt-named model into Workspace minutes later. Check Workspace and delete it.
- **Avatar NPC fixes that worked:** build a humanoid from a `HumanoidDescription` with `CreateHumanoidModelFromDescription`. `SetAccessories` replaces rigid accessories too, so list hats with `IsLayered = false`. Leave hair out if hats hide faces. Use a dynamic head bundle and play its mood animation at Core priority for a smile. For NPCs cloned on the client, anchor the root part and set its CFrame every frame, and play Roblox's own R15 animations.

## Code snippets

### 1. Soft skin weights along a bone chain (Blender)

```python
import bpy
from mathutils import Vector

def weight_chain(obj, arm, bone_names):
    """Blend each vertex between the two bones nearest it along the chain."""
    heads = [arm.data.bones[n].head_local for n in bone_names]
    for n in bone_names:
        if n not in obj.vertex_groups:
            obj.vertex_groups.new(name=n)
    for v in obj.data.vertices:
        p = obj.matrix_world @ v.co
        d = sorted(((p - arm.matrix_world @ h).length, i) for i, h in enumerate(heads))
        (d0, i0), (d1, i1) = d[0], d[1]
        w1 = d0 / (d0 + d1 + 1e-9)          # closer bone gets the bigger weight
        obj.vertex_groups[bone_names[i0]].add([v.index], 1.0 - w1, 'REPLACE')
        obj.vertex_groups[bone_names[i1]].add([v.index], w1, 'REPLACE')
    # Rigid parts (armour, gems) get 1.0 on a single bone instead: add([...], 1.0, 'REPLACE')

# prefix bone names with the species key, e.g. 'Wolf_Neck1', 'Wolf_Neck2': names must be unique per FBX
```

### 2. Contact times on exact frames (Blender / Python)

```python
# Every hit "contact" must land on a baked frame. Pick fps so contact * fps is a whole number.
CLIPS = {                       # name: (length_s, contact_s)  contact from the game's config, never edited
    'Fast':    (1.0, 0.28),
    'Charged': (2.0, 1.20),
    'Sweep1':  (1.8, 1.30),
}
FPS = 50

def check_contacts():
    for name, (length, contact) in CLIPS.items():
        frame = contact * FPS
        assert abs(frame - round(frame)) < 1e-6, f'{name}: contact {contact}s is not on a frame at {FPS} fps'
        assert round(length * FPS) <= 10000

def bake(action, sample_pose, length):
    """sample_pose(t) poses the armature; keyframe every pose bone on every frame."""
    arm = bpy.context.object
    for f in range(int(round(length * FPS)) + 1):
        sample_pose(f / FPS)
        for pb in arm.pose.bones:
            pb.keyframe_insert('rotation_quaternion', frame=f)
            pb.keyframe_insert('location', frame=f)
check_contacts()
```

### 3. Bone rest frames: effect anchors that stay on the bone (Luau)

```lua
-- A posed bone's WorldCFrame already includes its PARENT bones' animation, so it is not the rest frame.
-- Build the rest frame from the bones' rest CFrames instead.
local function restFrame(bone: Bone): CFrame
	local parent = bone.Parent
	if parent and parent:IsA("Bone") then
		return restFrame(parent) * bone.CFrame
	end
	return (parent :: BasePart).CFrame * bone.CFrame      -- a bone directly under the root part
end

-- measure an effect's offset once, from the rest pose...
local offset = restFrame(bone):PointToObjectSpace(effectPoint)
-- ...then each frame place it with the bone's CURRENT (animated) frame
effect.Position = bone.TransformedWorldCFrame:PointToWorldSpace(offset)
```

### 4. Playing a baked rotation as a Bone Transform; accepting both joint types (Luau)

```lua
-- Clip data stores each bone's rotation R relative to its parent in MODEL axes.
-- Bone.Transform lives in the bone's own axes, so convert with the rest rotation F:  F^-1 * R * F.
local function toTransform(restCF: CFrame, rotation: CFrame): CFrame
	local F = restCF.Rotation
	return F:Inverse() * rotation * F
end

RunService.PreSimulation:Connect(function()                -- after the Animator has run
	for bone, rotation in decodedPoseThisFrame do
		bone.Transform = toTransform(bone.CFrame, rotation)
	end
end)

-- Avatars' LowerTorso.Root is an AnimationConstraint now, not a Motor6D. Accept both.
local function jointIn(part: Instance?, name: string): (Motor6D | AnimationConstraint)?
	for _, child in (part and part:GetChildren()) or {} do
		if child.Name == name and (child:IsA("Motor6D") or child:IsA("AnimationConstraint")) then
			return child
		end
	end
	return nil
end
```

### 5. Freezing the player for a cutscene without a controls module (Luau)

```lua
local ProximityPromptService = game:GetService("ProximityPromptService")

local function freezePlayer(humanoid: Humanoid)
	local saved = {
		walk = humanoid.WalkSpeed, jumpPower = humanoid.JumpPower,
		jumpHeight = humanoid.JumpHeight, autoRotate = humanoid.AutoRotate,
		prompts = ProximityPromptService.Enabled,
	}
	humanoid.WalkSpeed, humanoid.JumpPower, humanoid.JumpHeight = 0, 0, 0
	humanoid.AutoRotate = false
	ProximityPromptService.Enabled = false          -- no prompts firing during the shot
	-- also dismount the player and block mounts here if your game has them

	return function()                                -- call this in cleanup, even when the player skips
		humanoid.WalkSpeed, humanoid.JumpPower, humanoid.JumpHeight = saved.walk, saved.jumpPower, saved.jumpHeight
		humanoid.AutoRotate = saved.autoRotate
		ProximityPromptService.Enabled = saved.prompts
	end
end
```

## Prompts that worked

**1. A rig session brief (adapted from our monkey-king and djinn rig sessions):**

```text
Rig and animate the Cloud-Monkey King boss. Same pipeline as the djinn: compact clip data, nothing uploaded, one boss this session, no review renders or videos. Use the approved model unchanged: the rigged mesh must equal the approved FBX vertex for vertex. Keep every warning and contact time from BossConfig. Clips: Idle (8 s loop), Entrance, Fast, Charged, three chained Storm clips (each starts where the last landed), Hit, Defeat. Make the motion big and readable from the battle camera's side: whole-body wind-ups, lagging tails and plumes, boots planted by IK. First write the plan (bone list, clip list with contact times) and wait for my OK. I will do the Import 3D.
```

**2. Feedback that fixed weak animation (our words, Oct 3-4, shortened):**

```text
These clips are very poor. The tail doesn't even move, there is barely any movement at all. The frog doesn't hop or stick its tongue out. Scratch the contact sheet and review video, we can split the two bosses up as well.
```

What made it work: three concrete, checkable failures, and a cost cut (no review media, one boss per session).

**3. Example (our style, not a real log): a cutscene storyboard request.**

```text
Design the entrance cutscene for [boss]. Give me a shot-by-shot table (time, camera, what happens), about 10 seconds, every shot with the subject in the middle third so a vertical crop works. Include the big beat, one funny beat, a name card and a SKIP button that appears after 1.5 seconds. For each choice, offer 2 options and a default; I will answer "d" to take your defaults. Don't build anything until I approve the storyboard. Use the area's own lighting system, not direct Lighting writes. What questions do you have for me before you start?
```

## Do it in your game

1. **Start with one rig.** Pick a simple animal or boss. Ask Claude for Idle, Walk and one Attack. Say "bold and energetic" or whatever feel you want, and ask for big motion from the camera's side. Approve the motion before copying the pipeline to everything else.
2. **Decide how clips are stored.** Easiest: published Roblox animations (set Creator to your group if you have one). Our way: bake to a data module and play it with a small runtime that sets Bone Transforms. Pick one and say so in your notes.
3. **Rig from the builder.** Capture the approved mesh, place bones from named builder points, weight by tags, plant feet with IK and bake at a frame rate that puts contact times on frames. Make Claude print floor and slide checks.
4. **Install with a script.** You do Import 3D (Custom rig, Keep Zero Influence Bones, Scale Factor 1). Claude stages it: hidden root, Motor6D per mesh, backup of the static model, checks against the manifest.
5. **Check in Studio from the camera your players see.** Take stills from the battle or game camera. Play on copies at several scales and check no bone drifts.
6. **Then a cutscene.** Storyboard first. Freeze the player through Humanoid and prompts. Use your game's lighting system, not direct writes. Add SKIP. One boss or one scene per session, with a hand-over note.

Starter prompt:

```text
I'm adding animation to my Roblox game. I want rigged [pets / a boss] animated with Python in Blender and played in Studio, then an entrance cutscene for [boss]. Here is the model: [name, notes]. Please: (1) propose how we store and play the clips (published animations owned by my group, or baked data played by our own runtime) and why; (2) start with ONE rig: bones placed from the model's own points, skin weights, Idle and Attack with big readable motion from the camera's side, with floor and foot-slide checks; (3) give me the exact Import 3D settings and a Luau install script that checks the result against a manifest; (4) write a storyboard table for the cutscene with options and defaults before building anything. Read my notes first for what I have rejected before. What questions do you have for me so I can help you implement this feature as best as possible?
```
