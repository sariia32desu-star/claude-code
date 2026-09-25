# Vampire Girl: Beauty Obsessed Overhaul, Session Handoff

This document is self-contained. It replaces the original overhaul doc (`Beauty_Obsessed_Overhaul.md`), which the next session does NOT need. The only other document required is the style guide (`VampireGirl_Style_Guide_SS.md`), which is mandatory reading before any prose work.

Steps 1 through 6 are done. **Step 7 is next.** Steps 7 through 17 are fully specified below, including every change the author made along the way.

---

## 0. Start Here (Next Session Checklist)

1. Read the style guide in full. Every rule in it is mandatory. Deviation is an error.
2. Read this document in full.
3. The working file is **`/home/user/claude-code/Vampire_Girl.html`** on git branch **`claude/vampire-girl-overhaul-zeoswy`** (repo `sariia32desu-star/claude-code`). Pull the branch first (`git fetch origin claude/vampire-girl-overhaul-zeoswy && git checkout claude/vampire-girl-overhaul-zeoswy && git pull`). That file is the latest. Never work from an older copy, never copy an earlier upload over it. If the author uploads a new file, work on the new file instead.
   - Note: `/mnt/user-data/outputs` does not exist in this environment. The repo file IS the latest output.
4. Do **one step per turn**. Build it, verify it, commit, push, send the file to the author, then wait for confirmation. Never bundle steps.
5. Start with Step 7 (see section 7 below). Step 7A is different from the original doc: the author wants `bathroom_panic` itself rewritten as a Beauty Obsessed variant, NOT a new choice.

---

## 1. What This Overhaul Is

Beauty Obsessed (trait id `pretty_face`, kept for save compatibility) used to be an unreachable trait with a flat +15 appeal. The overhaul turns it into one of the biggest choices in the game:

- It sits at **age five** in character creation ("The Body You Built"), as the counterpart to the four athlete traits. The athletes built bodies that do things. She built one people can't stop looking at. **Her body is a lure, theirs is a weapon.**
- She is **always gorgeous** (no face choice at 13, see 3.5).
- She starts the game in a black satin mini dress, an underwired lace set, and satin stiletto mules, carrying a **designer backpack (50 slots)** holding the other 28 pieces of her own 32-piece clothing collection, plus her makeup bag.
- She fixes her face in her mother's bathroom before she's figured out what she's become (Step 7).
- Jaewon meets a girl who looks incredible and is sitting in an alley anyway (Step 8).
- The trait **evolves** like the athlete traits: Beauty Obsessed, Polished, Flawless, Iconic, driven by days she stays radiant (Steps 12 to 14).

Mental model (keep this in mind for all prose):
1. **The Choice.** At five, she picks the mirror over the field.
2. **The Collection.** She starts owning better clothes than most players buy in their first month, in real sets, with a perk (Allure XP) only her clothes carry.
3. **The Contrast.** She wakes up a monster in a beautiful dress and fixes her face before she's figured out what she is. Jaewon meets a girl who looks incredible and has nowhere to go.
4. **The Upkeep.** The routine, the mirror, the streak. It pays her every day she keeps it and nags her when she doesn't.
5. **The Evolution.** The longer she stays radiant, the more of the city knows her face.

---

## 2. Working Conventions (Learned This Session)

### 2.1 Author preferences (non-negotiable)
- **2nd person present tense** for all game prose, character creation included. (Trait backstory fields were 3rd person past; they have since been deleted as dead code.)
- **Contractions always** ("you're," "she's," never "you are," "she is").
- **No em dashes** except a paired parenthetical aside or a dialogue cut-off. Never as a connector.
- **Banned:** "Not a question."; "A beat." / "A pause."; negation-before-reveal ("Not X. Y." / "not because X, but because Y"); Not/Not or No/No stacks ("Not the hardest hitter. Not the tallest blocker."); the "You're not the star. You're the girl who..." stanza formula; "the kind of" / "the kind that"; "the way" (anywhere, including "all the way down"); "in a way"; "genuinely" in narration; paired "Something X. Something Y."; formal uncontracted language; walls of text (break prose over ~300 visible characters).
- **Crude, anatomical register** for anything erotic or aroused ("nipples," "pussy," "clit," "cunt"). No euphemisms.
- Match the game's existing voice. Deviation from the game's writing style is an error.
- The author is "Nathan" in design docs. Nathan's decisions win over any doc default.

### 2.2 How to report
- After each step: short summary, quote the new prose so the author can review it, flag any judgment calls or found bugs, then say you're ready for the next step. The author likes sample prose quoted in the reply.
- Surface bugs you find instead of silently fixing things outside scope, unless the fix is tiny and clearly in the spirit of the step (then flag it).

### 2.3 Git
- Commit and push after each step: `git push -u origin claude/vampire-girl-overhaul-zeoswy`.
- End commit messages with the attribution lines the session's system reminder provides. Never put a model name in commits or files.
- Do not open a pull request unless asked.

### 2.4 Technical rules for the HTML file
- One HTML file, ~217,200 lines. Two `<script>` blocks (the big one starts ~line 12659). **Line numbers drift constantly; always search by function name or scene id.**
- All prose lives in JS strings, mostly single-quoted. **Every apostrophe inside a single-quoted string must be `\'`.** Watch for double-escaping (`\\'` breaks the string).
- Some scenes contain curly apostrophes (U+2019); match the existing encoding inside a string you edit.
- The picker code contains literal `★ ◆ · ›` characters (not `\u` escapes). When editing it with Python `str.replace`, match the literal characters.
- **Edit with Python** (`str.replace` with an `assert s.count(old)==1` guard), never `sed` with `\x27`.
- **Syntax check after every edit:**
  ```bash
  cd /home/user/claude-code && python3 -c "
  import re;s=open('Vampire_Girl.html',encoding='utf-8').read()
  b=re.findall(r'<script>(.*?)</script>',s,re.S)
  [open('/tmp/s%d.js'%i,'w',encoding='utf-8').write(x) for i,x in enumerate(b)]" && node --check /tmp/s0.js && node --check /tmp/s1.js && echo OK
  ```
  (Use the session scratchpad instead of /tmp if one is listed.)
- **Browser testing:** Playwright is installed globally, Chromium is at `/opt/pw-browsers`. Load it in a Node script with
  `require(require('child_process').execSync('npm root -g').toString().trim()+'/playwright')`, then `page.goto('file:///home/user/claude-code/Vampire_Girl.html')`, wait ~1.5s, and call game functions through `page.evaluate`. Collect `pageerror` events and report them.
- **Images are not in the repo** (`Images/...` 404s). For screenshots, route `**/Images/icons/*.png` to an inline SVG containing an emoji (Noto Color Emoji is installed) so icons show. Tell the author the emoji are stand-ins.
- **Rendering a character creation question in a test:** `showCharacterCreation()`, hide `#cc-intro`, show `#cc-content` (`display:block`), set `ccState.selectedTraits` as needed, `const i = CC_QUESTIONS.findIndex(q => q.id === 'q_x'); ccState.questionIndex = i; ccRenderQuestion(i, true);` then wait ~4 to 6 seconds for the choice cascade animation.
- **Running a story scene's hooks in a test:** scenes live in the global `story` object (`story.room_search.onEnter()` etc.). `story[id].text` / `parts` / `choices` may be strings, arrays, or functions.
- Viewport 412x915 at deviceScaleFactor 2 is a good phone check; confirm `document.documentElement.scrollWidth` equals the viewport width (no sideways scroll).

