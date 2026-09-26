# Mesh & Sheer Clothing Overhaul: Session Handoff #3

**Status at handoff:** Steps 1 through 18 are complete and approved. **Next up: Step 19 (Masturbation Integration), then Step 20 (Tips & Guide), then Step 21 (Save Migration & Final Sweep).**

**This document stands alone.** You don't need the original overhaul doc or either earlier handoff. The only companion docs you need:
- `VampireGirl_Style_Guide_SS.md` (every line of prose)
- `VampireGirl_Masturbation_Standards.md` (Step 19; ask the user to upload it before writing)
- `VampireGirl_Sex_Standards.md` (only if a fix touches a sex scene)

How this doc is laid out:
- **Part A:** where the work lives, the user's rules, and how to validate.
- **Part B:** the sheer system reference you'll need for Steps 19 through 21.
- **Part C:** what was done in session 3 (Steps 17B, 18, and the harassment rework), with every approved decision.
- **Part D:** Step 19, detailed.
- **Part E:** Step 20, detailed, including a full fact sheet of everything the overhaul built, so the guide documents what's real.
- **Part F:** Step 21, detailed.
- **Part G:** open items and traps.

---

## Part A: Orientation

### A1. Where the work lives

- **Repo:** `sariia32desu-star/claude-code`
- **Branch used in session 3:** `claude/vampire-girl-continuation-jlilql`. Everything is committed and pushed. The next session will likely get a new branch name. If that branch doesn't contain these commits, merge or fast-forward from `claude/vampire-girl-continuation-jlilql` first. (Sessions 2 and 3 both started this way: the new branch lacked the game file until it was merged from the previous session's branch.)
- **Working file:** `vampire-girl/Vampire_Girl.html`, about 223,400 lines.
- **Script blocks:** 5830 to 5896, and 12690 to about 223402. These shift with every edit. Always re-grep `<script>` / `</script>`.
- **Line numbers in this doc are approximate.** Always `grep -n "function name("` or `grep -n "scene_id: {"` before editing.
- **Earlier handoffs:** `vampire-girl/Mesh_Overhaul_Handoff.md` (handoff #2). This file replaces it.

**Session 3 commits, oldest first:**

| Commit | Content |
|---|---|
| `5de864e` | 17B: `slums_sheer_exhibition` Innocent tier |
| `e17fb92` | 17B: Experienced tier |
| `dd89a51` | 17B: DC tier and slums routing |
| `0610e31` | Exhibition harassment: single "They take you away" choice at every tier |
| `3ac5d77` | Rewrite of 24 exhibition openings to follow "They take you away" |
| `f401b1d` | Split three long slums exhibition paragraphs |
| `7da3023` | 18: Backlight, male (two parts) |
| `3d4345a` | 18: Backlight, female |
| `cf1df3c` | 18: Flash, male (two parts) |
| `fc0fc8d` | 18: Flash, female |
| `d336d53` | 18: Snag, male (two parts) |
| `9d6fa13` | 18: Snag, female |
| (handoff commit) | This doc |

### A2. The user's working rules (non-negotiable)

**Process:**
- **Always work from the latest file**: the one on the branch, or a newer file the user uploads. Never copy an earlier version over it. If the user uploads a newer `Vampire_Girl.html`, that file becomes the working file.
- **One step (or one tier, or one scene) per output.** Wait for the user's go-ahead before the next. The user asked for gallery scenes one tier or one scene at a time, "for focused quality and depth," and wants each one to "sing."
- **After each step, tier or scene:**
  1. Commit with a clear message and push to the branch.
  2. **Present the updated game file to the user with SendUserFile.** (Session 3 forgot this until the end, and the user noticed. Don't skip it.)
  3. Summarize what was done, every deviation and every judgment call, so the user can approve them.
- **When a step needs a decision the spec leaves open, ask before starting, with a recommendation.** The user answers quickly. Use AskUserQuestion when the choice is genuinely theirs.
- **Commit trailer:** use the lines your session's system reminder gives you. Never put a model name in a commit message.

**The user's standing prose preferences** (they apply to every line of prose in the game file):
- No em dashes, except a paired parenthetical aside or a dialogue cut-off.
- No "Not a question."
- No AI tells: no "Not because X. Because Y.", no paired "Not…/Not…" speech (for example "Not the hardest hitter. Not the tallest blocker."), no "A beat."
- No formal uncontracted language like "She is" or "You are." Use "She's" and "You're." When a sentence can't contract (sentence-final "there you are," "right where it is"), leave it only if it reads naturally; otherwise rewrite.
- No stanza formulas such as "You're not the star. / You're the girl coaches trust / to be in the right place."
- Stick strictly to the game's writing style. Any deviation counts as an error.

**Technical rules for the HTML/JS:**
- Every apostrophe inside a single-quoted JS string must be escaped as `\'`. Never produce `\\'`. Never insert apostrophes with sed `\x27`; use Python or the Edit tool.
- Ternaries inside single-quoted strings break out with unescaped quotes: `' + (_bs === 'large' ? 'a' : 'b') + '`. After every ternary the next character must be `)`, `+` or `;`.
- A `return` or `desc +=` that ends in a ternary must wrap the whole expression in parentheses before appending anything.
- Static `text:` properties that reference `cv` must become `text: function()`.
- **`galleryOnEnter` runs in live play too.** `showScene()` calls it on part 0 whether or not the gallery is open. Any `galleryOnEnter` that changes the outfit or state must start with `if (!gameState.flags._galleryMode) return;`.
- `onEnter` does **not** run in gallery mode.
- Gallery mode snapshots `gameState` on entry (`_gallerySavedState`) and restores it on exit, so a `galleryOnEnter` may safely preset flags the scene reads (Step 18's Snag scenes preset a zone this way).
- Run `node --check` on both script blocks after every change.

**Body branching standards (required in all prose):**
- Read body attributes from `gameState.bodyAppearance`.
- **`bodyType`:** petite, athletic, curvy, thick, or ordinary (default). Every bodyType branch fills all five. `_bt5(b, petite, athletic, curvy, thick, ordinary)` does this.
- **`breastSize` / `buttSize`:** large, small, average (default). Always `'large'`, never `'big'`.
- The ordinary and average branches must read as real prose, never placeholders.
- Typical top-of-function extraction:
  ```javascript
  var _ba = gameState.bodyAppearance || {};
  var _bs = _ba.breastSize || 'average';
  var _butt = _ba.buttSize || 'average';
  var _bt = _ba.bodyType || 'ordinary';
  var cv = getSceneClothingVars();
  var b = _sheerBodyCtx();   // sheer scenes: garment names, what shows, framed/lingerie words
  ```
- Branching point targets: short or single-tier 3 to 5; standard 4 to 8; long or multi-tier 6 to 10. bodyType at undressing, position changes, build and orgasm; breastSize where her chest is touched or revealed; buttSize where her hips or ass are gripped.
- **Clothing awareness:** use `cv.braless` / `cv.commando`, never raw `!o.bra` checks. Name garments with `cv.topName`, `cv.bottomName`, `b.top` / `b.bottom` / `b.outfit`, or `proseItemName(item)`. Undressing branches on `cv.liftable` (hike up) vs `cv.pulldown` (push down). Plural garments take plural verbs ("your jeans are").

**Register by scene type:**

| Scene type | Register |
|---|---|
| Romance | Tender, hungry |
| Stranger | Raw physical facts; no NPC thoughts |
| Assault | Visceral, invasive; her body seen from outside |
| Masturbation | From inside her body; she knows it |
| Exhibition | Heightened awareness of every sightline |

**Style lessons (each one was a real hit that had to be fixed):**
- "The way" is banned in every idiom: "all the way," "on the way," "the whole way" is fine but "Maintenance is on the way" was a hit. Rewrites: "down to the base," "Up to your hips," "Maintenance is coming."
- "Pulse," "pulses," "pulsing," "wave," "waves" and "core" are banned in intimate prose.
- "Cum" for orgasm (present and noun); "came" is the house past tense (the game uses "You came."). "Come" is fine for movement ("come down," "come shopping").
- "A beat" hides inside phrases: "half a beat slower" was a hit.
- Doubled words and repeated nouns come from composed fragments. Read composed paragraphs in full: "Your bare tits, bare under your shoved-up tee," "The tear… The tear," "His knuckles… His knuckles," "steer… steer."
- A fragment written for a see-through top must branch when the chest is opaque ("through the mesh" vs "under your shoved-up white tee"), and "it" must still point at the right garment ("The window light goes straight through it" pointed at jeans).
- Paragraphs must stay under about 300 visible characters. Split at a natural sentence break.
- NPC thoughts (`<em>He thought:</em>`) are only for romance characters.

### A3. Validation and testing recipes

**Syntax check** (run after every change):
```bash
cd vampire-girl
E=$(grep -n '</script>' Vampire_Girl.html | tail -1 | cut -d: -f1)
sed -n '5831,5895p' Vampire_Girl.html > /tmp/s1.js && node --check /tmp/s1.js
sed -n "12691,$((E-1))p" Vampire_Girl.html > /tmp/s2.js && node --check /tmp/s2.js && echo OK
```
Use the scratchpad directory instead of `/tmp` if the session gives you one. Re-check the first block's bounds with grep if the early part of the file changes.

**Runtime tests** use headless Chromium through Playwright (preinstalled). Launch with `chromium.launch({ executablePath: '/opt/pw-browsers/chromium' })` and run with `NODE_PATH=$(npm root -g) node test.js`.
- Open `file:///home/user/claude-code/vampire-girl/Vampire_Girl.html` and wait about 3 seconds.
- Fake items look like `{ id, name, category, appeal, tags: ['material:mesh'], damage: 0, cleanliness: 100 }`. Use real registry IDs for mesh pieces so `getMeshProfile()` finds them.
- Set `gameState.currentOutfit` with all six slots: top, bottom, dress, bra, panties, outerwear.
- Scenes live in the global `story[sceneId]`: `.text()`, `.onEnter()`, `.choices()`, `.galleryOnEnter()`.
- `advanceTime(min)` needs `gameState.timeStarted = true`. The current scene is the global `currentScene`.
- Stub `window.showMessage = (t, f) => {...}` to capture popups and their close callback. `window.isDark = () => true` forces night.
- **Test the gallery** with `enterGalleryMode(catIndex, sceneIndex, tierIndex)`. It shows the scene after `requestAnimationFrame` + `setTimeout`, so wait about 2.5 seconds. Find indexes by scanning `SCENE_GALLERY_REGISTRY[i].scenes[j].sceneId`. After a live `showScene`, wait about 1.5 seconds before entering the gallery, or its snapshot catches the previous scene mid-transition.
- **Test `galleryOnEnter` through the real `showScene()`** with `_galleryMode` false as well as true.

**Standard outfit matrix** (used for Steps 17 and 18; adapt for Step 19):
1. Off-Shoulder Mesh Mini Dress alone (sheer bare)
2. Rainbow Fishnet Mini Dress alone (sheer bare, fishnet)
3. Mesh Party Dress over Sheer Mesh Panties (stacked sheer)
4. Mesh Crop Top + Sheer Mesh Mini Skirt (sheer bare, separates)
5. Mesh Crop Top + jeans + cotton panties (sheer braless, pants)
6. Mesh Crop Top + denim mini skirt, no panties (sheer braless, commando under opaque)
7. Mesh Crop Top + Sheer Mesh Mini Skirt over cotton panties (braless, framed hips)
8. White tee + bra + Sheer Mesh Mini Skirt (sheer commando, opaque chest)
9. Off-Shoulder dress over an opaque bra, no panties (framed chest, veiled hips)
10. Sheer Mesh Bra + Sheer Mesh Panties only (sheer lingerie)
11. Sheer Mesh Bra + jeans + panties (lingerie top)
12. White tee + bra + Sheer Mesh Panties only (lingerie bottom)
13. Extras used in session 3: Mesh Crop Top + panties only (chest veiled, hips underwear); bra + Sheer Mesh Mini Skirt (chest underwear, hips veiled); white tee + Sheer Mesh Mini Skirt, no bra.

Render every new prose function for all 45 body combinations (3 breast × 3 butt × 5 type) × every outfit the state allows × each tier.

**Prose QA method** (it caught real errors in every step):
- Split renders into unique paragraphs (on `</p>` and `\n\n`, strip tags).
- Scan for `undefined`, leftover `{tokens}`, paragraphs over 300 visible characters, and this regex:
  `the way|in a way|kind of|kind that|genuinely|a beat|a pause|not because|she is|you are|it is|do not|does not|cannot|all the way|\bpulse|\bwave|\bcore\b|\bcome\b|\bcame\b|\b(\w+) \1\b`
- Scan em dashes outside quotes (inside quotes they must be cut-offs).
- Review every hit by hand. False positives seen: "clit is throbbing" (it is), "does nothing" (does no…), "there you are," "come shopping," "You came."
- **Read full renders of every branch.** Most real bugs were only visible in full reads (doubled nouns, wrong antecedents, spatial contradictions).
- Session 3's QA scripts lived in the scratchpad and won't carry over; rebuild them from this recipe.

---

## Part B: The Sheer System Reference

### B1. The mesh wardrobe (`MESH_GARMENTS` registry, source of truth)

| ID | Name | Zones | Weave | Noun | Source / notes |
|---|---|---|---|---|---|
| `bo_off_shoulder_mesh_mini_dress` | Off-Shoulder Mesh Mini Dress | chest, hips | sheer | mesh | Beauty Obsessed. Off both shoulders, geometric cutouts. Upscale. **The gallery default outfit.** |
| `bo_mesh_mini_skirt` | Sheer Mesh Mini Skirt | hips | sheer | mesh | BO. Ruched sides, solid waistband. |
| `bo_rainbow_halter_mini_dress` | Rainbow Fishnet Mini Dress | chest, hips | fishnet | fishnet | BO. Halter, open back, wide diamond holes. |
| `bo_leopard_halter_mini_dress` | Sheer Leopard Mini Dress | chest, hips | sheer | sheer fabric | BO. |
| `jaewon_mesh_top` | Jaewon's Sheer Mesh Top | chest | sheer | mesh | Jaewon's wardrobe; borrowing needs friendship 60+. |
| `mesh_club_dress` | Mesh Club Dress | chest, hips | semi | mesh | Urban Edge, $1000. Passes the upscale door. |
| `mesh_crop` | Mesh Crop Top | chest | sheer | mesh | Urban Edge, $260. |
| `mesh_tease_bra` | Sheer Mesh Bra | chest | sheer | mesh | Urban Edge, $12. Half of the Mesh Tease Set (+8 appeal). |
| `mesh_tease_panties` | Sheer Mesh Panties | hips | sheer | mesh | Urban Edge, $6. |
| `mesh_party_dress` | Mesh Party Dress | chest, hips | sheer | mesh | Noir, $2200, appeal 96. O-ring halter, underbust cutout, ribbon-tie slits. Added by the overhaul. |
| `one_shoulder_club_dress` | One-Shoulder Mesh Club Dress | chest, hips | sheer | mesh | Noir, $2600, appeal 102. Single halter string, bell sleeves, drawstring hip. Added by the overhaul. |

Weave multipliers: semi 0.7, sheer 1.0, fishnet 1.1. Stacked layers read at the most transparent weave. Both Noir dresses are on the Noir upscale list (Lucius and Regency doors).

Helpers: `getMeshProfile(item)`, `isMeshOverZone(item, zone)`, `getSheerNoun(st)`, `getFabricNoun(item)`, `getFabricAdj(item)`, `proseItemName(item)`.

### B2. `getSheerState()`: the zone model

Each zone (chest, hips) gets a status:

| Status | Meaning |
|---|---|
| `opaque` | Covered by a non-sheer outer layer, or concealed by a coat (full cover hides both zones, partial cover such as a hoodie hides the chest). |
| `framed` | Every outer layer is sheer, over opaque underwear. The underwear is on display. |
| `veiled` | Every outer layer is sheer, over nothing or over sheer underwear. Bare skin shows. |
| `lingerie` | Sheer underwear with no outer layer. |
| `underwear` | Opaque underwear with no outer layer. |
| `bare` | Nothing (in practice unreachable; slot states take over first). |

Combined state (first match wins), with the leave-house gate:

| State | Condition | Gate |
|---|---|---|
| `sheer_lingerie` | chest lingerie and hips lingerie | 8001 |
| `sheer_lingerie_top` | chest lingerie | 6001 |
| `sheer_lingerie_bottom` | hips lingerie | 6001 |
| `sheer_bare` | chest veiled and hips veiled | 4001 |
| `sheer_commando` | hips veiled | 4001 |
| `sheer_braless` | chest veiled | 2001 |
| `sheer_framed` | any zone framed | none |
| `none` | no sheer involvement | |

The sheer state is only computed when `getExposureLevel()` is `clothed` or `underwear_only`; topless, bottomless and naked slot states own the body.

Return shape: `{ state, chest, hips, chestGarment, hipsGarment, chestUnder, hipsUnder, stacked, framedChest, framedHips, weave, hipsWeave, weaveMult, tint, zoneWeave: { chest, hips } }`.

Other state helpers:
- `getSheerHarassState()`: '' for framed or none; otherwise `sheer_braless` / `sheer_commando` / `sheer_bare` / `sheer_lingerie` (all three lingerie states collapse).
- `_sheerOwnsZone(zone)` (~36901): true when that zone is veiled; the opaque braless/commando systems stand down for it.
- `isSheerExposureState(state)`: true for braless, commando, bare and the three lingerie states.

### B3. `cv` sheer fields (`getSceneClothingVars()`, ~41140)

| Field | Meaning |
|---|---|
| `sheerState` | `getSheerState().state` |
| `topSheer` / `bottomSheer` | The outer garment on that zone is in the registry (garment check, not visibility) |
| `braSheer` / `pantiesSheer` | The worn bra or panties are in the registry |
| `seeThroughChest` / `seeThroughHips` | The zone is veiled or lingerie. **Use these for "can strangers see her nipples / pussy."** |
| `meshNoun` | 'mesh', 'fishnet' or 'sheer fabric' |
| `topFabric` / `bottomFabric` / `braFabric` / `pantiesFabric`, plus `…Adj` | Fabric nouns and adjectives |

`cv.braless` and `cv.commando` keep their meaning: **a mesh bra is still a bra.**

### B4. `_sheerBodyCtx()` (~22403) and `_bt5()` (~22484)

`_sheerBodyCtx()` returns: `st`, `bs`, `butt`, `bt`, `noun`, `top`, `bottom`, `outfit`, `same` (one garment covers both zones), `seeChest`, `seeHips`, `shows` / `Shows` (what shows through, breast-branched), `tits`, `ass`, `hips`, `frame`, `hipsWord`, `lookWord`, `under`, `braF`, `panF`, `sheer`, `bra`, `panties`. Use it in every sheer prose function.

### B5. Rates (for the guide)

`SHEER_PASSIVE_RATES`, per hour, as thrill / corruption / mesh-scratch arousal:

| State | Thrill | Corruption | Scratch |
|---|---|---|---|
| framed | 1 | 1 | 0 |
| braless | 3 | 2 | 1 |
| commando | 4 | 3 | 0 |
| bare | 6 | 4 | 1 |
| lingerie top / bottom | 9 | 3 | 0 |
| full lingerie set | 12 | 4 | 0 |

- Thrill is multiplied by the weave. These stack with the regular braless (+1/+1), commando (+2/+2) and both (+1/+1) rates. Lingerie replaces the underwear-only +3/hr thrill. Naked is +15/hr thrill for comparison.
- Verified totals at 3000 corruption: framed 1 corruption / 1 thrill; braless 3 / 4; commando 5 / 6; bare 8 / 10; lingerie 4 / 12.
- Private scenes (home, Jaewon's apartment) earn none of it, and don't hold off thrill decay.
- Lifetime minutes are tracked in public only: `exhibitionStats.sheerMinutesTotal`, `sheerBareMinutesTotal`, `sheerLingerieMinutesTotal`.

---

## Part C: What Session 3 Did (all approved)

### C1. Step 17B: `slums_sheer_exhibition` (~176040)

- Three tiers, cast of the leader, the wiry one and the big one. The slums men's joke is the **"shop window"** ("She put herself in the shop window." / "Window's open." / "Put yourself back in the window, sweetheart. We'll come shopping again.").
- **Innocent (assault):** they take her down the block to a stoop; the mesh is torn zone by zone; fingering against the stoop railing; the big one holds her in a **full nelson facing the street** with the leader in her pussy and himself in her ass; the wiry one finishes on the mesh ("Window's dirty now").
- **Experienced (conflicted, body cooperates):** tits pulled out over the mesh, crotch torn; the leader sits on the stoop and pulls her into his lap **facing the street**, lets go of her hips, and she rides him on her own ("There she goes"), the wiry one in her mouth, the big one in her fist.
- **DC (she picked the block):** she tears the mesh open herself with vampire strength, lies across the hood of a dead car under the streetlight ("Put me in the window"): the big one eats her out; her head off the hood for the leader while the big one takes her pussy, ankles on his shoulders; then the wiry one in her ass while she rubs her own clit.
- `onEnter`: `trackGalleryScene`, damage (outer mesh +40, mesh panties +60, capped at 99), `completeSexAssaultMale({ time: 20, hygiene: -35, rapeType: 'slums_sheer_exhibition', corruptionGain: 30, thrill: 20, partner: 'slums_group', skipPregnancy: true })`, pregnancy check into `_slumsSheerExhibPregnancy`, `slumsHarassmentCount` +1, `exhibitionEventsCompleted` +1. "Keep walking." to `slums_district_street`.
- Gallery: Exhibitionism, "Slums: Sheer Exhibition," Innocent 0 / Experienced 4001 / Deeply Corrupted 8001.

### C2. Harassment rework: "They take you away" (user-directed)

- **New helper `getHarassExhibScene(area)`** (~23382), area `'district'` or `'slums'`. Returns the exhibition scene the grope leads into, or null. Order: underwear / topless / bottomless / naked by exposure level, then sheer (`getSheerHarassState()`), then wet (wetness 60+).
- **Both grope scenes** (`street_harassment_grope`, `slums_harassment_grope`): in any exhibition state the only choice, at every corruption tier, is **"They take you away"**, which acts as a Continue button into that scene. Plain and framed outfits keep their normal choices (shove, step back, don't do anything, moan). The district grope drops its "What do you do?" line in exhibition states.
- Result: **every tier of every district and slums exhibition scene is now reachable in live play.** The user's rule: no gallery scene may be gallery-only.
- **24 exhibition openings were rewritten** so each scene picks up from the grope and the men taking her:
  - District DC tiers (6): the men take her toward the alley, and she goes eagerly, then takes over inside.
  - District bottomless Innocent: the old "They see your pussy before they see your face" (a first-sight beat) now continues from the grope.
  - All 18 slums tiers: the men walk her off the main drag down a side street instead of "They don't take you to an alley," and the seven openings that re-met the men ("Three men step out of a doorway") now continue from their hands. Slums DC tiers keep her planning the outing as memory.
- Also fixed: "Your Your" in the district wet DC tier (ordinary body), three No/No lines, and every touched paragraph over 300 characters.

### C3. Step 18: Discovery through the mesh (six scenes, all DC only)

**User decisions:**
- Every Step 18 scene is **one tier: Deeply Corrupted (8001)**, so the follow offer appears only at **8001+**.
- The Flash popup's +10 social media heat covers the photos. The Flash scenes add no second heat; Kelsie keeps the photos in prose only ("Keep them.").
- Condom choice comes at the end of part 1 of each male scene ("Hand him a condom" / "Go without," greyed out with no condoms), mirroring the diner pattern.

**Mechanism:**
- `SHEER_DISCOVERY_SCENES` (~23305): `{ backlight_m, backlight_f, flash_m, flash_f, snag_m, snag_f }` → scene IDs.
- `getSheerDiscoveryScene(event)` (~23309): returns the scene when corruption is 8001+, the state is `sheer_braless` / `sheer_commando` / `sheer_bare`, a scene exists for the key and gender, and (for the Backlight only) it's dark.
- In `checkAccidentalExposure()`: when that returns a scene, it stores `gameState.flags._sheerDiscovery = { key, gender, district, zone }` and the popup's close callback opens `sheer_discovery_offer`.
- The Snag event now returns `snagZone` ('chest' or 'hips'), stored as `zone`.
- `sheer_discovery_offer` (~176617): per-key text; choices "Let him follow." / "Let her follow." and "Keep walking." (clears the flag, returns to `<district>_district_street`).
- `_sheerDiscoveryGarb(gender)` (~23320): shared consensual garment helper. Returns `chestTouch`, `hipsTouch`, `chestPeel`, `hipsPeel`, `frame`, `bounce`, `chestFix`, `hipsFix` for every zone status; gender 'f' swaps the stranger's pronouns. Pants are stepped out of one leg.
- Every scene ends with "Keep walking." back to the district street, clearing `_sheerDiscovery` and `_sheerDiscoveryCondom`.

| Scene | Setting and shape |
|---|---|
| `sheer_backlight_sex_m` / `_2_m` | A closed boutique's lit doorway. She lets him look; he eats her out against the glass door; part 2 on the lit display window beside the mannequins while a woman walking a dog watches. |
| `sheer_backlight_sex_f` | Same doorway. She circles Kelsie in the light; fingers her from behind facing the street while a man outside a bar watches; Kelsie eats her against the glass; thigh grind to a second orgasm. |
| `sheer_flash_sex_m` / `_2_m` | Parking structure stairwell (P2). Photo shoot, "Open it. For the camera."; blowjob on the landing into the lens while someone watches from above; part 2 she rides him on the stairs while he shoots from below. |
| `sheer_flash_sex_f` | Same stairwell. She directs the shoot, eats Kelsie on the steps shooting blind; Kelsie takes the phone ("My turn"), then a 69 on the landing with the flash on a timer. |
| `sheer_snag_sex_m` / `_2_m` | His office tower elevator; Kelsie hits the stop between floors. Every try to pinch the tear shut finds skin and widens it. Fingers at the mirror under the camera; part 2 pinned to the wall while she answers the security intercom. Doors open on fourteen. Garment +30 damage. |
| `sheer_snag_sex_f` | A café's single restroom. Her safety pins find skin; the tear doubles; "Rip it." Fingers on the sink counter with a hand over Kelsie's mouth while someone tries the door; clit to clit at the counter's edge. Garment +30. |

- Male `onEnter` (part 2): `completeSexConsensualMale({ time: 30, hygiene: -20, partner: 'stranger', skipPregnancy: <condom>, thrill: 20 })`. Female: `completeSexConsensualFemale({ time: 30, hygiene: -15, thrill: 20 })`. Part 1 / single scenes call `trackGalleryScene`. Snag scenes add +30 to the snagged garment (capped at 99).
- Gallery: six entries in Exhibitionism right after "Slums: Sheer Exhibition," labeled "Discovery: The Backlight (Male)" and so on, tiers `[{l:'Deeply Corrupted',v:8001}]`. Male entries have `galleryChain: { text: 'Go without', nextScene: '…_2_m' }`. Part 2 `galleryOnEnter` sets the condom flag false in gallery mode. Snag `galleryOnEnter` presets `_sheerDiscovery.zone = 'chest'`.

---

## Part D: Step 19, Masturbation Integration (NEXT)

**Before writing:** ask the user to upload `VampireGirl_Masturbation_Standards.md` and follow it. The masturbation register is from inside her body; she knows it. Expect the user to want one scene or one tier at a time, each one to "sing."

### D1. Spec (verbatim intent)

> **19A. Alley relief scenes.** `masturbate_braless_alley`, `masturbate_commando_alley`, `masturbate_both_alley`: add sheer branches to the opening and closing paragraphs when the relevant zone is see-through. She's been on display all day, not just rubbed raw. She doesn't need to lift anything to watch her fingers work under a mesh skirt. The mesh scratch replaces the cotton scrape. No new gallery entries; these are variants.
>
> `getBralessCommandoAlleyChoice()` labels get sheer variants: "Duck into an alley (everyone's been looking)."
>
> **19B. Exhibition masturbation (alley + park bench).** `exhibition_masturbate_alley` and `exhibition_masturbate_park_bench` branch on exposure. Add a `sheer` branch (read `getSheerState()` when exposure is `clothed` or `underwear_only`, and the gallery exposure flag `=== 'sheer'` in gallery mode). Two tiers, Corrupted / DC, matching the other branches. Masturbation Standards compliant.
>
> The sheer hook: she touches herself through the mesh first. Only at the edge does she get her fingers under it.
>
> Gallery entries: "Alley: Sheer," "Park Bench: Sheer," same tiers as their siblings. Gallery-mode garment defaults to the Off-Shoulder Mesh Mini Dress, braless and commando.

### D2. 19A targets (current code)

- **`getBralessCommandoAlleyChoice(districtId)`** (~24534):
  - Fires at arousal 80+, when braless and/or commando, not in an overt exhibition state, once per day per scene type (`_bralessCommandoAlleyUsedToday`).
  - It uses **raw** `!o.bra && !!(o.top || o.dress)` / `!o.panties && …` checks. Those match `cv.braless` / `cv.commando`, so a mesh dress with no bra already counts as braless, and a mesh bra under mesh still counts as a bra (correct per the standards). You can switch it to `cv.braless` / `cv.commando` for consistency; the behavior is identical.
  - Labels today: both "Duck into an alley (the friction is unbearable)"; braless "(your nipples won't stop)"; commando "(the friction won't stop)".
  - **Sheer variant:** when the relevant zone is veiled (`cv.seeThroughChest` for braless, `cv.seeThroughHips` for commando, either or both for "both"), use "Duck into an alley (everyone's been looking)." Keep the same scene IDs.
  - It stores `gameState.flags._bralessAlleyReturnScene = districtId`.
- **`masturbate_braless_alley`** (~105048), **`masturbate_commando_alley`** (~105084), **`masturbate_both_alley`** (~105116):
  - Each is "one version, peak quality," not corruption-tiered, built as a single `textContent` string of `<p>` paragraphs, with its own alley setting (storefront alley; chain-link dead end; neon-lit alley behind a bar).
  - Each `onEnter` applies masturbation effects; choices return "Compose yourself" to `_bralessAlleyReturnScene`.
  - **The work:** add sheer opening and closing branches when the zone is see-through (`cv.seeThroughChest` / `cv.seeThroughHips`, or `_sheerOwnsZone(zone)`). Opening: she's been on display all day, strangers saw her nipples and slit through the mesh. Closing: the mesh goes back over a body that still shows through it. Mid-scene: where the scene lifts or pushes aside fabric for a veiled zone, she doesn't need to; she watches her fingers through the mesh. The mesh scratch (the grid dragging on her nipples or folds) replaces any cotton scrape. Use `b.noun` so fishnet and leopard read right.
  - Read each scene in full first; decide per scene which paragraphs need a sheer branch. The Masturbation Standards govern the middle.
- **No gallery entries for 19A.**

### D3. 19B targets (current code)

- **Entry points:**
  - `getExhibitionMasturbationChoice(districtId)` (~24499): corruption 6001+, thrill 60+, arousal 70+, and today's `_exhibMasturbationUsedToday.alley` unused. It doesn't check exposure, so it already fires while clothed or in sheer. It opens `exhibition_masturbation_choose` (~173190), whose "Duck into that alley." shows `exhibition_masturbate_alley`.
  - The park bench choice sits in the park's choices (~139606): same gates, "Touch yourself on a bench" to `exhibition_masturbate_park_bench`, once per day (`_exhibMasturbationUsedToday.park_bench`).
- **`exhibition_masturbate_alley`** (~173209):
  - `var exposure = gameState.flags._galleryMode ? (gameState.flags._alleyMastGalleryExposure || 'clothed') : getExposureLevel();`
  - A "physical setup" block branches `naked` / `topless` / `bottomless` / `underwear_only` / else (clothed), and later lines use inline ternaries on `exposure`. The DC split is `if (corruption >= 8001) … else …` near the end.
  - `onEnter`: marks `_exhibMasturbationUsedToday`, applies masturbation effects, +15 extra corruption, exhibition stats; choices may roll `rollWatcherEncounter()`.
- **`exhibition_masturbate_park_bench`** (~173326): same pattern with `_parkBenchMastGalleryExposure`.
- **The work:**
  1. Compute a sheer exposure value: in live play, when `getExposureLevel()` is `clothed` or `underwear_only` and `getSheerState().state` is a qualifying sheer state, use `'sheer'`; in gallery mode use the override flag. **Confirm with the user** whether `sheer_framed` qualifies (recommend: no, framed isn't bare skin; the spec's intent is braless, commando, bare and lingerie). Also confirm whether the sheer lingerie states use this branch (recommend: yes, they're see-through underwear in public).
  2. Add a `'sheer'` branch everywhere the scene branches on `exposure`, two tiers (Corrupted 6001 to 8000 / DC 8001+). The hook: she touches herself through the mesh first, rubbing her nipple and clit through the grid, and only at the edge gets her fingers under it. Use `_sheerBodyCtx()` and zone awareness: a braless-only look has an opaque bottom, a commando-only look has an opaque top.
  3. Add a guarded `galleryOnEnter` to each scene that dresses her in the Off-Shoulder Mesh Mini Dress, braless and commando, **only when the active override is `'sheer'`**, so the five existing gallery variants behave as before:
     ```javascript
     galleryOnEnter: function() {
         if (!gameState.flags._galleryMode || gameState.flags._alleyMastGalleryExposure !== 'sheer') return;
         gameState.currentOutfit = Object.assign(gameState.currentOutfit || {}, {
             top: null, bottom: null, bra: null, panties: null, outerwear: null,
             dress: { id: 'bo_off_shoulder_mesh_mini_dress', name: 'Off-Shoulder Mesh Mini Dress', category: 'dresses', appeal: 70, tags: ['provocative', 'material:mesh', 'occasion:club'] }
         });
     },
     ```
     (Park bench reads `_parkBenchMastGalleryExposure`.)
  4. Gallery entries (Exhibitionism, after the "Clothed" siblings at ~47982 and ~47987):
     - `{ sceneId: 'exhibition_masturbate_alley', label: 'Alley: Sheer', tiers: [{l:'Corrupted',v:6001},{l:'Deeply Corrupted',v:8001}], flagOverrides: { _alleyMastGalleryExposure: 'sheer' } }`
     - `{ sceneId: 'exhibition_masturbate_park_bench', label: 'Park Bench: Sheer', tiers: [{l:'Corrupted',v:6001},{l:'Deeply Corrupted',v:8001}], flagOverrides: { _parkBenchMastGalleryExposure: 'sheer' } }`
     - The older siblings use an em dash in their labels ("Alley — Naked"). New overhaul entries use a colon, per the spec. Keep the colon.
  5. Test: every sheer outfit × 45 bodies × both tiers × gallery and live; confirm the other five gallery variants still render unchanged; confirm `galleryOnEnter` leaves a live outfit alone.

---

## Part E: Step 20, Tips & Guide

### E1. Where the guide text lives (current lines, approximate)

The guide is static HTML near the top of the file (inside the first ~12,300 lines). Labels are UI text (exempt from contractions); the prose inside entries follows the style guide and the crude, anatomical register of the existing Exhibitionism entry.

| Location | ~Line | What it says now | Action |
|---|---|---|---|
| Clothing Systems: "Upscale Doors" | 7087 | Lists what passes the Lucius / Regency doors: any Noir piece, Urban Edge evening dresses (mesh club, bodycon, wrap, skater), Jaewon's dresses, BO dresses. | Name the two new Noir mesh dresses (Mesh Party Dress, One-Shoulder Mesh Club Dress). No bodysuit is mentioned anymore; confirm with grep. Add the Lucius doorman's reaction to sheer looks. |
| Clothing Systems: "Wet Clothes" | 7097 | "Wet clothes become see-through…" | Add wet mesh: mesh was see-through before the rain; wet and sheer thrill don't stack except the one-zone case. |
| Clothing Systems: "Sheer Pieces" | 7113 | "The Sheer Mesh Mini Skirt and the Sheer Leopard, Off-Shoulder Mesh, and Rainbow Fishnet dresses are see-through by design. Worn in public, each builds +1 Exhibition Thrill per hour, and +1 more if you're bare behind it. **Sheer never counts as visible underwear, so they're always street-legal.**" | **Rewrite.** It's out of date and the last sentence is false. Delete it. Point to the new Mesh & Sheer subsection. |
| Clothing Systems: "Visible Underwear" | 7124 | "You can't willingly go outside in underwear, topless, or bottomless without 6001 Corruption. The only exception is if your clothes got destroyed in combat." | Add the sheer gates: sheer bra and sheer panties with nothing over them need **8001**; one sheer piece shown alone needs 6001; the mesh-over-skin gates (braless 2001, commando 4001, bare 4001). |
| Exhibitionism: "Exposure States" | 7133 | Clothed, Underwear Only, Topless, Bottomless, Naked, Outerwear Concealment, Visible vs Actual. | Add a Sheer line (framed / bare behind mesh / sheer lingerie), pointing to the subsection. |
| Exhibitionism: "Thrill Sources" | 7158 | "…sheer pieces in public (+1/hr, +2/hr when braless or commando behind them)…" | **Wrong numbers.** Replace with the real sheer rates (B5): framed +1, braless +3, commando +4, bare +6, sheer bra or panties alone +9, full sheer set +12, × weave, stacking with braless/commando. |
| Exhibitionism: "Wet Clothing" | 7159 | Wet see-through thrill. | Note that wet and sheer don't stack (higher applies) except when exactly one zone is sheer. |
| Exhibitionism: "Decay" | ~7160 | "Thrill doesn't decay while in any exposure state, underwear-only and sheer pieces included." | Still true. Add: in private scenes sheer doesn't hold off decay. |
| Exhibitionism: "Accidental Exposure Events" | ~7175 | Wind, escalator, etc.; two systems (top, bottom). | Add the 8 sheer events and the 20 events' mesh behavior (E3). |
| Exhibitionism: "Deliberate Exhibition Actions" | ~7195 | Flash, Pose, Watcher Encounter. | Add Stop covering up / Step into the light / Peel the mesh down, and the sheer versions of pose and flash. |
| Exhibitionism: "Exhibition Seduction" | 7207 | "Requirements: … currently in an exhibition exposure state (underwear, topless, bottomless, or naked)." | Add sheer routing and the see-through underwear bonus. |
| Exhibitionism: "Exhibition Masturbation" | ~7235 | Alley, park bench, park clearing. | Add the sheer branches from Step 19. |
| Exhibitionism: "Slums Integration" | ~7240 | Exposure-aware harassment; passout. | Add sheer harassment, the slums sheer scene, and the single "They take you away" continuation. |
| Exhibitionism: "Arrest & Evidence" | ~7250 | Severity: underwear minor, topless/bottomless moderate, naked major; Cruz after 3+ entries. | Add sheer severities, the night camera bump, the weave odds, and Cruz's sheer lines. |
| Exhibitionism: "Ambient Interiority" | ~7258 | Exposure-state pop-ups. | Add sheer ambient lines and zone ownership. |
| Exhibitionism: "Wet Clothes & See-Through" | 7280 | Full wet system and its gallery list. | Add wet mesh lines; mention "District/Slums Wet Exhibition" are now reachable at every tier via "They take you away." |
| Exhibitionism: "Bus" | ~7330 | "The city bus is blocked while in any exhibition exposure state." | Add sheer transit: a sheer bra or panties alone is refused by bus and Uber (Uber −0.2 rating); bare or commando behind mesh boards, and the driver reacts on arrival. |
| Exhibitionism: "Gallery" / "Strategy" | ~7332 | Generic. | Add the mesh gallery scenes (E4) and the sheer strategy lines. |
| Exhibitionism: "Going Braless & Commando" | 7348 | The braless/commando subsection. | **Put the new "Mesh & Sheer" subsection right after this one.** Also note that a zone bare behind mesh is owned by the sheer system (its ambient and friction lines replace the braless/commando ones for that zone). |
| Thrill stat description block | 12287 | "Thrill Gain — Exposure States: … Sheer pieces (in public): +1 per hour, +2 when braless or commando behind them…" | **Wrong numbers.** Replace with the real rates. Decay line still true. |
| Stats panel labels | (code) | Already shows sheer rows. | Mention in the guide: Time in sheer, Time sheer & bare, Time in sheer lingerie. |

Grep anchors: `Sheer Pieces`, `Thrill Sources`, `Upscale Doors`, `Visible Underwear`, `Exposure States`, `Going Braless`, `Thrill Gain &mdash; Exposure States`, `Exhibition Seduction`, `Wet Clothes`.

Also double-check whether a Scene Gallery count appears anywhere in the guide for the Exhibitionism category and update it if so (the Jaewon section has one: 48, unchanged by these steps).

### E2. New subsection: "Mesh & Sheer" (inside the Exhibitionism entry, after "Going Braless & Commando")

Cover, in the entry's crude, anatomical register, with short headed paragraphs like the neighboring subsections:
1. **What mesh is.** The eleven sheer pieces (B1), their weaves (sheer, semi-sheer, fishnet; fishnet leaves bare skin in every hole), and the two Noir dresses.
2. **The five looks** in plain terms: framed (a bra or panties on display behind the mesh: legal, venue-safe, a deliberate look); braless behind mesh (nipples on show); commando behind mesh (her bare slit on show); bare (naked in a dress); sheer lingerie (sheer bra and/or panties with nothing over them: "naked with a technicality"). Each look's leave-home corruption gate (B2).
3. **Passive effects:** the thrill / corruption / mesh-scratch rates (B5), the weave multiplier, stacking with braless and commando, the Sheer Showcase appeal bonus (25% of the naked appeal bonus per see-through zone, capped at 50%), banners and the stats rows.
4. **What she feels and hears:** ambient interiority (30-minute cooldown, street scenes), framed ambient lines, mesh friction (the grid on her nipples or folds), district vignettes (strangers reacting to each look) and Kelsie's responses, district awareness lines, Ruby at the diner, the Lucius doorman, Hardy Bar, style comments (none for sheer lingerie).
5. **Accidental events:** the eight sheer events (E3), how the 20 existing exhibition events change with mesh, and the Discovery follow-ups at 8001+.
6. **Deliberate actions:** Stop covering up, Step into the light, Peel the mesh down, sheer pose and flash (E3 table).
7. **Risk:** harassment and night-assault bonuses, evidence severities, Cruz, venue rules, transit, the sheer lingerie police clock.
8. **Jaewon:** her reactions to Kelsie in mesh, "That's my top," the friend-tier reaction, "Come take it back." and "Her Mesh Top."
9. **Gallery scenes and their triggers** (E4).
10. **Strategy:** framed is free and safe; braless under mesh is the cheapest real exposure in the game; sheer lingerie is naked with better odds with the police and a smaller thrill payout.

### E3. Fact sheet: everything the overhaul built (document what's real)

**Gates and venues**
- Leave-home gates: braless behind mesh 2001; commando or bare 4001; a sheer bra or panties shown alone 6001; the full sheer set 8001; framed none. Below the gate she refuses at the door with a crude, body-aware "no way" block (unless her clothes were destroyed in combat). The riverside park has no gate, like naked.
- Venues (shown as a block message):
  - framed: allowed everywhere.
  - braless behind mesh: blocked at the hospital, library, blood bank and dojang.
  - commando or bare: allowed only at Lucius' Lounge, Hardy Bar, Urban Edge and the diner.
  - a sheer bra or panties alone: blocked everywhere.
- Sheer lingerie police: a 3-hour clock starts once she's out in the full sheer set; officers arrive with lines about the technicality ("It's see-through, Dave. That's the whole point of it."), then the naked escape flow.
- Transit: a sheer bra or panties alone is refused by the bus and Uber (Uber costs 0.2 rating). Bare or commando behind mesh can ride; the driver reacts on arrival ("Nice dress").
- Ruby (diner job, weekdays 7 to 17, bare or commando, once a day): exasperated under friendship 20, dry at 20 to 49 ("Bold choice for a Tuesday"), protective at 50+ (a cardigan, or an apron tied round her waist).
- Lucius doorman: 3 lines per sheer look, once a day, evening or night.
- Hardy Bar: once a day walking in bare or braless behind mesh (a comped drink or a table going quiet).

**Vignettes and interiority**
- District vignettes on street entry: 34 NPC reactions (framed 4, braless 6, commando 6, bare 6, lingerie 10) and 42 Kelsie responses (three tiers: under 2001 shame, under 6001 conflicted and wet, 6001+ deliberate). Framed fires 30% of the time. Stats per vignette: braless thrill 10, commando 12, bare 16, lingerie 24, × weave × draw; social heat by day outside the slums and residential.
- Ambient lines (30-minute cooldown), framed lines (45-minute), mesh friction, lingerie ambient lines, district awareness once per district per day.
- Wet mesh: rain beads on skin through the grid; wet and sheer thrill don't stack except the one-zone case (for example a mesh crop braless over wet jeans: braless 1 + mesh 3 + wet 4 = 8/hr).
- Style comments: 4 per look (framed, braless, commando, bare); none for sheer lingerie.

**Accidental events (15% per district transition, 60-minute cooldown)**
- Eight sheer events: **Backlight** (light pours through the mesh), **Flash** (a tourist's flash goes through it; at 6001+ the photographer always keeps the shot, +10 social media heat; below that 60%), **Snag** (a watch, zipper or fence wire tears the mesh; +8 base damage scaled by fabric, never destroys the garment), **Diamond** (fishnet over a bare chest: a nipple through a hole), **Ribbon** (Mesh Party Dress slit tie), **String** (One-Shoulder dress halter), **Reach** (Mesh Crop Top underboob), **Double Take** (a woman does a double take; bare or commando).
- Scaling: thrill and arousal × weave × look (framed 0.5, braless or commando 1.0, bare 1.4) × draw.
- Sheer lingerie gets none of these (it has vignettes and police instead).
- A zone bare behind mesh is owned by the sheer pool; the opaque braless/commando events stand down for it.
- **Discovery follow-ups (Step 18):** at 8001+, bare behind mesh, a Backlight (after dark only), Flash or Snag stranger can follow her. "Let him/her follow." or "Keep walking."
- The 20 existing exhibition events: hem events fire at half chance when the skirt or dress is sheer, top events at half chance when the top or dress is sheer (the wet and spill top events never fire on a sheer top). Sheer underwear under a lifted garment pays 75% of the bare reward (never less than the opaque value). The bus event gets a mesh line.

**Deliberate actions** (bare behind mesh only; commercial, residential and downtown choice lists):

| Action | Gate | Limit | Thrill / arousal / corruption |
|---|---|---|---|
| Stop covering up. | 4001+, thrill 30+ | Once a day | +10 / +8 / +25 |
| Step into the light. | 6001+, thrill 50+ | 60-minute cooldown | +18 / +12 / +20, × weave × draw |
| Peel the mesh down. | 8001+, thrill 70+, bare chest behind mesh | 45-minute cooldown | +25 / +15 / +15, suspicion +2 |

- Pose accepts sheer and bare looks and the full sheer set with sheer versions of each pose. Flash top and flash skirt add a paragraph when the lifted garment is sheer.

**Harassment and assault**
- Harassment chance bonus: framed +0.03, braless or commando +0.10, bare +0.20. Night assault: framed +0.03, braless or commando +0.07, bare +0.10 (wet and sheer don't stack at night; the higher applies).
- Harassers see through the mesh (each look has its own opener) and grope her through it; grope stats: lingerie 25 arousal / 25 corruption, bare 22/25, braless or commando 18/20.
- In any exhibition state (sheer included) the grope continues into its scene with a single "They take you away." District: `district_sheer_exhibition`; slums: `slums_sheer_exhibition`. Sheer routes ahead of wet (mesh was see-through before the rain). Framed keeps the normal choices.
- They tear the mesh instead of stripping it (outer mesh +40 damage, mesh panties +60, capped so she walks out still dressed in it).
- Slums passout assault has sheer inserts.

**Evidence and Cruz**
- Framed: no entry. Braless or commando behind mesh: minor (chance × weave). Bare: moderate (× weave). Any sheer lingerie: moderate. A night camera (after 8 PM in camera districts) bumps a sheer entry one level, so sheer lingerie can reach major.
- Weave changes the odds (commercial's 30% becomes 33% for fishnet, 21% for semi-sheer).
- Cruz: sheer entries count toward her 3+ pattern. When more than half of Kelsie's indecent exposure file is sheer, she says so ("Three stills. Same see-through dress. You know exactly what the cameras see." / "Most of it's technically clothed.").

**Exhibition Seduction through the mesh**
- Corrupted (6001+), 80+ appeal, dressed or in underwear: braless or a sheer bra alone → Topless branch; commando or sheer panties alone → Bottomless; bare → Naked; the full sheer set → Underwear branch with naked-level rewards (a "See-through underwear bonus"); framed → regular seduction.
- The stranger calls it out ("You know I can see your nipples through that, right?" "I know.") and peels the mesh before the first touch.

**Jaewon**
- As Kelsie's girlfriend (official girlfriends): Jaewon reacts to Kelsie bare behind mesh, by Jaewon's own corruption (Innocent: clutches a cushion, "Kelsie. I can see everything."; Experienced: "It's like touching you naked… Except worse."; DC: "You look naked and you're dressed and I can't decide which one makes me wetter.").
- Kelsie in Jaewon's top: "That's my top." lines (braless under it, or a bra framed behind it).
- Friend tier: a flustered once-a-day reaction.
- "Come take it back." (15 minutes after a braless "That's my top," girlfriends, Jaewon 4001+, once a day) → "Her Mesh Top." Experienced tier: the top goes back to Jaewon's wardrobe afterward. DC tier: Kelsie keeps it.

> Note for the guide writer: Jaewon is a woman; the guide's Jaewon section already describes "Her Mesh Top." Don't duplicate it; cross-reference.

### E4. Mesh gallery scenes and their live triggers (for the guide's gallery list)

| Gallery entry | Category | Tiers | Live trigger |
|---|---|---|---|
| District: Sheer Exhibition | Exhibitionism | Innocent / Experienced / DC | Street harassment grope while bare behind mesh or in sheer lingerie → "They take you away." |
| Slums: Sheer Exhibition | Exhibitionism | Innocent / Experienced / DC | Slums harassment grope, same states → "They take you away." |
| Discovery: The Backlight (Male / Female) | Exhibitionism | DC | Backlight event after dark, 8001+, bare behind mesh → "Let him/her follow." |
| Discovery: The Flash (Male / Female) | Exhibitionism | DC | Flash event, 8001+, bare behind mesh → follow |
| Discovery: The Snag (Male / Female) | Exhibitionism | DC | Snag event, 8001+, bare behind mesh → follow (garment +30 damage) |
| Alley: Sheer (Step 19) | Exhibitionism | Corrupted / DC | Public masturbation choice (6001+, thrill 60+, arousal 70+) in a sheer look |
| Park Bench: Sheer (Step 19) | Exhibitionism | Corrupted / DC | Park bench choice, same gates, in a sheer look |
| Her Mesh Top | Jaewon | Experienced / DC (Jaewon's corruption) | "Come take it back." (see E3) |

Every tier of every exhibition scene is now reachable in live play; the guide can say so.

---

## Part F: Step 21, Save Migration & Final Sweep

### F1. Flag defaults

Add each to the migration block beside the Braless/Commando block (grep `bothMinutesTotal === undefined`, ~16770), every one guarded with `=== undefined`, **and** to the initial `gameState.flags` defaults (~13800) so new games start with them:

- `_passiveAccum: {}`
- `_lastSheerExposureAmbientTime: 0`
- `_sheerDistrictAwarenessFired: {}`
- `_sheerNpcVignetteIndex: []`
- `_wetAwarenessJustFired: ''`
- `_luciusSheerDoorDay: 0`, `_rubySheerLineShift: 0`, `_hardySheerLineDay: 0`
- `_sheerStopCoveringDay: 0`, `_lastSheerLightMinute: 0`, `_lastSheerPeelMinute: 0`
- `_seductionSheer: null`
- `_jaewonMeshTopReactionDay: 0`, `_jaewonMeshTopReactionMin: 0`, `_jaewonMeshTopFromRoom: null`, `_jaewonMeshTopSexDay: 0`
- `_districtSheerExhibPregnancy: false`, `_slumsSheerExhibPregnancy: false`
- `_sheerDiscovery: null`, `_sheerDiscoveryCondom: null`
- Top level on `gameState` (not in flags): `sheerLingerieStartTime: null`
- `exhibitionStats.sheerMinutesTotal`, `sheerBareMinutesTotal`, `sheerLingerieMinutesTotal`: 0

Already present before the overhaul (don't re-add): `_lastJaewonBralessReactionMin`, `_jaewonBralessReactionCounter`.

Check each name with grep against the live code before adding; if Step 19 adds any flags, add them too.

### F2. Item migration

- Replace `designer_bodysuit` → `mesh_party_dress` and `lace_bodysuit` → `one_shoulder_club_dress` everywhere a saved item can live: `inventory.clothing`, every `gameState.wardrobes[*]`, `currentOutfit.dress`, and `outfitPresets`. Build each replacement from the Noir definition, keep `cleanliness`, `damage` and `wetness`, and show one notification on load if anything was swapped.
- Rewrite saved `jaewon_mesh_top` copies from `material:synthetic` to `material:mesh`.
- No sheer flag migration is needed; the registry reads item IDs.

### F3. Test sweep (from the spec, plus later additions)

Walk every path:
- Each sheer look entered at each gate boundary (2000/2001, 4000/4001, 6000/6001, 8000/8001), including the combat-destruction exception.
- Full coat, partial coat, and no coat over each look.
- Stacked sheer (mesh bra under a mesh top, mesh panties under a mesh skirt).
- A mesh top over an opaque skirt, commando (sheer chest plus the regular commando banner together).
- Passive accrual over a day of 2-minute ticks vs one long tick (should match within rounding).
- Banners, stats panel, and ambient yield order (sheer exposure, then both, then braless/commando, then framed).
- Vignettes for every look in every district; Ruby; Lucius; Hardy Bar.
- All 8 sheer accidental events; the 20 existing events with mesh underwear and mesh outer garments.
- Harassment and slums routing for every look, the "They take you away" choice at every tier, night assault odds, passout inserts.
- Evidence severity per look, the night camera bump, Cruz lines.
- Deliberate actions at their gates and cooldowns.
- Exhibition Seduction from each sheer look (male and female).
- Jaewon: girlfriend tiers, her top, friend tier, gallery scene.
- The Discovery follow offer for each event and gender, including "Keep walking." and the condom paths.
- All new gallery entries in gallery mode, every tier, and **every gallery scene through the real `showScene` with `_galleryMode` false**, to confirm no `galleryOnEnter` changes live state.
- Wet mesh in rain; cold weather lines.
- A save from before the overhaul holding a bodysuit, Jaewon's mesh top, and a Beauty Obsessed sheer dress.

### F4. Final checks

- `node --check` passes on both script blocks.
- Style scan every string the overhaul added or touched: em dashes, contractions, "the way," "the kind of," Not/Not, negation-before-reveal, "genuinely," "A beat," paragraph density. A useful sweep: `git diff <pre-overhaul commit> -- vampire-girl/Vampire_Girl.html | grep '^+'`. The commit before Step 1 is `e1bb7b0`.

---

## Part G: Open Items and Traps

- **Paragraph walls in older exhibition scenes.** The ten non-sheer district and slums exhibition scenes (underwear, topless, bottomless, naked, wet) hold about **385 distinct paragraphs over 300 characters**. They predate this overhaul (session 3 split only the ones it touched). The user deferred them; a natural home is the Step 21 sweep. Ask before doing it.
- **Unfixed floor bug (out of scope).** Jaewon proximity arousal, companion proximity arousal and the desperation MH drain in `advanceTime()` still use `Math.floor(hoursPassedPartial * rate)`, which is 0 on short ticks. Each is a one-line fix with `accruePassive`. Offer it; don't do it unasked.
- **Stranger NPC thoughts in the Exhibition Seduction scenes (pre-existing).** Those scenes contain `<em>He thought:</em>` lines for anonymous strangers, which the Sex Standards reserve for romance characters. Offer a cleanup; don't do it unasked.
- **Deferred "cotton" pass.** About 273 "cotton" mentions remain; most are legitimate (item definitions, uniforms, Jaewon's wardrobe). Candidates for a later pass: the Jaewon bedroom and sleep-touch scenes, the wet gallery scenes, `night_rape_main_female`. Not part of this overhaul.
- **Braless/commando thrill at home** stays unguarded by design.
- **Match fabric words to the registry noun.** Use `b.noun` / `cv.meshNoun` ('mesh', 'fishnet', 'sheer fabric') and the garment's own details (fishnet diamonds, leopard spots).
- **District street `onEnter` order** (for any new hook): exposure state → underwear/topless/bottomless block → `handleSheerDistrictEntry` (gate and vignette) → `tickSheerLingeriePolice` → `checkAccidentalExposure` (returns true to end `onEnter`; its popup's close callback can open the Discovery offer) → hit squad → harassment → Cruz → random district event → hero event → style comment → temperature → degradation → naked police.
- **`showScene()` check order:** Ruby intercept → sheer venue block → slot exposure block → ambient chain (arousal → exhibition ambient → evidence → wet chain → sheer exposure → both → braless → commando → framed → friction).
- **Gallery label style.** Older entries use an em dash ("District — Wet Exhibition"); overhaul entries use a colon ("District: Sheer Exhibition"). Keep the colon.
- **Scene Gallery sets `gameState.corruption` directly.** Use `flagOverrides` for other state; never invent corruption flags. Deeply Corrupted is always 8001.
