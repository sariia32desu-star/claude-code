# Vampire Girl: Beauty Obsessed Overhaul, Session Handoff

This document is self-contained. The only other document required is the style guide (`VampireGirl_Style_Guide_SS.md`), which is mandatory reading before any prose work.

Steps 1 through 13 are done. **Step 14 is next.** Steps 14 through 17 are fully specified below.

---

## 0. Start Here (Next Session Checklist)

1. Read the style guide in full. Every rule in it is mandatory. Deviation is an error.
2. Read this document in full.
3. The working file is **`/home/user/claude-code/Vampire_Girl.html`** in repo `sariia32desu-star/claude-code`. The latest version is on branch **`claude/vampire-girl-beauty-trait-0yu3k2`**.
   - First: `git fetch origin claude/vampire-girl-beauty-trait-0yu3k2`.
   - If the new session is assigned a different branch, fast-forward that branch onto this one (`git merge --ff-only origin/claude/vampire-girl-beauty-trait-0yu3k2`) before touching anything, then push to the assigned branch.
   - That file is the latest. Never work from an older copy, never copy an earlier upload over it. If the author uploads a new file, work on the new file instead.
   - `/mnt/user-data/outputs` does not exist in this environment. The repo file IS the latest output.
4. Do **one step per turn** unless the author asks for more. Build it, verify it, commit, push, send the file to the author, then wait for confirmation.
5. Start with Step 14 (section 14 below).

---

## 1. What This Overhaul Is

Beauty Obsessed (trait id `pretty_face`, kept for save compatibility) used to be an unreachable trait with a flat +15 appeal. The overhaul turns it into one of the biggest choices in the game:

- It sits at **age five** in character creation ("The Body You Built"), as the counterpart to the four athlete traits. The athletes built bodies that do things. She built one people can't stop looking at. **Her body is a lure, theirs is a weapon.**
- She's **always gorgeous** (no face choice at 13).
- She starts in a black satin mini dress, an underwired lace set, and satin stiletto mules, carrying a **designer backpack (50 slots)** holding the other 28 pieces of her 32-piece collection, plus her makeup bag.
- She fixes her face in her mother's bathroom before she's figured out what she's become.
- Jaewon meets a girl who looks incredible and is sitting in an alley anyway.
- The trait **evolves** like the athlete traits: Beauty Obsessed, Polished, Flawless, Iconic, driven by days she stays radiant.

Mental model (keep this in mind for all prose):
1. **The Choice.** At five, she picks the mirror over the field.
2. **The Collection.** She starts owning better clothes than most players buy in their first month, in real sets, with a perk (Allure XP) only her clothes carry.
3. **The Contrast.** She wakes up a monster in a beautiful dress and fixes her face before she's figured out what she is.
4. **The Upkeep.** The routine, the mirror, the streak. It pays her every day she keeps it and nags her when she doesn't.
5. **The Evolution.** The longer she stays radiant, the more of the city knows her face.

---

## 2. Working Conventions

### 2.1 Author preferences (non-negotiable)
- **2nd person present tense** for all game prose, character creation included.
- **Contractions always** ("you're," "she's," never "you are," "she is"). Clause-final "you are" / "it is" that can't contract ("Whatever you are now") is fine.
- **No em dashes** except a paired parenthetical aside or a dialogue cut-off. Never as a connector.
- **Banned:** "Not a question."; "A beat." / "A pause."; negation-before-reveal ("Not X. Y." / "not because X, but because Y"); Not/Not or No/No stacks; the "You're not the star. You're the girl who..." stanza formula; "the kind of" / "the kind that"; "the way" (anywhere); "in a way"; "genuinely" in narration; paired "Something X. Something Y."; formal uncontracted language; walls of text (break prose over ~300 visible characters).
- **Crude, anatomical register** for anything erotic or aroused ("nipples," "pussy," "clit," "cunt"). No euphemisms.
- Match the game's existing voice. Deviation from the game's writing style is an error.
- The author is "Nathan" in design docs. Nathan's decisions win over any doc default.

### 2.2 How to report
- After each step: short summary, quote the new prose so the author can review it, flag judgment calls and found bugs, then say you're ready for the next step. The author likes sample prose quoted in the reply.
- Surface bugs you find instead of silently fixing things outside scope, unless the fix is tiny and clearly in the spirit of the step (then flag it).
- When a spec targets code that turns out to be dead or unreachable, **stop and ask** before writing prose for it (this happened with the store walks in Step 10).

### 2.3 Git
- Commit and push after each step: `git push -u origin <assigned branch>`.
- End commit messages with the attribution lines the session's system reminder provides. Never put a model name in commits or files.
- Do not open a pull request unless asked.
- Update this handoff doc (step table + "as shipped" notes) in the same commit as each step.

