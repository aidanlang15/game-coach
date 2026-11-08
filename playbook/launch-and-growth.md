# Launch and growth: getting players

How we tried to get players into Battle for Pets: playtests, thumbnails and icons, Roblox ads, TikTok, Discord, update announcements, redeem codes and analytics. Mostly from Battle for Pets. This is what we did and learned, not a promise that it works for you. Results are given as comparisons, not numbers.

## What we built

- **A launch and growth plan** (a note). It started with a candid review: strengths, risks, what a good first test looks like. The user then decided what to do about each risk and what NOT to do.
- **A private playtest first.** A game-entry whitelist in code (user IDs allowed in, everyone else kicked), a small Discord for testers, a form channel to ask for access. Then the whitelist was switched off for the public launch.
- **A Discord server** with three channels visible to newcomers (welcome, how to playtest, request access), update channels (announcements, changelog, known issues) and tester channels (bug reports as a forum with tags, feedback, chat, clips). Roles for developers and testers. A bot gives new members a basic role automatically.
- **Store page art:** a game icon, a logo, and a set of 16:9 thumbnails, all made with AI image generation plus our own lettering.
- **Roblox ads:** several campaigns with different creatives (ad pictures), compared against each other.
- **TikTok:** one or two short videos a day, plus a `/social-video` Claude skill that planned, filmed (in a Studio playtest), cut and captioned them.
- **Update routine for every big update:** a What's New pop-up when players join, a redeem code, a short teaser post in Discord, and an update video.
- **Analytics:** server-side funnels, journeys, economy events and custom events in the Creator Hub, plus a small local dashboard that pulls the same data through the Analytics Query API.
- **A first-minute rebuild** (after the data said new players left early): the guide meets you at spawn, a quiet HUD until the tutorial ends, a first battle you can't lose.

## How we did it