### 2.5 Game code facts that matter for the remaining steps
- `hasTrait(id)` reads `gameState.traits.selectedTraits`.
- Clothing item shape: `{ id, name, category, appeal, price, owned, cleanliness, effect: [...], tags: [...] }`. Footwear adds `comfort, mobility, terrain, noise`. Categories in inventory: `bras, panties, tops, bottoms, dresses, footwear, outerwear`.
- Outfit slots (`gameState.currentOutfit`): `bra, panties, top, bottom, dress, outerwear, footwear, headwear, neckwear, earrings, rings, eyewear, other`.
- `equipWardrobeItem` already moves a worn dress to inventory when a top or bottom goes on, so dress + top/bottom can't coexist through normal equipping.
- `getSceneClothingVars()` (the `cv` pattern): `cv.isSkirt` is true when the bottom's **name** contains "skirt". Item names are lowercased straight into prose.
- `PRIVATE_UNEQUIP_SCENES` / `PRIVATE_CHANGE_SCENES` list private scenes. Used as the "not in public" test.
- Backpack: `gameState.backpackCapacity.max`, `calculateBackpackUsage()` counts clothing + other + medical + consumables + personalCare + bloodBags.
- Makeup Kit: `{ id: 'makeup', name: 'Makeup Kit', price }` in `inventory.consumables`. Hand Mirror `hand_mirror` and Hairbrush `hairbrush` in `inventory.personalCare`. Starting shower cap: `terry_cloth_cap`.
- Only three story scenes grant the backpack: `room_search`, `pack_after_cruel_words`, `after_attacking_mother`. Every prologue route passes through one of them.
- The prologue never resets `currentOutfit` after character creation.
- Day 1 is **Monday, June 9, 2025** (both the default state and `newGame()`).
- Kelsie can only change outfits from her backpack inside an apartment, hotel, or porta potty, so on the walk-to-the-city path she's always in the satin dress when she meets Jaewon.
- Story-scene outfit changes use `wearFromBag(ids)` and `stashWornSlot(slot)` (Step 9). Never hand-build clothing objects in a scene.

---

## 3. What's Done (Steps 1 to 6, plus extra work)

### 3.1 Step 1: The collection (data foundation)
- `const BO_IMG = 'Images/clothing/beautyobsessed/'` and `const BEAUTY_OBSESSED_COLLECTION = [...]`: 32 entries, defined just above `startingClothingDescriptions`. **Single source of truth. Never hardcode these items anywhere else.**
- Every entry: `collection: 'beauty_obsessed'`, `price: 0`, `owned: true`, `cleanliness: 100`, `image`, `effect`, `tags`, `description`. Footwear carries inline stats. Dresses (except the sherpa) carry `upscale: true`. Four pieces carry `sheer: true`.
- Helpers: `getBOItemDef(id)`, `isBOItem(item)`, `makeBOInventoryItem(id, overrides)` (deep copy; strips `description` and `image`, which are read from the definition). **Always use `makeBOInventoryItem` when an item lands in inventory or an outfit slot.**
- Wired into `getItemSVG()` (image from `BO_IMG + def.image`), `getClothingDescription()` (checks BO first), and `viewClothingDetail()` (falls through to the BO description). `getFootwearStats()` reads the inline stats (verified).
- Descriptions were rewritten from Nathan's own piece descriptions. The three image-based ones (underwired bra, underwired panties, white flared skirt) came from images.
- Nathan's calls: the `bo_underwired_lace_panties` piece is named **"Underwired Lace Panties"** (NOT "Eyelash Lace Thong"; there's no eyelash thong). **Do not add colors to names** (the criss-cross set is turquoise and yellow, Naughty & Nice is red; the names stay as they are).

The 32 pieces (id, name, category, appeal, flags):

| Id | Name | Cat | Appeal | Notes |
|---|---|---|---|---|
| bo_underwired_lace_bra | Underwired Lace Bra | bras | 15 | +3% Sed, +3% Allure XP |
| bo_underwired_lace_panties | Underwired Lace Panties | panties | 14 | +2% Sed, +2% Allure XP |
| bo_crisscross_lace_bra | Criss-Cross Lace Bralette | bras | 13 | turquoise lace, yellow straps |
| bo_crisscross_lace_panties | Criss-Cross Lace Thong | panties | 13 | |
| bo_naughty_nice_bra | Naughty & Nice Bra | bras | 15 | red satin |
| bo_naughty_nice_panties | Naughty & Nice Thong | panties | 14 | red sheer, embroidered |
| bo_black_tie_front_crop_top | Black Satin Tie-Front Crop Top | tops | 26 | |
| bo_keyhole_camisole_top | Black Keyhole Camisole | tops | 24 | |
| bo_denim_halter_crop_top | Denim Halter Crop Top | tops | 22 | |
| bo_cream_halter_tank_top | Cream Halter Tank | tops | 22 | |
| bo_satin_corset_top | Black Satin Corset Top | tops | 28 | +3% Allure XP |
| bo_pink_sequin_crop_top | Pink Sequin Lace Crop Top | tops | 26 | |
| bo_red_gingham_crop_top | Red Gingham Crop Top | tops | 22 | |
| bo_denim_booty_shorts | Lace-Up Denim Booty Shorts | bottoms | 24 | reads as pants (correct) |
| bo_denim_pleated_mini_skirt | Pleated Denim Mini Skirt | bottoms | 22 | |
| bo_white_fringe_mini_skirt | White Fringe Mini Skirt | bottoms | 24 | |
| bo_pink_fringe_mini_skirt | Hot Pink Fringe Mini Skirt | bottoms | 23 | |
| bo_mesh_mini_skirt | Sheer Mesh Mini Skirt | bottoms | 25 | sheer |
| bo_satin_pleated_micro_skirt | Satin Pleated Micro Skirt | bottoms | 27 | +2% Allure XP |
| bo_pink_sequin_mini_skirt | Pink Sequin Mini Skirt | bottoms | 25 | |
| bo_red_gingham_mini_skirt | Red Gingham Ruffle Skirt | bottoms | 22 | |
| bo_white_flared_mini_skirt | White Flared Mini Skirt | bottoms | 20 | -3% Fatigue |
| bo_satin_black_mini_dress | Black Satin Mini Dress | dresses | 80 | upscale, +4% Allure XP; her starting dress |
| bo_square_neck_mini_dress | Square-Neck Black Mini Dress | dresses | 74 | upscale |
| bo_leopard_halter_mini_dress | Sheer Leopard Mini Dress | dresses | 80 | upscale, sheer |
| bo_off_shoulder_mesh_mini_dress | Off-Shoulder Mesh Mini Dress | dresses | 78 | upscale, sheer |
| bo_rainbow_halter_mini_dress | Rainbow Fishnet Mini Dress | dresses | 72 | upscale, sheer |
| bo_diamond_cutout_mini_dress | Diamond Cutout Mini Dress | dresses | 82 | upscale |
| bo_sherpa_hoodie_dress | Camel Sherpa Hoodie Dress | dresses | 46 | NOT upscale; comfy piece; warm:2 tag |
| bo_satin_black_heels | Black Satin Stiletto Mules | footwear | 22 | comfort 0, mob 1, noise 2, street |
| bo_denim_platform_heels | Denim Platform Mules | footwear | 18 | 1/1/2 street |
| bo_blue_orchid_heels | Blue Orchid Block Heels | footwear | 20 | 2/2/1 street, park |

