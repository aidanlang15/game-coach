# UI: HUD, windows, cards and phone layouts

How we designed and built the interface of Battle for Pets (and what we carried into Axel and the +1 games): the style guide, mocking before building, juicy motion, phone layouts and the Roblox gotchas that cost us days.

## What we built

**A style guide, then a style kit in code.** After many screens had grown one at a time, we audited all of them and wrote one design language. It lives in a note ("UI Style Guide") and in one Luau module (`UIStyle`). Every window, card and button is built from that module, so a new screen looks like the old ones by default.

The look, in short:

- **One of everything.** One outline width (3 px ink stroke on every panel, card, button, tab and pill), one ink color, one light direction (top-left), one palette, one type scale, one icon family.
- **Flat and bold.** Saturated flat fills with one light top edge and one dark bottom edge. No glossy multi-stop gradients, no random sheen.
- **Color means something.** A window has one signature color (shared with its side button). A card is its item's rarity color. A button's color is its job: green buy, gray cash price, purple Robux, red close.
- **A fixed order on every card.** Name, rarity line, art on a soft glow, a dark stat panel (cyan number, white unit), then the button.
- **Pressable things sit on a lip.** A 4 px ink lip under the face. Pressed: the lip goes and the face drops 4 px.
- **Text is outlined.** White letters, an ink outline sized to the text (4 px at 30 px and up, 3 px at 18 to 29, 2 px below), and a hard drop shadow under it.
- **Studs on bases only.** Roblox's own stud texture on window bodies, wells and plain cards. Not on art banners, not inside letters.
- **Windows have a standard shape.** A title bar in the window's color with shine stripes, a red square close button, tabs on the left of the top row and a cash chip on the right.

**Screens built in this style:** the HUD (side buttons, cash readout, team bar, hotbar), the Gear shop, Crafting, Inventory, the Robux shop, Gifts, Daily rewards, Pets, Index, Fusion, Pen Upgrade, Training, the boss unlock card, the reward toasts, the battle screens and even the floating text over signs.

**Pieces that grew out of real problems:**

- **Reward cards.** XP, materials and unlock moments get a small designed card (gold heading, dark ink-bordered pills, pop-in), never a plain one-line toast.
- **3D item icons.** Capsules, boosts, cash and the shop tabs are Blender renders with an ink outline. Interface symbols (shop, index, lock, check, close) stay flat 2D drawings.
- **Pet pictures for phones.** Phones draw live 3D viewports at a fraction of the screen's resolution, so team cards looked pixelated. We captured each pet's real portraits (every mutation) once on a PC, packed them into sheets and show stills on phones.
- **Narrow info panels.** In shop-style windows the card row takes about 64% of the width and the info panel about a third. If the info doesn't fit, it scrolls with the buy button pinned.
- **A calm HUD.** On phones the task card draws at 80% and the team bar at 70%. Only one pop-up shows at a time. A first-time player sees almost nothing until the tutorial ends (see the onboarding guide).
- **Sales banners.** Featured offers get painted scenes, rays, a rainbow or glowing frame and a big mascot pet. Plain window UI stays calm so the banner stands out.

**The same kit in other games.** Axel's phone driving controls and +1 Mecha Cyborg's HUD both reuse the Battle for Pets kit (outline, lip, studs, press behavior) with their own art and fonts.

## How we did it

This is the order we settled on after redoing a lot of screens.