1. **Review before promotion.** Before spending any effort on marketing we asked Claude for an honest, critical review of the game and wrote the answer into a note: where would new players drop off, what is weak compared with the games we copy the hook from. The user decided which risks to fix. We set early targets from Roblox's own guidance (a good day-1 return, a good first-session length) so we knew what "bad" looked like.
2. **Recruit testers and watch them play without helping.** Testers joined the Discord, posted their Roblox name, we added their IDs to the whitelist, then published (a live server only enforces the new list after a publish, so batch the requests). Testers are never admins.
3. **Fix what testers show.** A tester with a huge pile of cash and nothing to buy led to the economy rebalance (see the economy guide). Another struggling for a rare material led to better drop odds. Their complaints went into notes as decisions.
4. **Make the store page.** Icon, logo, thumbnails, then a game-page trailer under Roblox's rules (real gameplay only, no voice-over, no promotional claims). See the AI image workflow below.
5. **Launch and run ads.** Several campaigns at once so creatives could be compared. Judge new creatives by click-through first, because cost per play looks bad for two days until plays arrive.
6. **Read the data every few days.** We built funnels for onboarding, the first 30 minutes, the road after the tutorial, boss progress, battles and the Robux shop. When a number looked bad we asked Claude for "a plan for the first problem", not for everything.
7. **Fix the first minute.** The data said most new players leave early. We picked one problem (the first 60 seconds), planned it in two question rounds with the user, and shipped it as one update.
8. **Ship updates on a rhythm** with the update routine (What's New, code, teaser, video).

**How we worked with Claude:**

- **Claude does the research and the numbers; the user decides.** Claude read dashboards in Chrome, pulled API data, wrote the plan notes and ran statistics (for example, comparing ad click rates), and the user chose.
- **The user makes the pretty things; Claude supports.** For thumbnails and icons the user makes the image in an AI image tool and Claude writes the prompts, renders reference pictures of real models, letters the final art and checks the facts (see below).
- **Claude never posts.** No uploads, posts or messages go out without the user.
- **One kind of video per run**, with notes read first (the skill lists what to read).

## The AI image workflow (thumbnails and icons)

We tried three ways before this worked. The user turned down all three:

- Flat vector "sticker" mock-ups.
- Cinematic Blender scenes with a home-made blocky avatar ("so bad", "make it look less AI").
- Split-screen thumbnails built from our own Studio renders after a tutorial video about "high CTR" thumbnails. "The ones you made are not good."

What worked:

1. The user collects reference thumbnails from popular games: glossy painted 3D, a bright baseplate or painted scene, a Roblox avatar with bacon hair from behind.
2. **Claude writes a ready-to-paste prompt per image**, and always asks for no text in the picture.
3. **Claude renders reference pictures of the real models** (plain blue-grey background so white pets show up) into a folder per image.
4. The user generates the picture in a normal chat with an image model, attaching those references.
5. **A small finishing script** crops to 1920x1080 (icons 512 and 1024), adds the lettering in the game's logo font, exports under Roblox's size limit and makes a contact sheet.
6. **Check small.** The contact sheet shows every image at thumbnail and phone sizes on light and dark backgrounds. If you can't read the subject at 160 pixels wide, redo it.
7. **Fact-check every number the image tool wrote.** It invented an absurd income number on one picture. The real number was far smaller and impossible values break Roblox's rules on misleading metadata. We erased it and lettered the true one. The user asked once to keep the bigger number; we advised against it.

## What we learned from ads (as comparisons)

- **What gets clicked:** the best ad showed the player's own point of view (an avatar seen from behind or over the shoulder) doing something to a pet, in bright colors, with little or no text. Two ads with that composition beat our earlier favorite, which was riding a mount. Mount riding beat generic "catch pets" and "beat bosses" pictures. Small-number hook thumbnails ("1 in 10,000") did worst early.
- **Test several creatives at once** in the same campaign, and wait until the data stops being tied. An early tie between creatives is common.
- **Ads can't be edited the way you expect.** Age targeting cannot be changed after a campaign is published; you must make a new campaign.
- **Campaigns can get rejected for a while** ("the experience has been suspended") even when the game is fine. One came back by itself. Wait a day or two before rebuilding.
- **Reporting is late.** Plays can arrive up to two days after clicks, and ads count plays by people who only saw the ad. Don't judge cost per play on a fresh creative.
- **Players follow spend.** Concurrent players followed ad spend almost directly. When ads paused, players fell back to the small organic base. Know your organic base before you judge an update.
- **Know who else touched the campaigns.** The editor history doesn't say who paused a campaign. With a partner, agree who runs ads.
- **A new-game audience gate cost us reach for a while.** For a couple of days the game page said "available to players ages 16+" under Roblox's Kids and Select trial, which explained weak click-to-play. Roblox lifted it by itself and it was probably a glitch. If your ads suddenly do badly, check Manage audience first. We never paid for an expedited review (a large Robux deposit); don't do it without thinking hard.

## How Roblox discovery works (what we read and kept)

From Roblox's own discovery docs (as of late October 2026, check for updates):

- **Recommended For You** (the home page) ranks games on averages **per player**, only for players who found the game there organically. Players from ads, friends, search and social media aren't counted in that ranking. So ad traffic does not drag your ranking down, but it also doesn't build it.
- It looks at three windows: day 1, days 2 to 7, days 8 to 28.
- **Most important signals:** play-through rate (do people who see the thumbnail play), first-play bounce (leaving in under a minute or three is negative), play days per player (do they come back), and playtime per player. Also counted: co-play with friends, qualified sessions, days with spending, and Robux per player.
- Ads, search and friends help the **retrieval** stage: getting you considered. Then Roblox tests the game on small recommended groups.
- Early on, our game got almost no home recommendations at all. That is normal for a new game.
- **So the levers are:** the thumbnail and icon (play-through), the first one to three minutes (bounce), and reasons to return on more days (daily rewards, gifts, codes, events).
- **Never re-upload the game to reset stats.**

## TikTok and short videos

We posted on one account several times a week before and after launch. What we saw:

- **Every post lost most viewers at second one.** The first second is the whole video.
- **Short ones were watched to the end more often.** Average watch time was about the same whatever the length, so aim for 10 to 20 seconds unless the story needs more.
- **Asking for comments works.** The three posts asking for feedback, playtesters or admins had the most comments, and two of them were the most viewed.
- **Search matters.** A real share of one post's views came from people searching phrases like "new roblox game". Put those phrases in the description.
- **Pre-launch hype posts out-performed post-launch posts.** Views were higher before launch (playtester calls to action) than after.
- **The videos Claude made did worse than the median.** Our scripted videos (a launch video, update teasers, a devlog) came out "clearly ai made, not high quality, and just aren't attractive" (the user, Nov 7). They had burned-in text, an AI voice, dark scenes and smooth fly-by camera moves.
- **What the user wants instead ("look human"):**
  - Someone actually playing: the normal third-person camera behind the avatar, moved by the user's mouse.
  - Bright and readable: daytime or snow, the avatar big in frame. No dark night scenes.
  - Creator voice in the text, like a person ("a SECRET pet just fell from the sky in my game"), never ad copy ("UPDATE JUST DROPPED!").
  - Real sound: a trending TikTok sound or the user's own voice.
  - Clean footage with no burned-in text; the user adds TikTok's own text and sound.
  - Keep one motion whole. Don't jump-cut inside run, battle, attack. Skip a second pet's fight with the HUD showing and go straight to the catch.
- **The split of work now:** Claude picks the moment, writes the text line, stages the game (admin tools, boosts, spawns), runs the recorder and trims at real speed. The user plays the take (5 to 10 minutes) and finishes it in TikTok.
- **Earlier lessons from our video skill:** open on the best moment (a rare catch, a boss hit, a mount) not a slow orbit; action over scenery; short punchy camera moves; no POV or meme formats; the same voice for the same kind of video (voiceless for hype montages, a voice for "what we added" overviews).
- **Gotchas:** start the recorder before you press the prompt; check the raw recording size before cutting; hide cursors, event bars and name tags in battle shots; Studio stops drawing when another window covers it, so films need the user hands-off; level sound effects against the voice.

## Discord, updates, codes

- **Every big update gets four things:** a What's New pop-up when you join (once per player per update, 3 to 5 highlights with pictures), a new redeem code, a short teaser post in Discord, and a video.
- **Every update announcement includes a code.** The user's rule: "for every update we do, add a code in the announcement, and I'll also task you with putting the codes into the game." Codes bring players back and push them to Discord. The code must be in the published game before the post goes out.
- **Code rewards are helpful, not progress-breaking:** boosts, capsules, a few minutes of the player's own income. Never flat huge cash.
- **Codes are server-only** so an unposted code stays secret. Each code works once per player, typed in any case.
- **Announcements are short teasers.** Our first Starfall post listed every feature, odds and prices. The user rejected it: "make the announcement less descriptive, let the players explore the update themselves". Now: a title, about five one-line teases with an emoji each, one hint to look for something in game, the code line exactly, and the game link.
- **Example post shape (invented content):**

```text
# THE WINTER UPDATE IS LIVE!
Something is happening at the top of the mountain...
A new area to explore
A new boss waiting at the end
8 new pets to find
A new mutation
Check the sky at the half hour

CODE: SNOWDAY -> 3 Starter Capsules + a 2x Money boost (Shop -> Codes)
[game link]
```

## Analytics: what we log and how we read it

- **Roblox only records events from the published game, never from Studio.** Data shows up about a day later. Retention and acquisition pages lag about two days; funnels are close to live.
- **Funnels (one-time):** Onboarding (each tutorial step), First 30 Minutes (by total time played), After Tutorial (team of 3, pet level 5, pet level 10, first boss...), Boss Progression, Tasks.
- **Funnels (repeating):** Boss Fights (opened card, started, won), Robux Shop (opened, buy pressed, purchased) with the source of the offer as a field, Wild Battle (started, won, threw capsule, caught).
- **Journeys** (a funnel with choices) show what new players pick first after the tutorial, drawn as a flow chart. We only enter players who finish the tutorial after the journey exists, so old players don't pollute it.
- **Economy events** log every cash gain and spend with a source and sink name, so you can see where money comes from and goes.
- **Custom events:** time to finish the tutorial, early leaves (minutes played, where they were, what they were doing, which tutorial step), boost use, tip shown, daily reward claimed, load time.
- **EarlyLeave is "the closest thing to asking players why they left."**
- **The client only reports that a window opened**, through one remote that accepts a short list of known names, rate-limited. Everything else is logged from the server.
- **Our local dashboard idea:** a small Python app (standard library plus one web page) that calls the Analytics Query API and shows funnels, retention, sources and ad payback next to our notes. A `/advice` command in that folder writes researched reports. The API key lives in a private `.env` file and is never printed.
- **Read the chart, not the headline.** Creator Hub averages count pre-launch zero days, so "average players" looked far lower than the real daily line.
- **A later review found the real issue**, which no single dashboard showed: half of new players left in the first minutes. We fixed one thing, the first minute.
- **A lesson about our own logging:** a mid-battle quit was counted as "exploring" because the battle ended before the activity was read. Our first review claimed players rarely quit during battles. A later cut of the data showed otherwise. Check that your events measure what you think they measure.

## Rules we follow now

- **Judge a creative by click-through first, then by plays.** Reason: plays arrive late.
- **Ask who changed a campaign** before blaming the game for a drop.
- **Use AI for images, not Claude-rendered thumbnails.** Reason: the user rejected render-based thumbnails three ways. Claude writes prompts, renders references, letters, and fact-checks.
- **Never put a made-up number on art.** Reason: Roblox rules and trust.
- **Videos: played takes, bright, creator voice, TikTok's own finish.** Reason: the scripted videos did poorly.
- **Update posts: short, vague, with a code.** Reason: players like discovering the update, and codes bring them back.
- **Every big update gets a What's New card.** Reason: players joining after an update should see it at once.
- **Fix one thing from the data at a time.** Reason: we kept getting asked for everything; one plan per round shipped.
- **Check the audience gate and the organic base before judging ads or an update.**
- **Late-game content isn't growth.** The review found that most recent work was late-game content only a few percent of players reach. New players are won in the first minutes.

## Mistakes and gotchas

- **Marketing before the first minute was good.** Ads brought players who left early. Do the first-minute fix before you buy lots of traffic.
- **Misreading "spawn rates".** Players asked for better spawn rates; Claude raised the number of pets. The user meant rare odds. When players complain, ask which they mean.
- **A tester skip button hid onboarding data.** Many players skipped the tutorial at step one, so the funnel looked like a quit. We removed Skip on first runs.
- **Funnel API quirks:** funnel and custom event names are filter-only in the Analytics Query API, rates come back as fractions, bursts of requests get a rate limit (retry), and one-day granularity is rejected for funnel metrics (query one window at a time).
- **Image upload quirks:** a new texture can show in Edit before Play loads it; Roblox loads an image only while its label is visible.
- **Dashboard uploads** of store images need a real browser with file upload (Claude in Chrome), not a built-in preview browser.
- **A glitch is sometimes a glitch.** The 16+ gate came and went. Waiting a day or two worked.

## Code snippets

### 1. Log a funnel step and an economy event from the server

```lua
local AnalyticsService = game:GetService("AnalyticsService")

local function logStep(player, funnel, sessionId, stepNumber, stepName, fields)
	-- pcall: analytics must never break the game
	pcall(function()
		AnalyticsService:LogFunnelStepEvent(player, funnel, sessionId, stepNumber, stepName, fields)
	end)
end

-- one-time funnel: pass nil as the session. Repeating funnel: one session id per attempt.
logStep(player, "Onboarding", nil, 3, "First Win")
logStep(player, "RobuxShop", HttpService:GenerateGUID(false), 1, "Opened", { [Enum.AnalyticsCustomFieldKeys.CustomField01.Name] = "Shop" })
```

Why: names in code must match the names you set in the Creator Hub exactly, and funnels only record from the published game.

### 2. A server-only redeem code, once per player

```lua
local Codes = {
	-- keys upper case, no spaces; the table lives in a server-only script
	SNOWDAY = { grant = { capsules = "Starter", count = 3, boost = "DoubleMoney", amount = 1 }, ends = nil },
}

local function redeem(player, typed)
	local key = string.upper(string.gsub(typed, "%s", ""))
	local code = Codes[key]
	if not code or (code.ends and os.time() > code.ends) then
		return false, "Code not found"
	end
	local profile = PlayerData.get(player)
	if profile.codes[key] then
		return false, "Already redeemed"
	end
	profile.codes[key] = os.time() -- record first, same save as the reward
	PlayerData.giveReward(player, code.grant)
	return true, "Redeemed!"
end
```

## Prompts that worked

**1. The review prompt:**

```text
Give me an honest, critical review of the whole game: what to add, change or
remove, how to keep new players, how to make it look and play better. Use the
notes, the code and the last 28 days of analytics. Rank the problems. Don't
change anything yet.
```

**2. Pick one problem and plan it:**

```text
Let's plan fixing problem 1 (the first 60 seconds). Ask me your questions in
two rounds, with a default for each so I can answer "d". Write a plan note with
the build order. Don't build yet.
```

**3. Example (our style, for image prompts):**

```text
I want a game icon in the style of the reference thumbnails I attached (glossy
painted 3D, bright sky, my avatar seen from behind throwing a capsule at a
giant pet). Write me a prompt I can paste into an image tool, ask for no text
in the picture, and render reference pictures of the real pet models so I can
attach them. Then tell me what the final art needs from you (lettering, sizes).
```

## Do it in your game

1. **Make the first minute good before you promote.** Play your own game as a new player with a stopwatch. What happens in the first 60 seconds? Write it in a note.
2. **Add the logging early.** An onboarding funnel (each tutorial step), a first-30-minutes funnel and an early-leave event. Publish and read them in the Creator Hub after a day.
3. **Run a small playtest** with a whitelist and a Discord. Watch without helping. Put what you see into notes as decisions.
4. **Make the store page**: an icon and three thumbnails with your own AI-image workflow and a contact sheet to check small sizes. Fact-check numbers in the art.
5. **Start tiny ads with two or three creatives** and compare click-through. Learn how late the data arrives before drawing conclusions.
6. **Pick a rhythm for updates**: What's New card, a code, a short teaser, a video of played footage. Keep it the same each time.

Starter prompt:

```text
I'm building a Roblox game: [fill in: one sentence about your game].
I want to plan how to get my first players, the way Battle for Pets did it:
read my game first, review my first minute and my store page honestly, plan
analytics funnels for the tutorial and first 30 minutes, set up a small
playtest with a whitelist and Discord, and make an update routine (What's New
card, code, short teaser post). Don't build anything yet. Write a plan note
with the order and what we are NOT doing.
What questions do you have for me so I can help you implement this feature as best as possible?
```
