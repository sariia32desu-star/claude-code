# Bra Exposed & Panties Exposed Overhaul: Session Handoff

**Project:** Vampire Girl (single-file HTML interactive fiction game)
**File:** `vampire_girl/Vampire_Girl.html` on branch `claude/vampire-girl-overhaul-jjgcb3` (repo `sariia32desu-star/claude-code`)
**Status at handoff:** Steps 1 through 10 of 17 are done, tested, committed, and pushed. Steps 11 through 17 remain.
**Last commit:** `fa9f817` (Step 10)

This document is self-contained. It replaces the original overhaul doc (`Bra_Panties_Exposed_Overhaul.md`) for the rest of the work: every remaining step is reproduced here in full, with everything learned while building Steps 1 through 10 added in. It was audited against the original line by line before handoff (see the Audit Record at the end).

---

## 0. Read This First

### Working rules (from the user, non-negotiable)

1. **One step per output. Wait for confirmation. Never bundle steps.** The user confirms each step before the next begins.
2. **Sex scenes are written one tier at a time**, lowest tier first (Innocent, then Experienced/Curious, then Corrupted/Deeply Corrupted). Deliver a tier, wait for confirmation, then the next. This applies to every scene with sexual content in Steps 11 through 15 (masturbation branches, Branch E sex scenes, and anything else with a sex beat).
3. **Always work on the latest file.** If the user uploads a new `Vampire_Girl.html`, work on that. Otherwise, work on the latest version on the branch. Never copy an earlier file over it. Never return to an older output mid-change.
4. **The user supplies companion docs when prose is needed.** Ask for them if a step needs prose and they haven't been sent:
   - `VampireGirl_Style_Guide_SS.md`: every writing rule (mandatory for all prose)
   - `VampireGirl_Body_Branching_Standards.md`: body/clothing branching standards
   - `VampireGirl_Sex_Standards.md`: mandatory for every intimate or sexual beat
   - `Arousal_System_Overhaul.md`: listed by the original doc for arousal work; the user has not sent it so far and said the Sex Standards alone were enough for the gang scenes. Ask if a step leans on arousal tiers.