Every piece also carries Seduction and/or Charisma XP effects in the Urban Edge pattern.

New gameState fields (in the initializer, the new-game reset, and migration defaults):
`boRadiantDays: 0`, `boTier: 1`, `boRitualDay: 0`, `boRitualMissStreak: 0`, and flags `boCollectionGranted`, `boFixedFaceAtHome`, `boSeenTier2`, `boSeenTier3`, `boSeenTier4` (all false). `flags.designerBackpack` is set by Step 6.

### 3.2 Step 2: Sets, looks, Allure XP, sheer pieces
- `underwearSets`: Underwired Lace Set (+12), Criss-Cross Lace Set (+10), Naughty & Nice Set (+12). Total underwear sets now **29**.
- `outfitEnsembles`: 10 collection ensembles in their own labeled block, each with `collection: 'beauty_obsessed'` and `required` = piece count. Total ensembles now **18**.
  - Co-ords: Satin (+14), Pink Sequin (+14), Gingham (+12), Denim (+10).
  - Looks: Little Black Satin (dress + satin heels, +12), Double Denim (+16), Garden Party (cream halter tank, white flared mini, orchid heels, +14), Last Dance (+14), All Black (+14), Cotton Candy (+12).
  - Invariant: no legal outfit triggers two collection ensembles (brute-force verified over all 348 legal combos).
  - Her starting outfit fires Underwired Lace Set + Little Black Satin = **+24**.
- `getItemSetInfo()` (all players): an item in several ensembles reports the one closest to done (complete > would complete > building > member); a fully worn ensemble now reports `complete`.
- Signature effect `allure_xp_bonus`: aggregated in `getActiveClothingEffects()`, capped at **0.15**, applied in `gainAllure()` to positive gains unless `skipScaling`; icon `allure.png` in `getItemEffectHTML()`.
- Sheer: `getVisibleSheerPieces()` returns `{ chest, hips }` (a full-cover coat hides both). In `advanceTime`: +1 Exhibition Thrill/hr while a sheer piece shows in public (public = not in `PRIVATE_UNEQUIP_SCENES`), and **+1/hr more in total** if braless behind a sheer chest or commando behind sheer hips. Sheer pieces never count as visible underwear for the 6001 corruption lock.
- Sheer ambient lines: `SHEER_AMBIENT_LINES` (underwear showing through, 4 corruption tiers x 2, `checkSheerAmbientLine`, 45-min cooldown, hooked after the commando ambient check) plus `SHEER_BRALESS_AMBIENT_LINES` / `SHEER_COMMANDO_AMBIENT_LINES` supplements added to the braless, commando, and both pools.
- "Your Collection" badge (mirror icon, `#ffb3e6`) on backpack and wardrobe item cards via `isBOItem()`.

### 3.3 Step 3: The trait, redefined
- `TRAITS.pretty_face` keeps id, name "Beauty Obsessed", mirror icon, color `#ffaadd`.
- `startBonus`: `+2 Charisma Levels, +15 Base Appeal. Starts with her 32-piece collection, a designer backpack (50 slots), and her makeup bag.`
- `passive` (tier 1): `Immaculate: +15 base appeal. Your own makeup lands harder (+28). Grooming flaws hit at half strength. A daily routine leaves a fresh-skin glow. Evolves as you stay radiant.`
- `apply()`: `gameState.charisma += 2;` then `applyBeautyObsessedStart()`.
- `evolutions` table keyed by `boRadiantDays` (`minDays`):

| Tier | Name | minDays | baseAppeal | makeupAppeal | allureFloor | firstImpression | ritualGlow | tipBonus | smearResist | coherenceAt | bonusCharisma | other |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Beauty Obsessed | 0 | 15 | 28 | 6 | 3 | 6 | 0.08 | 0 | 0.70 | 0 | |
| 2 | Polished | 7 | 20 | 30 | 9 | 4 | 7 | 0.10 | 0.20 | 0.70 | 1 | |
| 3 | Flawless | 21 | 25 | 32 | 12 | 5 | 8 | 0.12 | 0.40 | 0.65 | 1 | |
| 4 | Iconic | 45 | 30 | 35 | 15 | 6 | 10 | 0.15 | 0.60 | 0.65 | 1 | relationshipMult 1.10 |

  Each tier has its own `passive` string.
- Helpers: `isBeautyObsessed()`, `getBOTier()` (1 if not BO), `getBOEvolutionData()`. `updateTraitsDisplay()` shows the evolved name and passive.
- **None of the tier numbers are live yet** except the table itself. Base appeal is still the old flat +15, makeup still +20, etc. Step 12 wires them.

### 3.4 Old character creation removed (extra work)
The old trait-points creation was dead code. Removed `TRAIT_POINTS_MAX`, every subtype `backstory` field, and every parent `description` field. **TRAITS entries now hold only:** parent `{ id, name, icon, color, subtypes }`, subtype `{ id, name, startBonus, passive, apply, evolutions? }`. Only the in-depth, prose-heavy character creation exists.

### 3.5 Steps 4 and 5 plus extra character creation work
- **Trait choice (Step 4A):** in `q_trait_athlete` (age 5), between Volleyball and "Nothing organized":
  - id `trait_beauty_obsessed`, label `Yourself. You train in front of the mirror.`, `trait: 'pretty_face'`, `charisma: 1`.
  - text: "The other girls are out on a field somewhere. You're up on the bathroom counter in Margaret's heels, her lipstick all over your mouth, working out which side of your face the light likes better. / It's a game. It's also practice."
  - result: "Soccer lasts a week. Dance lasts two. Gym class turns into the one hour a day you'd rather be invisible. / By seven you've got a routine. By ten it takes up the whole counter, and Margaret's stopped asking where her good mascara went."
