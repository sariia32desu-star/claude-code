# Bra Exposed & Panties Exposed Overhaul: Session Handoff (Steps 13A–17)

**Project:** Vampire Girl (single-file HTML interactive fiction game)
**File:** `vampire_girl/Vampire_Girl.html` in repo `sariia32desu-star/claude-code`
**Latest file:** head of branch `claude/vampire-girl-overhaul-atypb0`, commit `7f3a285` or later. (Steps 1–10 were built on `claude/vampire-girl-overhaul-jjgcb3`; this session fast-forwarded from its head `8ea93c0` and built Steps 11–13B. The next session will be given its own branch name: bring this branch's head onto it first.)
**Status at handoff:** Steps 1–12 are done. Step 13B (all 23 Branch E scenes) is done. **Remaining: 13A (Branch E routing), 14, 15, 16, 17.**

This document is self-contained. It replaces the previous handoff (the one written after Step 10, preserved in git at `8ea93c0`). It carries every remaining step in full, everything learned in Steps 1–13B, and an audit against the previous handoff (see the Audit Record at the end).

---

## 0. Read This First

### Working rules (from the user, non-negotiable)

1. **One step per output. Wait for confirmation. Never bundle steps** unless the user says so. (This session the user asked for all five Step 12B verbs together, and for Step 11 as 11A + 11C, then 11B + 11D. Ask how to batch when a step has many parts.)
2. **Sex scenes are written one tier at a time**, lowest tier first, and one scene at a time. Deliver, wait for confirmation, continue. Exception set by the user this session: **Exhibition Seduction sex scenes are untiered**, copying the wiring of the existing branch scenes (see 2.9).
3. **Always work on the latest file.** If the user uploads a new `Vampire_Girl.html`, work on that. Otherwise, the head of the working branch. Never copy an earlier file over it. Never return to an older output mid-change.
4. **The user supplies companion docs when prose is needed.** Ask if a step needs prose and they haven't been sent:
   - `VampireGirl_Style_Guide_SS.md`: every writing rule (mandatory for all prose)
   - `VampireGirl_Body_Branching_Standards.md`: body/clothing branching standards
   - `VampireGirl_Sex_Standards.md`: mandatory for every intimate or sexual beat
   - `Arousal_System_Overhaul.md`: listed by the original plan for arousal work; never needed so far. Ask if a step leans on arousal tiers.
5. **For new scenes, study the closest existing scene in the same vein and copy its wiring.** The user said this for Step 8 and again for Branch E ("study that scene thoroughly for reference before implementing").
6. **Scope discipline.** In Step 6 the user said: "Only do Jack, Cruz, and Jaewon. No one else." The unbuilt Step 6 pieces stay unbuilt unless asked (Section 3, Step 6).
7. **Commit, push, send the file, summarize, then wait.** The user reads the summary; keep it plain and complete.

### User's personal style rules (apply everywhere in the file)

- No em dashes anywhere, except paired parenthetical asides or dialogue/sound cut-offs (`"No—"`, `"Ah—fuck—"`).
- Never write "Not a question."
- No AI tells. No "Not because X. Because Y." No "Not/Not" (or "No/No", "Nothing/Nothing", "Something/Something") paired patterns, including forms like "Not the hardest hitter. Not the tallest blocker."
- Never write "A beat." (also no "A pause.").
- No formal uncontracted language: "She's", "You're", "It's", "doesn't", never "She is", "You are".
- No negation-then-reveal stanzas ("You're not the star. / You're the girl coaches trust / to be in the right place."). No negation-before-reveal in any form.
- Also banned by the Style Guide: "the kind of", "the kind that", "genuinely", "The way" as an opener, "in a way", staccato one- and two-word sentence chains, walls of text (paragraphs over ~300 characters), stanza formatting. In practice this session avoided every "the way" phrase, including idioms like "on the way", "in the way", "the rest of the way"; "all the way" was kept.
- "cum / cums / cumming" for orgasm; "came" stays "came". "come" stays for movement and idiom ("comes out", "comes around", "comes bare").
- Sex Standards: never "core"; never "pulse/pulses/pulsing" or "wave/waves" in intimate prose; replacement words (clench, spasm, squeeze, throb, flutter, shudder, tremor, jerk, twitch) at most twice each per scene; pussy / clit / cunt; phonetic sounds spelled out; position tracking; every act gets entry, build, approach; every orgasm gets approach, hit, and linger; no NPC thoughts outside romance scenes; active present-tense final paragraph.
- All prose is 2nd person present tense. Stick strictly to the game's writing style. Any deviation is an error.

### Technical rules

- Every apostrophe inside a single-quoted JS string is escaped: `\'`. No `${}` inside single-quoted strings. Ternaries break out with `' + (…) + '`.
- `node --check` on both `<script>` blocks after every edit. Extract them with a regex over `<script>(.*?)</script>`; the file has exactly two.
- Line numbers drift. Always re-grep by function or scene name.
- Commit messages: descriptive, and end with the attribution lines the environment specifies. Never put model identifiers in commits or files. Push with `git push -u origin <branch>` (retry with backoff on network errors). Don't open a PR unless asked.
- **Chain edit → check → commit with `&&` or check the edit's exit status first.** This session a Python edit failed its assertion and the commit still ran in the same shell command, pushing a stale file; it took a follow-up commit to fix.
- Some of this session's inserted prose contains literal `—` characters rather than `—` escapes. When matching strings for an edit, grep the exact bytes first.

---

## 1. The Overhaul in Brief

**Scope.** The game has four named exhibition states (Underwear Only, Topless, Bottomless, Naked) and a full sheer (mesh) layer on top of them. Each has a mechanical fingerprint, NPC reactions, harassment routing, gang scenes in the district and the slums, an Exhibition Seduction branch, ambient lines, evidence rules, venue rules, and a Kelsie who reacts in three corruption voices. Two real outfits fell through the cracks: **a bra with a bottom and no top** (Bra Exposed) and **a top with panties and no bottom** (Panties Exposed). `getExposureLevel()` used to call both `clothed`. That one word was the whole problem:

1. **The law treated them identically, and wrongly.** A bra with jeans and panties with a tee had the same 6001 leave gate, the same bus refusal, the same guaranteed night assault. A bra with a skirt is a bold look. Panties with a tee is indecent exposure.
2. **The street didn't see them.** No exhibition ambient lines, style comments, evidence, or seduction-exposure lines.
3. **The scenes that did fire read the wrong outfit.** `getSceneClothingVars()` returned `topName: 'top'` when there was no top, so prose said "your top" on a girl in her bra. `getUnderwearVisibilityDesc()` said "wearing only your panties" on a girl in a tee.
4. **There was no content.** Neither state had a gang scene, seduction branch, deliberate acts, or accidental events.

The overhaul promotes both outfits to real exposure levels, splits them by legality, and gives each the depth the other states have:

- **Panties Exposed is a crime.** Full exhibition treatment on equal footing with Underwear Only and Bottomless: harassment routing into its own district and slums scenes, its own Exhibition Seduction branch (Branch E), evidence, venue blocks, deliberate acts, and a police response.
- **Bra Exposed is legal.** It's a look. The law leaves it alone, most doors stay open, the bus takes her fare. Eyes don't leave her chest, her nipples know it, and her pussy answers every look. A mesh bra, or a cotton bra soaked see-through, tips it over into a crime.

**Canon anchor (from the Tips & Guide):** exhibitionism is Kelsie's defining kink. Being seen exposed turns her on deeply at every corruption tier. At Innocent she's mortified and aroused anyway. Every tier of every piece of prose names what her body's doing.

**The target feel:** a player who steps out in her bra and a skirt should feel the street tilt toward her; a player who steps out with her panties showing under a tee should feel the city close in: the stares, the phones, the men who follow her, the cop who slows down, and her own wet cunt making it worse.

### Confirmed decisions (approved before Step 1; all built)

1. **Bus and Uber for Bra Exposed: approved.** Both rides take her, with a driver reaction line. A mesh bra or a soaked-through bra is still refused. Panties Exposed stays refused.
2. **Corruption gate for Bra Exposed: approved.** 2001 to walk out in it deliberately. A sports bra has no gate at all. Panties Exposed stays at 6001.
3. **Police clock for Panties Exposed and Underwear Only: approved.** Sheer lingerie keeps its three-hour clock. Panties Exposed and Underwear Only share one four-hour indecency clock.
4. **Thrill rates: approved.** Panties Exposed +3/hr, Bra Exposed +2/hr, sports bra +1/hr (soaked through +4, replacing the wet rate).

### Conventions

- **Corruption voices:** Innocent (0–2000), Curious/Experienced (2001–6000), Corrupted (6001–8000), Deeply Corrupted (8001+). Where a step lists three voices, Corrupted and Deeply Corrupted share the high voice unless the step says otherwise. Gang scenes use 4001 / 8001 splits. The 20 exhibition events use five tiers (<2001, <4001, <6001, <8001, 8001+).
- **Mesh first.** The Mesh Overhaul owns every see-through version: `sheer_lingerie_top` (a mesh bra with nothing over it) and `sheer_lingerie_bottom` (mesh panties with nothing over them). Never write a line for a mesh bra or mesh panties inside a Bra Exposed or Panties Exposed pool; route to the sheer content. "Bra Exposed" means an opaque bra; "Panties Exposed" means opaque panties. The one new see-through case, the **soaked opaque bra**, is owned by this overhaul (Step 2D) and borrows the sheer lingerie law. (Exception by design: Step 13A re-points `sheer_lingerie_bottom` to Branch E, where the sheer overlays speak for the mesh.)
- **The camera rule is the soul of the overhaul.** Bra Exposed is shot from the front: her chest, the cups, the straps. Panties Exposed is shot from behind: her ass, the leg band, the thong. If a Panties Exposed scene opens on her face, rewrite the opening.
- **Garment names come from `cv`, always.** In Bra Exposed there's no top; in Panties Exposed there's no bottom. The most likely bug in any new scene is a `cv.topName` that reads "top" on a girl in a bra, or a `cv.bottomName` that reads "skirt" on a girl in a tee.
- **Innocent Kelsie is still aroused.** Mortified, red-faced, arms folded, and wet.
- **Bra Exposed has to stay worth wearing.** The player should pick it on a warm evening on purpose.

---

## 2. What Exists Now

### 2.1 Exposure core (Step 1)

- **`getExposureLevel()`** returns `'naked'`, `'topless'`, `'bottomless'`, `'underwear_only'`, `'panties_exposed'`, `'bra_exposed'`, or `'clothed'` (in that priority). A top-slot item with `category === 'dress'` covers the hips everywhere (also in `isEffectivelyBottomless()` and `cv.commando`).
- **`getVisibleExposureLevel()`** applies outerwear: a full coat hides both new states (→ `clothed`); a partial jacket hides Bra Exposed (→ `clothed`) but not Panties Exposed.
- **`getSheerState()`** computes sheer states on `clothed`, `underwear_only`, `bra_exposed`, and `panties_exposed` (so `sheer_lingerie_top` / `sheer_lingerie_bottom` survive).
- **Legality helpers:** `isCriminalSheerState()` (true for the six indecent mesh states), `isWetBraThrough()` (opaque cotton/satin/lace bra, not sporty, not mesh, visible Bra Exposed, `clothesWetness >= 60`; fabrics in `WET_THROUGH_BRA_FABRICS`), `isLegallyDressed()` (clothed and not criminal-sheer, or Bra Exposed and neither criminal-sheer nor soaked), `isVisiblyExposed()`.
- **`getSceneClothingVars()` (cv) additions:** `exposureState`, `braExposed`, `pantiesExposed`, `braName`, `pantiesName`, `braStyle` (`'sporty'|'pushup'|'bralette'|'corset'|'lace'|'sheer'|'plain'`, via `getBraStyle(item)`), `pantiesCut` (`'thong'|'cheeky'|'boyshort'|'brief'`, via `getPantiesCut(item)`). `cv.braExposed` is true for mesh too; check `cv.braSheer` / `cv.pantiesSheer` to exclude mesh. `cv.topName` still falls back to `'top'` and `cv.bottomName` to `'skirt'`.
- **`getUnderwearVisibilityDesc()`** reads "in your {bra} and {bottom}" (Bra Exposed with panties) and "with your panties on show under your {top}" (Panties Exposed).

### 2.2 Gates and the law (Step 2)

- **`getVisibleUnderwearGate()`**: Bra Exposed opaque 2001, sports bra 0, mesh bra 6001, Panties Exposed 6001, both visible 6001 (8001 if both mesh).
- **`getExposureLeaveGate()`**: the gate raised to 6001 when she's also topless or bottomless. `checkVisibleUnderwearCorruption()` uses it; so do the refusal messages. `clothesDestroyedInCombat` still waives every gate.
- `unequipClothing()`: taking a top off over a bra and a bottom is blocked below 2001 (sports bra 0, mesh 6001).
- District-entry refusals rewritten for both states; the equip warning has per-state titles.
- **Venues:** `BRA_EXPOSED_VENUE_POLICY` (keyed by redirect id; `'allowed' | 'blocked' | 'evening'`), `getBraExposedVenueBlockMessage(restriction)`, and `getPartialExposureVenueBlockMessage(restriction)`, which runs in `showScene` before the sheer check. Panties Exposed is blocked everywhere restricted via `getExposureBlockMessage()`; Jack answers the Bargain Threads door himself. A soaked bra is blocked everywhere restricted. Lucius' allows Bra Exposed 6 PM to 6 AM (sports bra blocked there).
- **Wet bra:** `checkWetBraThroughNotice()` fires the one-line notification once per day from the rain branch of `updateClothesWetness()` (flag `_wetBraNoticeDay`).
- **Transit:** `travelToLocation()` lets Bra Exposed ride; `getBraExposedTransitLine(method)` gives the driver line (silent for a sports bra). Soaked bra and Panties Exposed are refused (Uber −0.2 rating).
- **Evidence:** `checkExhibitionEvidence()` logs `exposureState: 'panties_exposed'` or `'bra_wet_through'` (minor; moderate on a night camera). A dry opaque bra logs nothing.
- **Suspicion on district entry:** Bra Exposed 0, soaked +2, Panties Exposed +2.
- **Indecency clock:** `tickIndecencyPolice()` runs in every district street `onEnter` after `tickSheerLingeriePolice()`; four hours in visible Panties Exposed or Underwear Only (the full `sheer_lingerie` set is excluded; it has its own clock), then `handleIndecencyPoliceArrive(state)` and `handleNakedEscape()`. **`getIndecencyClockRemaining()` returns minutes left or `null` (Step 16B needs this).** Stored in `gameState.indecencyClockStartTime`.
- **Appeal:** Bra Exposed no longer suppresses imperfection penalties; Panties Exposed still does.

### 2.3 Passive layer (Step 3)

- Thrill: `braExposedThrill` (+2, sporty +1, soaked +4 which also switches off the wet-clothes thrill) and `pantiesExposedThrill` (+3) accumulators; public and visible only; mesh lingerie replaces them. Both hold thrill against decay. The old `underwearPartialThrill` accumulator is retired.
- Thong friction: `thongRideArousal`, +1 arousal per 30 min in public Panties Exposed, through `applyArousal()`.
- District-entry arousal (topless, bottomless, underwear) goes through `applyArousal()`. Mental health per state: Bra Exposed −1/0/+1, Panties Exposed −3/0/+2 plus `modifyKarma(-3)` at Corrupted+.
- Lifetime minutes: `braExposedMinutesTotal`, `pantiesExposedMinutesTotal`; `underwearMinutesTotal` means Underwear Only. Shown in the Current Stats exhibition card.
- Appearance tab: Bra Exposed box (rose border `#d9738f`, sparkle icon, `getBraExposedBoxSub()`); soaked shows `BRA EXPOSED (SOAKED)`. Panties Exposed box (`getPantiesExposedBoxSub()`). Images `bra-exposed.png` / `panties-exposed.png` in `Images/body/`.

### 2.4 Street content (Steps 4–7)

- **District entry:** `handleUnderwearDistrictEntry()` routes `bra_only` / `panties_only` to `getBraExposedNPCVignette()` / `getPantiesExposedNPCVignette()` (8 each, de-duped through `_bpePickVignette()` with `_braExposedReactionIdx` / `_pantiesExposedReactionIdx`) and `getBraExposedKelsieResponse(c)` / `getPantiesExposedKelsieResponse(c)`. First walks: `getBraExposedFirstWalk(c)` / `getPantiesExposedFirstWalk(c)` (flags `seenFirstBraExposedWalk` / `seenFirstPantiesExposedWalk`), which skip the 20-minute cooldown. The cooldown is `gameState.flags.lastExhibStateEventMinute`.
- **`getPantiesExposedAssView()`**: the shared body-branched view from behind (buttSize × pantiesCut). It can end in an appositive, so **always put it at the end of a sentence.**
- **Ambient lines:** `EXHIBITION_AMBIENT_LINES.bra_exposed` / `.panties_exposed` with `variants`; tokens resolve in `checkExhibitionAmbientLine()`.
- **Jack, Cruz, Jaewon (Step 6B):** `getJackPantiesDoorLine()`, `getJackBraExposedLine()`, `jackBraDiscountAvailable()`, scene `jack_bra_exposed_discount` (flag `_jackBraDiscountDay`, item `JACK_DISCOUNT_TOP`); `getCruzSheerExposureLine()` has a Panties Exposed read; `getJaewonPartialExposureReaction()` (flag `_jaewonBpeFriendDay`).
- **Harassment (Step 7):** chance rules in `checkDistrictHarassment()`; `getBpeHarassOpener(area)` / `getBpeHarassGrope(area)`; `getBpeGropeGains()`; `getBraExposedHarassLayer(kind)` wrapped into the eight choice scenes through a `_bpeBaseText` pattern.

### 2.5 Gang scenes, night assault, pass-out (Steps 8–10)

- **`district_exhibition_panties`** and **`slums_exhibition_panties`**, all three tiers, gallery entries "District: Panties Exposed Exhibition" / "Slums: Panties Exposed Exhibition". `completeSexAssaultMale(...)` in `onEnter`; own pregnancy checks (`_districtPantiesExhibPregnancy` / `_slumsPantiesExhibPregnancy`).
- **Torn panties at 8001+:** `onEnter` runs **before** `text()` in this engine, so `onEnter` only sets `_districtPantiesTornPending` / `_slumsPantiesTornPending`; the exit choice calls `bpeTearPantiesToBackpack(flagKey)`. Reuse this pattern whenever a scene removes clothing its own text still needs to name.
- **Tier routing:** `BPE_EXHIB_TIER_CAP` (both `Infinity`); `getHarassExhibScene(area)`.
- **Night assault:** `checkNightRapeEncounter()`; `getNightAssaultStateParagraph(who)` / `insertNightAssaultStateParagraph(text, who)`.
- **Pass-out:** both states in `slums_passout_rape_main` and `slums_passout_rape_aftermath`.

### 2.6 Built this session: Step 11 (accidents and events)

- **11A.** `checkAccidentalExposure()` now returns early only for Underwear Only (`_uwType === 'both'`). In Bra Exposed the hips can still have commando accidents under a skirt or dress; in Panties Exposed the chest can still have braless accidents (the existing `cv.commando` / `cv.braless` checks enforce the zone).
- **11C.** `_checkBpeAccident(districtId)` rolls first: visible, opaque pieces only, 15% per transition, 60-minute cooldown per state (`_lastBraAccidentMinute` / `_lastPantiesAccidentMinute`). Pool in `_bpeAccidentPool(state)`, fired by `_bpeFireAccident(event)` (thrill via `applyExhibitionThrillGain()`, arousal via `applyArousal()` with the actual gain shown; the old accidents still write arousal directly). Bra Exposed: strap slip (not sporty; corset gapes instead), the clasp gives (not sporty; at 8001+ she lets go, then hooks it again, so she stays in Bra Exposed), sports bra ride-up (sporty only). Panties Exposed: ride-up, the wet spot (arousal ≥ 50), cold bench.
- **11B.** `BPE_EVENT_LAYERS` holds one line per tier (five tiers) for the skirt/dress events in Bra Exposed (wind, staircase, bench, puddle, crowd, escalator, barstool, photo) and the top events in Panties Exposed (button, wet, reach, lean, fitslip, bra_wind, mirror, neckline, spill, turnstile). `getBpeEventLayer(ev)` resolves tokens `{bra} {bottom} {top} {panties} {panIt} {tits} {Tits} {ass} {Ass}`. `installBpeExhibitionLayers()` runs right after `installSheerSeductionOverlays()` and inserts the line **just before the closing paragraph** (one earlier when the closer starts with "But" or "It"); inserting after the opening broke a dozen scenes. **The fitting-room event no longer fires in Bra Exposed** (its prose strips a top she isn't wearing; condition `getExposureLevel() !== 'bra_exposed'` in its registration). The wind event's bare 7000 tier said "under that dress" for any bottom; it now names `cv.bottomName`.
- **11D.** `BPE_EVENT_LAYERS.bra_exposed.bus`: a two-paragraph branch per tier (the crowded aisle, a hand that brushes the cup, the man too close behind her). The bus trigger still fires only for a skirt or dress (from `travelToLocation()`).

### 2.7 Built this session: Step 12 (deliberate acts, park)

- **12A, masturbation.** Four full branches, both tiers each, written to the Sex Standards as standalone texts (the shared middle of the old scenes contains "wave", "pulses" and "come" for orgasm): `getBpeAlleyMastBraText()` / `_bpeAlleyMastBraDC(x)`, `getBpeAlleyMastPanText()` / `_bpeAlleyMastPanDC(x)`, `getBpeBenchMastBraText()` / `_bpeBenchMastBraDC(x)`, `getBpeBenchMastPanText()` / `_bpeBenchMastPanDC(x)`. Shared contexts `_bpeMastBraCtx()` and `_bpeMastPanCtx()` (the latter is reused all over Branch E). Routing sits at the top of each scene's `text`, visible and opaque only; `BPE_MAST_TIER_CAP` (`alley_bra`, `alley_pan`, `bench_bra`, `bench_pan`, all `Infinity` now). Gallery flag values `'bra_exposed'` / `'panties_exposed'` on `_alleyMastGalleryExposure` / `_parkBenchMastGalleryExposure`, dressed through each scene's `galleryOnEnter`. Gallery entries: "Alley: Bra Exposed", "Alley: Panties Exposed", "Park Bench: Bra Exposed", "Park Bench: Panties Exposed" (Corrupted and Deeply Corrupted). The two Step 12A shims are gone; what still reaches the old text in either state is a mesh piece (routed on to the sheer text) or underwear under outerwear, and the scenes say so explicitly. **Witnesses used** (avoid repeating them): alley Bra Exposed C suit man / DC delivery rider ("Stay there"); alley Panties Exposed C man in a work jacket / DC man with a terrier ("Don't go"); bench Bra Exposed C and DC the jogger (DC keeps him with a chin tip); bench Panties Exposed C and DC the jogger (DC: "Closer." then "That's close enough.").
- **12B, verbs.** `getExposedUnderwearChoices(districtId)` on the commercial, residential and downtown streets, right after the Mesh Step 14 line. Visible, opaque pieces only. Cooldowns in `gameState.flags._bpeActionMinutes` via `_bpeVerbReady(key, min)`; gains via `_bpeVerbGains(key, thrill, arousal, corruption, susp)`; helpers `_bpeNow()`, `_bpeBt5()`. Each `do*` also guards its own cooldown (stale buttons). Verbs: `doBpeRollShoulders` (2001, thrill 20, 30 min, 6/4/1/0, three voices), `doBpeUnhook(districtId)` (6001, thrill 50, 60 min; label "Take it off." for a sports bra; the bra goes in the backpack, then `lastExhibStateEventMinute = -99999` and `showScene(currentScene)`, so the street's own entry check fires the topless vignette and the choice list rebuilds; **no stat gains of its own**, the topless vignette applies them), `doBpeTugCup` (8001, thrill 70, 45 min, 12/10/3/1; "Tug it up." for a sports bra), `doBpeBendOver` (6001, thrill 40, 30 min, 10/8/2/1, two voices), `doBpePullAside` (8001, thrill 70, 45 min, 15/14/4/2; "Pull it aside." for a thong). The engine can't redraw choices without re-entering a scene (`showMessage` doesn't refresh them).
- **12C, Riverside Park.** `getParkExposureLevel()` (partial states read through outerwear, else the raw level), `isParkIndecent()` (dry opaque bra = dressed; soaked or mesh bra, Panties Exposed and Underwear Only = indecent), `getParkUnderwearAwareness(exposure)` (Underwear Only, Bra Exposed, Panties Exposed; three voices; mesh skips to the sheer line), `getParkPortaRushText(exposure)` (the rush scene `park_porta_potty_naked` was written for naked; the three underwear states now get their own). Legal Bra Exposed keeps "Sit on a bench and relax" and the plain porta potty; the homeless sleep option is still clothed-only (see 17E). The three Step 12C shims are gone. The bench masturbation choice already reads the new states through its 12A branches.

### 2.8 Built this session: Step 13B (Branch E scenes, not yet routed)

- **Male chain, 12 scenes:** `exhibition_seduce_pan_{approach, proposition, success_pivot, soft_pivot, exit_success, exit_soft, failure_dismissive, failure_apologetic, failure_disturbed, failure_impressed_but_no, sex, sex_2}_m`.
- **Female chain, 11 scenes:** the same minus `sex_2`. The Topless female track has a single sex scene, and the user asked for its exact wiring. (The original plan said 24 scenes, twelve per gender; Branch E has 23.) Ids follow `exhibition_seduce_pan_<scene>_<m|f>`, the Branch A–D pattern `exhibition_seduce_<branch>_<scene>_<m|f>`.
- **Content:** approach from behind (he or she has been walking behind her; she catches them in a reflection; openers "Enjoying the view back there?" / "You've been behind me for a block." and "Like what you see back there?" / "You've been following my ass for a block."). Hard proposition: "There isn't much left to take off." Pivots start with a hand on her ass. Failures: the dismissive one says no to her crotch; the disturbed one asks if somebody took her clothes and offers a jacket (male) or a scarf (female) for her hips; the impressed one photographs her ass as they leave. Sex scenes keep the top on until position two; panties aside, then off (cut branches); `cv.braless` decides what the hands find under the top. The female exit's lipstick smear is paid off in her sex scene.
- **Wiring:** Branch A's roll (`calculateExhibitionSeductionSuccess()`, result in `gameState.flags._exhibSeduceResult`, failure auto-redirect by `setTimeout` to `_failure_<type>_<g>`) and Branch A's stat gains, with arousal through `applyArousal()` and both exits incrementing `exhibitionStats.exhibitionSeductionCount` (no other branch increments it). Sex wiring copied from Topless: `sex_m` → `completeSexConsensualMale({ thrill: 15, time: 15, skipPregnancy: true })`, "Let him." → `sex_2_m` → `advanceTime(20)`, arousal 0, thirst 0; `sex_f` → `completeSexConsensualFemale({ thrill: 15, time: 20 })`, thirst 0. **A soft success now leaves through `exit_soft_*`**: Branches A–D define `exit_soft` scenes that nothing reaches.
- **Gallery:** "Panties Exposed (Male)" (chain "Let him." → `sex_2_m`) and "Panties Exposed (Female)" in the exhibitionism category, dressed by `bpeDressPantiesExposedForGallery()` (acts only in gallery mode; `galleryOnEnter` runs on every visit in this engine).

### 2.9 All `gameState` fields this overhaul uses

`gameState.indecencyClockStartTime`; `exhibitionStats.braExposedMinutesTotal`, `exhibitionStats.pantiesExposedMinutesTotal`, `exhibitionStats.exhibitionSeductionCount` (pre-existing, now incremented by Branch E); flags `_braExposedReactionIdx`, `_pantiesExposedReactionIdx`, `_lastBraAccidentMinute`, `_lastPantiesAccidentMinute`, `_bpeActionMinutes` (object keyed `roll`, `unhook`, `tug`, `bend`, `aside`), `seenFirstBraExposedWalk`, `seenFirstPantiesExposedWalk`, `_wetBraNoticeDay`, `_districtPantiesExhibPregnancy`, `_districtPantiesTornPending`, `_slumsPantiesExhibPregnancy`, `_slumsPantiesTornPending` (all with new-game defaults and save-migration lines). Read with `|| 0` guards and no migration default: `_jackBraDiscountDay`, `_jaewonBpeFriendDay`. No new fields were added in Steps 11–13B.

### 2.10 Remaining BPE shims (Step 17 must reach zero)

`grep -c "BPE shim: Step"` shows **5**: four call sites plus the comment in `_bpeShim()`'s own doc block.

| Site | Tag | Removed by |
|---|---|---|
| Current Stats exposure label (inside `_buildAllStatsIntoTemp()`, `_currExposure`) | Step 16B | 16B |
| `_seductExposureLine(g)` (`ex`) | Step 14A | 14A |
| `hunt_seduction` choices (`_hsExposure`) | Step 13A | 13A |
| `slums_sheer_exhibition` `clothesWord` | Step 17 | 17 |

The `_bpeShim()` helper itself (next to `getExposureLevel()`) is removed in Step 17 once no call remains.

---

## 3. Steps 1–13B: Done (summary, with deviations)

- **Step 1: Foundation.** All of 1A–1H as specified. Additions: the dress-category rule also applies in `isEffectivelyBottomless()` and `cv.commando`; `getBraStyle()` / `getPantiesCut()`; the shim table.
- **Step 2: The law.** All of 2A–2I (2A leave gates, 2B refusal text, 2C venues, 2D wet bra, 2E transit, 2F evidence, 2G suspicion on district entry, 2H indecency clock, 2I appeal). Additions: `getExposureLeaveGate()`; the soaked bra doesn't raise the leave gate; the indecency clock only checks on district entry, so time at home counts toward it; a single mesh piece in either state is covered by the indecency clock.
- **Step 3: Passive layer.** All of 3A–3E (3A thrill rates, 3B arousal and thong tick, 3C mental health and karma on district entry, 3D lifetime stats, 3E Appearance copy). Lifetime minutes count raw level (time at home counts).
- **Step 4: District entry.** All of 4A–4D.
- **Step 5: Ambient lines.** All of Step 5, plus `{tits}` / `{ass}` tokens.
- **Step 6: Style comments & named NPCs.** **Only Jack, Cruz, and Jaewon were built, on the user's instruction.** Not built (don't build unless asked): 6A `BRA_EXPOSED_STYLE_COMMENTS` / `PANTIES_EXPOSED_STYLE_COMMENTS`; Ruby (diner door, both states); Livie (convenience store door, both states); Lucius doorman line (Bra Exposed evening) and refusal line (Panties Exposed); Hardy Bar bartender free-drink line (Bra Exposed). The venue *policy* for those doors exists (Step 2C). Jack's discount became a real choice (`jack_bra_exposed_discount`).
- **Step 7: Harassment.** All of 7A–7D. Fixed an undeclared `cv` in both passive choice scenes; the slums flirt equivalent is `slums_harassment_moan_choice`.
- **Step 8: `district_exhibition_panties`.** All three tiers.
- **Step 9: `slums_exhibition_panties`.** All three tiers.
- **Step 10: Night assault & pass-out.** All of 10A (chance rules), 10B (ambush wording and scene state paragraphs), 10C (pass-out branches), plus correct garment words in the five night scenes.
- **Step 11: Accidents and events.** All of 11A–11D (Section 2.6). Deviations: the fitting-room event is kept out of Bra Exposed; layer lines sit before the closing paragraph; a soft "comes bare" idiom is fine in prose.
- **Step 12: Deliberate acts, park.** All of 12A–12C (Section 2.7). Deviations: "Unhook it" has no stat gains of its own; the park rush scene got state-aware text; the homeless sleep option stays clothed-only.
- **Step 13B: Branch E scenes.** All scenes written (Section 2.8). Deviation: the female chain has 11 scenes, not 12 (Topless female wiring). Not routed yet.

---

## 4. Existing Systems the Remaining Steps Touch (verified in the current file)

Line numbers drift; always re-grep the name.

**Seduction (Steps 13A and 14)**
- `hunt_seduction` choices, around line 71311: at 6001+ and appeal 80+ (`calculateTotalAppeal()`), routes `underwear_only` to Branch A (`exhibition_seduce_bra_*`), `topless` to B (`_top_`), `bottomless` to C (`_btm_`), `naked` to D (`_nkd_`); otherwise the regular choices (`seduce_street_man`, `seduce_street_woman`, `hunting_options`). Before routing it runs the Mesh Step 15A check: only when `_hsExposure` is `'clothed'` or `'underwear_only'`, `getSheerSeductionBranch()` may pick a branch, sets `gameState.flags._seductionSheer`, and maps it through `{ bra: 'underwear_only', top: 'topless', btm: 'bottomless', nkd: 'naked' }[_hsSheer.branch]`.
- `SHEER_SEDUCTION_BRANCH` (around line 25126): `sheer_braless` and `sheer_lingerie_top` → `top`, `sheer_commando` and `sheer_lingerie_bottom` → `btm`, `sheer_bare` → `nkd`, `sheer_lingerie` → `bra`. `getSheerSeductionBranch()` reads it.
- `installSheerSeductionOverlays()` (called after the story table, right before `installBpeExhibitionLayers()`): wraps every scene matching `/^exhibition_seduce_(bra|top|btm|nkd)_(.+)_([mf])$/`. On text: applies `SHEER_SEDUCTION_FIXES` (regex rewrites keyed by scene id, e.g. `/_btm_proposition_f$/`), inserts `getSheerSeductionOverlay('approach', g)` after the first paragraph matching `/gaze|eyes|looking|look/`, the proposition overlay after the first paragraph, and at `sex` the undress overlay (opening the scene for the bra branch; after the first paragraph for the others, via `_insertSheerParagraph(t, und, /./)`). On exits and failures (`/^(exit_success|exit_soft|failure_.+)$/`) it clears `_seductionSheer` and gives a see-through bonus only for `sheer_lingerie` on the bra branch.
- `getSheerSeductionOverlay(stage, gender)` and `_activeSeductionSheer()` (in gallery mode it only returns a state when the gallery entry's `flagOverrides._seductionSheer` matches; no gallery entry uses that today).
- `calculateSeductionSuccess()` (around line 35074); `_seductExposureLine(g)` / `_seductWithExposure()` (around line 58840); `seduce_lucius_man` / `seduce_lucius_woman` (an `isWearingVisibleUnderwear()` line); the regular street seduction chains `seduce_street_man`, `seduce_street_man_direct`, `seduce_street_man_flirt`, `seduce_street_woman`, `seduce_street_woman_direct`, `seduce_street_woman_flirt` and whatever they chain into (audit with a grep for `topName`).

**Combat (Step 15)**
- `handleDamagedClothing(damagedItems)` (around line 44236): combat destruction messages; sets `clothesDestroyedInCombat`.
- The topless and bottomless forced-state district entries (find them first; copy their wiring).

**UI (Step 16)**
- Current Stats exhibition card (built in `_buildAllStatsIntoTemp()`, around line 37388), About Stats, Tips & Guide exhibitionism entry, the Scene Gallery registry (`SCENE_GALLERY_REGISTRY`, entries `{ sceneId, label, tiers: [{l, v}], flagOverrides?, galleryChain? }`; scenes can define `galleryOnEnter`), the cheat panel.

**Other**
- `slums_sheer_exhibition` (around line 182188): its `clothesWord` shim (Step 17).

---

## 5. Remaining Steps, in Full

### Step 13A: Branch E routing (next)

**Original spec.** In `hunt_seduction`, at 6001+ and appeal 80+, `panties_exposed` routes to `exhibition_seduce_pan_approach_m` / `_f`. Re-point `SHEER_SEDUCTION_BRANCH.sheer_lingerie_bottom` from `btm` to `pan`: mesh panties under a top are this state, and the existing sheer overlays run on the new branch. Remove the Step 13A shim. The sheer-branch mapping line inside `hunt_seduction` needs a `pan: 'panties_exposed'` entry.

**Implementation plan (worked out from the current code):**
1. **Level.** Replace `var _hsExposure = _bpeShim(getExposureLevel()); // BPE shim: Step 13A` with a level that reads the partial states through outerwear (raw `panties_exposed` / `bra_exposed` → `getVisibleExposureLevel()`, so a coat over them reads `clothed`). `getParkExposureLevel()` is exactly this and can be reused or copied.
2. **Sheer check.** Widen `(_hsExposure === 'clothed' || _hsExposure === 'underwear_only')` to include `'panties_exposed'` and `'bra_exposed'`: mesh panties under a top are raw `panties_exposed` (`sheer_lingerie_bottom`), and a mesh bra over a bottom is raw `bra_exposed` (`sheer_lingerie_top`, which stays on Branch B).
3. **Map.** Add `pan: 'panties_exposed'` to the `_hsSheer.branch` map, and set `SHEER_SEDUCTION_BRANCH.sheer_lingerie_bottom = 'pan'`.
4. **Route.** Add a `panties_exposed` case beside the other four: "Approach him" → `exhibition_seduce_pan_approach_m`, "Approach her" → `exhibition_seduce_pan_approach_f`. An opaque, dry Bra Exposed falls through to the regular seduction (Step 14 layers it). Decide with the user whether a soaked-through bra (criminal) should also fall through to the regular flow (the likely answer) or borrow a branch.
5. **Overlays.** Add `pan` to the `installSheerSeductionOverlays()` regex: `/^exhibition_seduce_(bra|top|btm|nkd|pan)_(.+)_([mf])$/`.
6. **Undress overlay conflict (must fix).** For `sheer_lingerie_bottom`, `getSheerSeductionOverlay('undress')` returns the hips peel: "He hooks your panties and drags them down your thighs. He already knew exactly what was under them." For non-bra branches it's inserted after the sex scene's first paragraph. Branch E's sex scenes run their own sequence (rub through the crotch, pull it aside, then take the panties off), so the peel would contradict them. Either skip the undress overlay on `pan`, or give `pan` its own look-first line inserted at the "aside" paragraph. Check `sex_2_m` and `sex_f` too (only the `sex` stage gets the undress overlay; `sex_f` is the female's only sex scene).
7. **Mesh read-through.** Walk every Branch E scene with mesh panties on and `_seductionSheer = 'sheer_lingerie_bottom'`. Lines like "the crotch dark and damp" or `getPantiesExposedAssView()` phrasing read as opaque; add `SHEER_SEDUCTION_FIXES` entries keyed `/_pan_<scene>_<g>$/` where a line contradicts a see-through crotch, and confirm the approach/proposition overlays land sensibly (the approach overlay looks for `/gaze|eyes|looking|look/`).
8. **Exit bonus.** The see-through exit bonus is bra-branch-only (`sheer_lingerie`). Decide whether mesh panties on Branch E get one (the Mesh plan didn't give one to the single-piece states; probably none).
9. **Remove the shim and test:** opaque Panties Exposed → Branch E (both genders); mesh panties under a top → Branch E with overlays; mesh bra over a bottom → Branch B; opaque Bra Exposed → regular seduction; either state under a coat → regular; below 6001 or appeal under 80 → regular. Run a proposition with each tier forced (stub `calculateExhibitionSeductionSuccess`, and stub `setTimeout` for the fail redirect).

### Step 14: Bra Exposed Seduction Layer

Bra Exposed is legal, so it doesn't get an exhibition branch. It changes the regular seduction instead.

**14A. Mechanics.**
- `calculateSeductionSuccess()`: +0.03 in Bra Exposed (the look does half the work). Sports bra +0.01.
- `_seductExposureLine(g)`: add a `bra_exposed` line (two variants per gender) and a Panties Exposed line for the regular flow below 6001. Remove the Step 14A shim.
- `seduce_lucius_man` / `_woman`: replace the `isWearingVisibleUnderwear()` line with per-state lines.

**14B. Prose layer in the regular street seduction.** Every scene in the `seduce_street_man` / `seduce_street_woman` chains that names her top gets a `cv.braExposed` branch: no top to pull over her head, a strap slid off the shoulder instead, the cups pulled down, the clasp. Audit with a grep for `cv.topName` and `topName` inside those chains. BP: `breastSize` and `braStyle` at the first chest contact. Undressing below the waist uses `cv.liftable` / `cv.pulldown` (skirt hiked vs. jeans pulled down), as the 12A Bra Exposed branches do. (Also check `cv.bottomName` in those chains for Panties Exposed below 6001.) These chains contain sex scenes: Sex Standards apply to anything new written inside them, and new sex prose goes one tier/scene at a time if the scenes are tiered. Mesh (`cv.braSheer`) is excluded from the Bra Exposed branch.

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
- Thrill Sources, Decay, Transit, venues, harassment, night assault, evidence, police clock, the new verbs (Section 2.7, with gates and cooldowns), Branch E, the Bra Exposed seduction bonus, the wet-bra rule, the six accidents, the park.
- Scene list for the new gang scenes and masturbation branches.
- Document what's actually built (e.g. Jack's door and discount; not the unbuilt Ruby/Livie/Lucius/Hardy lines).

**16B. About Stats & Current Stats.**
- About Stats Exhibition Thrill: the thrill table rows for both states and the sports bra (and the soaked bra's +4).
- Current Stats exhibition card: label (remove the Step 16B shim and read the new levels directly), the state's live effects (thrill rate, suspicion, venue status, indecency clock remaining for Panties Exposed and Underwear Only via `getIndecencyClockRemaining()`), and the lifetime minutes from 3D (already shown). The Branch E exits now increment `exhibitionSeductionCount`, which the card already shows.

**16C. Scene Gallery.** Register every new scene with flag overrides / `galleryOnEnter` that set the outfit so it renders with a bra or panties and the right garments.
- **Done:** the two gang scenes (three tiers each); the four masturbation branches (two tiers each); Branch E "Panties Exposed (Male)" (with its chain) and "Panties Exposed (Female)". The original plan named a gallery section `Exhibition Seduction: Panties Exposed`; the two Branch E entries currently sit in the Exhibitionism category beside the other branches' sex entries. Ask the user whether to move them into their own section.
- **To do:** the verbs (Section 2.7; they're `showMessage` texts, not scenes: decide with the user whether to wrap them in gallery scenes or skip them), the accidents (same), the first-walk beats (same), and `jack_bra_exposed_discount` if wanted.

**16D. Cheat panel.** Add "Outfit: Bra Exposed", "Outfit: Panties Exposed", and "Soak clothes (wetness 70)" buttons for testing.

### Step 17: Save Migration, Polish & Balance

**17A. Save migration.**
- Every field in 2.9 has defaults (re-verify; add any fields Steps 13A–16 introduce, and `_jackBraDiscountDay`, `_jaewonBpeFriendDay` if wanted).
- `underwearMinutesTotal` keeps its value; the new counters start at 0.
- Every `// BPE shim` marker is gone: grep for `BPE shim`; zero hits. Then delete `_bpeShim()` and its doc comment. The last site is `slums_sheer_exhibition`'s `clothesWord` (Section 2.10); decide what it should say in the two partial states (it only runs from a sheer state).

**17B. Style guide audit.** Every new string: contractions; no em-dash connectors (paired parentheticals and dialogue cut-offs only); no "the kind of," "a beat," "a pause," "genuinely," "the way," "in a way"; no Not/Not, No/No, or Something/Something pairs; no negation-before-reveal; no stanza formatting; short paragraphs under ~300 characters; no staccato chains. Crude anatomical language everywhere Kelsie's aroused. `cum` for orgasm. Escaped apostrophes. `node --check` clean on both script blocks.

**17C. Body Branching audit.** Every scene: variable extraction at the top; `gameState.bodyAppearance` only; all five `bodyType` values; `'large'`/`'small'`/`'average'`; BP counts in range; no `cv.topName` in Bra Exposed prose and no `cv.bottomName` in Panties Exposed prose; `cv.braless` / `cv.commando` at every undressing step.

**17D. Sex Standards audit.** Every intimate beat: no "core," no "pulse/wave," phonetics spelled out, position tracked, approach/hit/linger on every orgasm, active present-tense ending, replacement words at most twice per scene.

**17E. Balance pass.**
- Bra Exposed should feel rewarding and safe enough to wear on purpose: small thrill, a seduction edge, most doors open, and a real risk only when it rains.
- Panties Exposed should feel as dangerous as Underwear Only: daily harassment, guaranteed night assault, evidence, blocked doors, the shared four-hour indecency clock, and the biggest thrill of the partial states.
- The indecency clock should land about once per long public outing in either state. If police arrive during ordinary errands, lengthen it; if a player can live in Underwear Only all day without consequence, shorten it. (Known quirk: the clock only checks on district entry and counts time spent at home.)
- A Kelsie in Bra Exposed with commando under a skirt should read as two layers of exposure stacking, not as a new state. (She can now also get commando accidents in Bra Exposed; check the combined rate: the state accidents and the zone accidents roll separately, about 28% of transitions for bra + commando + skirt in testing versus about 14% for bra + panties.)
- The thong arousal tick shouldn't push arousal into Edged on its own over a normal afternoon (it's ~2/hr now).
- The new verbs' cooldowns shouldn't let thrill farming outpace the Mesh verbs. (Roll 6 thrill / 30 min at 2001; Bend 10 / 30 min at 6001; Tug 12 / 45 min and Aside 15 / 45 min at 8001.)
- New questions from this session: should legal Bra Exposed allow sleeping on a park bench when homeless (today it's clothed-only)? Should the old accidents move to `applyArousal()` like the new six? Should Branches A–D route soft successes to their unused `exit_soft` scenes and count `exhibitionSeductionCount` like Branch E?

**17F. Mental model (the check for everything).**
1. **Legality splits the partial states.** Panties Exposed is a crime and gets everything a crime gets. Bra Exposed is a look and gets everything a look gets: attention, approval, disapproval, a seduction edge, and the ache between her thighs from being stared at.
2. **The line can move.** Rain soaks a cotton bra through, and the look becomes indecent. A mesh bra was indecent from the start. The player learns the weather is part of the outfit.
3. **The body is always in it.** Every state names what her nipples, her pussy, and her clit are doing, at every corruption tier, because being seen is what she's for.
4. **The camera follows the exposure.** Bra Exposed scenes are shot from the front: her chest, the cups, the straps. Panties Exposed scenes are shot from behind: her ass, the leg band, the thong. Body branching carries the difference.

---

## 6. Reference Tables

### Helper / function summary (status)

| Function | Step | Purpose | Status |
|---|---|---|---|
| (rework) `getExposureLevel()` | 1A | Adds `'panties_exposed'` and `'bra_exposed'` | Done |
| (rework) `getVisibleExposureLevel()` | 1B | Concealment for both new levels | Done |
| (rework) `getSheerState()` level gate | 1C | Keeps the sheer lingerie states computing | Done |
| (new) `isLegallyDressed()` / `isVisiblyExposed()` / `isCriminalSheerState()` | 1D | Law and eye checks | Done |
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
| (rework) `EXHIBITION_AMBIENT_LINES` + `checkExhibitionAmbientLine()` | 5 | Two new pools | Done |
| (new) `BRA_EXPOSED_STYLE_COMMENTS` / `PANTIES_EXPOSED_STYLE_COMMENTS` | 6A | Street style comments | **Not built (user scoped Step 6 to Jack, Cruz, Jaewon)** |
| (new) named-NPC line helpers | 6B | Per-state lines | Jack, Cruz, Jaewon done; others **not built by user choice** |
| (rework) `checkDistrictHarassment()` / `getHarassExhibScene()` | 7A, 7C | Chance rules and routing | Done |
| (rework) `checkNightRapeEncounter()` | 10A | Bra Exposed no longer guaranteed | Done |
| (rework) `checkAccidentalExposure()` + `_checkBpeAccident()` / `_bpeAccidentPool()` / `_bpeFireAccident()` | 11A, 11C | Zone gating, six new accidents | Done |
| (new) `BPE_EVENT_LAYERS` / `getBpeEventLayer()` / `installBpeExhibitionLayers()` | 11B, 11D | Event layer lines, bus branch | Done |
| (new) masturbation branch texts + `BPE_MAST_TIER_CAP` | 12A | Four branches, two tiers each | Done |
| (new) `getExposedUnderwearChoices()` + five `doBpe*` handlers | 12B | Deliberate acts | Done |
| (new) `getParkExposureLevel()` / `isParkIndecent()` / `getParkUnderwearAwareness()` / `getParkPortaRushText()` | 12C | Riverside Park | Done |
| (new) `bpeDressPantiesExposedForGallery()` | 13B | Gallery outfit for Branch E | Done |
| (rework) `hunt_seduction` routing, `SHEER_SEDUCTION_BRANCH`, `installSheerSeductionOverlays()` regex | 13A | Branch E routing | **To do** |
| (rework) `calculateSeductionSuccess()`, `_seductExposureLine()` | 14A | Bra Exposed bonus and lines | **To do** |
| (rework) `handleDamagedClothing()` | 15A | Combat messages | **To do** |

### Scene / content summary (status)

| Scene / beat | Step | Status |
|---|---|---|
| First-walk beats, district vignettes and responses, ambient lines, wet-bra notice, indecency police, transit lines, harassment openers/gropes/layers | 2–7 | Done |
| `district_exhibition_panties`, `slums_exhibition_panties` (3 tiers each) | 8, 9 | Done |
| Night assault paragraphs, slums pass-out branches | 10 | Done |
| Layer lines on the exhibition events (18 events × 5 tiers) | 11B | Done |
| Six new accidents | 11C | Done |
| Bus exhibition, Bra Exposed branch | 11D | Done |
| Masturbation branches (alley and bench × 2 states × 2 tiers) | 12A | Done |
| Five new verbs | 12B | Done |
| Riverside Park paragraphs and porta-potty rush texts | 12C | Done |
| Exhibition Seduction Branch E (12 male + 11 female scenes) | 13B | Done, **not routed** |
| Branch E routing and sheer overlays | 13A | **To do** |
| Bra Exposed seduction layer | 14 | **To do** |
| Combat messages, forced-state entry responses | 15 | **To do** |
| Tips & Guide, About Stats, Current Stats, gallery remainder, cheat panel | 16 | **To do** |
| Migration, audits, balance | 17 | **To do** |

### Consumer audit (status)

| Site | Owner | Status |
|---|---|---|
| `checkDistrictHarassment()`, `getHarassExhibScene()`, `getSheerState()` gate, building checks in `showScene`, `travelToLocation()`, passive thrill block, `checkExhibitionAmbientLine()`, `checkExhibitionEvidence()`, harassment events, `slums_passout_rape_main`, `getExposureBlockMessage()`, `handleUnderwearDistrictEntry()`, `checkNightRapeEncounter()` / `night_rape_ambush`, `calculateTotalAppeal()`, `unequipClothing()` / equip warnings / leave gate, Appearance tab | 1–10 | Done |
| `rollWatcherEncounter()` (naked only), `search_for_prey` (unused variable) | none | Left as is |
| `checkAccidentalExposure()` | 11A | Done |
| `riverside_park` (text and choices), `park_porta_potty` | 12C | Done |
| `exhibition_masturbate_alley` / `_park_bench` | 12A | Done |
| Current Stats exposure label | 16B | **Shimmed; to do** |
| `_seductExposureLine()` | 14A | **Shimmed; to do** |
| `hunt_seduction` routing | 13A | **Shimmed; to do** |
| `slums_sheer_exhibition` clothes word | 17 | **Shimmed; remove in 17** |
| `handleDamagedClothing()` | 15A | **To do** |
| `seduce_lucius_man` / `_woman` | 14A | **To do** |

---

## 7. Testing Approach That Worked

- Headless Chromium through Playwright: `require('/opt/node22/lib/node_modules/playwright')`, route non-`file:` requests to abort, load the page, wait ~3 s, then `page.evaluate(...)` against the live globals (`gameState`, `story`, the functions). Stub `showMessage`, `showNotification`, `showScene`, `handleNakedEscape` to capture output where needed. `currentScene` is a top-level `let` (assign it directly), and `advanceTime()` needs `gameState.timeStarted = true`.
- Build test outfits from real item objects, e.g. `{ id: 'basic_black_bra', name: 'Basic Black Bra', tags: ['material:cotton'] }`, `{ id: 'white_sports_bra', name: 'White Sports Bra', tags: ['sporty'] }`, `{ id: 'pu', name: 'Red Push-Up Bra' }`, `{ id: 'br', name: 'Criss-Cross Lace Bralette', tags: ['material:lace'] }`, `{ id: 'co', name: 'Black Satin Corset Bra' }`, `{ id: 'mesh_tease_bra', name: 'Mesh Tease Bra', tags: ['material:mesh'] }`, `{ id: 'thong_panties', name: 'Lace Thong' }`, `{ id: 'ch', name: 'Cheeky Panties' }`, `{ id: 'bs', name: 'Cotton Boyshorts' }`, `{ id: 'mesh_tease_panties', name: 'Mesh Tease Panties', tags: ['material:mesh'] }`, `{ id: 'plain_white_tee', name: 'Plain White T-Shirt', category: 'tops' }`, `{ id: 'mini_skirt', name: 'Mini Skirt', category: 'bottoms' }`, `{ id: 'basic_jeans', name: 'Basic Jeans', category: 'bottoms' }`, a jacket `{ tags: ['conceal:partial'] }`.
- Sweep every combination (5 body types × 3 sizes × bra styles or cuts × garments × tiers) and flag `undefined|NaN|null`, `{…}` tokens, banned words (including every "the way"), "your top/skirt" fallbacks, thong subject-verb agreement, paragraphs over ~330 visible characters, and any replacement word used more than twice. Then read full samples: most real fixes this session came from reading, not the sweep.
- For proposition scenes: stub `calculateExhibitionSeductionSuccess` to force `hard` / `soft` / `fail`, and stub `setTimeout` so the fail redirect doesn't fire during the sweep.
- For routing: test the negative cases every time (mesh piece, piece under outerwear, fully clothed, below the gate, stale cooldown).
- For flows (like "Unhook it"): run them on the real street scene with the real `showScene`, click through `Continue` buttons in the DOM, and check the state and the rebuilt choices.

## 8. Gotchas Learned

- **Thong grammar:** `cv.pantiesName` as a subject breaks with a thong ("your lace thong are"). Use local helpers (`panIt`, `panAre`, or restructure so the panties sit in the object slot).
- **Ass-view appositive:** `getPantiesExposedAssView()` always goes at the end of a sentence.
- **`onEnter` before `text()`:** see 2.5.
- **District vs slums format:** district scene text uses `<p>…</p>`; slums uses `\n\n`. Exhibition events and seduction scenes use `\n\n`; masturbation scenes use `<p>`.
- **Scene object name:** `const story = {…}`; a scene can call `story.<id>.<fn>()` at runtime. New scenes go inside the table, before its closing `};`.
- **Duplicate strings:** many prose lines appear more than once in the file; scope replacements to the scene.
- **The Mesh functions** own see-through; call them first and fall through to the BPE content only when they return nothing.
- **Inserted paragraphs:** inserting after a scene's opening often breaks its rhythm; before the closing paragraph is safer; check any closer that leans on the line before it.
- **`showMessage` never refreshes choices.** Re-enter the scene with `showScene(currentScene)` when a verb changes the state.
- **`galleryOnEnter` runs on every visit**, gallery or not; guard with `gameState.flags._galleryMode`.
- **`exit_soft` in Branches A–D is dead code;** `exhibitionSeductionCount` is only counted by Branch E.
- **The Topless female seduction track has one sex scene;** every other branch's male side has two.

---

## 9. How to Start the Next Session

1. Get the latest file (the user's upload if they send one; otherwise the head of `claude/vampire-girl-overhaul-atypb0`, `7f3a285` or later, brought onto the new session's branch). Confirm both script blocks pass `node --check` and that `grep -c "BPE shim: Step"` shows 5.
2. Ask for the companion docs the step needs: Style Guide and Body Branching at minimum; Sex Standards for Step 14B (it touches sex scenes in the regular seduction chains) and for anything else with a sex beat.
3. Step 13A is next and is mostly wiring (Section 5). Its one prose risk is the undress overlay and any `SHEER_SEDUCTION_FIXES` Branch E needs under mesh panties.
4. Before writing, study the code the step touches (Section 4).
5. Build, sweep-test, read full samples, commit, push, send the file, and summarize. Then wait for confirmation.

---

## Appendix A: Glossary of Existing Code (carried from the previous handoff)

**Exposure and clothing helpers**
- `isBraVisible()`: bra on, no top or dress. `arePantiesVisible()`: panties on, no bottom or dress (a top-slot `category === 'dress'` counts as covering). `isWearingVisibleUnderwear()`: either. `getUnderwearExposureType()`: `'both'`, `'bra_only'`, `'panties_only'`, or null. In code, `'bra_only'` is Bra Exposed and `'panties_only'` is Panties Exposed.
- `isEffectivelyNaked()`, `isEffectivelyTopless()`, `isEffectivelyBottomless()`: slot checks used by the passive block and gates. `checkAndSetExposureState()`: re-derives the exposure flags after an outfit change.
- `getOuterwearConcealment()`: `'full'`, `'partial'`, or `'none'` from outerwear tags (`conceal:full` / `conceal:partial`) or a fallback table.
- `getSceneClothingVars()` Mesh fields: `sheerState`, `topSheer`, `bottomSheer`, `braSheer`, `pantiesSheer`, `seeThroughChest`, `seeThroughHips`, `meshNoun`, fabric nouns/adjectives. Always read `cv.braName`, `cv.pantiesName`, `cv.pantiesExposed`, `cv.braExposed` in new prose.
- `proseItemName(item)`: lowercased item name with "Jaewon's " dropped. `getFabricNoun()`, `getFabricAdj()`, `resolveFabricTokens()`. `_sheerBodyCtx()`: the Mesh body-context object (`tits`, `ass`, `hips`, `frame`, `bra`, `panties`, …). `_sheerOwnsZone(zone)`: true when a zone is bare behind mesh (the braless/commando accidents stand down for it). `_insertSheerParagraph(text, para, re)`: inserts after the first paragraph matching `re` (or appends when `re` is null).
- `getSheerState()` / `isSheerExposureState()`: the Mesh state machine. `sheer_framed` is never indecent.
- Step 1G's shim form: `lvl === 'bra_exposed' || lvl === 'panties_exposed'` treated as `'clothed'` (implemented as `_bpeShim()`).

**Law, venues, police**
- `SHEER_STATE_GATES` / `getSheerStateGate()`, `handleSheerDistrictEntry()`, `tickSheerLingeriePolice()` / `handleSheerPoliceArrive()`: the Mesh-era patterns.
- `exposureRestrictedBuildings`: interior scene id → `{ redirect, name }`; checked in `showScene`. Redirect ids include `outside_diner`, `outside_convenience_store`, `outside_library`, `outside_hospital`, `outside_blood_bank`, `outside_luxury_hotel`, `outside_casino`, `outside_noir_boutique`, `outside_vajaros_realty`, `outside_bargain_threads`, `outside_urban_edge`, `outside_laundromat`, `outside_dry_cleaners`, `outside_hardy_bar`, `outside_lucius_lounge`, `outside_dojang`, `outside_gymnasium`, `outside_stadium`. `getExposureBlockMessage(buildingName)`. `SHEER_BRALESS_BLOCKED_VENUES` / `SHEER_BARE_ALLOWED_VENUES` with `getSheerVenueBlockMessage()`.
- `getSheerTransitReaction()`, `checkExhibitionEvidence(sceneId)` (camera 30% Commercial/Downtown, witness 15% Residential/park, none in the slums), `calculateTotalAppeal()` (`_undressedForPenalty`).

**Passive layer**
- `advanceTime()` passive block; `_anyExhibitionState` holds thrill against decay. `isPrivateExposureScene()`: no exhibition gains at home or at Jaewon's. The old gain pattern `gameState.arousal = Math.min(100, gameState.arousal + N)` is replaced by `applyArousal(N)` in anything this overhaul writes.

**District entry and ambient**
- `handleUnderwearDistrictEntry(districtId)`, `getUnderwearNPCVignette(type)` (pools `bothPool`, `braPool`, `pantiesPool`; only `bothPool` is still used), `getToplessNPCVignette()`, `getBottomlessNPCVignette()`, `getToplessKelsieResponse()`, `getBottomlessKelsieResponse()`. Step 15B's forced-state entries follow the topless/bottomless forced-entry pattern.

**Style comments and named NPCs (Step 6 pieces the user chose not to build)**
- `SHEER_STYLE_COMMENTS` and the coherence style-comment block (picker order: dirty/torn clothes, then the sheer slot, then coherence). The original 6A asked for `BRA_EXPOSED_STYLE_COMMENTS` (fashion register, like `sheer_framed`, six comments) and `PANTIES_EXPOSED_STYLE_COMMENTS` (scandal register, like `sheer_commando`, six comments) in the sheer slot.
- `LUCIUS_SHEER_DOOR_LINES` / `getLuciusSheerDoorLine()`, `rollHardySheerLine()`, `getRubySheerLine()`: sheer versions of the unbuilt Lucius, Hardy, and Ruby lines. The original 6B asked for: Ruby (Bra Exposed: points at the shirts-required sign, hands her an apron or a staff tee, three variants; Panties Exposed: pulls her inside the back door before a customer sees, three variants); Livie (Bra Exposed: taps the sign, dry and amused; Panties Exposed: stares, then pretends she didn't); Lucius doorman (Bra Exposed evening: waves her in with a line on the look; Panties Exposed: refusal line); Hardy bartender (Bra Exposed: free-drink line, once per day).
- `getJaewonSheerReaction()`: runs before the new Jaewon reaction.

**Harassment, gang scenes, events**
- `street_harassment_event` / `street_harassment_grope` and the slums pair use an `exhibState` ladder. Choice scenes: `street_harassment_aggressive`, `_firm`, `_passive`, `_flirt` (slums: `slums_harassment_aggressive`, `_firm`, `_passive`, `_moan_choice`).
- Gang-scene families: `district_exhibition_underwear|topless|bottomless|naked`, `district_wet_exhibition`, `district_sheer_exhibition`, and the `slums_*` equivalents. The Panties Exposed district scene uses `completeSexAssaultMale({ time: 15, hygiene: -30, rapeType: 'district_exhibition_panties', corruptionGain: 25, thrill: 15, partner: 'district_group', skipPregnancy: true })`.
- The 20 exhibition events (`exhibition_wind_trigger`, `exhibition_bra_wind_trigger`, `exhibition_barstool_trigger`, `exhibition_bench_trigger`, `exhibition_bus_trigger`, `exhibition_button_trigger`, `exhibition_crowd_trigger`, `exhibition_escalator_trigger`, `exhibition_fitslip_trigger`, `exhibition_fitting_trigger`, `exhibition_lean_trigger`, `exhibition_mirror_trigger`, `exhibition_neckline_trigger`, `exhibition_photo_trigger`, `exhibition_puddle_trigger`, `exhibition_reach_trigger`, `exhibition_spill_trigger`, `exhibition_staircase_trigger`, `exhibition_turnstile_trigger`, `exhibition_wet_trigger`) are registered in `buildDistrictEventPool(district)` with garment conditions and `exhibitionGlobalCooldownClear()`. `installSheerExhibitionOverlays()` (Mesh Step 10) and `getSheerExhibitionChanceMult()` wrap them. `getFlashExhibitionChoice()`, `getPoseExhibitionChoice()`, `getWetExhibitionChoices()`, `getSheerExhibitionChoices()`, `getExhibitionMasturbationChoice()`, `getBralessCommandoAlleyChoice()` share the street choice slot.

**Mesh-era numbers (for balance comparisons)**
- Visible-underwear gate before this overhaul: 6001 for any visible underwear, 8001 for the full sheer set. District-entry suspicion: +2 underwear, +3 topless, +4 bottomless. Harassment grope gains: 25/25 for exhibition states, 15/20 otherwise.


## Appendix B: Other Identifiers from the Previous Handoff (Steps 1–10 details, for grep)

- `Bra_Panties_Exposed_Overhaul.md`: the original overhaul plan (815 lines), superseded by the handoffs. `fa9f817` was the Step 10 commit; `git push -u origin claude/vampire-girl-overhaul-jjgcb3` was the old push target.
- `_bpeShim(level)` and its tag `// BPE shim: Step N` (Section 2.10). Shimmed sites in 12A/12C used tests like `exposure !== 'clothed'`, which is why they needed shims.
- `cv` Step 1E comparisons: `exposureState === 'bra_exposed'`, `exposureState === 'panties_exposed'`; `cv.braName` is `proseItemName(outfit.bra)` or `'bra'`, `cv.pantiesName` is `proseItemName(outfit.panties)` or `'panties'`; the fallback `bottomName: 'skirt'`; test a thong with `cv.pantiesCut === 'thong'`.
- The Mesh level gate before Step 1C: `_level !== 'clothed' && _level !== 'underwear_only'`. `sheer_lingerie_top/bottom` is shorthand for the two single-piece lingerie states.
- Thong grammar helpers from Step 5: `panWord = thong ? 'thong' : pan`, `panIt = thong ? 'it' : 'them'`, verb pairs like `'it stretches' : 'they stretch'`.
- Ambient tokens: `{bra} {panties} {top} {bottom} {tits} {Tits} {ass} {Ass}`, resolved in `checkExhibitionAmbientLine(sceneId)`; the Mesh fabric token is `{fabric}`.
- District entry: `getBraExposedKelsieResponse(corruption)` / `getPantiesExposedKelsieResponse(corruption)`; the unused scandal pool is in `getUnderwearNPCVignette()`.
- Jaewon: `getJaewonPartialExposureReaction()` runs inside `getJaewonBralessCommandoReaction()` after the sheer reactions.
- Harassment ladder values include `braless_commando`; `getSheerHarassState()` routes sheer harassment first; `getHarassExhibScene(area)` forces `<area>_exhibition_panties` for Panties Exposed.
- Gang scenes: `onEnter` increments `streetHarassmentCount` or `slumsHarassmentCount` plus `exhibitionEventsCompleted`; partners `district_group` / `slums_group`; the family model `slums_exhibition_underwear`; the template call in the original plan was `completeSexAssaultMale({ time: 15, hygiene: -30, corruptionGain: 25, thrill: 15, ... })`.
- Night scenes read garment words through local `_nt` / `_nb` / `_nl`.
- Exhibition events: registrations carry `hold_bra` / `hold_panties` / `hold_bare` variants for their hold scenes. The braless/commando accident entries use a corruption-tiered `kr` string; `_buildSheerAccidentPool()` builds the sheer accidents; the accident check is `checkAccidentalExposure(districtId)`.
- Masturbation: `exhibition_masturbation_choose` (the alley picker) and `exhibition_masturbate_park_bench` (offered from `riverside_park`).
- Appearance tab internals: `updateAppearanceDisplay()`, `getUnderClothesBannersHTML()`, `getSheerLingerieBoxInfo()`.
- Suspicion goes through `modifyCitySuspicion()`.
- Testing: `currentScene` is a top-level `let`, so assign it directly, not `window.currentScene`; a mesh test bra is `{ id: 'mesh_tease_bra', … }`.

---

## Audit Record

This handoff was checked against the previous handoff (the post-Step-10 `Bra_Panties_Exposed_Handoff.md`, 518 lines; the re-sent copy is byte-identical to the one read at the start of this session and to the copy in the repo at `8ea93c0`):

- **Every section of the previous handoff is represented:** Read This First (Section 0, extended with this session's rules and gotchas); The Overhaul in Brief with decisions and conventions (Section 1); the foundation 2.1–2.6 (Sections 2.1–2.5, 2.9); shims (2.10, updated to the five remaining markers); testing and gotchas (Sections 7–8, extended); Steps 1–10 done (Section 3); existing systems (Section 4, updated to what the remaining steps touch); the remaining steps (Section 5: 11 and 12 moved to done; 13 split into done 13B and to-do 13A with a worked plan; 14–17 reproduced in full with every table and sub-point, plus this session's additions); the three reference tables (Section 6, with status); How to Start (Section 9); Appendix A (carried over, with a few identifiers added); this record.
- **Identifier check:** every inline backticked identifier in the previous handoff (492) was checked for presence in this document by script. The first pass found 57 absent, mostly details of finished steps; Appendix B and a few in-place additions (the Branch E id pattern and the original's 24-scene count, the planned `Exhibition Seduction: Panties Exposed` gallery section, `cv.liftable` / `cv.pulldown` for Step 14B, the sub-step labels 2G, 3C and 10B) were added, and the re-run shows every one present. Every number in the previous handoff's remaining-step text (Steps 13–17) and every sub-step label (1A–17F) appears here.
- **Intentional differences:** Steps 11 and 12 and 13B are summarized as done rather than reproduced as instructions; their instructions are replaced by descriptions of what was built. The "reserved for Step 11/12" notes on `_lastBraAccidentMinute`, `_lastPantiesAccidentMinute` and `_bpeActionMinutes` are replaced by their real uses. The four consumer-audit rows for Steps 11 and 12 moved to done.