5. **For new scenes, study the closest existing scene in the same vein and copy its wiring** (the user's explicit instruction for Step 8). For example, the Panties Exposed gang scenes were wired from `district_sheer_exhibition` / `slums_sheer_exhibition`.
6. **Scope discipline.** In Step 6 the user said: "Only do Jack, Cruz, and Jaewon. No one else." So 6A (style comments) and the Ruby, Livie, Lucius doorman, and Hardy bartender lines were deliberately **not** built. Don't build them unless the user asks. (See Section 3, Step 6.)

### User's personal style rules (apply everywhere in the file)

- No em dashes anywhere, except paired parenthetical asides or dialogue/sound cut-offs (`"No—"`, `"Ah—fuck—"`).
- Never write "Not a question."
- No AI tells. No "Not because X. Because Y." No "Not/Not" (or "No/No", "Nothing/Nothing", "Something/Something") paired patterns, including forms like "Not the hardest hitter. Not the tallest blocker."
- Never write "A beat." (also no "A pause.").
- No formal uncontracted language: "She's", "You're", "It's", "doesn't", never "She is", "You are".
- No negation-then-reveal stanzas ("You're not the star. / You're the girl coaches trust / to be in the right place."). No negation-before-reveal in any form.
- Also banned by the Style Guide: "the kind of", "the kind that", "genuinely", "The way" as an opener, "in a way", staccato one- and two-word sentence chains, walls of text (paragraphs over ~300 characters), stanza formatting.
- "cum / cums / cumming" for orgasm; "came" stays "came". "come" stays for movement and idiom ("comes out", "comes down").
- Sex Standards: never "core"; never "pulse/pulses/pulsing" or "wave/waves" in intimate prose (heartbeat "pulse" and non-sexual idioms are fine, but even "wave" as a gesture was swapped out of a sex scene to be safe); pussy / clit / cunt; phonetic sounds spelled out; position tracking; every act gets entry, build, approach; every orgasm gets approach, hit, and linger; no NPC thoughts outside romance scenes; active present-tense final paragraph.
- All prose is 2nd person present tense. Stick strictly to the game's writing style. Any deviation is an error.

### Technical rules

- Every apostrophe inside a single-quoted JS string is escaped: `\'`. No `${}` inside single-quoted strings. Ternaries break out with `' + (…) + '`.
- `node --check` on both `<script>` blocks after every edit. Extract them with a regex over `<script>(.*?)</script>`; the file has exactly two.
- Line numbers drift. Always re-grep by function or scene name.
- Commit messages: descriptive, and end with the attribution lines the environment specifies. Never put model identifiers in commits or files. Push with `git push -u origin claude/vampire-girl-overhaul-jjgcb3` (retry with backoff on network errors). Don't open a PR unless asked.

---

## 1. The Overhaul in Brief

**Scope.** The game has four named exhibition states (Underwear Only, Topless, Bottomless, Naked) and a full sheer (mesh) layer on top of them. Each has a mechanical fingerprint, NPC reactions, harassment routing, gang scenes in the district and the slums, an Exhibition Seduction branch, ambient lines, evidence rules, venue rules, and a Kelsie who reacts in three corruption voices. Two real outfits fell through the cracks: **a bra with a bottom and no top** (Bra Exposed) and **a top with panties and no bottom** (Panties Exposed). `getExposureLevel()` used to call both `clothed`. That one word was the whole problem:

1. **The law treated them identically, and wrongly.** A bra with jeans and panties with a tee had the same 6001 leave gate, the same bus refusal, the same guaranteed night assault. A bra with a skirt is a bold look. Panties with a tee is indecent exposure.
2. **The street didn't see them.** No exhibition ambient lines, style comments, evidence, or seduction-exposure lines.
3. **The scenes that did fire read the wrong outfit.** Harassment fired every day but fell into its clothed branch. `getSceneClothingVars()` returned `topName: 'top'` when there was no top, so prose said "your top" on a girl in her bra. `getUnderwearVisibilityDesc()` said "wearing only your panties" on a girl in a tee.
4. **There was no content.** Neither state had a gang scene, seduction branch, deliberate acts, or accidental events.

The overhaul promotes both outfits to real exposure levels, splits them by legality, and gives each the depth the other states have:

- **Panties Exposed is a crime.** It gets the full exhibition treatment, on equal footing with Underwear Only and Bottomless: harassment routing into its own district and slums scenes, its own Exhibition Seduction branch, evidence, venue blocks, deliberate acts, and a police response.
- **Bra Exposed is legal.** It's a look: a bralette with high-waist jeans, a sports bra on a run, a push-up bra under an open blazer that got taken off. The law leaves it alone, most doors stay open, and the bus still takes her fare. But eyes don't leave her chest, her nipples know it, and her pussy answers every look. It gets its own register: bold, erotic, and on the edge of tipping over. A mesh bra, or a cotton bra soaked see-through, tips it over into a crime.

**Canon anchor (from the Tips & Guide):** exhibitionism is Kelsie's defining kink. Being seen exposed turns her on deeply at every corruption tier. At Innocent she's mortified and aroused anyway. Every tier of every piece of prose names what her body's doing.

**The target feel:** a player who steps out in her bra and a skirt should feel the street tilt toward her; a player who steps out with her panties showing under a tee should feel the city close in: the stares, the phones, the men who follow her, the cop who slows down, and her own wet cunt making it worse.

### Confirmed decisions (approved before Step 1; all built)

1. **Bus and Uber for Bra Exposed: approved.** Both rides take her, with a driver reaction line. A mesh bra or a soaked-through bra is still refused. Panties Exposed stays refused.
2. **Corruption gate for Bra Exposed: approved.** 2001 to walk out in it deliberately. A sports bra has no gate at all. Panties Exposed stays at 6001.
3. **Police clock for Panties Exposed and Underwear Only: approved.** Sheer lingerie keeps its three-hour clock. Panties Exposed and Underwear Only share one four-hour indecency clock.
4. **Thrill rates: approved.** Panties Exposed +3/hr, Bra Exposed +2/hr, sports bra +1/hr (soaked through +4, replacing the wet rate).

### Conventions

- **Corruption voices:** Innocent (0–2000), Curious/Experienced (2001–6000), Corrupted (6001–8000), Deeply Corrupted (8001+). Where a step lists three voices, Corrupted and Deeply Corrupted share the high voice unless the step says otherwise. Gang scenes use the three-tier split below 4001 / 4001–8000 / 8001+.
- **Mesh first.** The Mesh Overhaul owns every see-through version: `sheer_lingerie_top` (a mesh bra with nothing over it) and `sheer_lingerie_bottom` (mesh panties with nothing over them). Never write a line for a mesh bra or mesh panties inside a Bra Exposed or Panties Exposed pool; route to the sheer content. "Bra Exposed" means an opaque bra; "Panties Exposed" means opaque panties. The one new see-through case, the **soaked opaque bra**, is owned by this overhaul (Step 2D) and borrows the sheer lingerie law.
- **The camera rule is the soul of the overhaul.** Bra Exposed is shot from the front: her chest, the cups, the straps. Panties Exposed is shot from behind: her ass, the leg band, the thong. If a Panties Exposed scene opens on her face, rewrite the opening.
- **Garment names come from `cv`, always.** In Bra Exposed there's no top; in Panties Exposed there's no bottom. The most likely bug in any new scene is a `cv.topName` that reads "top" on a girl in a bra, or a `cv.bottomName` that reads "skirt" on a girl in a tee.
- **Innocent Kelsie is still aroused.** Mortified, red-faced, arms folded, and wet.
- **Bra Exposed has to stay worth wearing.** The player should pick it on a warm evening on purpose.

---

## 2. What Exists Now: the Foundation Built in Steps 1–10

This is the toolkit the remaining steps build on. All of it lives in the file today.

### 2.1 Exposure core

- **`getExposureLevel()`** returns `'naked'`, `'topless'`, `'bottomless'`, `'underwear_only'`, `'panties_exposed'`, `'bra_exposed'`, or `'clothed'` (in that priority). A top-slot item with `category === 'dress'` covers the hips everywhere (also in `isEffectivelyBottomless()` and `cv.commando`).
- **`getVisibleExposureLevel()`** applies outerwear: a full coat hides both new states (→ `clothed`); a partial jacket hides Bra Exposed (→ `clothed`) but not Panties Exposed.
- **`getSheerState()`** computes sheer states on `clothed`, `underwear_only`, `bra_exposed`, and `panties_exposed` (so `sheer_lingerie_top` / `sheer_lingerie_bottom` survive).
- **Legality helpers:** `isCriminalSheerState()` (true for the six indecent mesh states), `isWetBraThrough()` (opaque cotton/satin/lace bra, not sporty, not mesh, visible Bra Exposed, `clothesWetness >= 60`; fabrics in `WET_THROUGH_BRA_FABRICS`), `isLegallyDressed()`, `isVisiblyExposed()`.
- **`getSceneClothingVars()` (cv) additions:** `exposureState`, `braExposed`, `pantiesExposed`, `braName`, `pantiesName`, `braStyle` (`'sporty'|'pushup'|'bralette'|'corset'|'lace'|'sheer'|'plain'`, via `getBraStyle(item)`), `pantiesCut` (`'thong'|'cheeky'|'boyshort'|'brief'`, via `getPantiesCut(item)`). Note `cv.braExposed` is true for mesh too; check `cv.braSheer` / `cv.pantiesSheer` to exclude mesh. `cv.topName` still falls back to `'top'` and `cv.bottomName` to `'skirt'`.
- **`getUnderwearVisibilityDesc()`** now reads "in your {bra} and {bottom}" (Bra Exposed with panties) and "with your panties on show under your {top}" (Panties Exposed).

### 2.2 Gates and the law (Step 2)

- **`getVisibleUnderwearGate()`**: Bra Exposed opaque 2001, sports bra 0, mesh bra 6001, Panties Exposed 6001, both visible 6001 (8001 if both mesh).
- **`getExposureLeaveGate()`**: the gate raised to 6001 when she's also topless or bottomless. `checkVisibleUnderwearCorruption()` uses it; so do the refusal messages. `clothesDestroyedInCombat` still waives every gate.
- `unequipClothing()`: taking a top off over a bra and a bottom is blocked below 2001 (sports bra 0, mesh 6001).
- District-entry refusals rewritten for both states; the equip warning has per-state titles.
- **Venues:** `BRA_EXPOSED_VENUE_POLICY` (keyed by redirect id; `'allowed' | 'blocked' | 'evening'`), `getBraExposedVenueBlockMessage(restriction)`, and `getPartialExposureVenueBlockMessage(restriction)`, which runs in `showScene` before the sheer check. Panties Exposed is blocked everywhere restricted via `getExposureBlockMessage()`; Jack answers the Bargain Threads door himself. A soaked bra is blocked everywhere restricted. Lucius' allows Bra Exposed 6 PM to 6 AM (sports bra blocked there).
- **Wet bra:** `checkWetBraThroughNotice()` fires the one-line notification once per day from the rain branch of `updateClothesWetness()` (flag `_wetBraNoticeDay`).
- **Transit:** `travelToLocation()` lets Bra Exposed ride; `getBraExposedTransitLine(method)` gives the driver line (three openers × three voices per method; silent for a sports bra). Soaked bra and Panties Exposed are refused (Uber −0.2 rating).
- **Evidence:** `checkExhibitionEvidence()` logs `exposureState: 'panties_exposed'` or `'bra_wet_through'` (minor; moderate on a night camera). A dry opaque bra logs nothing.
- **Suspicion on district entry:** Bra Exposed 0, soaked +2, Panties Exposed +2.
- **Indecency clock:** `tickIndecencyPolice()` runs in every district street `onEnter` after `tickSheerLingeriePolice()`; four hours in visible Panties Exposed or Underwear Only (the full `sheer_lingerie` set is excluded; it has its own clock), then `handleIndecencyPoliceArrive(state)` (six texts: two states × three voices) and `handleNakedEscape()`. `getIndecencyClockRemaining()` returns minutes left or `null` (**Step 16B needs this**). Stored in `gameState.indecencyClockStartTime`.
- **Appeal:** Bra Exposed no longer suppresses imperfection penalties; Panties Exposed still does.

### 2.3 Passive layer (Step 3)

- Thrill: `braExposedThrill` (+2, sporty +1, soaked +4 which also switches off the wet-clothes thrill) and `pantiesExposedThrill` (+3) accumulators; public and visible only; mesh lingerie replaces them. Both hold thrill against decay. The old `underwearPartialThrill` accumulator is retired (cleared every tick).
- Thong friction: `thongRideArousal`, +1 arousal per 30 min in public Panties Exposed, through `applyArousal()`.
- District-entry arousal (topless, bottomless, underwear) goes through `applyArousal()`. Mental health per state: Bra Exposed −1/0/+1, Panties Exposed −3/0/+2 plus `modifyKarma(-3)` (toward Demonic) at Corrupted+.
- Lifetime minutes: `braExposedMinutesTotal`, `pantiesExposedMinutesTotal`; `underwearMinutesTotal` means Underwear Only again. Shown in the Current Stats exhibition card.
- Appearance tab: Bra Exposed box with rose border (`#d9738f`) and sparkle icon, sub-line by `braStyle` (`getBraExposedBoxSub()`); soaked shows `BRA EXPOSED (SOAKED)` with the warning icon. Panties Exposed keeps the warning icon, sub-line by cut and butt size (`getPantiesExposedBoxSub()`).

### 2.4 Street content (Steps 4–7)

- **District entry:** `handleUnderwearDistrictEntry()` routes `bra_only` / `panties_only` to `getBraExposedNPCVignette()` / `getPantiesExposedNPCVignette()` (8 each, de-duped through `_bpePickVignette()` and `_braExposedReactionIdx` / `_pantiesExposedReactionIdx`) and `getBraExposedKelsieResponse(c)` / `getPantiesExposedKelsieResponse(c)` (4 voices × 3). First walks: `getBraExposedFirstWalk(c)` / `getPantiesExposedFirstWalk(c)` (flags `seenFirstBraExposedWalk` / `seenFirstPantiesExposedWalk`), which skip the 20-minute cooldown. Underwear Only keeps its original block. The old scandal-register `braPool` inside `getUnderwearNPCVignette()` is now unused (left in place).
- **`getPantiesExposedAssView()`**: the shared body-branched view from behind (buttSize × pantiesCut). It can end in an appositive ("your full ass, bare on both sides of the thong"), so **always put it at the end of a sentence**.
- **Ambient lines:** `EXHIBITION_AMBIENT_LINES.bra_exposed` / `.panties_exposed` (6 per voice) with `variants` (`commando`, `sporty`, `soaked` [replaces the base], `braless`, `thong`). Tokens `{bra} {panties} {top} {bottom} {tits} {Tits} {ass} {Ass}` resolve in `checkExhibitionAmbientLine()`.
- **Jack, Cruz, Jaewon (Step 6B):** `getJackPantiesDoorLine()`, `getJackBraExposedLine()`, `jackBraDiscountAvailable()`, scene `jack_bra_exposed_discount` (V-neck at $4.50, equips it, once per day, flag `_jackBraDiscountDay`, item `JACK_DISCOUNT_TOP`); Cruz's `getCruzSheerExposureLine()` has a Panties Exposed read; `getJaewonPartialExposureReaction()` runs inside `getJaewonBralessCommandoReaction()` after the sheer reactions (girlfriend: tiered; below girlfriend: once per day, flag `_jaewonBpeFriendDay`).
- **Harassment (Step 7):** chance rules in `checkDistrictHarassment()`; openers/gropes `getBpeHarassOpener(area)` / `getBpeHarassGrope(area)` (district `<p>` format with the tall one and the stocky one; slums `\n\n` format with the leader, the wiry one, and the big one); gains `getBpeGropeGains()` (25/25 and 18/15); choice-scene layers `getBraExposedHarassLayer(kind)` wrapped into the eight choice scenes through a `_bpeBaseText` pattern.

### 2.5 Gang scenes, night assault, pass-out (Steps 8–10)

- **`district_exhibition_panties`** and **`slums_exhibition_panties`**: all three tiers written and live. Wiring copied from the sheer scenes: `galleryOnEnter` dresses her (Plain White T-Shirt, Basic Black Bra, Lace Thong); `onEnter` calls `completeSexAssaultMale(...)` (district: 15 min, −30 hygiene, 25 corruption, 15 thrill, `district_group`; slums: 20, −35, 30, 20, `slums_group`), runs its own pregnancy check (`_districtPantiesExhibPregnancy` / `_slumsPantiesExhibPregnancy`), and increments `streetHarassmentCount` or `slumsHarassmentCount` plus `exhibitionEventsCompleted`. Gallery entries: "District: Panties Exposed Exhibition" and "Slums: Panties Exposed Exhibition" (all three tiers).
- **Torn panties at 8001+:** `onEnter` runs **before** `text()` in this engine, so `onEnter` only sets `_districtPantiesTornPending` / `_slumsPantiesTornPending`; the exit choice calls `bpeTearPantiesToBackpack(flagKey)`, which moves the panties to the backpack at damage 100 and leaves her bottomless. Reuse this pattern whenever a scene removes clothing its own text still needs to name.
- **Tier-by-tier routing:** `BPE_EXHIB_TIER_CAP` (both scenes now `Infinity`). `getHarassExhibScene(area)` forces `<area>_exhibition_panties` only below the cap. Reuse the idea for any multi-tier scene built one tier at a time: route only to written tiers.
- **Night assault:** `checkNightRapeEncounter()` rules; `getNightAssaultStateParagraph(who)` / `insertNightAssaultStateParagraph(text, who)` add a state paragraph after the opening of the five night scenes; those scenes read garment words through local `_nt` / `_nb` / `_nl`.
- **Pass-out:** both states in `slums_passout_rape_main` and `slums_passout_rape_aftermath`.

### 2.6 All new `gameState` fields (defaults in the new-game state and the save migration)

`gameState.indecencyClockStartTime`; `exhibitionStats.braExposedMinutesTotal`, `exhibitionStats.pantiesExposedMinutesTotal`; flags `_braExposedReactionIdx`, `_pantiesExposedReactionIdx`, `_lastBraAccidentMinute` (reserved for Step 11), `_lastPantiesAccidentMinute` (reserved for Step 11), `_bpeActionMinutes` (reserved for Step 12), `seenFirstBraExposedWalk`, `seenFirstPantiesExposedWalk`, `_wetBraNoticeDay`, `_districtPantiesExhibPregnancy`, `_districtPantiesTornPending`, `_slumsPantiesExhibPregnancy`, `_slumsPantiesTornPending`. Also used without migration defaults (read with `|| 0` guards): `_jackBraDiscountDay`, `_jaewonBpeFriendDay`.

### 2.7 Remaining BPE shims (Step 17 must reach zero)

Step 1 wrapped every consumer of the exposure level in `_bpeShim(level)` (maps the two new levels back to `'clothed'`), tagged `// BPE shim: Step N`. Steps 2, 3, 5, 7, and 10 removed theirs. **Nine remain:**

| Site | Tag | Removed by |
|---|---|---|
| Current Stats exposure label (inside `_buildAllStatsIntoTemp()`, `_currExposure`) | Step 16B | 16B |
| `_seductExposureLine(g)` | Step 14A | 14A |
| `hunt_seduction` choices (`_hsExposure`) | Step 13A | 13A |
| `riverside_park` text (`exposure`) | Step 12C | 12C |
| `riverside_park` choices (`exposure`, porta-potty priority) | Step 12C | 12C |
| `park_porta_potty` text | Step 12C | 12C |
| `exhibition_masturbate_alley` (`exposure`, incl. gallery override) | Step 12A | 12A |
| `exhibition_masturbate_park_bench` (`exposure`, incl. gallery override) | Step 12A | 12A |
| `slums_sheer_exhibition` `clothesWord` | Step 17 | 17 |

The `_bpeShim()` helper itself (next to `getExposureLevel()`) is removed in Step 17 once no call remains. Note: the two `riverside_park` / porta-potty sites weren't in the original audit table; they were shimmed under 12C because `exposure !== 'clothed'` there would otherwise flip.

### 2.8 Testing approach that worked

- Headless Chromium through Playwright: `require('/opt/node22/lib/node_modules/playwright')`, route non-`file:` requests to abort, load the page, wait ~3 s, then `page.evaluate(...)` against the live globals (`gameState`, `story`, the functions). Stub `showMessage`, `showNotification`, `showScene`, `handleNakedEscape` to capture output. `currentScene` is a top-level `let` (assign it directly, not `window.currentScene`), and `advanceTime()` needs `gameState.timeStarted = true`.
- Build test outfits from real item objects (e.g. `{ id: 'basic_black_bra', name: 'Basic Black Bra', tags: ['material:cotton'] }`, `{ id: 'thong_panties', name: 'Lace Thong' }`, `{ id: 'white_sports_bra', name: 'White Sports Bra', tags: ['sporty'] }`, `{ id: 'mesh_tease_bra', … }` for mesh).
- For prose: sweep every combination (5 body types × 3 sizes × 4 cuts × bra/braless × tiers) and flag `undefined|NaN|null`, `{…}` tokens, banned words, "your top/skirt" fallbacks in the wrong state, thong subject-verb agreement, and paragraphs over ~330 visible characters. Then read a full sample.
- For behavior changes on existing systems: render the same outfits against the previous version (git history) and diff the outputs.

### 2.9 Gotchas learned

- **Thong grammar:** `cv.pantiesName` as a subject breaks with a thong ("your lace thong are"). Use local helpers: `panWord = thong ? 'thong' : pan`, `panIt = thong ? 'it' : 'them'`, and verb pairs (`'it stretches' : 'they stretch'`). Keep `{panties}` in the object slot in token pools.
- **Ass-view appositive:** see 2.4.
- **`onEnter` before `text()`:** see 2.5.
- **District vs slums format:** district scene text uses `<p>…</p>` paragraphs; slums uses `\n\n`.
- **Scene object name:** the scene table is `const story = {…}`; a scene can call `story.<id>.<fn>()` at runtime.
- **Duplicate strings:** many prose lines appear more than once in the file; scope replacements to the scene (find the scene header, then the first match after it).
- **The Mesh functions** (`_sheerBodyCtx()`, `getSheerHarassState()`, etc.) own see-through; call them first and fall through to the BPE content only when they return nothing.

---

## 3. Steps 1–10: Done (summary, with deviations)

Everything below is built, tested, committed, and pushed. Deviations and additions from the original spec are called out, because later steps and the Step 17 audit depend on them.

- **Step 1: Foundation.** All of 1A (two new levels), 1B (concealment), 1C (sheer gate), 1D (legality helpers), 1E (cv additions), 1F (`getUnderwearVisibilityDesc()`), 1G (consumer shims), 1H (new fields) as specified. Additions: the dress-category rule also applies in `isEffectivelyBottomless()` and `cv.commando`; `getBraStyle()` / `getPantiesCut()` helpers; the shim table in 2.7.
- **Step 2: The law.** All of 2A (leave gates), 2B (refusal text), 2C (venue policy), 2D (wet bra), 2E (transit), 2F (evidence), 2G (suspicion on district entry), 2H (indecency clock), 2I (appeal penalty suppression). Additions: `getExposureLeaveGate()` (stops a 2001 bra gate from letting her walk out bottomless); the soaked bra doesn't raise the leave gate (the doc listed evidence, venues, transit, harassment only); the indecency clock only checks on district entry, so time at home counts toward it (same as the sheer clock); a single mesh piece in either state (`sheer_lingerie_top/bottom`) is covered by the indecency clock.
- **Step 3: Passive layer.** All of 3A (thrill rates), 3B (arousal through `applyArousal()`, thong tick), 3C (mental health and karma on district entry), 3D (lifetime stats), 3E (Appearance tab copy). Lifetime minutes count raw level, like the other counters (time at home counts). The soaked-bra Appearance variant from 2D lives here.
- **Step 4: District entry.** All of 4A (routing), 4B (Bra Exposed vignettes), 4C (Panties Exposed vignettes), 4D (Kelsie responses and first walks).
- **Step 5: Ambient lines.** All of Step 5, plus `{tits}` / `{ass}` body tokens.
- **Step 6: Style comments & named NPCs.** **Only Jack, Cruz, and Jaewon were built, on the user's instruction.** Not built (don't build unless asked): 6A `BRA_EXPOSED_STYLE_COMMENTS` / `PANTIES_EXPOSED_STYLE_COMMENTS`; Ruby (diner door, both states); Livie (convenience store door, both states); Lucius doorman line (Bra Exposed evening) and refusal line (Panties Exposed); Hardy Bar bartender free-drink line (Bra Exposed). The venue *policy* for those doors exists (Step 2C); only the named-NPC lines are missing. Jack's Bra Exposed "discount" became a real choice (`jack_bra_exposed_discount`), per the spec's "offers a top she's expected to try on in front of him".
- **Step 7: Harassment.** All of 7A (chance rules), 7B (ladder, openers, gropes, gains), 7C (routing), 7D (choice-scene layers). Additions: both passive choice scenes had an undeclared `cv` in their text (a latent crash at 6000+), now fixed; their prose says the bra instead of "top" in Bra Exposed; the slums flirt equivalent is `slums_harassment_moan_choice`.
- **Step 8: `district_exhibition_panties`.** All three tiers. Innocent: taken from behind over the dumpster lid (park: bin lid behind the maintenance shed), panties aside then down to mid-thigh, both men in turn. Experienced: hands on the wall, eaten from behind and edged, she steps out of one side of her panties, the tall one from behind with her hand on the stocky one, who finishes across her ass. Deeply Corrupted: she leads, bends over first, pulls the crotch aside, directs fingers and a mouth, has them torn, walks out bottomless.
- **Step 9: `slums_exhibition_panties`.** All three tiers. Innocent: over the hood of a dead car at the curb, a man on a stoop watching, all three take turns. Experienced: in the leader's lap on a stoop facing the street, the wiry one filming, riding with her knees held together by her panties, the wiry one finishes into the crotch of her panties and she pulls them back up. Deeply Corrupted: under the one working streetlight, she bends over first, has them ripped off, rides the big one on the curb with the leader in her ass and the wiry one in her mouth, walks off bottomless.
- **Step 10: Night assault & pass-out.** All of 10A (chance rules), 10B (ambush wording and scene state paragraphs), 10C (pass-out branches). Additions: a bra hidden under a jacket is no longer guaranteed a night assault; the five night scenes now read garment words correctly in both states (including a hard-coded "your top" in the female Deeply Corrupted tier that was wrong for every outfit); the pass-out's "back of your top" names the bra.