1. **Collect references and say what you like.** We sent screenshots of three games whose UI we liked (a studded lime HUD, a shop window with a Featured banner, a card shop with colored item cards). We told Claude it was for style only and nothing would be copied. Claude broke each reference into techniques: what has an outline, where studs go, how cards are ordered, how big the readout is.
2. **Audit what exists.** Before inventing a new look, we had Claude screenshot the current screens and list what made them look generic: five shape languages on one screen, mixed icon styles, inconsistent outline widths, everything shouting at once, clipped text, uneven spacing, color without meaning. That list became the guide's rules.
3. **Write the style guide as a note.** Colors as hex, the type scale in pixels, the anatomy of a window and a card, the states (hover, pressed, disabled, selected, locked, ready, claimed), and the Roblox build notes. The guide is the file Claude reads before any UI task.
4. **Mock four options of the choices that matter.** For the first restyle we mocked the same screens four ways (ink color, font, title bar shape) next to the current look, plus a phone sheet. We picked one option and approved a small icon sample sheet before drawing 70 icons.
5. **Build the foundation first.** One session wrote the style module (tokens, text, face, raised button, card, ring, studs, scroll bar, readout). The HUD was the first visible result. Then one screen group per session, each reviewed before the next. Old screens kept working until their turn.
6. **Check every screen at five sizes.** Desktop, a phone held sideways, a phone upright, a gamepad, and Reduced Motion. We used Studio's device emulator and a Play session, and took screenshots.
7. **For new pieces, mock first in Figma.** From late October we mocked every new UI piece (2 to 4 labelled options as editable layers over a real game screenshot, at the element's real proportions). We said: "start using Figma from now on to create the UIs." Plain layout fixes we could describe exactly ("move it to the top") went straight in.
8. **Ship the reviewed option, then polish only what's visible.** We stopped when it looked right in the game, not in the mock.

**How we worked with Claude on UI:**

- First message of a UI task: "Don't build. Mock 3 options over a real screenshot." Claude read the style guide and the closest existing screen first.
- Screenshots were the language. We pasted our own phone screenshots when something looked wrong ("this pop-up sits over the bottom HUD and feels annoying") and Claude fixed from the picture.
- When Figma's monthly tool-call limit ran out (it does on a free-tier seat), Claude drew the same mock as an image with a short Python script (Pillow or an SVG rendered in a headless browser) from the same layout numbers. Sharper mocks, and the same drawings could become the game's images.
- Motion can't be judged in Figma. For the juicy-motion round Claude built a small animated page with the same window two ways, and we picked from that.
- When we sent a UI tutorial and three short videos of a polished simulator UI "to help train your UI skills", Claude pulled key frames with FFmpeg, broke the button down layer by layer and wrote a note sorting each idea into "already ours", "new and fits" and "clashes with what we picked before (ask first)".
- Claude checked our approved decisions (in memory and task logs) before every mock, so rejected looks didn't come back.

## Rules we follow now

1. **Mock first, build the pick.** We stopped a hotbar build halfway ("make a mock for me so I can see it before") and turned down two health bars that were built straight into the game. Judging looks needs pictures. Exception: exact layout fixes.
2. **2 to 4 options, side by side, with the current look.** A single mock gets a yes or no. Four labelled options get a real choice and teach Claude our taste.
3. **Make it cleaner than you think.** An update card we first built by reusing a busy look (dark studded wells, rays, three near-identical icons, a long subtitle) read as cluttered. We asked for "a lot cleaner and better". Aim for less text, one clear focal number per idea, fewer boxes.
4. **It must not look "AI made".** On Axel's first phone controls mock (flat circles with words in them, plain gray squares, unrelated colors) we said it "looks very AI made and UI is super important in games". What passed: one button family (candy domes with an ink rim, gloss and a lip), drawn icons instead of words, color by job, and meters that mean something. Zoom into your icons before showing them: a brake pedal read as a microphone, then a kettlebell. A brake disc worked.
5. **Color means something.** A window's color, an item's rarity, a button's job. Rainbow is only for featured or top-prize things.
6. **Studs are for the base only.** We said studs are "good for a base, but leave it at that." Reference banners have none. Put them on window bodies and wells.
7. **Use Roblox's real stud texture, not a drawn one.** Our drawn stud tile was called "very bad" next to the real one. See the gotchas for how to get it into a GUI.
8. **Sales banners must be flashy.** A flat studded card read as plain, so featured banners got a painted themed scene, rays, a glowing frame, a big pet, sparkles and shine. Mock it, then hold it to the quality bar.
9. **Genre-standard screens copy the genre's layout.** In +1 Mecha Cyborg we mocked three original HUD layouts in our own style and they were all turned down: "No, make it exactly like this" (a screenshot of a top game's HUD). We built that layout with our own icons and names and it went in with no further round. Ask for a reference before you mock a screen players already know (HUD, rebirth, shop). Never use another game's assets.
10. **Item icons are 3D renders.** We tried 2D drawings for everything. Comparing the two on a mock, we picked 3D ("it looks a lot better") and switched back. Interface symbols and stats can stay 2D. A new item gets a model and a render, not a drawing.
11. **Real mutation on the pet, on phones too.** Blender pictures with a glow or a recolor were turned down. Players want to see the real thing, so the captured portraits show each mutation.
12. **Reward moments get a card.** A line like "+150 XP, Dog reached Lv 4!" was turned down ("make the UI look better, it's not great right now"). Give every reward or progress moment a small designed card from the start.
13. **Narrow info panels.** Card row about 64%, info panel about a third. We had to ask twice because a second window widened its panel to fit. Scroll the contents; don't grow the panel.
14. **American spelling on screen.** Color, favorite, center. One Gear tab labelled "Colours" was corrected. Code comments and notes can stay as they are.
15. **Robux prices use Roblox's own icon and live price.** Never draw our own Robux icon. Cards ask Roblox for the player's price.
16. **Show the odds on anything paid and random.** The reference we studied had a lucky block with its contents listed. Our featured pet banner lists every chance on the card.
17. **One pop-up at a time on the HUD.** Offers, banners, tips and event pills wait for each other. A pop-up covering the bottom HUD on a phone was the most common complaint. A pop-up that sold almost nothing was removed.
18. **A genre font can be changed in one place.** In one game we switched the whole font after a sheet of four options. Because every label read its font from the kit's modules, the switch was a few lines in a few files. Keep fonts in the kit, never in individual labels.
19. **Reduced Motion turns all the motion off.** Floating gains, spinning rays, bounces, gleams.
20. **Motion marks what matters.** Plain cards stay still. Only featured, ready, for-sale and selected things move.

