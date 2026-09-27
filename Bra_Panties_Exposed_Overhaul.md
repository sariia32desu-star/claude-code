# Bra Exposed & Panties Exposed: Exposure State Overhaul

**Target implementer:** Claude Opus
**File:** `Vampire_Girl.html`
**Companion docs (load all of them before any step that writes prose):**
- `VampireGirl_Style_Guide_SS.md`: every writing rule. Mandatory.
- `VampireGirl_Body_Branching_Standards.md`: `gameState.bodyAppearance`, `getSceneClothingVars()`, ternary syntax, BP targets, encoding rules.
- `VampireGirl_Sex_Standards.md`: must be loaded for every intimate or sexual beat (Steps 8, 9, 10, 12, 13, 14).
- `Arousal_System_Overhaul.md`: arousal gains go through `applyArousal()`, tiers, corruption voices.

**Scope:** The game has four named exhibition states (Underwear Only, Topless, Bottomless, Naked) and a full sheer layer on top of them. Every one of those states has a mechanical fingerprint, NPC reactions, harassment routing, a gang scene in the district and in the slums, an Exhibition Seduction branch, ambient lines, evidence rules, venue rules, and a Kelsie who reacts to it in three corruption voices. Two real outfits fall through the cracks: a bra with a bottom and no top, and a top with panties and no bottom. `getExposureLevel()` calls both of them `clothed`.

That one word is the whole problem. Because the core function says `clothed`, almost every system in the game treats Kelsie as dressed:

1. **The law treats them identically, and wrongly.** A bra with jeans and panties with a tee get the same 6001 corruption leave gate, the same refusal on the bus, the same guaranteed night assault. The world doesn't work that way. A bra with a skirt is a bold look. Panties with a tee is indecent exposure.
2. **The street doesn't see them.** Neither state gets exhibition ambient lines, style comments, evidence, or seduction-exposure lines. Kelsie walks down Commercial with her ass out under a crop top and the street reacts like she's in jeans.
3. **The scenes that do fire read the wrong outfit.** Harassment fires every day (it checks `isWearingVisibleUnderwear()`), but `street_harassment_event` falls into its clothed branch. `getSceneClothingVars()` returns `topName: 'top'` when there's no top, so prose says "your top" on a girl in her bra. `getUnderwearVisibilityDesc()` says "wearing only your panties" on a girl in a tee.
4. **There's no content.** Neither state has a gang scene, a seduction branch, deliberate acts, or accidental events built for it. They're the only exposure states in the game without their own scenes.

This overhaul fixes all four. It promotes both outfits to real exposure levels, splits them by legality, and then gives each one the depth the other states have:

- **Panties Exposed is a crime.** It gets the full exhibition treatment, on equal footing with Underwear Only and Bottomless: harassment routing into its own district and slums scenes, its own Exhibition Seduction branch, evidence, venue blocks, deliberate acts, and a police response.
- **Bra Exposed is legal.** It's a look: a bralette with high-waist jeans, a sports bra on a run, a push-up bra under an open blazer that got taken off. The law leaves it alone, most doors stay open, and the bus still takes her fare. But eyes don't leave her chest, her nipples know it, and her pussy answers every look. It gets its own register: bold, erotic, and on the edge of tipping over. A mesh bra, or a cotton bra soaked see-through, tips it over into a crime.

**Canon anchor (from the Tips & Guide):** exhibitionism is Kelsie's defining kink. Being seen exposed turns her on deeply at every corruption tier. At Innocent she's mortified and aroused anyway. Every tier of every piece of prose in this doc names what her body's doing.

When this is done, a player who steps out in her bra and a skirt should feel the street tilt toward her, and a player who steps out with her panties showing under a tee should feel the city close in: the stares, the phones, the men who follow her, the cop who slows down, and her own wet cunt making it worse.

---

## How to Read This Document

Organized into **17 sequential steps**. One step per output. Wait for confirmation before proceeding. Do not bundle steps.

Conventions from every prior overhaul apply: short paragraphs, contractions, escaped apostrophes in every single-quoted JS string, `node --check` on both script blocks after every step, no AI-isms, no banned style-guide patterns. All prose is 2nd person present tense. Intimate prose follows the Sex Standards doc. Body and clothing branching follows the Body Branching Standards doc: variable extraction at the top of every text function, `gameState.bodyAppearance` only, all five `bodyType` values filled, `'large'`/`'small'`/`'average'` for breasts and butt, cv for every garment name.

**Corruption voices used throughout:** Innocent (0–2000), Curious/Experienced (2001–6000), Corrupted (6001–8000), Deeply Corrupted (8001+). Where a table below lists three voices, Corrupted and Deeply Corrupted share the high voice unless the step says otherwise. Gang scenes use the existing three-tier split (below 4001, 4001–8000, 8001+) to match `district_exhibition_underwear`.

**Mesh first.** The Mesh Overhaul already owns every see-through version of these outfits: `sheer_lingerie_top` (a mesh bra with nothing over it) and `sheer_lingerie_bottom` (mesh panties with nothing over them). Don't duplicate that content. Where this doc says "Bra Exposed" it means an opaque bra; "Panties Exposed" means opaque panties. Each step says how the sheer versions route.

---

## Decisions to Confirm Before Step 1

These are design calls the rest of the doc depends on. Each has a recommendation; confirm or change them before building.

1. **Bus and Uber for Bra Exposed.** The current build refuses both. If Bra Exposed is legal, the recommendation is that both rides take her, with a driver reaction line (the same pattern `getSheerTransitReaction()` uses for bare behind mesh). A mesh bra or a soaked-through bra is still refused. Panties Exposed stays refused.
2. **Corruption gate for Bra Exposed.** Recommendation: 2001 (Curious) to walk out in it deliberately, matching `sheer_braless`. A sports bra has no gate at all: it's workout clothing. Panties Exposed stays at 6001.
3. **Police clock for Panties Exposed.** Sheer lingerie gets a three-hour police clock (`tickSheerLingeriePolice()`). Underwear Only has none. Recommendation: give Panties Exposed a four-hour clock (its lower evidence severity buys an extra hour), and flag Underwear Only's missing clock as a follow-up outside this overhaul.
4. **Thrill rates.** The current build gives both states +2 thrill per hour. Recommendation: Panties Exposed rises to +3 (the same as Underwear Only, since the crotch is what makes both indecent), Bra Exposed stays at +2, and a sports bra drops to +1.

---

## Existing Systems This Connects To (verified in the current file)

Line numbers drift. Always re-grep the function name.