---

## 4. Existing Systems the Remaining Steps Touch (verified in the current file)

Line numbers drift; always re-grep the name.

**Events and deliberate acts**
- The **20 exhibition events**, registered in the district event pool with garment conditions (skirt/dress events and top events), `hold_bra` / `hold_panties` / `hold_bare` variants, outerwear concealment multipliers, and Mesh Step 10 sheer multipliers (`getSheerExhibitionChanceMult()`). The 20 scene ids: `exhibition_wind_trigger`, `exhibition_bra_wind_trigger`, `exhibition_barstool_trigger`, `exhibition_bench_trigger`, `exhibition_bus_trigger`, `exhibition_button_trigger`, `exhibition_crowd_trigger`, `exhibition_escalator_trigger`, `exhibition_fitslip_trigger`, `exhibition_fitting_trigger`, `exhibition_lean_trigger`, `exhibition_mirror_trigger`, `exhibition_neckline_trigger`, `exhibition_photo_trigger`, `exhibition_puddle_trigger`, `exhibition_reach_trigger`, `exhibition_spill_trigger`, `exhibition_staircase_trigger`, `exhibition_turnstile_trigger`, `exhibition_wet_trigger`.
- `checkAccidentalExposure(districtId)`: braless/commando accidents; currently returns early for any visible-underwear type. Called from every district street `onEnter` after the sheer/indecency ticks.
- `getFlashExhibitionChoice()` (6001+, thrill 70), `getPoseExhibitionChoice()` (8001+, thrill 75, topless/bottomless/naked or sheer pose states), the public masturbation choice (`exhibition_masturbation_choose`, `exhibition_masturbate_alley`, `exhibition_masturbate_park_bench`; 6001+ with thrill and arousal gates, once per day each), `getSheerExhibitionChoices()`.
- `exhibition_bus_trigger` fires from `travelToLocation()` (skirt/dress condition, 20% × sheer multiplier). Bra Exposed now rides the bus, so it can reach this scene.
- `riverside_park`: exposure-aware description for naked, topless, bottomless; nothing for underwear states.