## Mistakes and gotchas

**One UIStroke per object.** A GuiObject with two UIStrokes (an ink outline plus a yellow selection stroke) draws only one. The second replaced the outline. Fix: the selection ring is its own transparent child frame, 3 px bigger on each side, with its own corner and stroke.

**CanvasGroup makes UI blurry.** CanvasGroup draws its children into a lower-resolution texture. Our area banner was a CanvasGroup so it could fade; players said it looked "a little blurry, like 720p". Fix: a plain Frame that slides in and out. Use CanvasGroup only for short effects where softness doesn't matter.

**9-slice breaks slanted art.** `ScaleType.Slice` mis-draws skewed plates: our slice spanning the full image height drew the plate far too tall and cut off its outline, and adding top and bottom borders put kinks in the slanted ends. What works: `ScaleType.Stretch` at a fixed design size (scale the whole widget with a UIScale). For a bar whose fill changes width, put a full-size fill image inside a clipping frame sized to the value. A rotated gradient step looks like a slanted cut but does not map 1:1 to the value on wide bars. Test a new piece at real size in Studio before building on it.

**Roblox has no Overlay blend.** The stud texture in Roblox's install is mid-gray, made for Overlay. A GUI can't blend that way. Fix: bake two layers from it, a white layer (what Overlay would lighten) and a black layer (what it would darken), and tile them over the panel at low opacity. We later softened the opacity because the first strength was loud.