- **`q_puberty_body` `body_athletic` (4C):** label, text, and result are functions; BO wording "Athletic. Lean and toned, and every inch of it on purpose." Stat and bodyType unchanged.
- **Trait picker UI (Nathan asked for it):** any CC question whose choices grant a trait renders as a tile grid via `ccRenderTraitPicker()` (defined just above `ccRenderQuestion`). Three tiles per row on phones, per-trait icon and color (`CC_TRAIT_TILE_STYLE`), animated medallion ring and shine, ◆ for evolving traits. Tapping a tile opens a detail panel (tagline, prose, stat chips, You Start With, Passive, Evolves path) with a "Choose X" button that calls `ccSelectChoice(q, choice, tile)`. Opt-out choices (no trait) render as a dashed button under the grid. Applies to all five trait questions (`q_trait_athlete`, `q_trait_bookworm`, `q_trait_social`, `q_trait_street`, `q_trait_innate`).
- **Quiet and Sharp Eyes** now exist in `TRAITS` (`quiet_nature`, `sharp_eyes`), with passives describing their real hooks. Their stats come from the CC choice, so `apply()` is empty and `startBonus` is ''.
- **Weave (Step 5):** helper `ccIsBO()` (defined above `ccCliqueVision`). BO-only lines in `q_lesson`, `q_clique`, `q_puberty_full_bloom` (jacket line swapped; also `bloom_careful` "old hoodie" clause swapped to "You still dress the way you always have"), `q_prom` (one line ahead of all three branches: "The dress has been planned since January. The shoes since February."), `q_reputation`. `q_party_flash` untouched on purpose.
- **Always gorgeous (Nathan's decision):** for BO, `q_puberty_face` shows a mirror scene (its `subtitle`, `text`, and `choices` are functions) and a single choice: id `face_gorgeous`, label `Continue`, `resultHeading: 'Gorgeous'`, `facialBeauty: 'gorgeous'`. The screen after a choice shows `choice.resultHeading || label` as its heading (new optional CC choice field).
- **No face-privilege Strength XP penalty for BO (Nathan's decision):** `getStartingFaceStrengthMultiplier()` returns 1.0 for `pretty_face`; the CC stat summary and the Body & Presentation "Born beautiful" row skip it for her. The "-10% Strength XP" prose line was removed from her face scene.

### 3.6 Step 6: Starting state
- `applyBeautyObsessedStart()`: equips fresh copies of `bo_underwired_lace_bra` (cleanliness 60), `bo_underwired_lace_panties` (60), `bo_satin_black_mini_dress` (70), `bo_satin_black_heels` (70); `top`/`bottom` null; **clears `inventory.clothing`** (the base game preloads the nine basics, and she never owned them); backpack max 50; `flags.designerBackpack = true`.
- `grantStartingPacking()` replaces the three hardcoded arrays in `room_search`, `pack_after_cruel_words`, `after_attacking_mother`:
  - Not BO: `getBasicStartingClothes()` (the nine basics, all with `cleanliness: 100`).
  - BO: every collection piece not already worn or carried (28), 5 Makeup Kits, Hand Mirror, Hairbrush (skipped if present). Sets `flags.boCollectionGranted`, never double-grants. Bag: **36/50**.
- `stashWornDress()`: moves the worn dress into inventory keeping its cleanliness (no duplicate by id), clears the slot. Replaced the hardcoded party-dress pushes in `wear_jaewon_clothes` and `wear_own_best` (the original doc said four scenes; only these two had the push).
- No-trait start (`ccBlankSlate()`) verified identical to before.
- Measured: BO day-one appeal in her starting look (gorgeous face, no makeup applied) is **172**, versus 12 for a no-trait start. Keep this in mind for the Step 17 balance pass.

---

## 4. Decisions Already Made (from the original doc and this session)

- Designer backpack: 50 slots (base 30).
- Lucius' Lounge and the Regency Hotel let her in when she's wearing Beauty Obsessed clothing (Step 11).
- The collection **replaces** the basic starting clothes. She starts with zero pants and zero outerwear, on purpose. **Do not patch the gap by quietly adding a hoodie.** Jaewon's cropped leather jacket is the one thing in that apartment she needs (Step 9), and it's funny.
- The sherpa hoodie dress does not count toward upscale access. Every other BO dress does, and so does any BO top worn with a BO bottom.
- No Strength XP penalty of any kind for the trait. The cost is what she didn't become (no athlete trait) and a wardrobe that shreds in a fight.
- The daily routine (Step 13) is free. It only needs a mirror and twenty minutes.
- Sheer pieces have the light exhibition hook (done in Step 2).
- She is always gorgeous. No ordinary or pretty face for her.
- Keep the id `pretty_face` so existing `hasTrait('pretty_face')` checks and saves keep working.
- The existing bugs listed in Steps 9, 10, and 11 get fixed for **all** players inside this overhaul.

---

## 5. Existing Trait Hooks (all read `hasTrait('pretty_face')`)

Step 12 swaps hardcoded numbers in these for tier values. Don't restructure the functions.

- `getBodyBaseAppeal()` (+15 clothed) and the naked branch of `calculateTotalAppeal()` (+15 naked).
- `getAllure()` derived floor (+6), mirrored in the Current Stats modal breakdown and the Body & Presentation panel breakdown.
- `getHairGroomFlat()` (glossy "down" hair +6), `getDailyHairCleanlinessDrop()` (8 instead of 12), hair growth (+0.1/day).
- `getAppealPenalties()` grooming softener (`_groomSoften14d = 0.5` on smeared makeup and unkempt hair) and the naked-branch equivalent (`_hairSoftN`).
- Nap-proof hair in the sleep handler.
- Body & Presentation panel: a Beauty Obsessed box in the face section (currently says "Grooming devotion, not innate looks: a flat +15 appeal, smeared makeup and messy hair hit you softer, and a short nap won't wreck your hair." **This is now false because she's gorgeous; rewrite it in Step 12H.**) and one in the hair section.
- Appeal breakdown labels ("Glossy Hair (Beauty Obsessed)") and a benefits list.

Useful function names (search for them; line numbers drift): `getBodyBaseAppeal`, `calculateTotalAppeal`, `getAppealPenalties`, `getAllure`, `gainAllure`, `getFaceFirstImpression`, `evaluatePresentationStreak`, `getSpaGlowBonus`, `applyMakeup`, `calculateStyleCoherence`, `calculateStyleCoherenceWith`, `getLocationOutfitSuggestion`, `isWearingNoirBoutique`, `updateTraitsDisplay`, `getActiveClothingEffects`, `APPEAL_TIERS`, `getAppealTier`, `appealTierAtLeast`.

---

## 6. Remaining Steps Overview

| Step | Title | Status |
|---|---|---|
| 7 | The prologue at home | Done |
| 8 | Meeting Jaewon | Done |
| 9 | The clothing choice at Jaewon's (bug fixes + variants) | Done |
| 10 | The city sees her | Done |
| 11 | Upscale access | Done |
| 12 | The buffs: her face, her eye, her draw | Done |
| 13 | The ritual and the mirror | **NEXT** |
| 14 | Evolution: staying radiant | |
| 15 | UI and Tips & Guide | |
| 16 | Save migration | |
| 17 | Polish and integration | |

All new prose: 2nd person present, style guide clean, short paragraphs, escaped apostrophes, `node --check` after every step. BO variants are variants, not rewrites: match the surrounding prose's length, and leave non-BO text untouched except where a bug fix requires it.

---

## 7. Step 7: The Prologue at Home

The prologue is written for a girl whose room is a mess and whose best clothes are a sundress. BO Kelsie needs her own version wherever her room, her bag, or her face is the subject.

### 7A. `bathroom_panic`: rewrite as a Beauty Obsessed variant (CHANGED FROM THE ORIGINAL DOC)

**Nathan's instruction:** do NOT add a "Fix your face" choice or a separate `bo_fix_face` scene. Instead, `bathroom_panic` itself gets a Beauty Obsessed variant. This is the most important beat in the overhaul: she sees the vampire in the mirror for the first time, and for her that face is the thing she's spent thirteen years building. Give it the most care of any prose in the overhaul.

Current structure of `bathroom_panic` (read it in full first):
- `parts`: three entries. Part 1 (string): she stumbles in, slams the door, the heartbeats muffle, her reflection catches her eye. Part 2 (string): "A stranger stares back." Deathly pale, luminescent skin, crimson glowing irises, fangs extended. Part 3 (function): karma-branched reaction (demonic: alive, "the rules are done"; angelic: "Am I a monster?", finds the scar on her chin, she's still her; neutral: strange, "What can I do?").
- `text` (string): the panic subsides, fangs retract, eyes shift to dark brown, skin warms: the **illusion** turns her human-looking, and the thirst deepens because the illusion costs energy.
- `onEnter`: +3 thirst, `illusionActive = true`, `flags.lookedInMirror = true`, `flags.illusionUnlocked = true`.
- `choices` (function): Occult Enthusiast gets "Wait... you know what this is" → `occult_vampire_insight`; everyone gets "Explore the bathroom" → `explore_bathroom`, "Practice the illusion" → `practice_illusion`, "Splash water on your face" → `remember_bathroom`, "Leave immediately" → `bathroom_exit`.

Implementation approach: make the BO-relevant parts functions that branch on `hasTrait('pretty_face')` (or `isBeautyObsessed()`), leaving the non-BO text byte-identical. Keep the illusion beat and every mechanic in `onEnter` intact for her. Decide with Nathan (or use judgment and flag it) whether her choices list stays the same.

Direction for the BO variant (from the original `bo_fix_face` spec, now folded into this scene):
- She looks closer than anyone else would. The vampire face is better than the one she built. Skin flawless overnight, every pore she's fought since seventh grade gone. Eyes that were red a minute ago.
- The mascara under her eyes is still a mess, though. Some things don't fix themselves.
- She does her face anyway: her own counter, her own products, hands steady in a way they shouldn't be. The routine is the only thing this morning that still makes sense.
- Karma coloring (part 3): angelic, the routine as an anchor (if her hands can still do a wing, she's still her); neutral, curiosity layered over habit; demonic, delight (whatever she is now, it's gorgeous, and people are going to look).
- Anchor lines (use them):
  - `Every pore you\'ve fought since seventh grade is gone. Somebody sanded you smooth overnight.`
  - `The mascara\'s another story. It\'s halfway down your cheek.`
- Remember: she's always gorgeous now (Nathan's decision), so the vampire face is gorgeous plus more.

Mechanics for her (add to `onEnter` inside a BO branch): `bodyState.makeup = true` (no kit consumed; this is her own counter at home), `bodyState.hairGroom = 'down'` (brushed out; check the current grooming field name in the hair system before writing), `flags.boFixedFaceAtHome = true`, +3 MH for neutral and angelic karma (use the game's MH helper), +10 minutes (`advanceTime(10)`), and the usual +3 thirst (already there). The original spec routed onward like `explore_bathroom` ("Stay and listen" / "Face her now"); since this is now the panic scene itself, check what `explore_bathroom`'s choices are and decide which routing reads best. Flag your choice.

### 7B. `explore_bathroom`
Swap "Makeup scattered across the counter. Your makeup." for a BO line: the counter is her whole life in bottles, serums in order of application. One or two sentences. The rest of the scene stays. (If 7A already has her doing her face, make sure 7B doesn't contradict it.)

### 7C. `room_search` (three karma variants plus neutral)
Her room is the opposite of the current "It's a mess" text. `room_search.text` is a function with branches `k >= 1000` (angelic), `k <= -1000` (demonic), else neutral.
- All BO branches: the closet's organized by color, there's a vanity with a ring light, and the designer backpack Margaret gave her last Christmas is what she grabs. Packing is fast because she already knows what she'd take: looks, planned down to the shoes. She fits thirty-two pieces into one bag and it still zips. (Technically 28 pieces go in the bag and 4 are on her body; the prose can say she packs her whole collection.)
- Angelic: the photo of her and her mom still gets wrapped carefully, in her softest dress this time. The sticky notes become notes on the vanity mirror.
- Neutral: the photo still comes. The laptop line ("Probably sold it for party money") stays.
- Demonic: clinical and fast, the same cold beats. She takes the looks and leaves everything sentimental.
- `onEnter` already calls `grantStartingPacking()` (done in Step 6).

### 7D. `last_look_room`, `pack_after_cruel_words`, `after_attacking_mother`
- `last_look_room`: "The posters. The mess." becomes the vanity and the empty hangers. One swapped sentence.
- `pack_after_cruel_words` ("Backpack. Clothes. Essentials."): one BO line about grabbing the designer bag and her makeup bag first. `onEnter` already calls `grantStartingPacking()`.
- `after_attacking_mother`: packing is silent (already uses `grantStartingPacking()`). No prose change.

### 7E. Budget
Variants, not rewrites. Match the surrounding prose's length.

---

## 8. Step 8: Meeting Jaewon

Every early Jaewon beat assumes Kelsie looks like a wreck. BO Kelsie looks incredible and is sitting in an alley anyway. That contrast is the point: Jaewon sees it immediately, and it tells her something went badly wrong. **Jaewon's early kindness is the emotional anchor of the game. Her kindness doesn't depend on Kelsie looking wrecked, and the variants should prove that.**

### 8A. `jaewon_alley_conversation_start`
"The party dress. The backpack. The smudged makeup." Variant: the satin dress, the designer backpack, and (if `flags.boFixedFaceAtHome`) a face that's perfectly done, or (if not) smudged mascara on a face that's still somehow gorgeous.

### 8B. The money-gift line (three karma versions)
"Look at you. You're sitting in this alley with a rough-looking dress and a backpack." appears in `jaewon_money_gift` (angelic and neutral branches) and `jaewon_money_gift_low_karma`. Each needs a BO variant that keeps Jaewon's logic: she's worried the money is too much of what Kelsie has.

Anchor (angelic; adapt the follow-up per branch):
`"Look at you." Her voice comes out small and scraped raw. "You\'re in a dress nicer than anything I own, and you\'re sitting in an alley at eight in the morning with your whole life in a backpack. That\'s gotta be at least half of what you have."`

Neutral keeps "That's like, a crazy amount of money for you, right?" Demonic keeps "is that everything you have?"

### 8C. The bus (`accept_jaewon_couch`, part 2)
"The rumpled party dress, the scuffed heels, the whole wreck of last night still hanging off you." Variant: the satin dress, the heels, a face that somehow held up. Jaewon registers the gap between how Kelsie looks and where she found her, files it away, and looks back out the window.

### 8D. The apartment (`accept_jaewon_couch`, text)
"Her eyes move over the party dress, the heels, the leftover makeup smudged under your eyes." and Jaewon's "I'll dig you out some real clothes." Variant: Jaewon's eyes take in the dress and the face, and she's honest about it. She still suggests the shower (hygiene is 0; the vampire girl still needs one) and offers to lend something comfy.

Anchor: `"Okay. First things first." She says it gently. "You look amazing, which is honestly kind of rude after the morning you\'ve had. Shower anyway. You\'ll feel better."`

If `boFixedFaceAtHome` is false, keep the "leftover makeup smudged" detail in the variant.

### 8E. The shower (`jaewon_shower_accept`), optional
"Everything she owns is modest and deliberate. Like her." stays, preceded by Kelsie clocking the drugstore shampoo and deciding it's the nicest thing she's ever used anyway. Keep it warm; the scene is the first safe place and her obsession shouldn't undercut that.

---

## 9. Step 9: The Clothing Choice at Jaewon's (Bug Fixes + Variants)

Scenes (all near each other, search by id): `jaewon_just_change`, `jaewon_clothes_check`, `own_clothes_check`, `wear_jaewon_clothes`, `wear_own_best`, `wear_sundress`, `wear_comfy`, `wear_party_dress_again`, `wear_own_other`.

### 9A. Bugs (fix for ALL players)
1. **Every `wear_*` scene overwrites her underwear** with the basic white set (`wear_jaewon_clothes`, `wear_own_best`, `wear_sundress`, `wear_comfy`, `wear_party_dress_again`). Remove those lines. She keeps her own underwear (for the basic path that's still the white set, so nothing visible changes).
2. **"Wear the skinny jeans and tank top (best you've got)"** in `own_clothes_check` routes to `wear_own_best`, which puts on the sundress. Create `wear_skinny_tank` (equips real `skinny_jeans` + `black_tank` from inventory) and point that choice at it. `wear_own_best` stays the sundress scene for "Wear your sundress" in `jaewon_clothes_check`.
3. **`wear_comfy` equips id-less objects** (`{ name: "Oversized Sweater", appeal: 3 }`, `{ name: "Black Leggings", appeal: 5 }`). Equip real copies of `black_leggings` and `oversized_sweater` from inventory. The sweater is outerwear in inventory; equip it consistently with how the inventory equips it. **Also found this session:** `wear_comfy` never clears the dress slot, so the dress stays equipped under a top and bottom. Stash it with `stashWornDress()`.
4. **`jaewon_just_change` says she changed but changes nothing.** Equip the skinny jeans and black tank from inventory (basic path).
- Also found: `wear_sundress` doesn't stash the worn dress either (it overwrites the slot). Use `stashWornDress()` in every wear scene that swaps the dress. `wear_party_dress_again` hardcodes `basic_black_heels` as footwear; don't overwrite her shoes.

### 9B. `jaewon_just_change` (BO variant)
She changes into something from her bag. Equip Garden Party (cream halter tank, white flared mini skirt, blue orchid heels) with `makeBOInventoryItem` or by moving the owned copies from inventory (prefer moving her actual inventory copies so nothing duplicates). The "Better than the party dress. But still not great." line becomes her being pleased with a look she put together in a four-by-six bathroom in ninety seconds.

### 9C. `jaewon_clothes_check` (BO variant)
The text ends "these are way nicer than anything in your backpack." For her, Jaewon's pieces are good (her taste lines up with Kelsie's), and the jacket's the find, because it's the one layer she didn't pack and it goes with everything in her bag. (Day 1 is June 9. Never write February or cold weather here.) **Done in Step 9.**
BO choices:
- Wear Jaewon's outfit (tee, jeans, jacket) → `wear_jaewon_clothes`
- **Wear Jaewon's jacket over one of your looks** → new `wear_bo_with_jacket`
- Wear one of your own looks → `own_clothes_check`
The sundress option doesn't appear for her.

### 9D. `own_clothes_check` (BO variant)
The basic version lists five dull outfits and ends on "you might need to invest in better clothes." Hers is the opposite: she dumps the bag on the counter and it's a closet's worth of looks. A short, fast rundown in her voice naming three or four pieces with pride, then the realization she has more options than Jaewon's whole wardrobe and nowhere to put them. Under six short paragraphs.
BO choices, each → `wear_bo_look` with a transient flag `_boLook` (set, read, then null):
- The satin dress again (Little Black Satin)
- Garden Party (halter tank, flared mini, orchid heels)
- Double Denim (halter crop, booty shorts, platform mules)
- Something cozy (sherpa hoodie dress, orchid heels)
- Pick from your bag yourself → the wardrobe modal (same as `wear_own_other`)

### 9E. New scenes `wear_bo_look` and `wear_bo_with_jacket`
- `wear_bo_look`: short text keyed to `_boLook`, two or three short paragraphs each: how the look sits, what it does for her body (use `bodyTypeDescriptor()` / `getSynergyFlavorText()` for one line; confirm these exist), and that it's hers. `onEnter` equips the pieces (from her inventory copies), stashes the worn dress if it changes, keeps her underwear, calls `updateStatus()`.
- `wear_bo_with_jacket`: she keeps the satin dress on and takes only the jacket. Jaewon sees it and tells her to keep it. Equip `jaewon_leather_jacket` to `outerwear` and remove it from Jaewon's wardrobe permanently (the same splice pattern `wear_jaewon_clothes` uses, after `initializeJaewonWardrobe()`). Set `flags.jaewonGaveOutfit = true`.

### 9F. `wear_jaewon_clothes` (three karma variants)
"Way better than anything you own" (neutral) and the same premise in angelic and demonic. BO variant for all three: the jeans are good and the tee's better quality than it looks, but the jacket's the real find. Jaewon's "Those look better on you than they do on me" and "Keep them" beats stay.
Anchor as shipped: `Your closet\'s better. You\'d never say it out loud. / The jacket\'s the real find. Cropped, black, silver zippers. You packed thirty-two pieces, and not one of them was a layer.`

### 9G. `wear_party_dress_again` (BO variant)
"It's also the only sexy thing you own." is false for her. Variant: she puts the satin dress back on because it still looks incredible and she knows it. Route BO players here through the "satin dress again" option in 9D (or have `wear_bo_look` handle it; pick one and flag it). Basic path unchanged apart from the 9A fixes.

---

## 10. Step 10: The City Sees Her (DONE)

**As shipped:** the `walk_to_*` scenes were orphaned (travel always lands on `outside_*`), so they were deleted along with the older Jack chain (`bargain_entrance` / `jack_intro` / `jack_tour`). "Go inside" now plays each store's intro on the first visit (`bargain_threads_entrance`, `urban_edge_entrance`, `noir_entrance`) and the browse scene after. The outfit-aware street reaction (`getStreetReaction(store)`) shows in the `outside_*` scene on arrival only (`isStreetArrival`). Helpers: `proseItemName`, `getWornOutfitPhrase`, `getWornBOPiece`, `capFirst`. Staff flags: `boUrbanStaffNoticed`, `boNoirStaffNoticed`. The Noir street woman carries a quilted designer bag (no fur coat in June).

### 10A. Duplicate scene definitions (bug), DONE
The dead first copies of `walk_to_bargain`, `walk_to_urban`, `walk_to_noir` were deleted (along with the dead `urban_entrance` / `maya_help` scenes only they reached). Each walk now has one live definition.

### 10B. Outfit-aware walks (all players)
The live walks hardcode "Your basic outfit stands out here" and "In your rumpled party dress." Make each walk read her actual appearance: branch on `getAppealTier()` and `calculateStyleCoherence().label`, name the garment through `cv`. Three short bands per walk (low, middle, high). At high appeal on the Noir walk, the woman in the fur coat looks for a different reason. A BO line joins the high band when she's wearing collection pieces: someone clocks the shoes, then the bag, then her.

### 10C. `bargain_threads_entrance` (Jack)
"The rumpled party dress. Your exposed legs. The fabric clinging." becomes `cv`-aware for everyone. Jack's leer stays exactly as uncomfortable. Only the garment changes.

### 10D. Store staff recognition (optional polish)
**There are no named store owners.** Urban Edge and Noir Boutique are established businesses staffed by unnamed employees (Maya and Vivienne were removed from the game entirely, relationships included). First visit to each while wearing collection pieces, one line each: an Urban Edge staffer recognizes a piece and asks where she found it; a Noir Boutique staffer notes the look is excellent and the labels aren't theirs. One-time flags. Store scene ids: `urban_edge_entrance`, `urban_edge_help`, `urban_edge_browse`, `urban_edge_return`, `noir_entrance`, `noir_recommend`, `noir_browse`; visit flags `visitedUrbanEdge`, `visitedNoirBoutique`.

---

## 11. Step 11: Upscale Access (DONE)

**As shipped:** `meetsUpscaleDressCode()` returns `{ ok, via }` (checks beauty first). Noir ids fixed to the catalog and checked across bra, panties, top, bottom, dress, outerwear, footwear, headwear, neckwear, earrings, rings. `statement_ring` dropped from the list (Urban Edge sells the same id) and replaced by `diamond_ring`. Door lines in `lucius_lounge_first_night` / `_first_day` and `outside_luxury_hotel`; the Lucius rejection checklist names her collection; `ownsBOPieces()` and `_boDoorNote()` add a note to the Lucius and Regency outfit suggestions.

### 11A. Replace the whitelist
Move the logic of `isWearingNoirBoutique()` into `meetsUpscaleDressCode()`, returning `{ ok, via }` where `via` is `'noir'`, `'urban'`, `'jaewon'`, or `'beauty'`. Keep `isWearingNoirBoutique()` as a wrapper (`return meetsUpscaleDressCode().ok;`) so all call sites keep working.
BO rule: `ok` when the worn dress has `upscale: true`, or when both the worn top and the worn bottom are collection pieces. The sherpa hoodie dress has no `upscale` flag.

### 11B. Fix the broken ids (bug)
The Noir list contains ids that don't exist (`luxury_lace`, `silk_burgundy`, `corset_set`) and checks `outfit.underwear`, which isn't a slot. Use real ids (`luxury_lace_bra`, `luxury_lace_panties`, etc.) and check `bra` and `panties`. Review the whole list against the Noir catalog (`shopInventories.noir_boutique`) and fix any other mismatches.

### 11C. The doors
- `lucius_lounge`: the bouncer's rejection logic stays for hygiene, hair, and makeup. When she passes via `'beauty'`, add one line to his once-over: his eyes stop at the dress and he steps aside.
- `outside_luxury_hotel`: the doorman's "designer attire from Noir Boutique" line only shows on a fail. When she passes via `'beauty'`, a one-line nod.
- `getLocationOutfitSuggestion()`: when she owns collection pieces, the Lucius and Regency suggestions mention them.

---

## 12. Step 12: The Buffs (Her Face, Her Eye, Her Draw) (DONE)

**As shipped:** helpers `boVal(key, fallback)`, `getOwnMakeupAppeal()`, `trySmearMakeup(source)` (resists only her own makeup, never Godpeia premium; used at all seven smear sites incl. sleep), `getBOTipMultiplier()` (all three tip sites), `getGodpeiaServicePrice(base)` (flat services, hair menu via `chargeGodpeiaHairService`; the face upgrade is untouched since she's already gorgeous). The Iconic x1.10 sits in `modifyRelationship`'s `applyBonus` beside Class President (Party Girl has no relationship hook; its x1.08 is Allure XP). The Body State panel's makeup label said +10 while the math gave +20; it now shows the real number for everyone. Nap-proof hair is still not named in the passive (question to Nathan still open).

Turns the Step 3 tier table into live numbers. Read every number from `getBOEvolutionData()`.

- **12A Base appeal:** `getBodyBaseAppeal()` and the naked branch of `calculateTotalAppeal()`: replace the flat `+15` with `baseAppeal` (15/20/25/30).
- **12B Her own face (makeup):**
  - `calculateTotalAppeal()` (both branches) and the Current Stats breakdown: when `bodyState.makeup === true` and she's BO, use `makeupAppeal` (28/30/32/35) instead of +20. Label it "Makeup (your own): +28". (Godpeia premium makeup is `'premium'`, +35, $200; leave it as is.)
  - `applyMakeup()`: 5 minutes instead of 10 for her; change the `apply_makeup_scene` text for her to one line in her voice.
  - **Smear resistance:** wherever makeup is set to `'smeared'` (rain, overheating, crying, sex; search `makeup = 'smeared'`, about seven sites), roll against `smearResist` first (0/20/40/60%). One helper, `trySmearMakeup(source)`, used at every site.
- **12C Allure floor:** `getAllure()` derived floor, the Current Stats breakdown, and the Body & Presentation breakdown: replace the flat +6 with `allureFloor` (6/9/12/15).
- **12D First impressions:** `getFaceFirstImpression()`: add `firstImpression` (3/4/5/6) to `weight` for her.
- **12E Her eye for a look:** `calculateStyleCoherence()`: for her the Coordinated threshold drops from 0.75 to `coherenceAt` (0.70, then 0.65 at Flawless). Apply the same inside `calculateStyleCoherenceWith()` so the Try On preview matches.
- **12F Money:**
  - Diner tips: beside Party Girl's +20% tip hook, when she's BO and wearing makeup with her hair down or better, multiply tips by `(1 + tipBonus)` (8/10/12/15%).
  - Godpeia: 15% off every Godpeia service. Services deduct a hardcoded `200`; route them through one `getGodpeiaServicePrice(base)` helper and replace every deduction and displayed price.
- **12G Relationships (Iconic only):** positive relationship gains while she's Attractive or higher get ×1.10 through the same modifier path Party Girl uses (×1.08).
- **12H Update every description:** the face and hair perk boxes in the Body & Presentation panel, the appeal breakdown labels, the benefits list. Read numbers from `getBOEvolutionData()`. **The face box must be rewritten**: it currently says "Grooming devotion, not innate looks," which contradicts her always-gorgeous face. Also check whether the passive should mention the nap-proof hair (Nathan was asked; it's currently not mentioned in the passive but the hook works).

---

## 13. Step 13: The Ritual and the Mirror

### 13A. The morning routine
New action **"Do your routine,"** once per day at any mirror location (`canApplyMakeupHere()`) or anywhere with the Hand Mirror. It appears in the personal-care inventory panel beside Apply Makeup and Brush Hair, for her only.
- 20 minutes. Sets `boRitualDay = day`, resets `boRitualMissStreak = 0`, +3 MH.
- Grants a **Fresh Skin** glow for the rest of the day: +`ritualGlow` appeal (6/7/8/10). Same pattern as `getSpaGlowBonus()`, its own function `getBORitualGlow()`, added in both appeal branches and shown in the breakdown.
- Short scene `bo_ritual_scene` with a rotating pool of three or four one-paragraph variants. Karma coloring optional.

### 13B. Skipping it
At rollover, if she skipped yesterday's routine, increment `boRitualMissStreak`. From the second consecutive skip: −2 MH per day, and a mild **"Skipped Routine"** imperfection (−3% of base appeal) in the Imperfections list with a condition-aware line. It clears the moment she does the routine. It's a small tax and the only real downside the trait carries.

### 13C. The mirror
The mirror interaction from the Appeal overhaul (e.g. `own_apartment_mirror`; search for the mirror scenes and `mirrorLastDay`):
- Its daily MH effect is doubled for her in both directions.
- One extra line after the face line, from a small pool keyed to her tier and whether she's done her routine. She notices things nobody else would.
- The "vampire in the mirror" flicker gets one BO variant: she sees the true face and it's the best work she's ever done.

### 13D. Presentation streak
`evaluatePresentationStreak()`: for her, the charisma XP and MH payouts are ×1.5, and breaking a streak of 3+ costs her −3 MH with a notification that sounds like her.

---

## 14. Step 14: Evolution (Staying Radiant)

### 14A. The counter
In `evaluatePresentationStreak()` (runs once per day at rollover): if she's BO and `appealTierAtLeast('alluring')`, increment `boRadiantDays`. Lifetime, not consecutive.

### 14B. Tier-ups
After incrementing, compare `getBOTier()` to the cached `boTier`. On a tier-up:
- Apply `bonusCharisma` (`gameState.charisma += 1`), update `boTier`.
- Fire a one-time beat (flag-gated with `boSeenTier2/3/4`), queued for the next time she's at a mirror or at home, whichever comes first. One or two short paragraphs each:
  - **Polished (7 days):** a stranger asks what she uses on her skin, and she realizes she's stopped checking her reflection. She already knows.
  - **Flawless (21 days):** rain, heat, a long night, and her face holds through all of it.
  - **Iconic (45 days):** someone in the city describes her to someone else, and she hears it secondhand. Karma and corruption coloring: angelic, pride in the work; demonic, the pleasure of being unforgettable to prey.
- Notification with the new tier name and a one-line summary of what improved.
- The existing `showTraitEvolutionPopup(tierNum, iconSrc, newData, oldData, strGain, traitKey)` is how the athlete traits announce evolutions; consider reusing it.

### 14C. Display
- Traits panel already shows the evolved name and passive (Step 3).
- The Body & Presentation panel's BO block shows the tier, `boRadiantDays` progress toward the next tier, and the current numbers.

---

## 15. Step 15: UI and Tips & Guide

### 15A. UI
- Backpack header reads "Designer Backpack" and 50 when `flags.designerBackpack` is true.
- The collection badge is done (Step 2). The `allure_xp_bonus` effect shows with the allure icon in the wardrobe and backpack (done); confirm it also shows in the shop Try On and the Current Stats clothing effects list.
- The routine action, the Fresh Skin glow, and the Skipped Routine imperfection all show in the appeal breakdown.

### 15B. Tips & Guide: Clothing Systems (`data-tips-section="clothing-systems"`)
- Matching Sets & Ensembles: underwear sets 26 → **29**; ensembles 8 → **18**, noting BO co-ords and looks need every piece, where the original eight need three of four.
- Item Effects & Perks: add Allure XP, its 15% cap, and "Beauty Obsessed collection: seduction, charisma, and Allure XP, which only her clothes carry."
- Occasion & Dress Codes: Lucius' Lounge and the Regency accept BO dresses and co-ords.
- New subsection **BEAUTY OBSESSED COLLECTION:** what she starts with, the sets and looks, the sheer pieces and their exhibition hook, the designer backpack.

### 15C. Tips & Guide: appearance sections
Update the three existing BO mentions (Imperfections, Beauty Obsessed & Hair, Maintenance Loop) to the new numbers and framing. Add one entry covering the trait end to end: age-five origin, always gorgeous, the collection, the buffs by tier, the routine and its cost, evolution to Iconic, no Strength XP penalty.

Guide text follows the style rules (UI labels may use uncontracted phrasing per the style guide's exceptions) and uses the crude register only where a section is already about sex or exhibition. Also update the character creation section of the guide if it describes trait selection or the face question.

---

## 16. Step 16: Save Migration

In the migration code (the Step 1F defaults already live in the big migration block next to the Appeal & Body defaults; search `MIGRATION (Beauty Obsessed overhaul`):
- Defaults are done for every Step 1F field.
- For saves with `pretty_face`:
  - `backpackCapacity.max` at least 50, `flags.designerBackpack = true`.
  - If `!flags.boCollectionGranted`, grant the 32 pieces via `makeBOInventoryItem`: into the backpack while there's room, the rest into `wardrobes.apartment` if she's living with Jaewon (check the flag the game uses), otherwise the backpack up to capacity. One notification explaining where her collection went. Set the flag.
  - Compute `boTier` from `boRadiantDays`.
- Normalize any item in inventory, outfit, or wardrobes whose id starts with `bo_` against `getBOItemDef()` so old copies pick up data changes (appeal, effects, tags, name, upscale, sheer), preserving cleanliness and damage.

---

## 17. Step 17: Polish and Integration

### 17A. Style audit
Every new and touched string from Steps 1 to 16 against the style guide checklist. Paragraph density scan (>320 visible chars) on every new scene. Escapes and curly-apostrophe consistency. `node --check` clean.

### 17B. Balance pass
- A fully groomed BO Kelsie in her starting look lands high on day one (measured 172 appeal before makeup in Step 6, with her gorgeous face). That's the design (the trait is her whole childhood), but confirm the early seduction bands and Lucius' Lounge don't trivialize the first week. Lucius still demands hygiene, styled hair, and makeup, which is the natural brake.
- Confirm seduction and charisma XP caps hold with a full collection outfit plus Jaewon's jacket.
- Confirm the Skipped Routine tax stays mild and never spirals.
- Confirm no single outfit triggers two BO ensembles (verified in Step 2; re-check if ensembles change).

---

## 18. Scenes Still Receiving BO Variants

`bathroom_panic` (rewritten variant, 7A), `explore_bathroom`, `room_search`, `last_look_room`, `pack_after_cruel_words`, `jaewon_alley_conversation_start`, `jaewon_money_gift`, `jaewon_money_gift_low_karma`, `accept_jaewon_couch` (parts + text), `jaewon_shower_accept` (optional), `jaewon_just_change`, `jaewon_clothes_check`, `own_clothes_check`, `wear_jaewon_clothes`, `wear_party_dress_again`, `walk_to_bargain` / `walk_to_urban` / `walk_to_noir` (outfit-aware for all), `bargain_threads_entrance`, `lucius_lounge`, `outside_luxury_hotel`, `apply_makeup_scene`.

New scenes still to build: `wear_skinny_tank` (9), `wear_bo_look` (9), `wear_bo_with_jacket` (9), `bo_ritual_scene` (13), tier-up beats (14), store staff recognition lines (10, optional).

New helpers still to build: `meetsUpscaleDressCode()` (11), `trySmearMakeup(source)` (12), `getGodpeiaServicePrice(base)` (12), `getBORitualGlow()` (13).

## 19. Bug List

| # | Bug | Status |
|---|---|---|
| 1 | Beauty Obsessed unreachable in CC | Fixed (Step 4) |
| 2 | `wear_*` scenes force-equip the basic white underwear | Fixed (Step 9) |
| 3 | "Wear the skinny jeans and tank top" puts on the sundress | Fixed (Step 9) |
| 4 | `wear_comfy` equips id-less items (and leaves the dress on) | Fixed (Step 9) |
| 5 | `jaewon_just_change` changes nothing | Fixed (Step 9) |
| 6 | Two of three prologue packing arrays omit `cleanliness` | Fixed (Step 6) |
| 7 | `walk_to_*` scenes defined twice | Fixed (before Step 10) |
| 8 | `isWearingNoirBoutique()` checks nonexistent ids and an `underwear` slot | Fixed (Step 11) |
| 9 | Quiet and Sharp Eyes missing from `TRAITS` | Fixed (extra) |
| 10 | Set badge reported only the first ensemble an item belonged to | Fixed (Step 2) |
| 11 | `wear_sundress` overwrites the worn dress without stashing it | Fixed (Step 9) |