**Seduction**
- `hunt_seduction` choices: at 6001+ and appeal 80+, routes `underwear_only` to Branch A (`exhibition_seduce_bra_*`), `topless` to B (`_top_`), `bottomless` to C (`_btm_`), `naked` to D (`_nkd_`). Each branch has twelve scenes per gender (`_m` / `_f`): `approach`, `proposition`, `success_pivot`, `soft_pivot`, `exit_success`, `exit_soft`, `failure_dismissive`, `failure_apologetic`, `failure_disturbed`, `failure_impressed_but_no`, `sex`, `sex_2`. Scene ids follow `exhibition_seduce_<branch>_<scene>_<m|f>`.
- `SHEER_SEDUCTION_BRANCH` currently maps `sheer_braless` and `sheer_lingerie_top` → `top`, `sheer_commando` and `sheer_lingerie_bottom` → `btm`, `sheer_bare` → `nkd`, `sheer_lingerie` → `bra`; `getSheerSeductionBranch()` reads it; `hunt_seduction` sets `gameState.flags._seductionSheer` and the sheer overlays run from `_activeSeductionSheer()`.
- `_seductExposureLine(g)` / `_seductWithExposure()`: one exposure line for the regular street seduction flow (currently shimmed).
- `calculateSeductionSuccess()`.
- `seduce_lucius_man` / `seduce_lucius_woman`: an `isWearingVisibleUnderwear()` line.
- Regular street seduction chains: `seduce_street_man`, `seduce_street_man_direct`, `seduce_street_man_flirt`, `seduce_street_woman`, `seduce_street_woman_direct`, `seduce_street_woman_flirt` (and whatever they chain into; audit with a grep for `topName`).

**Combat**
- `handleDamagedClothing()`: combat destruction messages; sets `clothesDestroyedInCombat`.

**UI**
- Appearance tab (`updateAppearanceDisplay()`, `getUnderClothesBannersHTML()`, `getSheerLingerieBoxInfo()`): done in Step 3E.
- Current Stats exhibition card (built in `_buildAllStatsIntoTemp()`), About Stats, Tips & Guide exhibitionism entry, the Scene Gallery registry (`SCENE_GALLERY_REGISTRY`, entries `{ sceneId, label, tiers: [{l, v}], flagOverrides?, galleryChain? }`; scenes can define `galleryOnEnter`), the cheat panel.

---

## 5. Remaining Steps (11–17), in Full

### Step 11: Accidental Events

**11A. Open the gate.** `checkAccidentalExposure()` returns early for any visible-underwear type. Change it to return early only for Underwear Only and above. In the two partial states, only the covered zone can have an accident:

| State | Zone that can have an accident | Existing events that apply |
|---|---|---|
| Bra Exposed | hips (skirt or dress bottom only) | commando events if bare; the skirt/dress exhibition events |
| Panties Exposed | chest | braless events if bare; the top exhibition events |