### 2.4 Technical rules for the HTML file
- One HTML file, ~217,000 lines, ~19 MB. Two `<script>` blocks. **Line numbers drift constantly; always search by function name or scene id.**
- All prose lives in JS strings, mostly single-quoted. **Every apostrophe inside a single-quoted string must be `\'`.** Watch for double-escaping (`\\'` breaks the string).
- Some scenes contain curly apostrophes (U+2019); match the existing encoding inside a string you edit.
- The picker code contains literal `★ ◆ · ›` characters. Match them literally in Python `str.replace`.
- **Edit with Python** (`str.replace` with an `assert s.count(old)==1` guard). Write the edit script to a file in the scratchpad and run it once; guard with `git diff --quiet &&` so it can't apply twice. Never `sed` with `\x27`.
- Python gotchas seen this session: a triple-quoted string that ends in `"` needs `\"` before the closing `"""`; a key like `dye` also matches inside `dyeBold` (use word boundaries).
- **Syntax check after every edit:**
  ```bash
  cd /home/user/claude-code && SP=<scratchpad> && python3 -c "
  import re;s=open('Vampire_Girl.html',encoding='utf-8').read()
  b=re.findall(r'<script>(.*?)</script>',s,re.S)
  [open('$SP/s%d.js'%i,'w',encoding='utf-8').write(x) for i,x in enumerate(b)]" && node --check $SP/s0.js && node --check $SP/s1.js && echo OK
  ```