**Exposure core**
- `getExposureLevel()`: returns `'naked'`, `'topless'`, `'bottomless'`, `'underwear_only'`, or `'clothed'`. Both target outfits land in `'clothed'`.
- `getVisibleExposureLevel()`: applies outerwear concealment (`getOuterwearConcealment()` returns `'full'`, `'partial'`, or `'none'`). A full coat hides everything; a partial jacket hides the chest.
- `isBraVisible()`, `arePantiesVisible()`, `isWearingVisibleUnderwear()`, `getUnderwearExposureType()` (`'both'`, `'bra_only'`, `'panties_only'`). Note: `arePantiesVisible()` treats a top with `category === 'dress'` as covering the hips; `isBraVisible()` and `getExposureLevel()` don't. Reconcile in Step 1.
- `isEffectivelyTopless()`, `isEffectivelyBottomless()`, `isEffectivelyNaked()`.
- `getSceneClothingVars()`: 14 documented properties plus the Mesh additions (`sheerState`, `braSheer`, `pantiesSheer`, `seeThroughChest`, `seeThroughHips`, `meshNoun`, fabric nouns and adjectives). Falls back to `topName: 'top'` and `bottomName: 'skirt'` when the garment is missing.
- `getSheerState()`: only computes a sheer state when `getExposureLevel()` is `'clothed'` or `'underwear_only'`. Promoting the new levels without updating this gate silently kills `sheer_lingerie_top` and `sheer_lingerie_bottom`.
- `proseItemName(item)`, `getFabricNoun()`, `getFabricAdj()`, `resolveFabricTokens()`, `_sheerBodyCtx()` (body context object: `tits`, `ass`, `hips`, `frame`, `bra`, `panties`).
- `getUnderwearVisibilityDesc()`: used by `night_rape_ambush`; returns "wearing only your panties" for Panties Exposed (wrong: she's wearing a top).

**Gates and law**
- `checkVisibleUnderwearCorruption()` / `getVisibleUnderwearGate()`: 6001 for any visible underwear, 8001 for the full sheer set; now also covers topless/bottomless with nothing showing.
- `unequipClothing()`: simulated-outfit undress block below 6001 (only blocks a new exposure).
- `handleUnderwearDistrictEntry(districtId)`: hard block, then per-type NPC vignette plus Kelsie response. Called from every district street including slums. Suspicion +2 (underwear), +3 (topless), +4 (bottomless). Arousal is added directly to `gameState.arousal`, bypassing `applyArousal()`.
- `handleSheerDistrictEntry()`, `SHEER_STATE_GATES`, `tickSheerLingeriePolice()` / `handleSheerPoliceArrive()` (three-hour clock, then `handleNakedEscape()`).
- `exposureRestrictedBuildings` and `getExposureBlockMessage(buildingName)` (checked in `showScene`); `getSheerVenueBlockMessage()` with `SHEER_BRALESS_BLOCKED_VENUES` and `SHEER_BARE_ALLOWED_VENUES`.
- `travelToLocation()`: sheer transit reaction, then bus and Uber exposure refusal.
- `checkExhibitionEvidence(sceneId)`: indecent exposure ledger entries (camera 30% in Commercial/Downtown, witness 15% in Residential/park, none in the slums), severity minor/moderate/major. `getCruzSheerExposureLine()` reads the ledger.

**Passive layer**
- `advanceTime()` passive block: braless +1/hr, commando +2/hr, Underwear Only +3/hr, Bra/Panties Exposed +2/hr (the `underwearPartialThrill` accumulator added this session), topless, bottomless, naked, sheer, and wet rates; the `_anyExhibitionState` decay hold; `exhibitionStats` minute totals (`underwearMinutesTotal` currently lumps every partial state together).
- `isPrivateExposureScene()`: no thrill at home or at Jaewon's.
- `calculateTotalAppeal()`: `_undressedForPenalty` suppresses imperfection penalties in any visible-underwear state.

**Street content**
- `checkExhibitionAmbientLine(sceneId)` and `EXHIBITION_AMBIENT_LINES` (pools per exposure level and per sheer lingerie state, three corruption voices, `{bra}` / `{panties}` tokens).
- `getUnderwearNPCVignette(type)`: `bothPool`, `braPool`, `pantiesPool`, five vignettes each. The `braPool` is written as a scandal ("Did you drop your shirt? Were you mugged?"), which no longer fits a legal Bra Exposed.
- `getToplessNPCVignette()`, `getBottomlessNPCVignette()`, `getToplessKelsieResponse()`, `getBottomlessKelsieResponse()`: the model for dedicated per-state response functions.
- NPC style comments (`SHEER_STYLE_COMMENTS`, the style-comment block keyed by coherence), `LUCIUS_SHEER_DOOR_LINES` / `getLuciusSheerDoorLine()`, the Hardy Bar sheer line, `getRubySheerLine()`, `getJaewonSheerReaction()` / `getJaewonBralessCommandoReaction()`.

**Harassment and assault**
- `checkDistrictHarassment()`: guaranteed once a day for any visible underwear and for topless/bottomless/naked; otherwise district/time chance plus modifiers.
- `street_harassment_event` / `street_harassment_grope` (district) and the slums pair: an `exhibState` ladder (`naked`, `bottomless`, `topless`, `underwear_only`, `braless_commando`, `braless`, `commando`, sheer states, clothed) for the opener and the grope; grope gains 25/25 for exhibition states, 15/20 otherwise; `getHarassExhibScene(area)` forces the matching gang scene.
- Gang scenes: `district_exhibition_underwear|topless|bottomless|naked`, `district_wet_exhibition`, `district_sheer_exhibition`, and the six `slums_*` equivalents. Three tiers each; `completeSexAssaultMale({ time: 15, hygiene: -30, corruptionGain: 25, thrill: 15, ... })`; pregnancy check; Gallery registration.
- `checkNightRapeEncounter()`: guaranteed at night for any visible underwear (Bra Exposed included), otherwise 15% base plus modifiers. `night_rape_ambush` reads `getUnderwearVisibilityDesc()`.
- `slums_passout_rape_main` branches on exposure.

**Events and deliberate acts**
- The 20 exhibition events (`exhibition_wind_trigger` through `exhibition_turnstile_trigger`) registered in the district event pool with garment conditions (skirt/dress events and top events), `hold_bra` / `hold_panties` / `hold_bare` variants, outerwear concealment multipliers, and Mesh Step 10 sheer multipliers.
- `checkAccidentalExposure(districtId)`: braless/commando accidents; returns early for any visible-underwear type.
- `getFlashExhibitionChoice()` (6001+, thrill 70), `getPoseExhibitionChoice()` (8001+, thrill 75, topless/bottomless/naked or sheer pose states), the public masturbation choice (`exhibition_masturbation_choose`, `exhibition_masturbate_alley`, `exhibition_masturbate_park_bench`, 6001+ with thrill and arousal gates, once per day each), `getSheerExhibitionChoices()`.
- `riverside_park`: exposure-aware description for naked, topless, bottomless; nothing for underwear states.

**Seduction**
- `hunt_seduction` choices: at 6001+ and appeal 80+, routes `underwear_only` to Branch A (`exhibition_seduce_bra_*`), `topless` to B (`top`), `bottomless` to C (`btm`), `naked` to D (`nkd`). Each branch has twelve scenes per gender: `approach`, `proposition`, `success_pivot`, `soft_pivot`, `exit_success`, `exit_soft`, `failure_dismissive`, `failure_apologetic`, `failure_disturbed`, `failure_impressed_but_no`, `sex`, `sex_2`.
- `SHEER_SEDUCTION_BRANCH` maps `sheer_lingerie_top` to `top` and `sheer_lingerie_bottom` to `btm`, with sheer overlays.
- `_seductExposureLine(g)` / `_seductWithExposure()`: one exposure line for the regular street seduction flow.
- `seduce_lucius_man` / `seduce_lucius_woman`: an `isWearingVisibleUnderwear()` line.

**UI**
- Appearance tab (`updateAppearanceDisplay()`, `getUnderClothesBannersHTML()`, `getSheerLingerieBoxInfo()`): BRA EXPOSED / PANTIES EXPOSED boxes with `bra-exposed.png` / `panties-exposed.png`.
- Current Stats exhibition card (exposure label now shows Bra Exposed / Panties Exposed), About Stats, Tips & Guide exhibitionism entry (lists both states), the Scene Gallery registry, the cheat panel.
- `handleDamagedClothing()`: combat destruction messages.

---

## Step 1: Foundation: Two New Exposure Levels

### 1A. Promote both outfits in `getExposureLevel()`

Add two return values. Order matters: the more exposed state wins.

```javascript
function getExposureLevel() {
    // Returns: 'naked', 'topless', 'bottomless', 'underwear_only',
    // 'panties_exposed', 'bra_exposed', or 'clothed'
    const o = gameState.currentOutfit;
    const noTop = !o.top && !o.dress;
    const noBottom = !o.bottom && !o.dress;
    const noBra = !o.bra;
    const noPanties = !o.panties;
    if (noTop && noBottom && noBra && noPanties) return 'naked';
    if (noTop && noBra) return 'topless';
    if (noBottom && noPanties) return 'bottomless';
    if (noTop && noBottom) return 'underwear_only';
    if (noBottom) return 'panties_exposed';   // top on, panties showing
    if (noTop) return 'bra_exposed';          // bottom on, bra showing
    return 'clothed';
}
```

Reconcile the dress-category quirk: `arePantiesVisible()` counts a top whose `category === 'dress'` as covering the hips. Pick one rule and apply it in `getExposureLevel()`, `isBraVisible()`, and `arePantiesVisible()` alike. Recommendation: a top with `category === 'dress'` covers both zones everywhere.

### 1B. Concealment in `getVisibleExposureLevel()`

| Actual | Full coat | Partial jacket | None |
|---|---|---|---|
| `bra_exposed` | `clothed` | `clothed` | `bra_exposed` |
| `panties_exposed` | `clothed` | `panties_exposed` | `panties_exposed` |

### 1C. Keep the sheer layer alive

`getSheerState()` only computes when the level is `'clothed'` or `'underwear_only'`. Add `'bra_exposed'` and `'panties_exposed'` to that gate, or `sheer_lingerie_top` and `sheer_lingerie_bottom` vanish the moment Step 1 lands. Audit `isSheerExposureState()` and every Mesh function that checks `_level !== 'clothed' && _level !== 'underwear_only'`.

### 1D. Legality helpers

Two helpers every later step uses instead of raw level checks:

```javascript
// True when the law sees her as dressed: clothed, or an opaque bra on show.
// A mesh bra (sheer_lingerie_top) or a soaked-through bra (Step 2D) is indecent.
function isLegallyDressed() {
    var lvl = getVisibleExposureLevel();
    if (lvl === 'clothed') return !isCriminalSheerState();
    if (lvl === 'bra_exposed') return !isCriminalSheerState() && !isWetBraThrough();
    return false;
}

// True whenever strangers can see something they'd call exposure, legal or not.
function isVisiblyExposed() {
    return getVisibleExposureLevel() !== 'clothed' || isSheerExposureState(getSheerState().state);
}
```

`isCriminalSheerState()` wraps the Mesh states that already count as indecent (lingerie states, bare, braless and commando behind mesh). `isWetBraThrough()` lands in Step 2D; stub it to `false` here.

### 1E. Clothing awareness additions to `getSceneClothingVars()`

The Body Branching doc warns that `topName` falls back to `'top'`. In these two states that fallback puts a garment in the prose that isn't there. Add:

| Property | Value |
|---|---|
| `exposureState` | `getExposureLevel()` |
| `braExposed` | `exposureState === 'bra_exposed'` (opaque or sheer) |
| `pantiesExposed` | `exposureState === 'panties_exposed'` |
| `braName` | `proseItemName(outfit.bra)` or `'bra'` |
| `pantiesName` | `proseItemName(outfit.panties)` or `'panties'` |
| `braStyle` | `'sporty'` (tag `sporty`), `'pushup'` (name has push-up or balconette), `'bralette'` (name has bralette), `'corset'` (corset or bustier), `'lace'` (material:lace), `'sheer'` (mesh profile), else `'plain'` |
| `pantiesCut` | `'thong'` (thong, g-string), `'cheeky'` (brazilian, cheeky), `'boyshort'` (boyshort, boy short), else `'brief'` |

Rules for prose: in `bra_exposed`, never write `cv.topName`; the chest garment is `cv.braName`. In `panties_exposed`, never write `cv.bottomName`; the hips garment is `cv.pantiesName`. `liftable` and `pulldown` are false in `panties_exposed` (there's no bottom to hike or pull), so every undressing beat branches on `cv.pantiesExposed` first.

### 1F. Fix `getUnderwearVisibilityDesc()`

| State | Current | Correct |
|---|---|---|
| Panties Exposed | "wearing only your panties" | "with your panties on show under your {top}" |
| Bra Exposed, commando | "with your bra exposed and no panties" | keep |
| Bra Exposed | "with your bra exposed" | "in your {bra} and {bottom}" |

### 1G. Consumer audit (no behavior change yet)

Promoting the levels changes what 39 call sites receive. Step 1 must leave every one of them behaving exactly as today, then each later step switches its own sites to the new behavior. Do it with a temporary shim in each site (`lvl === 'bra_exposed' || lvl === 'panties_exposed'` treated as `'clothed'`), marked `// BPE shim: Step N` so later steps can find them. The full site list and owning step is in the **Consumer Audit** table at the end of this doc.

### 1H. New gameState fields

```javascript
gameState.exhibitionStats.braExposedMinutesTotal = 0;
gameState.exhibitionStats.pantiesExposedMinutesTotal = 0;
gameState.pantiesExposedStartTime = null;        // police clock (Decision 3)
gameState.flags._braExposedReactionIdx = [];     // vignette de-dupe
gameState.flags._pantiesExposedReactionIdx = [];
gameState.flags._lastBraAccidentMinute = 0;
gameState.flags._lastPantiesAccidentMinute = 0;
gameState.flags._bpeActionMinutes = {};          // per-verb cooldowns (Step 12)
gameState.flags.seenFirstBraExposedWalk = false;  // one-time beats (Step 4D)
gameState.flags.seenFirstPantiesExposedWalk = false;
```

Defaults go in the save migration now; Step 17 re-verifies them.

---

## Step 2: The Law: Gates, Venues, Transit, Evidence, Police

This step is where the two states split. Everything here is mechanical; prose in this step is short system text written to the style guide.

### 2A. Leave gates

| State | Gate | Below the gate |
|---|---|---|
| Bra Exposed (opaque) | 2001 (Decision 2) | Refusal message, back to the previous scene |
| Bra Exposed, sports bra | none | Walks out freely |
| Bra Exposed, mesh bra | 6001 (`sheer_lingerie_top`, existing) | Existing sheer refusal |
| Panties Exposed (opaque) | 6001 (existing) | Existing underwear refusal, rewritten per 2B |
| Panties Exposed, mesh panties | 6001 (`sheer_lingerie_bottom`, existing) | Existing sheer refusal |

Split `getVisibleUnderwearGate()` so it returns the right gate per state instead of one number for any visible underwear. `clothesDestroyedInCombat` still waives every gate. `unequipClothing()` gets the same split: taking a top off over a bra and a bottom is blocked below 2001, not 6001.

### 2B. Refusal text

`handleUnderwearDistrictEntry()` already has `bra_only` and `panties_only` refusal descriptions. Rewrite them for the new split. Samples (system register, contractions, her body named):

- Bra Exposed, below 2001: *"You look down at yourself. Your {bra} and your {bottom}, and nothing else over your chest. It's legal. You know it's legal. You still can't make your feet go out the door, and your nipples are already stiff in the cups at the thought."*
- Panties Exposed, below 6001: *"You look down at yourself. Your {top} stops at your hips, and below that it's your {panties} and your bare legs. You're not walking out there like this. Your pussy clenches anyway."*

### 2C. Venue policy

Add a `BRA_EXPOSED_VENUE_POLICY` next to the sheer venue lists. Panties Exposed is blocked everywhere in `exposureRestrictedBuildings`, the same as Underwear Only.

| Venue (redirect id) | Bra Exposed | Sports bra | Why |
|---|---|---|---|
| Diner (`outside_diner`) | Blocked | Blocked | Ruby's shirts-required sign. Ruby line (Step 6) |
| Convenience store (`outside_convenience_store`) | Blocked | Blocked | Shirts-required sign on the door. Livie line (Step 6) |
| Library, hospital, blood bank | Blocked | Blocked | Public institution dress codes |
| Luxury hotel, casino (Rey y Reinas), Noir Boutique, Vajaros Realty | Blocked | Blocked | Dress codes |
| Bargain Threads, Urban Edge | Allowed | Allowed | Jack doesn't care; Urban Edge sells bralettes |
| Laundromat, dry cleaners | Allowed | Allowed | Nobody dresses up for laundry |
| Hardy Bar | Allowed | Allowed | Bartender line (Step 6) |
| Lucius' Lounge | Allowed after 6 PM | Blocked | Doorman line; a sports bra reads wrong for the room |
| Dojang, gymnasium, stadium | Blocked | Allowed | Workout clothes only |

Write `getBraExposedVenueBlockMessage(restriction)` on the `getSheerVenueBlockMessage()` pattern: three corruption voices, the building name, her body named. A mesh or soaked bra uses the existing sheer venue messages.

### 2D. Wet bra goes see-through

New: when she's in Bra Exposed with an opaque cotton, satin, or lace bra (not `sporty`, not a mesh profile) and `clothesWetness >= 60`, the bra is see-through. `isWetBraThrough()` returns true, and for the law the state counts as `sheer_lingerie_top`: evidence, venue blocks, transit refusal, harassment routing. The Appearance box and ambient lines get wet-bra variants. This is the only way a legal outfit turns criminal without the player touching the wardrobe, so the transition fires a one-line notification the first time per day: *"The rain's soaked your {bra} through. Your nipples show dark and stiff under the wet fabric, and now anyone looking can see them."*

### 2E. Transit

Per Decision 1. Recommended behavior:

| State | Bus | Uber |
|---|---|---|
| Bra Exposed | Rides. Driver line on boarding | Rides. Driver line on pickup |
| Bra Exposed, sports bra | Rides, silently | Rides, silently |
| Bra Exposed, mesh or soaked | Refused (existing sheer text) | Refused, -0.2 rating |
| Panties Exposed | Refused | Refused, -0.2 rating |

Driver lines (write three per method, three corruption voices each on Kelsie's reaction). Sample bus line: *"The driver's eyes go to your {bra} and stay there while your card beeps. He waves you back. You ride with your arms folded, and your nipples stiffen against the cups every time the bus hits a pothole."*

If Bra Exposed rides the bus, `exhibition_bus_trigger` can fire. Give it a Bra Exposed branch in Step 11.

### 2F. Evidence

`checkExhibitionEvidence()` today returns early for `'clothed'`. Add:

| State | Ledger entry | Severity | Night camera |
|---|---|---|---|
| Bra Exposed (opaque, dry) | None | none | none |
| Bra Exposed, soaked through | Yes | minor | moderate |
| Panties Exposed | Yes | minor | moderate |

`exposureState` on the entry records `'panties_exposed'` or `'bra_wet_through'` so Cruz can read it. Witness descriptions: *"Civilian witness to public indecency, subject in a top and underwear, no pants or skirt."*

### 2G. Suspicion on district entry

| State | Suspicion |
|---|---|
| Bra Exposed | 0 (legal) |
| Bra Exposed, soaked through | +2 |
| Panties Exposed | +2 (same as Underwear Only) |

### 2H. Police clock for Panties Exposed

Per Decision 3. `tickPantiesExposedPolice()` on the `tickSheerLingeriePolice()` pattern: starts on district entry in Panties Exposed at or above the gate, resets whenever she leaves the state, fires `handlePantiesExposedPoliceArrive()` at 240 minutes. Three corruption voices, the officers noticing her panties first and the wet spot second, then `handleNakedEscape()`. Sample high-voice beat: *"The officer looks at your {top}, then lower, and stays there. 'Ma'am. Where are your pants?' You shift your weight onto one hip so he gets a better look at the damp crotch of your {panties}. 'I'm wearing a shirt.' His partner laughs into his radio."*

### 2I. Appeal penalty suppression

`calculateTotalAppeal()` suppresses imperfection penalties in any visible-underwear state. Bra Exposed is a look, so penalties apply as normal (a filthy bra is a filthy outfit). Panties Exposed keeps the suppression, like the other exhibition states.

---

## Step 3: The Passive Layer: Thrill, Arousal, MH, Stats

### 3A. Thrill rates

Per Decision 4. The existing `underwearPartialThrill` accumulator splits into two:

| State | Thrill / hr | Stacks with |
|---|---|---|
| Bra Exposed | +2 | commando (+2) if bare under the bottom |
| Bra Exposed, sports bra | +1 | commando |
| Bra Exposed, soaked through | +4 (replaces the +2; wet does not stack) | commando |
| Panties Exposed | +3 | braless (+1) if bare under the top |
| Mesh versions | existing sheer rates replace these | as today |

All public only (`isPrivateExposureScene()`), all respect `getVisibleExposureLevel()`, and both hold thrill against decay.

### 3B. Arousal

Every arousal gain in this overhaul goes through `applyArousal()` so the commando/braless and thirst multipliers apply. That includes rewriting the direct `gameState.arousal = Math.min(100, gameState.arousal + N)` lines in `handleUnderwearDistrictEntry()`.

Passive arousal: none beyond what thrill already feeds. Panties Exposed adds one friction source: when `cv.pantiesCut === 'thong'`, +1 arousal per 30 minutes in public (the thong riding up between her cheeks with every step).

### 3C. Mental health on district entry

| State | Innocent | Curious/Experienced | Corrupted+ |
|---|---|---|---|
| Bra Exposed | -1 | 0 | +1 |
| Panties Exposed | -3 | 0 | +2 |

Demonic karma: Panties Exposed at Corrupted+ is +3.

### 3D. Lifetime stats

Split the minute tracking: `braExposedMinutesTotal` and `pantiesExposedMinutesTotal` get their own counters; `underwearMinutesTotal` goes back to meaning Underwear Only. Show both in the Current Stats exhibition card's lifetime section.

### 3E. Appearance tab copy

The boxes and images already exist. Rewrite the sub-lines so they read the state and the body:

- **BRA EXPOSED** loses the warning icon (use the sparkle icon) and the red border; it gets a rose border. Sub-line branches on `braStyle`. Sample for `pushup`: *"Your {bra} and your {bottom}. Your tits sit high and pushed together in the cups, and every eye on the street lands there first."* Sample for `sporty`: *"Your sports bra and your {bottom}. Dressed for a run, and still, people look."*
- **PANTIES EXPOSED** keeps the warning icon. Sub-line branches on `pantiesCut` and `buttSize`. Sample for `thong` with `'large'`: *"Your {top} ends at your hips. Below it, your thong vanishes between your heavy cheeks and your whole ass is out."*

---

## Step 4: District Entry: Vignettes & Kelsie's Response

`handleUnderwearDistrictEntry()` currently runs one block for all three underwear types with three inline responses per tier. Split it to match the topless and bottomless handlers.

### 4A. Route by state

- `underwear_only` keeps the existing block.
- `bra_exposed` calls `getBraExposedNPCVignette()` and `getBraExposedKelsieResponse(corruption)`.
- `panties_exposed` calls `getPantiesExposedNPCVignette()` and `getPantiesExposedKelsieResponse(corruption)`.
- Mesh versions keep routing to `handleSheerDistrictEntry()`.

Both new states share the 20-minute undress-state cooldown.

### 4B. Bra Exposed vignettes (8, rewrite the old `braPool`)

The register shifts from scandal to attention: people clock the look, some approve, some disapprove, most men stare at her chest. Nobody asks if she was mugged. Mix: a woman who likes the look, a man who walks into something, a teenager with a phone, an older woman who disapproves, a guy who says something crude, a barista who comps her drink, a jogger who nods (sports bra only), a construction crew. Each vignette names the bra (`cv.braName`) and branches once on `breastSize` or `braStyle`. Sample:

*"A barista wiping down the patio tables straightens up as you pass. Her eyes go to your {bra}, then to your face, and she grins. 'Okay, I love that,' she says. Behind her, a man at the corner table hasn't looked at his laptop since you turned the corner."*

### 4C. Panties Exposed vignettes (8, expand the old `pantiesPool` from 5)

Full scandal register, the same weight as the bottomless pool. The camera is behind her as often as in front: her ass is what people see first. Every vignette branches on `pantiesCut` and `buttSize` at least once. Sample:

*"Two women at a bus shelter go quiet when you pass. You hear it start behind you. 'Is she... wearing pants?' 'She's wearing a thong.' 'That's what I said.' You can feel both of them staring at your bare ass the whole length of the block."*

### 4D. Kelsie's response functions

`getBraExposedKelsieResponse(corruption)` and `getPantiesExposedKelsieResponse(corruption)`: four voices (Innocent, Curious/Experienced, Corrupted, Deeply Corrupted), three variants each, every variant naming what her body's doing. BPs: `breastSize` in every Bra Exposed variant; `buttSize` and `pantiesCut` in every Panties Exposed variant; `bodyType` in one variant per voice. Braless and commando layer in where they apply (`cv.commando` under a skirt in Bra Exposed; `cv.braless` under the top in Panties Exposed).

Samples:
- Bra Exposed, Innocent: *"You keep your arms folded over your {bra} and your eyes on the pavement. It's allowed. You keep telling yourself it's allowed. Your nipples press hard into the cups, and your pussy clenches every time someone looks twice."*
- Bra Exposed, Deeply Corrupted: *"You walk with your shoulders back and your tits pushed up in the cups, and you let them look. A man at the crosswalk forgets the light changed. Your clit throbs, slow and pleased with itself."*
- Panties Exposed, Innocent: *"You tug the hem of your {top} down with both hands and it doesn't reach. Everyone behind you gets your ass. Your face burns, and the crotch of your {panties} is damp by the end of the block."*
- Panties Exposed, Deeply Corrupted: *"You slow down so the man behind you can take his time. Your {panties} are soaked through at the crotch and you know he can see the dark patch. Your cunt clenches around nothing and you smile at the next person who stares."*

**One-time first-walk beats:** `seenFirstBraExposedWalk` and `seenFirstPantiesExposedWalk` fire a longer version (5 to 7 short paragraphs) the first time she enters a district in each state, in her current voice.

---

## Step 5: Ambient Exhibition Lines

Add `bra_exposed` and `panties_exposed` pools to `EXHIBITION_AMBIENT_LINES`, three corruption voices, six lines per voice. Remove the `'clothed'` early return for these states in `checkExhibitionAmbientLine()`. Use `{bra}`, `{panties}`, `{top}`, `{bottom}` tokens and extend the token resolver to cover `{top}` and `{bottom}`.

Pool variants (lines chosen by condition inside the voice):
- Bra Exposed with commando: the bare pussy under the skirt or jeans gets named.
- Bra Exposed, soaked through: nipples through wet fabric.
- Bra Exposed, sports bra: sweat, bounce, and the stares that don't care it's gym wear.
- Panties Exposed with braless: nipples through the top on top of everything else.
- Panties Exposed, thong: the ride-up, the bare cheeks, cold air.

Samples:
- *"Every window you pass shows you the same girl: {bra}, {bottom}, and a chest that pulls every stare on the block. Your nipples ache in the cups."*
- *"A breeze comes down the street and finds your bare thighs and the thin {panties} between them. Your pussy tightens. You're wet, and you're sure it shows."*

---

## Step 6: Style Comments & Named NPCs

### 6A. Style comments

`BRA_EXPOSED_STYLE_COMMENTS` (fashion register, like `sheer_framed`): six comments. A bralette as a top, a push-up bra under nothing, a sports bra downtown. Body context via `_sheerBodyCtx()`-style helper extended to non-sheer states.

`PANTIES_EXPOSED_STYLE_COMMENTS` (scandal register, like `sheer_commando`): six comments.

Priority in the style-comment picker: after dirty/torn clothes, before coherence comments, same slot the sheer comments use.

### 6B. Named NPC lines

| NPC | Bra Exposed | Panties Exposed |
|---|---|---|
| Ruby (diner door) | Points at the shirts-required sign, hands her an apron to wear on the walk home or a staff tee; three variants | Pulls her inside the back door before a customer sees; three variants |
| Livie (convenience store door) | Taps the sign on the glass, dry and amused | Stares, then pretends she didn't |
| Lucius doorman (evening) | Waves her in; one line on the look | Blocked by venue; doorman line on the refusal |
| Hardy Bar bartender | Free drink line, once per day | Blocked by venue |
| Jack (Bargain Threads) | Leers, offers a top at a "discount" she's expected to try on in front of him | Blocked at the door; Jack's line from the doorway |
| Cruz | none | `getCruzSheerExposureLine()` gains a Panties Exposed read when those entries dominate her file |
| Jaewon (when Kelsie leaves the apartment in either state) | Girlfriend: teasing, possessive; below girlfriend: flustered | Girlfriend: alarmed, then aroused, depending on Jaewon's corruption; below girlfriend: begs her to put pants on |

Jaewon lines route through `getJaewonBralessCommandoReaction()` ahead of the braless/commando reactions, the same way the sheer reactions do.

---

## Step 7: Harassment

### 7A. Chance rules in `checkDistrictHarassment()`

| State | Rule |
|---|---|
| Panties Exposed | Guaranteed once per day (as today) |
| Bra Exposed | Not guaranteed. District chance +0.15, +0.10 more at night |
| Bra Exposed, soaked through | Guaranteed |
| Sports bra | +0.05 |

### 7B. `street_harassment_event` and the slums event

Add both states to the `exhibState` ladder between `underwear_only` and `braless_commando`, with their own opener paragraphs (what the two men see) and grope paragraphs. Remove the `'clothed'` fallthrough that currently hands them the clothed opener.

- **Panties Exposed opener:** they come up behind her. The first thing named is her ass in the panties. BPs: `buttSize`, `pantiesCut`, `bodyType`; `cv.braless` adds the nipples through the top.
- **Panties Exposed grope:** a hand on her bare ass, fingers under the leg band, the crotch of her panties pulled aside. Gains 25/25.
- **Bra Exposed opener:** they're in front of her. Her chest in the bra is what they're talking about. BPs: `breastSize`, `braStyle`.
- **Bra Exposed grope:** hands on her tits over the cups, then a cup yanked down so a nipple's out on the street. Gains 18/15. `cv.commando` adds the hand that finds she's bare under her skirt.

### 7C. Routing

`getHarassExhibScene(area)`:
- `panties_exposed` routes to `district_exhibition_panties` / `slums_exhibition_panties` (Steps 8 and 9), forced at every tier like the other exhibition states.
- `bra_exposed` doesn't force a scene. The normal choices stay (shove, firm, passive, flirt). Innocent through Experienced get the choices as written; at 6001+ the flirt choice's scene gains a Bra Exposed layer (Step 7D).
- Soaked-through bra routes as `sheer_lingerie_top` does today.

### 7D. Bra Exposed layers in the choice scenes

`street_harassment_aggressive`, `_firm`, `_passive`, `_flirt` (and slums equivalents) get one inserted paragraph each when `cv.braExposed`: the cup that got pulled down, her fixing it or leaving it, what her body did while it was out.

---

## Step 8: `district_exhibition_panties`

A full gang scene on the `district_exhibition_underwear` template: two men, the alley (park: behind the maintenance shed), three tiers (below 4001, 4001–8000, 8001+). Same section spine: location and sensory grounding, the strip, Act 1 hands, transition, Act 2 penetration, build, approach, her orgasm (hit), their orgasm, linger, aftermath.

What makes it this state's own scene:
- **The strip is one move.** Her top stays on through most of it; there's nothing to take off below the waist except the panties. They're pulled aside, then down, then torn at 8001+ where she lets them. The top gets shoved up over her tits in Act 1 (`cv.braless` branches: bare tits spill out, or the bra cups get pulled down under the top).
- **The camera starts behind her.** Grounding describes her ass in the panties the whole walk into the alley, from their side.
- **Aftermath:** she walks out in the same top, with torn or wet panties or none, which may flip her to Bottomless for the walk home (check the outfit and say so).
- **Deeply Corrupted tier:** she drives it. She bends over in front of them before anyone touches her and pulls the crotch aside herself.

BP targets: 8 to 10 per tier. Mandatory: `buttSize` at the grounding and at the first grab; `pantiesCut` at the strip; `breastSize` when the top comes up; `bodyType` at a position change and at her orgasm; `cv.braless` at the top; `cv.topName` whenever the top is named.

`onEnter`: `completeSexAssaultMale({ time: 15, hygiene: -30, rapeType: 'district_exhibition_panties', corruptionGain: 25, thrill: 15, partner: 'district_group', skipPregnancy: true })`, the scene pregnancy check, `streetHarassmentCount` and `exhibitionEventsCompleted` increments, and a panties outcome (`torn` at 8001+ removes the panties to inventory as damaged; below that they stay on). Gallery entry `District: Panties Exposed Exhibition` with all three tiers.

---

## Step 9: `slums_exhibition_panties`

The slums version on the `slums_exhibition_underwear` template: its own setting, cast, and harder edge (more men, and nobody filing evidence or calling police). Same state-specific rules as Step 8. Gallery entry `Slums: Panties Exposed Exhibition`.

---

## Step 10: Night Assault & the Slums Pass-Out

### 10A. `checkNightRapeEncounter()`

| State | Rule |
|---|---|
| Panties Exposed | Guaranteed (as today) |
| Bra Exposed | Not guaranteed. +0.08 to the chance |
| Bra Exposed, soaked through | Guaranteed |

### 10B. `night_rape_ambush` and the night assault scenes

The ambush opening reads `getUnderwearVisibilityDesc()` (fixed in Step 1F). The male, female, and group scenes each get a state paragraph at the first grab: Panties Exposed from behind, Bra Exposed at the chest. BPs per the Body Branching doc.

### 10C. `slums_passout_rape_main`

Add both states to its exposure branches: what she's wearing when she wakes, and what's been done to it.

---

## Step 11: Accidental Events

### 11A. Open the gate

`checkAccidentalExposure()` returns early for any visible-underwear type. Change it to return early only for Underwear Only and above. In the two partial states, only the covered zone can have an accident:

| State | Zone that can have an accident | Existing events that apply |
|---|---|---|
| Bra Exposed | hips (skirt or dress bottom only) | commando events if bare; the skirt/dress exhibition events |
| Panties Exposed | chest | braless events if bare; the top exhibition events |

### 11B. The 20 exhibition events

Their garment conditions already filter correctly (skirt events need a skirt, top events need a top). Add a layer line to the events that can fire in each state:
- Skirt events in Bra Exposed: the hem lifting while her tits are already out in the bra. One inserted sentence per event, per tier.
- Top events in Panties Exposed: the button pops, or the top rides up, while her panties are already on display. One inserted sentence per event, per tier.

### 11C. New state-specific accidents

Three per state, in the accident pool format (`text`, `thrill`, `arousal`, corruption-tiered `kr`), 60-minute shared cooldown, 15% per transition:

**Bra Exposed**
1. **Strap slip.** A strap slides off her shoulder, the cup peels down, and a nipple's out on the sidewalk for two seconds. (`breastSize` BP.) thrill 10, arousal 7.
2. **The clasp gives.** Back clasp pops mid-stride. She catches the cups against her chest with both arms. Anyone behind her sees her bare back and the bra hanging open. thrill 12, arousal 8. At 8001+ she lets go.
3. **Sports bra ride-up** (sporty only). Reaching for something high, the band rides up and her underboob's out. thrill 8, arousal 5.

**Panties Exposed**
1. **Ride-up.** Her panties creep up between her cheeks with every step until her ass is nearly bare. (`pantiesCut`, `buttSize` BPs; a thong version where it's already there and now it's worse.) thrill 10, arousal 7.
2. **The wet spot.** At arousal 50+, the crotch of her panties goes dark and a stranger's eyes find it. thrill 12, arousal 8.
3. **Cold bench.** She sits without thinking and the bench is cold metal through thin fabric; the man across from her gets a straight view between her knees. thrill 10, arousal 7.

### 11D. `exhibition_bus_trigger`

If Decision 1 lets Bra Exposed ride the bus, add a Bra Exposed branch: the crowded aisle, a hand that brushes the cup, a man who stands too close behind her.

---

## Step 12: Deliberate Acts

### 12A. Existing verbs

| Verb | Bra Exposed | Panties Exposed |
|---|---|---|
| Flash (`getFlashExhibitionChoice`, 6001+, thrill 70) | Flash top: pull the cups down. Flash bottom: skirt only | Flash top: lift the top (bra or bare). No bottom to flash |
| Strike a pose (`getPoseExhibitionChoice`, 8001+, thrill 75) | Excluded | Included; add Panties Exposed text to standing, seated, walk-by |
| Public masturbation (alley and park bench, 6001+) | Add a Bra Exposed exposure branch | Add a Panties Exposed exposure branch |

`exhibition_masturbate_alley` and `exhibition_masturbate_park_bench` each gain two `exposure` branches and two Gallery entries (`Alley: Bra Exposed`, `Alley: Panties Exposed`, `Park Bench: Bra Exposed`, `Park Bench: Panties Exposed`). Panties Exposed: fingers under the leg band, then the crotch pulled aside; the top stays on. Bra Exposed: one hand pulling a cup down, the other down the front of her skirt or jeans (`cv.liftable` / `cv.pulldown`).

### 12B. New verbs

Offered on district streets through a `getExposedUnderwearChoices()` function (same slot as `getSheerExhibitionChoices()`):

| Verb | State | Gate | Cooldown | Gains (thrill / arousal / corruption / suspicion) |
|---|---|---|---|---|
| **Roll your shoulders back** | Bra Exposed | 2001, thrill 20 | 30 min | 6 / 4 / 1 / 0 |
| **Unhook it** | Bra Exposed | 6001, thrill 50 | 60 min | Removes the bra into the backpack and moves her to Topless; topless systems take over | 
| **Tug a cup down** | Bra Exposed | 8001, thrill 70 | 45 min | 12 / 10 / 3 / 1 |
| **Bend over** | Panties Exposed | 6001, thrill 40 | 30 min | 10 / 8 / 2 / 1 |
| **Pull them aside** | Panties Exposed | 8001, thrill 70 | 45 min | 15 / 14 / 4 / 2 |

Each verb gets a text function with three corruption voices (only the voices its gate allows), 4 to 6 BPs, and an NPC reaction beat. "Unhook it" fires the topless district-entry vignette immediately afterward (it bypasses the cooldown once).

### 12C. Riverside park

`riverside_park`'s exposure description covers naked, topless, and bottomless. Add Underwear Only, Bra Exposed, and Panties Exposed paragraphs, and let the bench masturbation choice read them.

---

## Step 13: Exhibition Seduction Branch E: Panties Exposed

### 13A. Routing

In `hunt_seduction`, at 6001+ and appeal 80+, `panties_exposed` routes to `exhibition_seduce_pan_approach_m` / `_f`. Re-point `SHEER_SEDUCTION_BRANCH.sheer_lingerie_bottom` from `btm` to `pan`: mesh panties under a top are this state, and the existing sheer overlays run on the new branch.

### 13B. The 24 scenes

Twelve per gender on the Branch A skeleton: `approach`, `proposition`, `success_pivot`, `soft_pivot`, `exit_success`, `exit_soft`, `failure_dismissive`, `failure_apologetic`, `failure_disturbed`, `failure_impressed_but_no`, `sex`, `sex_2`.

What this branch owns:
- **The approach is from behind.** The target sees her ass before her face. Her opener plays on it.
- **The proposition lives on her clothes.** She's already half-dressed for it; the pitch is how little is left to move.
- **The sex scenes never take the top off first.** Top stays on until position two. Panties aside, then off (`pantiesCut` branches), then the top comes up. `cv.braless` decides what his hands find under it.
- **Failure scenes** each have a Panties Exposed flavor: the dismissive one who looks at her crotch while he says no, the disturbed one who asks if she's okay, the impressed one who takes a photo as he leaves.

BP targets: approach and proposition 3 to 5; pivots and exits 2 to 3; failures 2; sex scenes 8 to 10. Gallery section: `Exhibition Seduction: Panties Exposed`.

---

## Step 14: Bra Exposed Seduction Layer

Bra Exposed is legal, so it doesn't get an exhibition branch. It changes the regular seduction instead.

### 14A. Mechanics

- `calculateSeductionSuccess()`: +0.03 in Bra Exposed (the look does half the work). Sports bra +0.01.
- `_seductExposureLine(g)`: add a `bra_exposed` line (two variants per gender) and a Panties Exposed line for the regular flow below 6001.
- `seduce_lucius_man` / `_woman`: replace the `isWearingVisibleUnderwear()` line with per-state lines.

### 14B. Prose layer in the regular street seduction

Every scene in the `seduce_street_man` / `seduce_street_woman` chains that names her top gets a `cv.braExposed` branch: no top to pull over her head, a strap slid off the shoulder instead, the cups pulled down, the clasp. Audit with a grep for `cv.topName` and `topName` inside those chains. BP: `breastSize` and `braStyle` at the first chest contact.

---

## Step 15: Combat & Forced States

### 15A. `handleDamagedClothing()` messages

| Result | Message |
|---|---|
| Top destroyed, bra intact, bottom intact | "Your {top}'s torn away. You're down to your bra and your {bottom}." |
| Bottom destroyed, panties intact, top intact | "Your {bottom}'s shredded. Your {panties} are all that's left below your {top}." |

Both set `clothesDestroyedInCombat` as today. For Bra Exposed, the flag only matters if the bra itself is sheer or soaked.

### 15B. Forced-state gate text

When `clothesDestroyedInCombat` waives the gate, the first district entry in each state gets a forced-state response in her voice (she didn't choose this; her body reacts anyway), the pattern the topless and bottomless forced entries use.

---

## Step 16: UI

### 16A. Tips & Guide

In the exhibitionism entry:
- Exposure States: rewrite the Bra Exposed and Panties Exposed entries around legality, gates, thrill, and what each unlocks.
- Thrill Sources, Decay, Transit, venues, harassment, night assault, evidence, police clock, the new verbs, Branch E, the Bra Exposed seduction bonus, the wet-bra rule.
- Scene list for the new gang scenes and masturbation branches.

### 16B. About Stats & Current Stats

- About Stats Exhibition Thrill: the thrill table rows for both states and the sports bra.
- Current Stats exhibition card: label (already done), the state's live effects (thrill rate, suspicion, venue status, police clock remaining for Panties Exposed), and the lifetime minutes from 3D.

### 16C. Scene Gallery

Register every new scene: two gang scenes (three tiers each), four masturbation branches, Branch E (24 scenes, two genders), the verbs, the accidents, the first-walk beats. Gallery flag overrides set the outfit so the scene renders with a bra or panties and the right garments.

### 16D. Cheat panel

Add "Outfit: Bra Exposed", "Outfit: Panties Exposed", and "Soak clothes (wetness 70)" buttons for testing.

---

## Step 17: Save Migration, Polish & Balance

### 17A. Save migration

- Every field from 1H defaults.
- `underwearMinutesTotal` keeps its value; the new counters start at 0.
- Every `// BPE shim` marker from 1G is gone. Grep for it; zero hits.

### 17B. Style guide audit

Every new string: contractions; no em-dash connectors (paired parentheticals and dialogue cut-offs only); no "the kind of," "a beat," "a pause," "genuinely," "the way," "in a way"; no Not/Not, No/No, or Something/Something pairs; no negation-before-reveal; no stanza formatting; short paragraphs under ~300 characters; no staccato chains. Crude anatomical language everywhere Kelsie's aroused. `cum` for orgasm. Escaped apostrophes. `node --check` clean on both script blocks.

### 17C. Body Branching audit

Every scene: variable extraction at the top; `gameState.bodyAppearance` only; all five `bodyType` values; `'large'`/`'small'`/`'average'`; BP counts in range; no `cv.topName` in Bra Exposed prose and no `cv.bottomName` in Panties Exposed prose; `cv.braless` / `cv.commando` at every undressing step.

### 17D. Sex Standards audit

Every intimate beat: no "core," no "pulse/wave," phonetics spelled out, position tracked, approach/hit/linger on every orgasm, active present-tense ending.

### 17E. Balance pass

- Bra Exposed should feel rewarding and safe enough to wear on purpose: small thrill, a seduction edge, most doors open, and a real risk only when it rains.
- Panties Exposed should feel as dangerous as Underwear Only: daily harassment, guaranteed night assault, evidence, blocked doors, a police clock, and the biggest thrill of the partial states.
- A Kelsie in Bra Exposed with commando under a skirt should read as two layers of exposure stacking, not as a new state.
- The thong arousal tick shouldn't push arousal into Edged on its own over a normal afternoon.
- The new verbs' cooldowns shouldn't let thrill farming outpace the Mesh verbs.

### 17F. Mental model

1. **Legality splits the partial states.** Panties Exposed is a crime and gets everything a crime gets. Bra Exposed is a look and gets everything a look gets: attention, approval, disapproval, a seduction edge, and the ache between her thighs from being stared at.
2. **The line can move.** Rain soaks a cotton bra through, and the look becomes indecent. A mesh bra was indecent from the start. The player learns the weather is part of the outfit.
3. **The body is always in it.** Every state names what her nipples, her pussy, and her clit are doing, at every corruption tier, because being seen is what she's for.
4. **The camera follows the exposure.** Bra Exposed scenes are shot from the front: her chest, the cups, the straps. Panties Exposed scenes are shot from behind: her ass, the leg band, the thong. Body branching carries the difference.

---

## New Helper / Function Summary

| Function | Step | Purpose |
|---|---|---|
| (rework) `getExposureLevel()` | 1A | Adds `'panties_exposed'` and `'bra_exposed'` |
| (rework) `getVisibleExposureLevel()` | 1B | Concealment for both new levels |
| (rework) `getSheerState()` level gate | 1C | Keeps the sheer lingerie states computing |
| (new) `isLegallyDressed()` | 1D | Law-facing check |
| (new) `isVisiblyExposed()` | 1D | Eye-facing check |
| (new) `isCriminalSheerState()` | 1D | Wraps the indecent Mesh states |
| (rework) `getSceneClothingVars()` | 1E | `exposureState`, `braExposed`, `pantiesExposed`, `braName`, `pantiesName`, `braStyle`, `pantiesCut` |
| (fix) `getUnderwearVisibilityDesc()` | 1F | Correct wording for both states |
| (rework) `getVisibleUnderwearGate()` | 2A | Per-state gates, sports bra exemption |
| (new) `getBraExposedVenueBlockMessage()` | 2C | Venue refusals for Bra Exposed |
| (new) `isWetBraThrough()` | 2D | Soaked opaque bra counts as sheer |
| (rework) `travelToLocation()` | 2E | Transit per Decision 1 |
| (rework) `checkExhibitionEvidence()` | 2F | Panties Exposed and wet-bra entries |
| (new) `tickPantiesExposedPolice()` / `handlePantiesExposedPoliceArrive()` | 2H | Four-hour clock |
| (rework) passive thrill block | 3A | Split accumulators, sports bra, wet bra |
| (rework) `handleUnderwearDistrictEntry()` | 4A | Routes both states to their own functions; `applyArousal()` |
| (new) `getBraExposedNPCVignette()` / `getBraExposedKelsieResponse()` | 4B, 4D | District entry |
| (new) `getPantiesExposedNPCVignette()` / `getPantiesExposedKelsieResponse()` | 4C, 4D | District entry |
| (rework) `EXHIBITION_AMBIENT_LINES` + `checkExhibitionAmbientLine()` | 5 | Two new pools, `{top}` / `{bottom}` tokens |
| (new) `BRA_EXPOSED_STYLE_COMMENTS` / `PANTIES_EXPOSED_STYLE_COMMENTS` | 6A | Street style comments |
| (new) named-NPC line helpers (Ruby, Livie, Lucius, Hardy, Jack, Jaewon, Cruz) | 6B | Per-state lines |
| (rework) `checkDistrictHarassment()` / `getHarassExhibScene()` | 7A, 7C | Chance rules and routing |
| (rework) `checkNightRapeEncounter()` | 10A | Bra Exposed no longer guaranteed |
| (rework) `checkAccidentalExposure()` | 11A, 11C | Zone gating, six new accidents |
| (new) `getExposedUnderwearChoices()` + five verb handlers | 12B | Deliberate acts |
| (rework) `hunt_seduction` routing, `SHEER_SEDUCTION_BRANCH` | 13A | Branch E |
| (rework) `calculateSeductionSuccess()`, `_seductExposureLine()` | 14A | Bra Exposed bonus and lines |
| (rework) `handleDamagedClothing()` | 15A | Combat messages |

## New Scene Summary

| Scene / beat | Step | Type | Trigger |
|---|---|---|---|
| Bra Exposed first-walk beat | 4D | One-time | First district entry in Bra Exposed |
| Panties Exposed first-walk beat | 4D | One-time | First district entry in Panties Exposed |
| Bra Exposed district vignettes (8) + responses (12) | 4B, 4D | Repeatable | District entry, 20-minute cooldown |
| Panties Exposed district vignettes (8) + responses (12) | 4C, 4D | Repeatable | District entry, 20-minute cooldown |
| Ambient lines (2 pools × 3 voices × 6) | 5 | Repeatable | Street scenes, 15-minute cooldown |
| Wet-bra notification | 2D | Once per day | Opaque bra soaked to 60+ in Bra Exposed |
| Panties Exposed police arrival (3 voices) | 2H | Repeatable | Four hours in public in the state |
| Transit driver lines | 2E | Repeatable | Bus or Uber in Bra Exposed |
| Harassment openers and gropes (2 states × district and slums) | 7B | Repeatable | Daily harassment |
| Harassment choice-scene layers (Bra Exposed) | 7D | Repeatable | Bra Exposed harassment choices |
| `district_exhibition_panties` (3 tiers) | 8 | Repeatable | Panties Exposed harassment, district |
| `slums_exhibition_panties` (3 tiers) | 9 | Repeatable | Panties Exposed harassment, slums |
| Night assault state paragraphs | 10B | Repeatable | Night assault in either state |
| Slums pass-out state branches | 10C | Repeatable | Slums pass-out in either state |
| Layer lines on the 20 exhibition events | 11B | Repeatable | Event fires in either state |
| Six new accidents | 11C | Repeatable | District transitions |
| Bus exhibition, Bra Exposed branch | 11D | Repeatable | Bus ride in Bra Exposed (if Decision 1) |
| Masturbation branches (alley and bench × 2 states) | 12A | Once per day each | Existing gates |
| Five new verbs | 12B | Cooldown | District streets |
| Riverside park exposure paragraphs | 12C | Repeatable | Park visit |
| Exhibition Seduction Branch E (24 scenes) | 13 | Repeatable | Hunt seduction in Panties Exposed, 6001+, appeal 80+ |
| Bra Exposed seduction layer | 14 | Repeatable | Regular street seduction in Bra Exposed |
| Forced-state entry responses | 15B | Repeatable | First entry after combat destruction |

## Consumer Audit

Every place that reads the exposure level or the visible-underwear helpers today, and the step that owns its new behavior. Step 1 shims each one to its current behavior.

| Site | Reads | Owner |
|---|---|---|
| `checkDistrictHarassment()` | `isWearingVisibleUnderwear()`, level | 7A |
| `getSheerState()` gate | level | 1C |
| `getSheerVenueBlockMessage()` / building check in `showScene` | level | 2C |
| `getHarassExhibScene()` | level | 7C |
| `rollWatcherEncounter()` | level (naked only) | no change |
| `travelToLocation()` sheer transit + bus/Uber | level, visible underwear | 2E |
| Current Stats exposure label | visible level | 16B |
| Passive thrill block | visible level, visible underwear | 3A |
| `checkExhibitionAmbientLine()` | visible level | 5 |
| `checkExhibitionEvidence()` | visible level | 2F |
| `_seductExposureLine()` | visible level | 14A |
| `hunt_seduction` routing | level | 13A |
| `street_harassment_event` / `_grope` / `onEnter` | level | 7B |
| Slums harassment event / grope | level | 7B |
| `riverside_park` | level | 12C |
| `search_for_prey` | level (unused variable) | no change |
| `exhibition_masturbate_alley` / `_park_bench` | level | 12A |
| `slums_sheer_exhibition` clothes word | level | 1G shim only |
| `slums_passout_rape_main` | level | 10C |
| `getExposureBlockMessage()` | level | 2C |
| `handleUnderwearDistrictEntry()` | underwear type | 4A |
| `checkAccidentalExposure()` | underwear type | 11A |
| `checkNightRapeEncounter()` / `night_rape_ambush` | visible underwear | 10A, 10B |
| `handleDamagedClothing()` | visible underwear | 15A |
| `calculateTotalAppeal()` | visible underwear | 2I |
| `seduce_lucius_man` / `_woman` | visible underwear | 14A |
| `unequipClothing()` / equip warnings / leave gate | visible underwear, gate | 2A |
| Appearance tab | visible underwear | 3E |

---

## Implementation Notes

- **Build one step. Output the file. Wait for confirmation.** These two states touch the law, the street, the passive tick, harassment, assault, events, seduction, and the UI. A bundled step is a debugging nightmare.
- **Step 1 is the risk.** Changing what `getExposureLevel()` returns changes 39 call sites at once. The shim pattern exists so Step 1 ships with zero behavior change. Verify it in the browser: a bra-and-skirt outfit and a tee-and-panties outfit should behave exactly as they did before Step 1 in every system except the ones Step 1 itself names.
- **Mesh owns see-through.** Never write a line for a mesh bra or mesh panties inside a Bra Exposed or Panties Exposed pool. Route to the sheer content. The one new see-through case, the soaked opaque bra, is owned here (2D) and borrows the sheer lingerie law.
- **Garment names come from cv, always.** In Bra Exposed there is no top; in Panties Exposed there is no bottom. The single most likely bug in every new scene is a `cv.topName` that reads "top" on a girl in a bra.
- **The camera rule is the soul of the overhaul.** Bra Exposed is shot from the front. Panties Exposed is shot from behind. If a Panties Exposed scene opens on her face, rewrite the opening.
- **Innocent Kelsie is still aroused.** Mortified, red-faced, arms folded, and wet. Every Innocent line names her body responding, per the exhibitionism canon.
- **Bra Exposed has to stay worth wearing.** If the balance pass finds it's just a weaker Panties Exposed with fewer scenes, the seduction edge and the venue access are too small. The player should pick it on a warm evening on purpose.