**11B. The 20 exhibition events.** Their garment conditions already filter correctly (skirt events need a skirt, top events need a top). Add a layer line to the events that can fire in each state:
- Skirt events in Bra Exposed: the hem lifting while her tits are already out in the bra. One inserted sentence per event, per tier.
- Top events in Panties Exposed: the button pops, or the top rides up, while her panties are already on display. One inserted sentence per event, per tier.

**11C. New state-specific accidents.** Three per state, in the accident pool format (`text`, `thrill`, `arousal`, corruption-tiered `kr`), 60-minute shared cooldown (flags `_lastBraAccidentMinute` / `_lastPantiesAccidentMinute` already exist), 15% per transition.

Bra Exposed:
1. **Strap slip.** A strap slides off her shoulder, the cup peels down, and a nipple's out on the sidewalk for two seconds. (`breastSize` BP.) thrill 10, arousal 7.
2. **The clasp gives.** Back clasp pops mid-stride. She catches the cups against her chest with both arms. Anyone behind her sees her bare back and the bra hanging open. thrill 12, arousal 8. At 8001+ she lets go.
3. **Sports bra ride-up** (sporty only). Reaching for something high, the band rides up and her underboob's out. thrill 8, arousal 5.

Panties Exposed:
1. **Ride-up.** Her panties creep up between her cheeks with every step until her ass is nearly bare. (`pantiesCut`, `buttSize` BPs; a thong version where it's already there and now it's worse.) thrill 10, arousal 7.
2. **The wet spot.** At arousal 50+, the crotch of her panties goes dark and a stranger's eyes find it. thrill 12, arousal 8.
3. **Cold bench.** She sits without thinking and the bench is cold metal through thin fabric; the man across from her gets a straight view between her knees. thrill 10, arousal 7.

**11D. `exhibition_bus_trigger`.** Bra Exposed rides the bus (Decision 1), so add a Bra Exposed branch: the crowded aisle, a hand that brushes the cup, a man who stands too close behind her.

Implementation notes: study how `checkAccidentalExposure()` builds its braless/commando pool and how the sheer accidents (`_buildSheerAccidentPool()`) are structured before writing. Mesh pieces route to the sheer accidents, never these. Arousal through `applyArousal()`. The user may want Step 11 split (e.g. 11A + 11C, then 11B + 11D); ask.

### Step 12: Deliberate Acts

**12A. Existing verbs.**

| Verb | Bra Exposed | Panties Exposed |
|---|---|---|
| Flash (`getFlashExhibitionChoice`, 6001+, thrill 70) | Flash top: pull the cups down. Flash bottom: skirt only | Flash top: lift the top (bra or bare). No bottom to flash |
| Strike a pose (`getPoseExhibitionChoice`, 8001+, thrill 75) | Excluded | Included; add Panties Exposed text to standing, seated, walk-by |
| Public masturbation (alley and park bench, 6001+) | Add a Bra Exposed exposure branch | Add a Panties Exposed exposure branch |

`exhibition_masturbate_alley` and `exhibition_masturbate_park_bench` each gain two `exposure` branches and two Gallery entries (`Alley: Bra Exposed`, `Alley: Panties Exposed`, `Park Bench: Bra Exposed`, `Park Bench: Panties Exposed`). Panties Exposed: fingers under the leg band, then the crotch pulled aside; the top stays on. Bra Exposed: one hand pulling a cup down, the other down the front of her skirt or jeans (`cv.liftable` / `cv.pulldown`). Remove the two Step 12A shims. The masturbation scenes take their gallery exposure from `_alleyMastGalleryExposure` / `_parkBenchMastGalleryExposure` flag overrides (existing gallery entries use values like `'underwear_only'`, `'clothed'`, `'sheer'`); add `'bra_exposed'` / `'panties_exposed'` entries and make the scenes read them. These are sexual beats: Sex Standards apply, and they're written one tier at a time (the scenes' tiers are Corrupted 6001 and Deeply Corrupted 8001).

**12B. New verbs.** Offered on district streets through a `getExposedUnderwearChoices()` function (same slot as `getSheerExhibitionChoices()`):

| Verb | State | Gate | Cooldown | Gains (thrill / arousal / corruption / suspicion) |
|---|---|---|---|---|
| **Roll your shoulders back** | Bra Exposed | 2001, thrill 20 | 30 min | 6 / 4 / 1 / 0 |
| **Unhook it** | Bra Exposed | 6001, thrill 50 | 60 min | Removes the bra into the backpack and moves her to Topless; topless systems take over |
| **Tug a cup down** | Bra Exposed | 8001, thrill 70 | 45 min | 12 / 10 / 3 / 1 |
| **Bend over** | Panties Exposed | 6001, thrill 40 | 30 min | 10 / 8 / 2 / 1 |
| **Pull them aside** | Panties Exposed | 8001, thrill 70 | 45 min | 15 / 14 / 4 / 2 |

Each verb gets a text function with three corruption voices (only the voices its gate allows), 4 to 6 BPs, and an NPC reaction beat. "Unhook it" fires the topless district-entry vignette immediately afterward (it bypasses the cooldown once). Per-verb cooldowns go in `gameState.flags._bpeActionMinutes` (already migrated). Arousal through `applyArousal()`, thrill through `applyExhibitionThrillGain()`, suspicion through `modifyCitySuspicion()`.

**12C. Riverside park.** `riverside_park`'s exposure description covers naked, topless, and bottomless. Add Underwear Only, Bra Exposed, and Panties Exposed paragraphs, and let the bench masturbation choice read them. Remove the three Step 12C shims (`riverside_park` text and choices, `park_porta_potty`). Decide per site: the porta-potty "rush to get dressed" priority makes sense for Panties Exposed (a crime) but probably not for a legal Bra Exposed.

### Step 13: Exhibition Seduction Branch E: Panties Exposed

**13A. Routing.** In `hunt_seduction`, at 6001+ and appeal 80+, `panties_exposed` routes to `exhibition_seduce_pan_approach_m` / `_f`. Re-point `SHEER_SEDUCTION_BRANCH.sheer_lingerie_bottom` from `btm` to `pan`: mesh panties under a top are this state, and the existing sheer overlays run on the new branch. Remove the Step 13A shim. Check the sheer-branch mapping line inside `hunt_seduction` (`{ bra: 'underwear_only', top: 'topless', btm: 'bottomless', nkd: 'naked' }[_hsSheer.branch]`) needs a `pan: 'panties_exposed'` entry.

**13B. The 24 scenes.** Twelve per gender on the Branch A skeleton: `approach`, `proposition`, `success_pivot`, `soft_pivot`, `exit_success`, `exit_soft`, `failure_dismissive`, `failure_apologetic`, `failure_disturbed`, `failure_impressed_but_no`, `sex`, `sex_2`. Ids: `exhibition_seduce_pan_<scene>_<m|f>`.

What this branch owns:
- **The approach is from behind.** The target sees her ass before her face. Her opener plays on it.
- **The proposition lives on her clothes.** She's already half-dressed for it; the pitch is how little is left to move.
- **The sex scenes never take the top off first.** Top stays on until position two. Panties aside, then off (`pantiesCut` branches), then the top comes up. `cv.braless` decides what his hands find under it.
- **Failure scenes** each have a Panties Exposed flavor: the dismissive one who looks at her crotch while he says no, the disturbed one who asks if she's okay, the impressed one who takes a photo as he leaves.

BP targets: approach and proposition 3 to 5; pivots and exits 2 to 3; failures 2; sex scenes 8 to 10. Gallery section: `Exhibition Seduction: Panties Exposed`. Copy the wiring of Branch A (`exhibition_seduce_bra_*`) exactly: study its success roll, the sheer overlays, the counters (`exhibitionSeductionCount`), feeding, the gallery registration, and the male/female split before writing. The sex scenes are written one tier at a time if they branch by tier; check Branch A's structure and ask the user how they want the 24 scenes batched (likely non-sex scenes first, then the sex scenes).

### Step 14: Bra Exposed Seduction Layer

Bra Exposed is legal, so it doesn't get an exhibition branch. It changes the regular seduction instead.

**14A. Mechanics.**
- `calculateSeductionSuccess()`: +0.03 in Bra Exposed (the look does half the work). Sports bra +0.01.
- `_seductExposureLine(g)`: add a `bra_exposed` line (two variants per gender) and a Panties Exposed line for the regular flow below 6001. Remove the Step 14A shim.
- `seduce_lucius_man` / `_woman`: replace the `isWearingVisibleUnderwear()` line with per-state lines.