- **Browser testing:** Playwright is installed globally, Chromium at `/opt/pw-browsers`. In a Node script: `require(require('child_process').execSync('npm root -g').toString().trim()+'/playwright')`, `page.goto('file:///home/user/claude-code/Vampire_Girl.html')`, wait ~1.5 s, call game functions through `page.evaluate`, collect `pageerror` events. Route `**/Images/**` to an empty SVG (images aren't in the repo).
- **Regression pattern that worked every step:** `git show HEAD:Vampire_Girl.html > $SP/orig.html`, render the touched scenes (or compute the touched numbers) for a **non-BO** player in both files, and assert they're byte-identical. Then dump the BO output and scan it for banned patterns and paragraphs over 300 chars.
- Scenes live in the global `story` object. `text` / `parts` / `choices` may be strings, arrays, or functions; `parts` entries may themselves be functions. `onEnter` runs once, at part 0, **before** `text` is evaluated. Choices support `effect: () => {}` (runs before `showScene(nextScene)`) and `action`.
- `showScene` sets the global `previousScene` right before `onEnter`.
- `advanceTime()` is a no-op until `gameState.timeStarted` (set at the vision), so prologue time costs do nothing.
- Viewport 412x915 at deviceScaleFactor 2 is a good phone check.

### 2.5 Game facts that matter for the remaining steps
- `hasTrait(id)` reads `gameState.traits.selectedTraits`.
- Clothing item shape: `{ id, name, category, appeal, price, owned, cleanliness, effect: [...], tags: [...] }`. Footwear adds `comfort, mobility, terrain, noise`. Inventory categories: `bras, panties, tops, bottoms, dresses, footwear, outerwear`.
- Outfit slots: `bra, panties, top, bottom, dress, outerwear, footwear, headwear, neckwear, earrings, rings, eyewear, other`. There is no `underwear` or `accessories` slot.
- A bra with no top or dress counts as visible underwear (outerwear doesn't cover it); she can't leave like that below 6001 corruption.
- `getSceneClothingVars()` (`cv`): `cv.isSkirt` is true when the bottom's **name** contains "skirt".
- `PRIVATE_UNEQUIP_SCENES` / `PRIVATE_CHANGE_SCENES` list private scenes (the "not in public" test).
- Backpack: `gameState.backpackCapacity.max`, `calculateBackpackUsage()`.
- Makeup Kit `{ id: 'makeup' }` in `inventory.consumables`; Hand Mirror `hand_mirror` and Hairbrush `hairbrush` in `inventory.personalCare`.
- Makeup states: `bodyState.makeup` is `false | true | 'premium' | 'smeared'`. Hair: `bodyState.hairGroom` is `'unkempt' | 'down' | 'styled' | 'salon'` (plus `hairStyle`, `hairStyleClass`, `syncLegacyHairField()`).
- Mental health: always use `modifyMentalHealth(n)` for new code.
- Day 1 is **Monday, June 9, 2025** (the default state and `newGame()` agree). It's summer; never write February, coats, or cold weather into early-game prose.
- Kelsie can only change outfits from her backpack inside an apartment, hotel, or porta potty, so she's always in the satin dress when she meets Jaewon.
- **Urban Edge and Noir Boutique have no named owners or clerks.** Maya and Vivienne were removed from the game entirely (relationships, scenes, flags). Staff are unnamed employees. Only Bargain Threads has an owner (Jack).
- Travel to a store lands on its `outside_*` scene; "Go inside" plays the store's intro on the first visit and the browse scene after.

---

## 3. What's Done

### 3.1 Step 1: The collection
- `BO_IMG` and `BEAUTY_OBSESSED_COLLECTION` (32 entries, just above `startingClothingDescriptions`). **Single source of truth. Never hardcode these items anywhere else.**
- Every entry: `collection: 'beauty_obsessed'`, `price: 0`, `owned: true`, `cleanliness: 100`, `image`, `effect`, `tags`, `description`. Footwear carries inline stats. Dresses (except the sherpa) carry `upscale: true`. Four pieces carry `sheer: true`.
- Helpers: `getBOItemDef(id)`, `isBOItem(item)`, `makeBOInventoryItem(id, overrides)`. **Always use `makeBOInventoryItem` when an item lands in inventory or an outfit slot.**
- Nathan's calls: `bo_underwired_lace_panties` is "Underwired Lace Panties". No colors in names. The **Diamond Cutout Mini Dress stays** (image `seamless-mini-dress.jpg`; the name fits it better).

| Id | Name | Cat | Appeal | Notes |
|---|---|---|---|---|
| bo_underwired_lace_bra | Underwired Lace Bra | bras | 15 | +3% Sed, +3% Allure XP |
| bo_underwired_lace_panties | Underwired Lace Panties | panties | 14 | +2% Sed, +2% Allure XP |
| bo_crisscross_lace_bra | Criss-Cross Lace Bralette | bras | 13 | |
| bo_crisscross_lace_panties | Criss-Cross Lace Thong | panties | 13 | |
| bo_naughty_nice_bra | Naughty & Nice Bra | bras | 15 | |
| bo_naughty_nice_panties | Naughty & Nice Thong | panties | 14 | |
| bo_black_tie_front_crop_top | Black Satin Tie-Front Crop Top | tops | 26 | |
| bo_keyhole_camisole_top | Black Keyhole Camisole | tops | 24 | |
| bo_denim_halter_crop_top | Denim Halter Crop Top | tops | 22 | |
| bo_cream_halter_tank_top | Cream Halter Tank | tops | 22 | |
| bo_satin_corset_top | Black Satin Corset Top | tops | 28 | +3% Allure XP |
| bo_pink_sequin_crop_top | Pink Sequin Lace Crop Top | tops | 26 | |
| bo_red_gingham_crop_top | Red Gingham Crop Top | tops | 22 | |
| bo_denim_booty_shorts | Lace-Up Denim Booty Shorts | bottoms | 24 | reads as pants |
| bo_denim_pleated_mini_skirt | Pleated Denim Mini Skirt | bottoms | 22 | |
| bo_white_fringe_mini_skirt | White Fringe Mini Skirt | bottoms | 24 | |
| bo_pink_fringe_mini_skirt | Hot Pink Fringe Mini Skirt | bottoms | 23 | |
| bo_mesh_mini_skirt | Sheer Mesh Mini Skirt | bottoms | 25 | sheer |
| bo_satin_pleated_micro_skirt | Satin Pleated Micro Skirt | bottoms | 27 | +2% Allure XP |
| bo_pink_sequin_mini_skirt | Pink Sequin Mini Skirt | bottoms | 25 | |
| bo_red_gingham_mini_skirt | Red Gingham Ruffle Skirt | bottoms | 22 | |
| bo_white_flared_mini_skirt | White Flared Mini Skirt | bottoms | 20 | -3% Fatigue |
| bo_satin_black_mini_dress | Black Satin Mini Dress | dresses | 80 | upscale, +4% Allure XP; starting dress |
| bo_square_neck_mini_dress | Square-Neck Black Mini Dress | dresses | 74 | upscale |
| bo_leopard_halter_mini_dress | Sheer Leopard Mini Dress | dresses | 80 | upscale, sheer |
| bo_off_shoulder_mesh_mini_dress | Off-Shoulder Mesh Mini Dress | dresses | 78 | upscale, sheer |
| bo_rainbow_halter_mini_dress | Rainbow Fishnet Mini Dress | dresses | 72 | upscale, sheer |
| bo_diamond_cutout_mini_dress | Diamond Cutout Mini Dress | dresses | 82 | upscale |
| bo_sherpa_hoodie_dress | Camel Sherpa Hoodie Dress | dresses | 46 | NOT upscale; warm:2 |
| bo_satin_black_heels | Black Satin Stiletto Mules | footwear | 22 | |
| bo_denim_platform_heels | Denim Platform Mules | footwear | 18 | |
| bo_blue_orchid_heels | Blue Orchid Block Heels | footwear | 20 | |

gameState fields (initializer, new-game reset, migration defaults): `boRadiantDays: 0`, `boTier: 1`, `boRitualDay: 0`, `boRitualMissStreak: 0`; flags `boCollectionGranted`, `boFixedFaceAtHome`, `boSeenTier2/3/4`, `designerBackpack`. Later steps added flags `boUrbanStaffNoticed`, `boNoirStaffNoticed`, `visitedUrbanEdge`, `visitedNoirBoutique` and transient flags `_boLook`, `_streetReaction`, `_boStaffLine`.

### 3.2 Step 2: Sets, looks, Allure XP, sheer
- 3 underwear sets (29 total), 10 collection ensembles (18 total; each needs every piece). No legal outfit triggers two collection ensembles. Starting outfit fires Underwired Lace Set + Little Black Satin (+24).
- `allure_xp_bonus` effect, capped at 0.15, applied in `gainAllure()`.
- Sheer: `getVisibleSheerPieces()`, +1 Exhibition Thrill/hr in public (+1 more if bare behind it), ambient pools. Sheer never counts as visible underwear.
- "Your Collection" badge on item cards.

### 3.3 Step 3: The trait
- `TRAITS.pretty_face` → subtype `pretty_face`: `startBonus`, `passive`, `apply()` (`charisma += 2`, `applyBeautyObsessedStart()`), `evolutions` 1 to 4 keyed by `boRadiantDays` (`minDays`).
- Helpers: `isBeautyObsessed()`, `getBOTier()`, `getBOEvolutionData()`, and (Step 12) `boVal(key, fallback)`.

| Tier | Name | minDays | baseAppeal | makeupAppeal | allureFloor | firstImpression | ritualGlow | tipBonus | smearResist | coherenceAt | bonusCharisma | other |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Beauty Obsessed | 0 | 15 | 28 | 6 | 3 | 6 | 0.08 | 0 | 0.70 | 0 | |
| 2 | Polished | 7 | 20 | 30 | 9 | 4 | 7 | 0.10 | 0.20 | 0.70 | 1 | |
| 3 | Flawless | 21 | 25 | 32 | 12 | 5 | 8 | 0.12 | 0.40 | 0.65 | 1 | |
| 4 | Iconic | 45 | 30 | 35 | 15 | 6 | 10 | 0.15 | 0.60 | 0.65 | 1 | relationshipMult 1.10 |

**All numbers are live except `bonusCharisma` (Step 14).** Every tier's passive now says "Grooming flaws hit at half strength, and a short nap won't wreck your hair," and from Polished up, "any makeup on your face" resists smears.

### 3.4 Old character creation removed
TRAITS entries hold only: parent `{ id, name, icon, color, subtypes }`, subtype `{ id, name, startBonus, passive, apply, evolutions? }`.

### 3.5 Steps 4 and 5: Character creation
- Trait choice `trait_beauty_obsessed` in `q_trait_athlete` (age 5). Trait picker tile grid `ccRenderTraitPicker()` for all five trait questions.
- Quiet and Sharp Eyes exist in `TRAITS`.
- Weave via `ccIsBO()` in `q_lesson`, `q_clique`, `q_puberty_full_bloom`, `q_prom`, `q_reputation`.
- Always gorgeous: `q_puberty_face` shows a mirror scene and a single `face_gorgeous` choice (`resultHeading: 'Gorgeous'`).
- No face-privilege Strength XP penalty for her.
- `q_best_friend_secret` checks used a stale `tell_maya` id; now `spread_it` (the "Tell people" betrayal branch).

### 3.6 Step 6: Starting state
- `applyBeautyObsessedStart()`, `grantStartingPacking()` (28 pieces, 5 Makeup Kits, Hand Mirror, Hairbrush; bag 36/50), `stashWornDress()`.
- BO day-one appeal in her starting look, no makeup: **172**.

### 3.7 Step 7: The prologue at home
- `bathroom_panic` BO variant: part 1 adds "It always does."; part 2 has her lean in ("Every pore you've fought since seventh grade is gone. Somebody sanded you smooth overnight." / "Thirteen years of practice, and whatever happened last night did it better."); part 3 is a karma reaction to the face (angelic finds the chin scar she's covered since sixth grade; neutral "picking out a lip color for it"; demonic "The light likes both sides now"); the text screen is the illusion, then "The mascara's another story. It's halfway down your cheek.", her routine at her own counter, and a karma closer (angelic "If I can still do a wing, I'm still me."). `onEnter` for her: makeup true, hair down, `boFixedFaceAtHome`, +3 MH (non-demonic), `advanceTime(10)`.
- Her "Splash water on your face" choice reads "Run cold water over your wrists" (`remember_bathroom` opens with a BO wrists line).
- `occult_vampire_insight` fixed for everyone ("The calm holds." / "a face that had fangs and red eyes a minute ago").
- `explore_bathroom`, `room_search` (3 karma variants), `last_look_room`, `pack_after_cruel_words` BO swaps.

### 3.8 Step 8: Meeting Jaewon
- `getBOLookWords()`. BO variants in `jaewon_alley_conversation_start` (satin dress, designer backpack, done face; "you look way too good to be sitting in an alley at eight in the morning"), `jaewon_money_gift` + `_low_karma` ("a dress nicer than anything I own... your whole life in a backpack"), `accept_jaewon_couch` bus and apartment ("You look amazing, which is honestly kind of rude after the morning you've had. Shower anyway."), `jaewon_shower_accept` (drugstore shampoo beat).

### 3.9 Step 9: The clothing choice at Jaewon's
- Helpers `stashWornSlot(slot)` and `wearFromBag(ids)`: move real items between bag and outfit, never touch underwear, never lose or duplicate. **Use these for every story-scene outfit change.** `BO_JAEWON_LOOKS` = satin / garden / denim / cozy.
- Bug fixes for all players: no forced underwear, new `wear_skinny_tank`, `wear_comfy` uses real items (tank goes under the sweater so she's not in visible underwear), `jaewon_just_change` changes her, sundress scenes stash the dress, `wear_party_dress_again` keeps her shoes, `wear_jaewon_clothes` stashes worn top/bottom/outerwear.
- BO: `jaewon_just_change` (Garden Party in ninety seconds), `jaewon_clothes_check` (the jacket's the find; 3 choices), `own_clothes_check` (bag dump naming the sheer leopard dress, pink sequin co-ord, corset top, denim halter + booty shorts; 4 looks + pick-your-own), new `wear_bo_look` and `wear_bo_with_jacket`, `wear_jaewon_clothes` (3 karma variants). BO never reaches `wear_party_dress_again`.

### 3.10 Step 10: The city sees her
- The `walk_to_*` scenes and the older Jack chain (`bargain_entrance` / `jack_intro` / `jack_tour`) were orphaned and are deleted. Jack's canon intro is `bargain_threads_entrance` → `jack_interaction` / `jack_shows_around` → `bargain_threads_browse`; return visits use `jack_browse`.
- First-visit wiring on the three "Go inside" choices (`metJack`, `visitedUrbanEdge`, `visitedNoirBoutique`). Intro chains exit through `bargain_checkout` / `urban_checkout`.
- `getStreetReaction(store)` (3 appeal bands, coherence line, BO "clocks your shoes first, then the bag, then you") shown in `outside_*` on arrival only (`isStreetArrival`, `STORE_INTERIOR_SCENES`). Helpers `proseItemName`, `getWornOutfitPhrase`, `getWornBOPiece`, `capFirst`.
- Jack's once-over names her real outfit. One-time BO staff lines at Urban Edge ("Okay, wait. Where'd you get that?") and Noir ("It isn't one of ours. I'm a little jealous.").
- `loadSaveData` maps renamed and deleted scene ids (`_renamedScenes`).

### 3.11 Step 11: Upscale access
- `meetsUpscaleDressCode()` → `{ ok, via }` ('beauty', 'noir', 'urban', 'jaewon'); `isWearingNoirBoutique()` wraps it. BO passes in any upscale collection dress (all but the sherpa) or a collection top + collection bottom.
- Noir list fixed to real catalog ids across real slots; `statement_ring` (shared with Urban Edge) replaced by `diamond_ring`.
- Door lines: Lucius first-visit bouncer ("His eyes stop at the dress"), Regency doorman nod, Lucius checklist names her collection, `ownsBOPieces()` / `_boDoorNote()` in the dress-code hints.

### 3.12 Step 12: The buffs
- Helpers: `boVal(key, fallback)`, `getOwnMakeupAppeal()`, `trySmearMakeup(source)`, `getBOTipMultiplier()`, `getGodpeiaServicePrice(base)`.
- Live: base appeal (clothed + naked), her own makeup (both branches, Current Stats "Makeup (your own)", face lever display, benefits list, Body State), `applyMakeup` takes 5 min with a one-line scene, allure floor (getAllure + both breakdowns), first-impression weight, Coordinated threshold (Try On follows), diner tips at all three sites, Godpeia 15% off (flat services and the hair menu; face upgrade untouched), Iconic ×1.10 relationships in `modifyRelationship`'s `applyBonus` while Attractive+.
- **Smear resistance covers any makeup on her face, Godpeia premium included** (Nathan's call). `trySmearMakeup` is used at all seven smear sites (rain, heat, 2× sleep, 3× sweat/stripping).
- Body & Presentation face box rewritten: tier, a row per buff with live numbers, "Thirteen years in front of the mirror..." Current Stats shows "Beauty Obsessed (Tier): +N".
- Bug fix for all players: Body State showed applied makeup as +10 while the math gave +20.
- Note for balance: imperfection penalties scale with base appeal, so a higher tier also makes a neglected state cost slightly more allure.

### 3.13 Step 13: The ritual and the mirror
- Helpers (just below `isBeautyObsessed`): `boRoutineDoneToday`, `getBORitualGlow`, `isBORoutineLapsed`, `getBOSkippedRoutinePenalty(base)`, `getBOSkippedRoutineLine`, `processBORitualRollover`, `getBORoutineCardHTML`, `getBOMirrorLine`. Action `doBORoutine()` (next to `brushHair`), scene `bo_ritual_scene` (4 rotating variants keyed to `day % 4`; the 4th opens differently at a sink vs the hand mirror). `bo_ritual_scene` is in all three temporary-scene guards.
- Routine: free, 20 min, +3 MH, once a day, any `canApplyMakeupHere()` location or the Hand Mirror. "Your Routine" card leads the personal-care inventory section for her (shows even if she has no personal-care items).
- Fresh Skin: +`ritualGlow` in both appeal branches, Current Stats ("Fresh Skin (your routine)"), benefits list, and a "Daily routine" row in the Body & Presentation face box.
- Skipping: `processBORitualRollover()` runs at the end of `onNewDay()` (gated on `arrivedInCity`). A day without the routine increments `boRitualMissStreak`; from 2, −2 MH per rollover (one notification at 2). Skipped Routine imperfection: flat 3% of base (min 1), pushed straight into the narratives (never face-softened or inverted), naked branch too. Clears the moment she does the routine. No new gameState fields.
- Mirror: `applyMirrorMentalHealth` doubles the delta for her; `own_apartment_mirror` adds `getBOMirrorLine()` after the face line (tier × routine-done, 8 lines); `vampireInMirror` has a BO response ("the best work you've ever done. You aren't sure you did it.") that replaces the karma responses.
- Streak: charisma XP and MH ×1.5 (rounded); breaking a 3+ streak costs her −3 MH with her own notification.
- **Bug fix (all players):** `canApplyMakeupHere()` listed three dead ids (`your_apartment_main`, `lucius_lounge_main`, `lucius_lounge_vip`) and missed her own apartment entirely. Now: own apartment main + bathroom, Jaewon's bathroom, `lucius_lounge` + restroom, diner customer restroom, motel / Parkside / hotel bathrooms.

---

## 4. Decisions Already Made

- Designer backpack: 50 slots.
- The collection **replaces** the basic starting clothes. She starts with zero pants and zero outerwear, on purpose. **Don't patch the gap.** Jaewon's cropped leather jacket is the one thing she needs.
- The sherpa hoodie dress doesn't count toward upscale access.
- No Strength XP penalty of any kind for the trait.
- The daily routine (Step 13) is free. It only needs a mirror and twenty minutes.
- She's always gorgeous.
- Keep the id `pretty_face`.
- Existing bugs found inside the overhaul get fixed for **all** players.
- Smear resistance applies to Godpeia makeup too. The passives mention nap-proof hair.
- The Diamond Cutout Mini Dress stays.
- No named staff at Urban Edge or Noir Boutique.

---

## 5. Helper and Hook Index (search by name)

Trait: `isBeautyObsessed`, `getBOTier`, `getBOEvolutionData`, `boVal`, `updateTraitsDisplay`, `showTraitEvolutionPopup`.
Collection: `BEAUTY_OBSESSED_COLLECTION`, `getBOItemDef`, `isBOItem`, `makeBOInventoryItem`, `ownsBOPieces`, `getWornBOPiece`, `applyBeautyObsessedStart`, `grantStartingPacking`.
Outfit changes: `wearFromBag`, `stashWornSlot`, `stashWornDress`, `BO_JAEWON_LOOKS`.
Prose: `getBOLookWords`, `proseItemName`, `getWornOutfitPhrase`, `capFirst`, `getStreetReaction`, `bodyTypeDescriptor`, `getSynergyFlavorText`, `getSceneClothingVars`.
Appeal: `getBodyBaseAppeal`, `calculateTotalAppeal`, `getAppealPenalties`, `getOwnMakeupAppeal`, `getSpaGlowBonus`, `getHairGroomFlat`, `APPEAL_TIERS`, `getAppealTier`, `appealTierIndex`, `appealTierAtLeast`, `calculateStyleCoherence`, `calculateStyleCoherenceWith`.
Allure: `getAllure`, `gainAllure`, `getFaceFirstImpression`.
Grooming: `applyMakeup`, `brushHair`, `trySmearMakeup`, `canApplyMakeupHere`, `hasHandMirror`, `hasHairbrush`.
Money: `getBOTipMultiplier`, `getGodpeiaServicePrice`, `chargeGodpeiaHairService`, `GODPEIA_HAIR_PRICES`.
Doors: `meetsUpscaleDressCode`, `isWearingNoirBoutique`, `getLocationOutfitSuggestion`, `_boDoorNote`.
Daily: `evaluatePresentationStreak`, `modifyMentalHealth`, `modifyRelationship`.
Panels: `renderBodyProfilePanel` (Body & Presentation; `_bpRow`, `_bpDesc`, `_bpSection`), `_buildAllStatsIntoTemp` (Current Stats).

Existing BO hooks not tier-scaled (by design): glossy "down" hair +6 (`getHairGroomFlat`), slower hair oiling, hair growth +0.1/day, grooming softener ×0.5, nap-proof hair in the sleep handler.

---

## 6. Remaining Steps

| Step | Title | Status |
|---|---|---|
| 1 to 12 | | Done |
| 13 | The ritual and the mirror | Done |
| 14 | Evolution: staying radiant | **NEXT** |
| 15 | UI and Tips & Guide | |
| 16 | Save migration | |
| 17 | Polish and integration | |

All new prose: 2nd person present, style guide clean, short paragraphs, escaped apostrophes, `node --check` after every step. BO variants are variants, not rewrites: match the surrounding prose's length, and leave non-BO text byte-identical except where a bug fix requires it.

---

## 13. Step 13: The Ritual and the Mirror

### 13A. The morning routine
New action **"Do your routine,"** once per day at any mirror location (`canApplyMakeupHere()`) or anywhere with the Hand Mirror. It appears in the personal-care inventory panel beside Apply Makeup and Brush Hair, for her only. (Find where Apply Makeup / Brush Hair buttons are built; `applyMakeup` and `brushHair` save `previousScene` and show a temporary scene, then return; follow the same pattern, and add the new scene to the temporary-scene guards they use.)
- 20 minutes. Sets `boRitualDay = day`, resets `boRitualMissStreak = 0`, `modifyMentalHealth(3)`.
- Grants a **Fresh Skin** glow for the rest of the day: +`ritualGlow` appeal (6/7/8/10) via `boVal('ritualGlow', 0)`. Same pattern as `getSpaGlowBonus()`: its own function `getBORitualGlow()` (returns the glow when `boRitualDay === today`), added in **both** appeal branches and shown in the Current Stats breakdown and benefits list.
- Short scene `bo_ritual_scene` with a rotating pool of three or four one-paragraph variants. Karma coloring optional.

### 13B. Skipping it
At rollover, if she skipped yesterday's routine, increment `boRitualMissStreak`. From the second consecutive skip: −2 MH per day, and a mild **"Skipped Routine"** imperfection (−3% of base appeal) in the Imperfections list with a condition-aware line. It clears the moment she does the routine. It's a small tax and the only real downside the trait carries. (Find the day-rollover code and the imperfections list via `getAppealPenalties` and the Imperfections display.)

### 13C. The mirror
The mirror interaction from the Appeal overhaul (search for the mirror scenes, e.g. `own_apartment_mirror`, and `mirrorLastDay`):
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
  - **Flawless (21 days):** rain, heat, a long night, and her face holds through all of it (this is the smear resistance made visible).
  - **Iconic (45 days):** someone in the city describes her to someone else, and she hears it secondhand. Karma and corruption coloring: angelic, pride in the work; demonic, the pleasure of being unforgettable to prey.
- Notification with the new tier name and a one-line summary of what improved.
- `showTraitEvolutionPopup(tierNum, iconSrc, newData, oldData, strGain, traitKey)` is how the athlete traits announce evolutions (there's already a `traitId === 'pretty_face' && sub.evolutions` branch near it); consider reusing it.

### 14C. Display
- Traits panel already shows the evolved name and passive.
- The Body & Presentation face box (Step 12) shows the tier and every number; add `boRadiantDays` progress toward the next tier ("12 / 21 radiant days to Flawless").

---

## 15. Step 15: UI and Tips & Guide

### 15A. UI
- Backpack header reads "Designer Backpack" and 50 when `flags.designerBackpack` is true.
- Confirm `allure_xp_bonus` shows in the shop Try On and the Current Stats clothing effects list.
- The routine action, the Fresh Skin glow, and the Skipped Routine imperfection all show in the appeal breakdown.

### 15B. Tips & Guide: Clothing Systems (`data-tips-section="clothing-systems"`)
- Matching Sets & Ensembles: underwear sets 26 → **29**; ensembles 8 → **18**, noting BO co-ords and looks need every piece, where the original eight need three of four.
- Item Effects & Perks: add Allure XP, its 15% cap, and "Beauty Obsessed collection: seduction, charisma, and Allure XP, which only her clothes carry."
- Occasion & Dress Codes: Lucius' Lounge and the Regency accept BO dresses (not the sherpa) and co-ords. Also fix any guide text that still names the old Noir ids or implies a ring from Urban Edge counts.
- New subsection **BEAUTY OBSESSED COLLECTION:** what she starts with, the sets and looks, the sheer pieces and their exhibition hook, the designer backpack.

### 15C. Tips & Guide: appearance sections
Two known stale BO mentions: around the Imperfections guide ("softens grooming flaws... She also...") and the line "The Beauty Obsessed trait is grooming devotion, not innate looks: +15 appeal, softer grooming flaws, and a short nap won't wreck your hair." (**now false: she's gorgeous**). Search the guide for "Beauty Obsessed" and update every mention (Imperfections, Beauty Obsessed & Hair, Maintenance Loop) to the new numbers and framing. Add one entry covering the trait end to end: age-five origin, always gorgeous, the collection, the buffs by tier, smear resistance on any makeup, Godpeia 15% off, the routine and its cost, evolution to Iconic, no Strength XP penalty. Also check the Allure XP modifier list (it lists "theater_kid / party_girl: ×1.08"; the collection's Allure XP bonus belongs there too). Update the character creation section of the guide if it describes trait selection or the face question.

Guide text follows the style rules (UI labels may use uncontracted phrasing per the style guide's exceptions).

---

## 16. Step 16: Save Migration

In the migration code (search `MIGRATION (Beauty Obsessed overhaul`):
- Defaults exist for the Step 1F fields. Add defaults for any field added since (Step 13's ritual state if new fields are introduced; the staff and visit flags default to falsy already).
- For saves with `pretty_face`:
  - `backpackCapacity.max` at least 50, `flags.designerBackpack = true`.
  - If `!flags.boCollectionGranted`, grant the 32 pieces via `makeBOInventoryItem`: into the backpack while there's room, the rest into `wardrobes.apartment` if she's living with Jaewon (check the flag the game uses, e.g. `stayingWithJaewon`), otherwise the backpack up to capacity. One notification explaining where her collection went. Set the flag.
  - Compute `boTier` from `boRadiantDays`.
- Normalize any item in inventory, outfit, or wardrobes whose id starts with `bo_` against `getBOItemDef()` so old copies pick up data changes (appeal, effects, tags, name, upscale, sheer), preserving cleanliness and damage.
- Already in place this session: `loadSaveData` maps renamed/deleted scene ids; the migration block deletes `relationships.maya` / `vivienne` and carries `metMaya` / `metVivienne` into `visitedUrbanEdge` / `visitedNoirBoutique`.

---

## 17. Step 17: Polish and Integration

### 17A. Style audit
Every new and touched string from Steps 1 to 16 against the style guide checklist. Paragraph density scan (>300 visible chars) on every new scene. Escapes and curly-apostrophe consistency. `node --check` clean.

### 17B. Balance pass
- A fully groomed BO Kelsie lands high on day one (172 before makeup; roughly 300 fully groomed at tier 1 with hygiene 85). That's the design, but confirm the early seduction bands and Lucius' Lounge don't trivialize the first week. Lucius still demands hygiene 70+, styled hair, and makeup.
- Confirm seduction and charisma XP caps hold with a full collection outfit plus Jaewon's jacket.
- Confirm the Skipped Routine tax stays mild and never spirals.
- Confirm no single outfit triggers two BO ensembles.
- Look at how imperfection penalties scale with her higher base appeal at Iconic.

---

## 18. Open Items for Nathan (minor, raise if relevant)

- The Lucius bouncer's BO line only exists in the first-visit scenes (regular visits have no once-over).
- The Regency doorman's refusal still says "designer attire from Noir Boutique or at minimum an evening dress from Urban Edge" (he wouldn't know her collection).
- Old saves that shopped at Bargain Threads before Step 10 never set `metJack`, so they'll see Jack's intro once.
- A little girl named Maya appears in one street reaction line ("Don't stare, Maya."). Unrelated to the stores; left alone.

## 19. Bug List

| # | Bug | Status |
|---|---|---|
| 1 | Beauty Obsessed unreachable in CC | Fixed (Step 4) |
| 2 | `wear_*` scenes force-equip the basic white underwear | Fixed (Step 9) |
| 3 | "Wear the skinny jeans and tank top" puts on the sundress | Fixed (Step 9) |
| 4 | `wear_comfy` equips id-less items (and leaves the dress on) | Fixed (Step 9) |
| 5 | `jaewon_just_change` changes nothing | Fixed (Step 9) |
| 6 | Two of three prologue packing arrays omit `cleanliness` | Fixed (Step 6) |
| 7 | `walk_to_*` scenes defined twice (and unreachable) | Fixed (Step 10) |
| 8 | `isWearingNoirBoutique()` checks nonexistent ids and slots | Fixed (Step 11) |
| 9 | Quiet and Sharp Eyes missing from `TRAITS` | Fixed |
| 10 | Set badge reported only the first ensemble an item belonged to | Fixed (Step 2) |
| 11 | `wear_sundress` overwrites the worn dress without stashing it | Fixed (Step 9) |
| 12 | `occult_vampire_insight` contradicted the settled illusion | Fixed (Step 8 turn) |
| 13 | Maya / Vivienne existed as relationships with no role | Removed |
| 14 | Store first-visit intros unreachable | Fixed (Step 10) |
| 15 | Default game date said February 3, 2026 | Fixed (June 9, 2025) |
| 16 | `tell_maya` stale choice id in character creation | Fixed (→ `spread_it`) |
| 17 | Body State showed applied makeup as +10 | Fixed (Step 12) |
| 18 | Intro chains exited through the old `after_shopping` GPS scene | Fixed (Step 10) |
| 19 | `canApplyMakeupHere()` listed dead scene ids, missed her own apartment and every bathroom | Fixed (Step 13) |