**A freshly uploaded image can draw blank for a while.** Uploads take minutes to process and sometimes much longer to load in Play. Check `ImageLabel.IsLoaded` before judging a screenshot. Never hide an ImageLabel until it loads: Roblox only loads images that are drawn, so a hidden one stays unloaded. Let it draw blank (or show the card's color) and fade in.

**Phones draw ViewportFrames at low resolution.** 3D models in cards look pixelated on phones. Fixes: static renders for icons (zero cost), captured picture sheets for pets, and keeping live 3D only on desktop.

**Scale by the shorter side.** Our HUD scaled by screen height, so a phone held upright (tall) got a HUD 1.6 times bigger than the same phone sideways, and things ran off the edge. Fix: scale by `min(width, height)`. See the snippet below.

**Roblox's top bar pushes the screen down.** About 58 px on a phone. A layout planned for the full screen ran into it. Plan phone layouts on the area below the top bar. In Axel the standings list ran into the steering buttons for the same reason.

**A ScreenGui that was never shown reports a default size** (800 by 504 on our machine). A card that works out its layout before it shows picked the landscape layout on every first show, so an upright phone saw tiles cut off at both sides. Fix: read the camera's view size until the card is up, then fit again after it pops in.

**Fit once is a bug.** A card that sized itself once didn't follow a screen or scale change. Refit whenever the viewport or the HUD scale changes.

**Cards that don't fit upright.** A wide card on an upright phone shrank to a third of its size with unreadable words. Give portrait its own layout (smaller title, subtitle on two lines, tiles in rows) instead of scaling the landscape one.

**ZIndex differs between ScreenGuis.** A test ScreenGui made with `Instance.new` layers by global ZIndex while the game's HUD layered by sibling. Match `ZIndexBehavior` before judging a copy of your module. Also, pieces of the HUD live in different ScreenGuis, so look another piece up in its own ScreenGui.

**A button's `Visible` isn't enough.** A hidden panel's child button still reports `Visible = true`. A highlight tracking it drew an empty glow over the hotbar. Check every ancestor and the ScreenGui.

**Parallel sessions edit shared UI files.** Another Claude session was changing the same HUD file. Check `git status` and what else is running before touching shared HUD modules.

**The MCP can't click an emulated phone.** Claude's clicks in Studio don't reach Studio's device emulator, and gamepad presses don't register. Claude checks layout from values and screenshots; we test touch and controller by hand. Say so honestly in the task log ("checked in code, not on a real phone").

**Figma limits.** A free-tier seat gives about 20 tool calls a month. One session burned them in one mock round. Put everything into one build call (page, components, placeholders), use the one-call screenshot, and fall back to local renders.

**Drawn shop icons need to be read at 40 px.** Each icon must read as a silhouette small, use at most four colors plus ink, and carry no text except `$`, `2x` and `?`.

**Don't ship the first version of a busy card.** Both the economy update card and the first Axel controls were redone after "this looks too busy" and "this looks AI made". Mock at production quality from round one.

## Code snippets

All names are generic. `LIGHT` and `SHADE` are your two uploaded stud layers (`rbxassetid://YOUR_ID`).

### 1. A panel with an ink outline and baked studs

```lua
local INK = Color3.fromHex("111018")
local STUD_PITCH = 22 -- design pixels per stud

local function studLayer(panel: GuiObject, image: string, transparency: number, radius: number)
	local layer = Instance.new("ImageLabel")
	layer.BackgroundTransparency = 1
	layer.Image = image
	layer.ScaleType = Enum.ScaleType.Tile
	layer.TileSize = UDim2.fromOffset(STUD_PITCH * 2, STUD_PITCH * 2) -- the texture holds 2 x 2 studs
	layer.ImageTransparency = transparency
	layer.Size = UDim2.fromScale(1, 1)
	layer.ZIndex = 0
	Instance.new("UICorner", layer).CornerRadius = UDim.new(0, radius)
	layer.Parent = panel
end

local function studPanel(parent: Instance, color: Color3, radius: number): Frame
	local panel = Instance.new("Frame")
	panel.BackgroundColor3 = color
	panel.BorderSizePixel = 0
	Instance.new("UICorner", panel).CornerRadius = UDim.new(0, radius)
	local stroke = Instance.new("UIStroke") -- the ONE stroke this object gets
	stroke.Color, stroke.Thickness = INK, 3
	stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
	stroke.Parent = panel
	studLayer(panel, LIGHT, 0.68, radius) -- white above the gray: lightens
	studLayer(panel, SHADE, 0.58, radius) -- black below the gray: darkens
	panel.Parent = parent
	return panel
end
```

### 2. A pressable button: face on an ink lip

```lua
local TweenService = game:GetService("TweenService")
local LIP = 4

local function raisedButton(parent: Instance, color: Color3): (TextButton, Frame)
	local holder = Instance.new("TextButton") -- invisible hit area
	holder.Text, holder.BackgroundTransparency = "", 1
	holder.Parent = parent

	local lip = Instance.new("Frame") -- dark copy, 4 px lower
	lip.BackgroundColor3 = INK
	lip.Size = UDim2.new(1, 0, 1, -LIP)
	lip.Position = UDim2.fromOffset(0, LIP)
	Instance.new("UICorner", lip).CornerRadius = UDim.new(0, 10)
	lip.Parent = holder

	local face = Instance.new("Frame")
	face.BackgroundColor3 = color
	face.Size = UDim2.new(1, 0, 1, -LIP)
	face.ZIndex = 2
	Instance.new("UICorner", face).CornerRadius = UDim.new(0, 10)
	face.Parent = holder

	local scale = Instance.new("UIScale", holder) -- hover 1.04 with a little overshoot
	local quick = TweenInfo.new(0.12, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
	holder.MouseEnter:Connect(function() TweenService:Create(scale, quick, { Scale = 1.04 }):Play() end)
	holder.MouseLeave:Connect(function() TweenService:Create(scale, quick, { Scale = 1 }):Play() end)
	holder.MouseButton1Down:Connect(function() face.Position = UDim2.fromOffset(0, LIP) end) -- sinks onto the lip
	holder.MouseButton1Up:Connect(function() face.Position = UDim2.fromOffset(0, 0) end)
	return holder, face
end
```

### 3. A selection ring as its own frame

```lua
-- A second UIStroke on the card would replace its outline, so the ring is a child frame.
local function selectionRing(card: GuiObject, radius: number, color: Color3?): Frame
	local ring = Instance.new("Frame")
	ring.BackgroundTransparency = 1
	ring.Size = UDim2.new(1, 6, 1, 6) -- 3 px bigger on every side
	ring.Position = UDim2.fromOffset(-3, -3)
	ring.ZIndex = 9
	Instance.new("UICorner", ring).CornerRadius = UDim.new(0, radius + 3)
	local line = Instance.new("UIStroke")
	line.Thickness = 4
	line.Color = color or Color3.fromHex("ffd23f")
	line.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
	line.Parent = ring
	ring.Parent = card
	return ring -- set ring.Visible when the card is selected
end
```

### 4. One HUD scale for desktop and phones, by the shorter side

```lua
-- Design the HUD for a 720 px tall screen and let one UIScale do the rest.
local function hudScale(): number
	local view = workspace.CurrentCamera.ViewportSize
	local short = math.min(view.X, view.Y) -- NOT view.Y: an upright phone is tall
	if short >= 600 then
		return math.min(short / 720, 1.5)
	end
	return math.max(short / 600, 0.6) -- phones: gentler
end

local function smallScreen(): boolean -- cards that draw smaller on phones
	return math.min(workspace.CurrentCamera.ViewportSize.X, workspace.CurrentCamera.ViewportSize.Y) < 600
end

local uiScale = Instance.new("UIScale", hud) -- hud = your root ScreenGui's top frame
local function refit() uiScale.Scale = hudScale() end
workspace.CurrentCamera:GetPropertyChangedSignal("ViewportSize"):Connect(refit)
refit()
```

### 5. Gleam and burst, switched off by Reduced Motion

```lua
local GuiService = game:GetService("GuiService")
local TweenService = game:GetService("TweenService")

local function reduced(): boolean
	local ok, value = pcall(function() return GuiService.ReducedMotionEnabled end)
	return ok and value == true
end

-- holder needs ClipsDescendants = true. A white band sweeps across in 0.8 s, then waits 2 s.
local function gleam(holder: GuiObject)
	if reduced() then return end
	local band = Instance.new("Frame")
	band.BackgroundColor3, band.BackgroundTransparency = Color3.new(1, 1, 1), 0.7
	band.BorderSizePixel, band.Rotation = 0, 20
	band.AnchorPoint, band.Size = Vector2.new(0.5, 0.5), UDim2.fromScale(0.22, 1.6)
	band.Parent = holder
	task.spawn(function()
		while holder.Parent do
			band.Position = UDim2.fromScale(-0.2, 0.5)
			local sweep = TweenService:Create(band, TweenInfo.new(0.8), { Position = UDim2.fromScale(1.2, 0.5) })
			sweep:Play()
			sweep.Completed:Wait()
			task.wait(2)
		end
	end)
end

-- 12 copies of an icon fly out evenly from the button and fade in 0.6 s.
local function burst(button: GuiObject, image: string)
	if reduced() then return end
	for i = 1, 12 do
		local angle = (i / 12) * math.pi * 2
		local piece = Instance.new("ImageLabel")
		piece.BackgroundTransparency, piece.Image = 1, image
		piece.AnchorPoint, piece.Size = Vector2.new(0.5, 0.5), UDim2.fromOffset(28, 28)
		piece.Position = UDim2.fromScale(0.5, 0.5)
		piece.Parent = button
		local goal = { Position = UDim2.new(0.5, math.cos(angle) * 90, 0.5, math.sin(angle) * 90), ImageTransparency = 1 }
		local tween = TweenService:Create(piece, TweenInfo.new(0.6, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), goal)
		tween.Completed:Once(function() piece:Destroy() end)
		tween:Play()
	end
end
```

## Prompts that worked

**1. Mock first, with options (our style, as used for new UI pieces).**

```text
Don't build anything yet. Read notes/UI Style Guide.md and look at how the
Gear shop window is built. Then make 3 labelled mock options for a new
"boss unlocked" card, as editable Figma layers over a real screenshot of the
game, at the card's real proportions. Show what it unlocks (a new area, a level
cap, capsules, a title). Aim for less text and one clear focal idea. Tell me
which option you'd pick and why, then wait.
```

**2. Fix from my own screenshot (based on our phone fixes).**

```text
Here are four screenshots from my phone. The pop-up covers the bottom HUD, the
money chip covers the training panel and the crafting window looks confusing.
Don't fix yet. List what's wrong in each, give me 2 or 3 options per problem
as mock images using my screenshots, and recommend one. Keep the 3D models on
the cards.
```

**3. Learn from a reference (example, in our style).**

```text
I'm sending three short videos and a tutorial of a polished simulator UI. Pull
key frames with FFmpeg and break the button down layer by layer with colors.
Then sort every idea into: already in our style guide, new and fits, or clashes
with what I picked before. Don't change the game. Write the breakdown into
notes/UI References.md. What questions do you have for me?
```

## Do it in your game

1. **Collect two or three screenshots of UI you love and one line on what you like.** Ask Claude to audit your current screens against them and list what makes yours look generic. Fix the list before you design anything new.
2. **Write the style guide as a note** (the hex colors, the one outline width, the type scale, what each state looks like, the order inside a card). Put "read the UI Style Guide before any UI task" in your CLAUDE.md.
3. **Build one style module in code**, with the panel, button, card, ring and text from the snippets, and the scale rule. Make every new screen from it. Start with one screen (your HUD) and review it before the next.
4. **Mock before building.** For each new piece ask for 2 to 4 options over a real screenshot (Figma if you have calls left, a local image if not). Pick one. Build only that one.
5. **Check on a phone every time.** Device emulator sideways and upright, and Reduced Motion. Put screenshots in the task log and say what you couldn't check.
6. **Add motion last and sparingly** (gleam, burst, wiggle on things that matter), with Reduced Motion off-switch from the start.

Starter prompt:

```text
I'm building a Roblox game: [fill in: one sentence about your game].
I want my UI to look hand-designed, not generic, the way Battle for Pets does
it: one style guide note, one UI style module in code (outlined text, panels
with studs on the base only, buttons on a lip, color by meaning), mocks first
with 2 to 4 options, a HUD that scales by the screen's shorter side, and
checks on phone and controller. Don't build yet. First read my project notes,
then look at my current screens and tell me what makes them look generic.
Then write a short UI Style Guide note for me to approve. My references are:
[fill in: screenshots or links].
What questions do you have for me so I can help you implement this feature as best as possible?
```