**14B. Prose layer in the regular street seduction.** Every scene in the `seduce_street_man` / `seduce_street_woman` chains that names her top gets a `cv.braExposed` branch: no top to pull over her head, a strap slid off the shoulder instead, the cups pulled down, the clasp. Audit with a grep for `cv.topName` and `topName` inside those chains. BP: `breastSize` and `braStyle` at the first chest contact. (Also check `cv.bottomName` in those chains for Panties Exposed below 6001.)

### Step 15: Combat & Forced States

**15A. `handleDamagedClothing()` messages.**

| Result | Message |
|---|---|
| Top destroyed, bra intact, bottom intact | "Your {top}'s torn away. You're down to your bra and your {bottom}." |
| Bottom destroyed, panties intact, top intact | "Your {bottom}'s shredded. Your {panties} are all that's left below your {top}." |

Both set `clothesDestroyedInCombat` as today. For Bra Exposed, the flag only matters if the bra itself is sheer or soaked. (Thong grammar: "Your lace thong is all that's left…" when the cut is a thong.)

**15B. Forced-state gate text.** When `clothesDestroyedInCombat` waives the gate, the first district entry in each state gets a forced-state response in her voice (she didn't choose this; her body reacts anyway), the pattern the topless and bottomless forced entries use. Find those forced entries first and copy their wiring. Consider whether this should take the place of the Step 4D first-walk beat when the state was forced.

### Step 16: UI

**16A. Tips & Guide.** In the exhibitionism entry:
- Exposure States: rewrite the Bra Exposed and Panties Exposed entries around legality, gates, thrill, and what each unlocks.
- Thrill Sources, Decay, Transit, venues, harassment, night assault, evidence, police clock, the new verbs, Branch E, the Bra Exposed seduction bonus, the wet-bra rule.
- Scene list for the new gang scenes and masturbation branches.
- Document what's actually built (e.g. Jack's door and discount; not the unbuilt Ruby/Livie/Lucius/Hardy lines).

**16B. About Stats & Current Stats.**
- About Stats Exhibition Thrill: the thrill table rows for both states and the sports bra (and the soaked bra's +4).
- Current Stats exhibition card: label (the label already reads Bra Exposed / Panties Exposed through the Step 1 fallback, but remove the Step 16B shim and read the new levels directly), the state's live effects (thrill rate, suspicion, venue status, indecency clock remaining for Panties Exposed and Underwear Only via `getIndecencyClockRemaining()`), and the lifetime minutes from 3D (already shown).

**16C. Scene Gallery.** Register every new scene: two gang scenes, three tiers each (**done**); four masturbation branches (Step 12); Branch E (24 scenes, two genders); the verbs; the accidents; the first-walk beats. Gallery flag overrides set the outfit so the scene renders with a bra or panties and the right garments (`galleryOnEnter` works for this; the gang scenes use it).

**16D. Cheat panel.** Add "Outfit: Bra Exposed", "Outfit: Panties Exposed", and "Soak clothes (wetness 70)" buttons for testing.

### Step 17: Save Migration, Polish & Balance

**17A. Save migration.**
- Every field from 1H defaults (already migrated; re-verify, and add any fields Steps 11–16 introduce, e.g. `_jackBraDiscountDay`, `_jaewonBpeFriendDay` if wanted).
- `underwearMinutesTotal` keeps its value; the new counters start at 0.
- Every `// BPE shim` marker from 1G is gone. Grep for it; zero hits. Then delete `_bpeShim()`.

**17B. Style guide audit.** Every new string: contractions; no em-dash connectors (paired parentheticals and dialogue cut-offs only); no "the kind of," "a beat," "a pause," "genuinely," "the way," "in a way"; no Not/Not, No/No, or Something/Something pairs; no negation-before-reveal; no stanza formatting; short paragraphs under ~300 characters; no staccato chains. Crude anatomical language everywhere Kelsie's aroused. `cum` for orgasm. Escaped apostrophes. `node --check` clean on both script blocks.

**17C. Body Branching audit.** Every scene: variable extraction at the top; `gameState.bodyAppearance` only; all five `bodyType` values; `'large'`/`'small'`/`'average'`; BP counts in range; no `cv.topName` in Bra Exposed prose and no `cv.bottomName` in Panties Exposed prose; `cv.braless` / `cv.commando` at every undressing step.

**17D. Sex Standards audit.** Every intimate beat: no "core," no "pulse/wave," phonetics spelled out, position tracked, approach/hit/linger on every orgasm, active present-tense ending.

**17E. Balance pass.**
- Bra Exposed should feel rewarding and safe enough to wear on purpose: small thrill, a seduction edge, most doors open, and a real risk only when it rains.
- Panties Exposed should feel as dangerous as Underwear Only: daily harassment, guaranteed night assault, evidence, blocked doors, the shared four-hour indecency clock, and the biggest thrill of the partial states.
- The indecency clock should land about once per long public outing in either state. If police arrive during ordinary errands, lengthen it; if a player can live in Underwear Only all day without consequence, shorten it. (Known quirk to weigh: the clock only checks on district entry and counts time spent at home.)
- A Kelsie in Bra Exposed with commando under a skirt should read as two layers of exposure stacking, not as a new state.
- The thong arousal tick shouldn't push arousal into Edged on its own over a normal afternoon (it's ~2/hr now).
- The new verbs' cooldowns shouldn't let thrill farming outpace the Mesh verbs.

**17F. Mental model (the check for everything).**
1. **Legality splits the partial states.** Panties Exposed is a crime and gets everything a crime gets. Bra Exposed is a look and gets everything a look gets: attention, approval, disapproval, a seduction edge, and the ache between her thighs from being stared at.
2. **The line can move.** Rain soaks a cotton bra through, and the look becomes indecent. A mesh bra was indecent from the start. The player learns the weather is part of the outfit.
3. **The body is always in it.** Every state names what her nipples, her pussy, and her clit are doing, at every corruption tier, because being seen is what she's for.
4. **The camera follows the exposure.** Bra Exposed scenes are shot from the front: her chest, the cups, the straps. Panties Exposed scenes are shot from behind: her ass, the leg band, the thong. Body branching carries the difference.

---

## 6. Reference Tables

### New helper / function summary (from the original, with status)

| Function | Step | Purpose | Status |
|---|---|---|---|
| (rework) `getExposureLevel()` | 1A | Adds `'panties_exposed'` and `'bra_exposed'` | Done |
| (rework) `getVisibleExposureLevel()` | 1B | Concealment for both new levels | Done |
| (rework) `getSheerState()` level gate | 1C | Keeps the sheer lingerie states computing | Done |
| (new) `isLegallyDressed()` | 1D | Law-facing check | Done |
| (new) `isVisiblyExposed()` | 1D | Eye-facing check | Done |
| (new) `isCriminalSheerState()` | 1D | Wraps the indecent Mesh states | Done |
| (rework) `getSceneClothingVars()` | 1E | `exposureState`, `braExposed`, `pantiesExposed`, `braName`, `pantiesName`, `braStyle`, `pantiesCut` | Done |
| (fix) `getUnderwearVisibilityDesc()` | 1F | Correct wording for both states | Done |
| (rework) `getVisibleUnderwearGate()` | 2A | Per-state gates, sports bra exemption | Done (+ `getExposureLeaveGate()`) |
| (new) `getBraExposedVenueBlockMessage()` | 2C | Venue refusals for Bra Exposed | Done |
| (new) `isWetBraThrough()` | 2D | Soaked opaque bra counts as sheer | Done |
| (rework) `travelToLocation()` | 2E | Transit per Decision 1 | Done |
| (rework) `checkExhibitionEvidence()` | 2F | Panties Exposed and wet-bra entries | Done |
| (new) `tickIndecencyPolice()` / `handleIndecencyPoliceArrive(state)` | 2H | Shared four-hour clock | Done |
| (rework) passive thrill block | 3A | Split accumulators, sports bra, wet bra | Done |
| (rework) `handleUnderwearDistrictEntry()` | 4A | Routes both states; `applyArousal()` | Done |
| (new) `getBraExposedNPCVignette()` / `getBraExposedKelsieResponse()` | 4B, 4D | District entry | Done |
| (new) `getPantiesExposedNPCVignette()` / `getPantiesExposedKelsieResponse()` | 4C, 4D | District entry | Done |
| (rework) `EXHIBITION_AMBIENT_LINES` + `checkExhibitionAmbientLine()` | 5 | Two new pools, `{top}` / `{bottom}` tokens | Done |
| (new) `BRA_EXPOSED_STYLE_COMMENTS` / `PANTIES_EXPOSED_STYLE_COMMENTS` | 6A | Street style comments | **Not built (user scoped Step 6 to Jack, Cruz, Jaewon)** |
| (new) named-NPC line helpers (Ruby, Livie, Lucius, Hardy, Jack, Jaewon, Cruz) | 6B | Per-state lines | Jack, Cruz, Jaewon done; others **not built by user choice** |
| (rework) `checkDistrictHarassment()` / `getHarassExhibScene()` | 7A, 7C | Chance rules and routing | Done |
| (rework) `checkNightRapeEncounter()` | 10A | Bra Exposed no longer guaranteed | Done |
| (rework) `checkAccidentalExposure()` | 11A, 11C | Zone gating, six new accidents | **To do** |
| (new) `getExposedUnderwearChoices()` + five verb handlers | 12B | Deliberate acts | **To do** |
| (rework) `hunt_seduction` routing, `SHEER_SEDUCTION_BRANCH` | 13A | Branch E | **To do** |
| (rework) `calculateSeductionSuccess()`, `_seductExposureLine()` | 14A | Bra Exposed bonus and lines | **To do** |
| (rework) `handleDamagedClothing()` | 15A | Combat messages | **To do** |

### New scene summary (from the original, with status)

| Scene / beat | Step | Type | Trigger | Status |
|---|---|---|---|---|
| Bra Exposed first-walk beat | 4D | One-time | First district entry in Bra Exposed | Done |
| Panties Exposed first-walk beat | 4D | One-time | First district entry in Panties Exposed | Done |
| Bra Exposed district vignettes (8) + responses (12) | 4B, 4D | Repeatable | District entry, 20-minute cooldown | Done |
| Panties Exposed district vignettes (8) + responses (12) | 4C, 4D | Repeatable | District entry, 20-minute cooldown | Done |
| Ambient lines (2 pools × 3 voices × 6) | 5 | Repeatable | Street scenes, 15-minute cooldown | Done |
| Wet-bra notification | 2D | Once per day | Opaque bra soaked to 60+ in Bra Exposed | Done |
| Indecency police arrival (2 states × 3 voices) | 2H | Repeatable | Four hours in public | Done |
| Transit driver lines | 2E | Repeatable | Bus or Uber in Bra Exposed | Done |
| Harassment openers and gropes (2 states × district and slums) | 7B | Repeatable | Daily harassment | Done |
| Harassment choice-scene layers (Bra Exposed) | 7D | Repeatable | Bra Exposed harassment choices | Done |
| `district_exhibition_panties` (3 tiers) | 8 | Repeatable | Panties Exposed harassment, district | Done |
| `slums_exhibition_panties` (3 tiers) | 9 | Repeatable | Panties Exposed harassment, slums | Done |
| Night assault state paragraphs | 10B | Repeatable | Night assault in either state | Done |
| Slums pass-out state branches | 10C | Repeatable | Slums pass-out in either state | Done |
| Layer lines on the 20 exhibition events | 11B | Repeatable | Event fires in either state | **To do** |
| Six new accidents | 11C | Repeatable | District transitions | **To do** |
| Bus exhibition, Bra Exposed branch | 11D | Repeatable | Bus ride in Bra Exposed | **To do** |
| Masturbation branches (alley and bench × 2 states) | 12A | Once per day each | Existing gates | **To do** |
| Five new verbs | 12B | Cooldown | District streets | **To do** |
| Riverside park exposure paragraphs | 12C | Repeatable | Park visit | **To do** |
| Exhibition Seduction Branch E (24 scenes) | 13 | Repeatable | Hunt seduction in Panties Exposed, 6001+, appeal 80+ | **To do** |
| Bra Exposed seduction layer | 14 | Repeatable | Regular street seduction in Bra Exposed | **To do** |
| Forced-state entry responses | 15B | Repeatable | First entry after combat destruction | **To do** |

### Consumer audit (from the original, with status)

| Site | Reads | Owner | Status |
|---|---|---|---|
| `checkDistrictHarassment()` | `isWearingVisibleUnderwear()`, level | 7A | Done |
| `getSheerState()` gate | level | 1C | Done |
| `getSheerVenueBlockMessage()` / building check in `showScene` | level | 2C | Done |
| `getHarassExhibScene()` | level | 7C | Done |
| `rollWatcherEncounter()` | level (naked only) | no change | Left as is |
| `travelToLocation()` sheer transit + bus/Uber | level, visible underwear | 2E | Done |
| Current Stats exposure label | visible level | 16B | **Shimmed; to do** |
| Passive thrill block | visible level, visible underwear | 3A | Done |
| `checkExhibitionAmbientLine()` | visible level | 5 | Done |
| `checkExhibitionEvidence()` | visible level | 2F | Done |
| `_seductExposureLine()` | visible level | 14A | **Shimmed; to do** |
| `hunt_seduction` routing | level | 13A | **Shimmed; to do** |
| `street_harassment_event` / `_grope` / `onEnter` | level | 7B | Done |
| Slums harassment event / grope | level | 7B | Done |
| `riverside_park` | level | 12C | **Shimmed; to do** (plus its choices and `park_porta_potty`) |
| `search_for_prey` | level (unused variable) | no change | Left as is |
| `exhibition_masturbate_alley` / `_park_bench` | level | 12A | **Shimmed; to do** |
| `slums_sheer_exhibition` clothes word | level | 1G shim only | **Shimmed; remove in 17** |
| `slums_passout_rape_main` | level | 10C | Done |
| `getExposureBlockMessage()` | level | 2C | Done |
| `handleUnderwearDistrictEntry()` | underwear type | 4A | Done |
| `checkAccidentalExposure()` | underwear type | 11A | **To do** |
| `checkNightRapeEncounter()` / `night_rape_ambush` | visible underwear | 10A, 10B | Done |
| `handleDamagedClothing()` | visible underwear | 15A | **To do** |
| `calculateTotalAppeal()` | visible underwear | 2I | Done |
| `seduce_lucius_man` / `_woman` | visible underwear | 14A | **To do** |
| `unequipClothing()` / equip warnings / leave gate | visible underwear, gate | 2A | Done |
| Appearance tab | visible underwear | 3E | Done |

---

## 7. How to Start the Next Session

1. Get the latest file (the user's upload if they send one; otherwise the branch head, `fa9f817` or later). Confirm both script blocks pass `node --check` and that `grep -c "BPE shim: Step"` shows 10 (9 call sites plus the comment inside `_bpeShim()`'s own doc block).
2. Ask for the companion docs Step 11 needs (Style Guide and Body Branching at minimum; Sex Standards if any accident or event layer turns sexual).
3. Ask whether the user wants Step 11 as one step or split (the plan at handoff was to offer 11A + 11C, then 11B + 11D).
4. Before writing, study the existing code the step touches (for Step 11: `checkAccidentalExposure()`, its braless/commando pool, `_buildSheerAccidentPool()`, the 20 event scenes and their registration, `exhibition_bus_trigger`).
5. Build, sweep-test in the browser, read a full sample, commit, push, send the file, and summarize. Then wait for confirmation.

---

## Appendix A: Glossary of Existing Code the Original Doc References

Every identifier the original overhaul doc names, beyond those already covered above, with what it is and where it matters now. Use these as models and hooks.

**Exposure and clothing helpers**
- `isBraVisible()`: bra on, no top or dress. `arePantiesVisible()`: panties on, no bottom or dress (a top-slot `category === 'dress'` counts as covering). `isWearingVisibleUnderwear()`: either. `getUnderwearExposureType()`: `'both'`, `'bra_only'`, `'panties_only'`, or null. In code, `'bra_only'` is Bra Exposed and `'panties_only'` is Panties Exposed.
- `isEffectivelyNaked()`, `isEffectivelyTopless()`, `isEffectivelyBottomless()`: slot checks used by the passive block and gates.
- `getOuterwearConcealment()`: `'full'`, `'partial'`, or `'none'` from outerwear tags (`conceal:full` / `conceal:partial`) or a fallback table.
- `getSceneClothingVars()` Mesh fields: `sheerState`, `topSheer`, `bottomSheer`, `braSheer`, `pantiesSheer`, `seeThroughChest`, `seeThroughHips`, `meshNoun`, fabric nouns/adjectives. Its fallbacks are `topName: 'top'` and `bottomName: 'skirt'`. The Step 1E fields are `exposureState` (`getExposureLevel()`), `braExposed` (`exposureState === 'bra_exposed'`), `pantiesExposed` (`exposureState === 'panties_exposed'`), `cv.braName` (`proseItemName(outfit.bra)` or `'bra'`), `cv.pantiesName` (`proseItemName(outfit.panties)` or `'panties'`), `braStyle`, and `pantiesCut` (e.g. `cv.pantiesCut === 'thong'`). Always read `cv.braName`, `cv.pantiesName`, `cv.pantiesExposed`, `cv.braExposed` in new prose.
- `proseItemName(item)`: lowercased item name with "Jaewon's " dropped. `getFabricNoun()`, `getFabricAdj()`, `resolveFabricTokens()`: fabric words and `{fabric}` tokens. `_sheerBodyCtx()`: the Mesh body-context object (`tits`, `ass`, `hips`, `frame`, `bra`, `panties`, …).
- `getSheerState()` / `isSheerExposureState()`: the Mesh state machine. Its original level gate was `_level !== 'clothed' && _level !== 'underwear_only'` (now widened, Step 1C). `sheer_framed` (underwear framed behind mesh) is never indecent.
- Step 1G's shim form: `lvl === 'bra_exposed' || lvl === 'panties_exposed'` treated as `'clothed'` (implemented as `_bpeShim()`).

**Law, venues, police (Mesh-era, the patterns Step 2 followed)**
- `SHEER_STATE_GATES` / `getSheerStateGate()`: corruption per sheer state. `handleSheerDistrictEntry()`: sheer gate + vignettes. `tickSheerLingeriePolice()` / `handleSheerPoliceArrive()`: the three-hour sheer lingerie clock the indecency clock copies.
- `exposureRestrictedBuildings`: interior scene id → `{ redirect, name }`; checked in `showScene`. Redirect ids include `outside_diner`, `outside_convenience_store`, `outside_library`, `outside_hospital`, `outside_blood_bank`, `outside_luxury_hotel`, `outside_casino`, `outside_noir_boutique`, `outside_vajaros_realty`, `outside_bargain_threads`, `outside_urban_edge`, `outside_laundromat`, `outside_dry_cleaners`, `outside_hardy_bar`, `outside_lucius_lounge`, `outside_dojang`, `outside_gymnasium`, `outside_stadium`. `getExposureBlockMessage(buildingName)`: the naked/topless/bottomless/underwear/panties refusal. `SHEER_BRALESS_BLOCKED_VENUES` / `SHEER_BARE_ALLOWED_VENUES` with `getSheerVenueBlockMessage()`: sheer venue rules.
- `getSheerTransitReaction()`: the sheer bus/Uber pattern the Bra Exposed driver lines copy.
- `checkExhibitionEvidence(sceneId)`: ledger entries (camera 30% Commercial/Downtown, witness 15% Residential/park, none in the slums). `getCruzSheerExposureLine()` reads them.
- `calculateTotalAppeal()`: its `_undressedForPenalty` flag suppresses imperfection penalties (now false for Bra Exposed).

**Passive layer**
- `advanceTime()` passive block: braless +1/hr, commando +2/hr, Underwear Only +3/hr, the two new accumulators, topless, bottomless, naked, sheer, and wet rates; `_anyExhibitionState` holds thrill against decay (the new verbs of Step 12 should not break it). `isPrivateExposureScene()`: no exhibition gains at home or at Jaewon's; **Step 12B verbs and Step 11 accidents should respect it.** The old gain pattern `gameState.arousal = Math.min(100, gameState.arousal + N)` is replaced by `applyArousal(N)` in anything this overhaul touches; don't add new direct writes to `gameState.arousal`.

**District entry and ambient (models)**
- `handleUnderwearDistrictEntry(districtId)`: the router. `getUnderwearNPCVignette(type)`: the original pools `bothPool`, `braPool`, `pantiesPool` (only `bothPool` is still used). `getToplessNPCVignette()`, `getBottomlessNPCVignette()`, `getToplessKelsieResponse()`, `getBottomlessKelsieResponse()`: the dedicated per-state functions that `getBraExposedKelsieResponse(corruption)` / `getPantiesExposedKelsieResponse(corruption)` were modelled on. **"Unhook it" (12B) fires the topless vignette/response through this router**, and **15B's forced-state entries follow the topless/bottomless forced-entry pattern.**
- `checkExhibitionAmbientLine(sceneId)`: the ambient checker; 15-minute cooldown.

**Style comments and named NPCs (Step 6 pieces the user chose not to build)**
- `SHEER_STYLE_COMMENTS` and the coherence style-comment block (the picker order: dirty/torn clothes, then the sheer slot, then coherence). The original 6A asked for `BRA_EXPOSED_STYLE_COMMENTS` (fashion register, like `sheer_framed`, six comments) and `PANTIES_EXPOSED_STYLE_COMMENTS` (scandal register, like `sheer_commando`, six comments) in the sheer slot. Not built.
- `LUCIUS_SHEER_DOOR_LINES` / `getLuciusSheerDoorLine()`, the Hardy Bar sheer line (`rollHardySheerLine()`), `getRubySheerLine()`: the sheer versions of the unbuilt Lucius, Hardy, and Ruby lines. The original 6B asked for: Ruby (Bra Exposed: points at the shirts-required sign, hands her an apron or a staff tee, three variants; Panties Exposed: pulls her inside the back door before a customer sees, three variants); Livie (Bra Exposed: taps the sign, dry and amused; Panties Exposed: stares, then pretends she didn't); Lucius doorman (Bra Exposed evening: waves her in with a line on the look; Panties Exposed: refusal line); Hardy bartender (Bra Exposed: free-drink line, once per day). Not built by user choice.
- `getJaewonSheerReaction()`: runs before the new Jaewon reaction.
- Image assets `bra-exposed.png` / `panties-exposed.png` (in `Images/body/`) are what the Appearance boxes show.

**Harassment and gang scenes (models)**
- `street_harassment_event` / `street_harassment_grope` / its `onEnter` and the slums pair use an `exhibState` ladder: `naked`, `bottomless`, `topless`, `underwear_only`, `panties_exposed`, `bra_exposed`, `braless_commando`, `braless`, `commando`, sheer states, clothed. The choice scenes are `street_harassment_aggressive`, `_firm`, `_passive`, `_flirt` (slums: `slums_harassment_aggressive`, `_firm`, `_passive`, `_moan_choice`).
- The gang-scene families: `district_exhibition_underwear|topless|bottomless|naked`, `district_wet_exhibition`, `district_sheer_exhibition`, and the six `slums_*` equivalents (e.g. `slums_exhibition_underwear`, `slums_sheer_exhibition`). The original template call was `completeSexAssaultMale({ time: 15, hygiene: -30, corruptionGain: 25, thrill: 15, ... })`; the Panties Exposed district scene uses exactly `completeSexAssaultMale({ time: 15, hygiene: -30, rapeType: 'district_exhibition_panties', corruptionGain: 25, thrill: 15, partner: 'district_group', skipPregnancy: true })`, and the slums one the slums values in Section 2.5.

**Mesh-era numbers referenced by the original (for balance comparisons)**
- Visible-underwear gate before this overhaul: 6001 for any visible underwear, 8001 for the full sheer set. District-entry suspicion: +2 underwear, +3 topless, +4 bottomless. Harassment grope gains: 25/25 for exhibition states, 15/20 otherwise.

---

## Audit Record

This handoff was checked against the original `Bra_Panties_Exposed_Overhaul.md` (815 lines; the re-sent copy is byte-identical to the one used all session):

- **Every section of the original is represented:** Scope and the four problems (Section 1); How to Read / conventions (Sections 0–1); Confirmed Decisions 1–4 (Section 1); Existing Systems (Sections 2 and 4, updated to the current file); Steps 1–10 (Section 3, as done, with deviations); Steps 11–17 (Section 5, reproduced in full, every table and sub-point); New Helper / Function Summary, New Scene Summary, Consumer Audit (Section 6, every row, with status); Implementation Notes (Sections 0–1 and 2.9: one step per output, Step 1 risk handled, Mesh owns see-through, garment names from cv, the camera rule, Innocent is still aroused, Bra Exposed stays worth wearing).
- **Identifier check:** every inline backticked identifier in the original (305) was checked for presence in this document by script. The first pass found 83 absent (mostly details of finished steps); Appendix A and the named sub-steps in Section 3 were added to cover them, and the re-run shows every one present. Every number in Steps 11–17 (gates, cooldowns, gains, thrill and arousal values) is present in Section 5, and every sub-step label (1A–17F) appears.
- **Intentional differences from the original:** Step 6's unbuilt pieces are marked "not built by user choice" rather than as to-do; the consumer audit gains the two `riverside_park` choice and `park_porta_potty` shim sites; the 20 exhibition events are listed by name (the original named only the first and last).
