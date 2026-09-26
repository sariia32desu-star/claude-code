# Mesh & Sheer Clothing Overhaul — Session Handoff

**Status at handoff:** Steps 1–8 of 21 are complete, plus a set of approved fixes between steps. Steps 9–21 remain.

**This document stands alone.** You don't need the original `Mesh_Sheer_Clothing_Overhaul.md`:
- Part F reproduces every remaining step verbatim.
- Part G reproduces the original "Existing Systems" reference verbatim.
- Part E carries the handoff notes that correct or extend the original spec, based on what's in the file now. **Where Part E and Part F disagree, Part E wins.**
- The only change from the original steps' text is heading levels, so each step fits under its Part.

---

## Part A — Orientation

### A1. Where the work lives

- **Repo:** `sariia32desu-star/claude-code`
- **Branch:** `claude/vampire-girl-mesh-clothing-jk9h5c`. All work is committed and pushed.
- **Working file:** `vampire-girl/Vampire_Girl.html`. About 219,680 lines at handoff; the original upload was 217,995.
- **Script blocks:** 5830–5896 and 12690–219678. These numbers shift with every edit, so always re-grep `<script>` / `</script>`.
- **Line numbers in this doc are approximate.** Always `grep -n "function name("` before editing.

Commits, oldest first:

| Commit | Content |
|---|---|
| `ab1582c` | Step 1: bug fixes and the Noir mesh dresses |
| `fc3f60d` | Step 2: registry, `getSheerState()`, sheer-aware `cv` |
| `ccaa115` | Step 3: fabric awareness pass |
| `8fd9b38` | Post-Step 3 fixes: slums "black set," puddle lace, 10 em dash connectors |
| `d953879` | Step 4: gates, police, venues, transit |
| `30daf56` | Step 5: passive mechanics, showcase appeal |
| `00ee697` | Step 6: banners, lingerie box, stats panel |
| `5e73b37` | Step 7: ambient interiority, mesh friction |
| `a6860c2` | Step 8: district vignettes, Ruby, district awareness |
| (handoff commit) | This doc, plus a style sweep of every line the overhaul touched (see D10) |

### A2. Working rules from the user (non-negotiable)

- **Always work from the latest file**: the one on the branch above, or a newer file the user uploads.
  - Never copy an earlier version over it.
  - If the user uploads a newer `Vampire_Girl.html`, that file becomes the working file.
- **One step per output.** Wait for confirmation before starting the next step. Don't bundle steps. Fixes the user asks for between steps are fine.
- After each step:
  - Commit with a clear message and push to the branch.
  - Send the user the updated file.
  - Summarize what was done, every deviation, and every judgment call, so the user can approve them.
- The user reviews each step and expects the style guide to be applied with no exceptions.

### A3. Writing rules (style guide + user preferences + body branching standards)

**POV and tense:** strictly 2nd person present. Any other POV or tense is the top-priority error.

**Banned:**
- **Em dashes**, except in two cases:
  - Paired parenthetical asides: `His voice—low and careful—cut through.`
  - Dialogue or vocal cut-offs: `"I can't just—"`, `*ah—*`, and stammers like `"Deeper—please—"`.
  - Every lone connector dash is banned, spaced or unspaced. Use a comma, period, colon or semicolon instead.
- **Formal language.** Always contract: she's, you're, it's, doesn't, can't, won't, I'm, I've, we're, there's, hasn't, wasn't, and so on.
  - This applies to all prose and all dialogue, dramatic moments included.
  - When English can't contract (sentence-final "how wet you are," "small as you are," "You are, technically"), rewrite the sentence.
  - Exempt: code comments, code logic, HTML attributes, UI and guide labels, and the `<video>` fallback text.
- **"The kind of … that" / "the kind that."**
- **"A beat." / "A pause."** in any form, including "a beat longer." Use an action, body language, or "a moment longer."
- **Paired or tripled negative inventories:** "Not X. Not Y." / "No X. No Y." / "Nothing X. Nothing Y."
  - A single "Not" is fine.
  - Natural dialogue ("Not bad for your first day") and panic italics (`<em>Not now. Not in front of her.</em>`) are fine.
- **Negation before reveal**, in any variant: "Not because X. Because Y." / "It wasn't anger. It was exhaustion." / "He lets you, not because…, but because…". Just state it.
- **The stanza formula**: "You're not the star. / You're the girl coaches trust…". No verse and no line-by-line reveals.
- **"Not a question."**
- **"genuinely"** modifying an emotion or state. It's allowed sparingly in natural dialogue, as is "Honestly?". "Straightforward" is fine when it describes a thing.
- **"The way" and "In a way," with no exceptions.** Even idioms get rewritten: "all the way down" became "straight down," "on the way" became "on the walk there," "the rest of the way in" became "the rest of himself in," and "all the way to the bar" became "every step to the bar."
- **Paired "Something X. Something Y."**
- **Passages built entirely from two-adjective rhythm pairs.** One pair per passage is fine.
- **AI vocabulary** (delve, tapestry, testament, multifaceted, nuanced) and purple prose.

**Required:**
- **Short, terse paragraphs.** No paragraph over about 300 visible characters; split them with `\n\n` or `</p><p>`, matching the scene's existing encoding.
- **Crude, anatomical, explicit language in every erotic moment and at every corruption tier.** Use nipples, tits, pussy, clit, cunt, slit, folds, slick. No euphemisms.
  - Low corruption is shame with a body that won't stop reacting, and it's still crude.
- **"Cum" for orgasm, never "come."** "Come up the block" isn't about orgasm and is fine.
- **Dialogue sounds spoken.** People clip words, trail off and interrupt. Nobody gives speeches.
- **Keep children out of this content entirely.** The doc's sample of a woman covering her son's eyes was changed to "turns her whole body away."

**Technical rules for the HTML/JS:**
- **Every apostrophe inside a single-quoted JS string must be escaped as `\'`**, whatever follows it: contractions, possessives, and apostrophes before em dashes.
  - Never produce `\\'`.
  - Never insert apostrophes with sed `\x27`. Use Python or Edit.
- **Ternaries inside single-quoted strings** break out with *unescaped* quotes: `' + (_bs === 'large' ? 'a' : 'b') + '`.
  - After `: '')` or `: 'text')` you must reopen the string with `+ '`.
  - After every ternary, the next character must be `)`, `+` or `;`. Anything else is stranded text.
- **`${}` works only inside backtick template literals.**
- **Static `text:` properties that reference `cv`** must be converted to `text: function()`.
- **Run `node --check` on both script blocks after every step.** The recipe is in A4.

**Body branching standards (the user requires them in all prose steps):**
- Read body attributes from `gameState.bodyAppearance`. `bodyAttributes` doesn't exist.
- **`bodyType`:** petite, athletic, curvy, thick, or ordinary (the default). **Every bodyType ternary fills all five values.**
- **`breastSize` and `buttSize`:** large, small, or average (the default). It's always `'large'`, never `'big'`.
- **The ordinary and average branches must read as real prose,** not placeholders. `'hips rock up off the mattress'` is right; `'normal-sized hips'` is wrong.
- **Extract variables at the top of the text function:**
  ```javascript
  var _ba = gameState.bodyAppearance || {};
  var _bs = _ba.breastSize || 'average';
  var _butt = _ba.buttSize || 'average';
  var _bt = _ba.bodyType || 'ordinary';
  var cv = getSceneClothingVars();
  var _braless = cv.braless;
  var _commando = cv.commando;
  ```
- **Branching points (BPs) per scene:**

  | Scene length | Target BPs |
  |---|---|
  | Short or single-tier | 3–5 |
  | Standard | 4–8 |
  | Long or multi-tier | 6–10 |

  - Put `bodyType` BPs at undressing, position changes, build and orgasm.
  - Put `breastSize` BPs where her chest is touched or revealed.
  - Put `buttSize` BPs where her hips or ass are gripped, lifted or positioned.
- **Clothing awareness:**
  - Use `cv.braless` and `cv.commando`, never raw outfit checks. If you find `!gameState.currentOutfit.bra`, replace it.
  - Name garments with `cv.topName`, `cv.bottomName`, or `proseItemName(item)` for sheer garments.
  - Undressing and redressing branch on `cv.liftable` (hike up, smooth down) versus `cv.pulldown` (yank down, pull back up).
  - A dress is one garment and comes off in one action (`cv.isDress`). Braless and commando discoveries merge into that single removal moment.
  - Exhibition hem events are gated to a skirt or dress.
- **Register by scene type:**

  | Scene type | Register |
  |---|---|
  | Romance | Tender, hungry |
  | Stranger | Raw physical facts |
  | Assault | Visceral, invasive; her body seen from outside |
  | Masturbation | From inside her body; she knows it |
  | Exhibition | Heightened awareness of every sightline |
  | Predator | Triumphant, taking |

**Standing lessons carried from earlier overhauls:**
- **Scene Gallery sets `gameState.corruption` directly.** Never invent `_galleryCorruption`-style flags.
  - Use `flagOverrides` in the gallery registry for any state that isn't corruption; the alley masturbation entries show the pattern.
  - Gallery mode is `_galleryMode`.
- **Deeply Corrupted is always 8001.**
- **Braless and commando checks go through `cv.braless` / `cv.commando`.** Sheer checks go through `getSheerState()` or the sheer `cv` fields.
  - Never read `item.sheer`, `outfit.dress.name`, or a hardcoded ID list.

### A4. Validation and testing recipes

Syntax check of both script blocks (run it after every change):

```python
import re
s=open('vampire-girl/Vampire_Girl.html',encoding='utf-8').read()
for i,b in enumerate(re.findall(r'<script>(.*?)</script>',s,re.S)):
    open(f'SCRATCH/script{i+1}.js','w',encoding='utf-8').write(b)
# then: for f in SCRATCH/script*.js; do node --check $f && echo OK; done
```

**Runtime tests** use headless Chromium through Playwright, which is preinstalled. Run them with `NODE_PATH=$(npm root -g) node test.js`.

- Open `file:///home/user/claude-code/vampire-girl/Vampire_Girl.html` and wait about 3 seconds.
- Stub `window.showMessage = (t) => msgs.push(t)` to capture popups. Stub `window.showScene` too if needed.
- Fake items look like `{ id, name, category, appeal, tags: ['material:mesh'] }`.
- Set `gameState.currentOutfit` with all slots: top, bottom, dress, bra, panties, outerwear.
- Set `gameState.bodyAppearance` and `gameState.corruption`.
- Scenes live in the global `story[sceneId]`: `.text()`, `.onEnter()`, `.choices()`.
- `advanceTime(min)` needs `gameState.timeStarted = true`. The private-scene guard reads the global `currentScene`.
- Ambient handlers only fire for scene IDs in `_arousalAmbientStreets`.

**Prose QA method** (it caught real errors in Steps 7 and 8):
- Render every new prose function for all 45 body combinations × every outfit the state allows × each corruption tier.
- Scan the output for:
  - `undefined` and leftover `{tokens}`
  - paragraphs over 300 visible characters
  - the regex `the way|in a way|kind of|kind that|genuinely|a beat|a pause|—|not because|she is|you are|it is|do not|does not|cannot`
- Review every hit by hand.
- **Also scan the diff against the original upload** so pre-existing lines you touched get checked too.

---

### A5. The vision, verbatim from the original doc

**Target implementer:** Claude Opus
**File:** `Vampire_Girl.html`
**Companion docs:** `VampireGirl_Style_Guide_SS.md` (must be loaded for all prose), `VampireGirl_Sex_Standards.md` (gallery-eligible sex scenes), `VampireGirl_Masturbation_Standards.md` (gallery-eligible masturbation scenes), `VampireGirl_Body_Branching_Standards.md` (body branching + cv clothing awareness)
**Predecessors:** `Braless_Commando_System_Overhaul.md` (complete), Exhibition Overhaul (complete), Wet Clothing Erotic Overhaul (complete), Beauty Obsessed Overhaul Step 2E (sheer pieces, partial)

**Scope:** Mesh is the only clothing in the game built to be seen through. A mesh dress over a bra is a look. A mesh dress over nothing is Kelsie walking down the street with her nipples and her slit on display behind a layer of black haze that everyone can read. A mesh bra and mesh panties with nothing over them is naked with a technicality attached.

Right now the game barely knows the difference. Beauty Obsessed Step 2E laid a thin sheer layer: a `sheer: true` flag on four BO items, +1 thrill per hour, a handful of supplement lines. Six of the ten mesh pieces carry no flag at all and do nothing. The mesh bra and panties read as ordinary underwear. Nobody on the street reacts to a see-through dress. The harassment pipeline, the evidence ledger, the vignettes, the banners, the accidental events, Jaewon: none of them can tell that Kelsie's tits are visible through her top.

This overhaul makes mesh its own exhibition layer. It sits between braless/commando (hidden, felt, private) and topless/bottomless/naked (overt, seen, public). Mesh is seen and dressed at the same time, and that contradiction is the whole engine. Every stranger gets to look. Nobody can say she's naked. Her pussy's slick behind the grid and she's still technically wearing clothes.

When this is done, pulling on the Off-Shoulder Mesh Mini Dress with no bra and no panties should feel like the single most charged decision in the wardrobe short of stripping. The mesh lingerie set worn alone should feel like naked with a lawyer. And a mesh dress over a matching bra should feel like what it is: a deliberate, legal, public showcase that still makes her clit throb every time someone's eyes drop.


**Headline design intents (from the original Implementation Notes):**
- **Sheer lingerie** is naked's close cousin: an 8001 gate, a 3-hour police clock, moderate evidence, thrill just under naked, half the naked appeal bonus, vignettes about the technicality, the bra seduction branch with naked rewards, and harassers who tear it instead of stripping it.
- **Framed** is the quiet win: street-legal and venue-legal, with a small passive thrill at zero risk. Its prose should make the look feel deliberate and good.
- **Bare behind mesh** is the heart: she's dressed, and every stranger can see her nipples and her slit anyway.
- **Garment signature lines** are where the prose sings. Use the O-ring, the underbust window, the single string, the bell sleeves, the fishnet diamonds, and Jaewon's top smelling like Jaewon.

---

## Part B — The Sheer System As Built (reference for every remaining step)

### B1. The mesh wardrobe and the `MESH_GARMENTS` registry (source of truth)

```javascript
var MESH_GARMENTS = {
  bo_off_shoulder_mesh_mini_dress: { zones:['chest','hips'], weave:'sheer',   tint:'black',     noun:'mesh',         shape:{ offShoulder, cutouts, mini } },
  bo_mesh_mini_skirt:              { zones:['hips'],         weave:'sheer',   tint:'black',     noun:'mesh',         shape:{ ruched, solidWaistband, mini } },
  bo_rainbow_halter_mini_dress:    { zones:['chest','hips'], weave:'fishnet', tint:'rainbow',   noun:'fishnet',      shape:{ halter, openBack, mini } },
  bo_leopard_halter_mini_dress:    { zones:['chest','hips'], weave:'sheer',   tint:'leopard',   noun:'sheer fabric', shape:{ halter, mini } },
  jaewon_mesh_top:                 { zones:['chest'],        weave:'sheer',   tint:null,        noun:'mesh',         shape:{ jaewons } },
  mesh_club_dress:                 { zones:['chest','hips'], weave:'semi',    tint:null,        noun:'mesh',         shape:{ mini } },
  mesh_crop:                       { zones:['chest'],        weave:'sheer',   tint:null,        noun:'mesh',         shape:{ crop } },
  mesh_tease_bra:                  { zones:['chest'],        weave:'sheer',   tint:null,        noun:'mesh',         shape:{} },
  mesh_tease_panties:              { zones:['hips'],         weave:'sheer',   tint:null,        noun:'mesh',         shape:{} },
  mesh_party_dress:                { zones:['chest','hips'], weave:'sheer',   tint:'black',     noun:'mesh',         shape:{ oRing, underbustCutout, slitTies, mini } },
  one_shoulder_club_dress:         { zones:['chest','hips'], weave:'sheer',   tint:'chocolate', noun:'mesh',         shape:{ oneShoulder, bellSleeves, drawstring, mini } }
};
var MESH_WEAVE_MULT = { semi: 0.7, sheer: 1.0, fishnet: 1.1 };  // fishnet: the skin inside each hole is literally bare
var MESH_WEAVE_RANK = { semi: 1, sheer: 2, fishnet: 3 };        // stacked layers read at the most transparent weave
```

| # | ID | Name | Slot | Source | Notes |
|---|---|---|---|---|---|
| 1 | `bo_off_shoulder_mesh_mini_dress` | Off-Shoulder Mesh Mini Dress | dress | Beauty Obsessed | Slides off both shoulders into short sleeves, geometric cutouts, sheer everywhere. `upscale: true`. |
| 2 | `bo_mesh_mini_skirt` | Sheer Mesh Mini Skirt | bottom | BO | Ruched sides, solid waistband. |
| 3 | `bo_rainbow_halter_mini_dress` | Rainbow Fishnet Mini Dress | dress | BO | Halter, open back, pink trim, wide diamond holes; bare skin sits in every hole. |
| 4 | `jaewon_mesh_top` | Jaewon's Sheer Mesh Top | top | Jaewon's wardrobe | `owner: "jaewon"`; borrowing needs friendship 60+. Now tagged `material:mesh` (saves still need migrating). |
| 5 | `mesh_club_dress` | Mesh Club Dress | dress | Urban Edge ($1000) | Semi-sheer. Passes the upscale door check. |
| 6 | `mesh_tease_bra` | Sheer Mesh Bra | bra | Urban Edge ($12) | Half of the Mesh Tease Set (+8 appeal). |
| 7 | `mesh_tease_panties` | Sheer Mesh Panties | panties | Urban Edge ($6) | The other half. |
| 8 | `mesh_crop` | Mesh Crop Top | top | Urban Edge ($260) | Bare midriff, underboob when she reaches. |
| 9 | `mesh_party_dress` | Mesh Party Dress | dress | Noir ($2200, appeal 96) | **Added in Step 1G.** Black mesh on spaghetti straps. The sheer sweetheart cups gather into an **O-ring** on a chain halter. An **underbust cutout** leaves bare skin below the cups. Ruched sides; the hem slits close with **ribbon ties**. See-through everywhere, cups included. Image `Images/clothing/noirboutique/mesh-party-dress.png`. |
| 10 | `one_shoulder_club_dress` | One-Shoulder Mesh Club Dress | dress | Noir ($2600, appeal 102) | **Added in Step 1G.** Chocolate mesh cut off one shoulder, with a **single halter string** on the bare side. Long **bell sleeves**. A **drawstring** ruches the skirt up one hip. It lays a bronze haze over the skin. Image `.../one-shouldered-club-dress.png`. |
| 11 | `bo_leopard_halter_mini_dress` | Sheer Leopard Mini Dress | dress | BO | Carried forward from BO Step 2E. |

Both Noir dresses are tagged `material:mesh` and `occasion:club`, and they sit on the Noir upscale list, so they pass the Lucius and Regency doors.

**Out of scope, noted for later:** `sheer_lace_bra`, `sheer_lace_panties` and `sheer_silk_top` (Noir), and `lace_dress` and `lace_cami` (Urban Edge). Each would be a one-line registry addition.

### B2. `getSheerState()` — zone model and combined state

**Zone status** is computed separately for the chest and the hips.

How the layers are counted:
- **Chest outer layers:** top, then dress.
- **Hips outer layers:** bottom, then dress, plus a top-slot item with `category === 'dress'`.
- If **any** outer layer on a zone isn't mesh over that zone, the zone is opaque. So an opaque top over a sheer dress hides the chest.
- **Concealment:** a full cover (`conceal:full`) makes both zones opaque. A partial cover (`conceal:partial`, such as a hoodie) makes only the chest opaque.

| Status | Meaning |
|---|---|
| `opaque` | Covered by a non-sheer outer layer, or concealed. |
| `framed` | Every outer layer is sheer, over opaque underwear. The underwear is on display. |
| `veiled` | Every outer layer is sheer, over nothing or over sheer underwear. Bare skin shows. |
| `lingerie` | Sheer underwear with no outer layer. |
| `underwear` | Opaque underwear with no outer layer. |
| `bare` | Nothing at all. |

**Combined state.** The first matching row wins.

| State | Condition | Reads as | Leave-house gate |
|---|---|---|---|
| `sheer_lingerie` | chest lingerie AND hips lingerie | Naked with a technicality | **8001** |
| `sheer_lingerie_top` | chest lingerie | Topless-lite in underwear | 6001 |
| `sheer_lingerie_bottom` | hips lingerie | Bottomless-lite in underwear | 6001 |
| `sheer_bare` | chest veiled AND hips veiled | Naked in a dress | 4001 |
| `sheer_commando` | hips veiled | Bare pussy on display | 4001 |
| `sheer_braless` | chest veiled | Bare tits on display | 2001 |
| `sheer_framed` | any zone framed | A deliberate, legal showcase | none |
| `none` | no sheer involvement | | |

**Slot states own the body.** When `getExposureLevel()` isn't `clothed` or `underwear_only` (so naked, topless or bottomless), the sheer state is `none`.

**Why the gates sit where they do:**
- Framed stays street-legal.
- Nipples through mesh is a fashion edge case, so it unlocks early.
- A visible pussy is a real line, so it sits with Experienced.
- Sheer underwear with nothing over it follows the rules for the underwear it is.
- The full set alone follows naked.

**Return shape:**
```javascript
{ state, chest, hips,
  chestGarment, hipsGarment,    // outermost sheer garment on the zone (the sheer bra/panties themselves in lingerie)
  chestUnder, hipsUnder,        // underwear under the sheer outer layer (opaque = framed, sheer = veiled)
  stacked, chestStacked, hipsStacked,
  framedChest, framedHips,
  weave, hipsWeave, weaveMult, tint,   // weave/tint from the chest; hipsWeave set when the hips differ; weaveMult = max of the zones
  zoneWeave: { chest, hips } }         // per-zone weave: use this for zone-specific prose
```

**Helpers:**
- `getMeshProfile(item)`: returns the registry entry or null. Guarded for load order.
- `isMeshOverZone(item, zone)`.
- `isSheerExposureState(state)`: true for braless, commando, bare and all three lingerie states.
- `getSheerNoun(st?)`.
- `getVisibleSheerPieces()`: the legacy BO wrapper. Returns `{chest, hips}` as the sheer outer garment wherever a zone is framed or veiled, and never lingerie.
- `isPrivateExposureScene(sceneId)`.

### B3. `cv` (`getSceneClothingVars()`)

**Original fields:**

| Field | Meaning |
|---|---|
| `isDress`, `isSkirt`, `isPants` | Garment type |
| `topName`, `bottomName` | Lowercase names; a dress fills both |
| `braless` | No bra and a covering top or dress |
| `commando` | No panties and a covering bottom or dress |
| `noBra`, `noPanties`, `hasBra`, `hasPanties` | Raw slot state |
| `liftable` | Dress or skirt |
| `pulldown` | Pants |

**Added by this overhaul:**

| Field | Meaning |
|---|---|
| `sheerState` | `getSheerState().state` |
| `topSheer` / `bottomSheer` | The outer garment covering that zone (top or dress / bottom or dress) is in the registry. This checks the garment, not visibility. |
| `braSheer` / `pantiesSheer` | The worn bra or panties are in the registry |
| `seeThroughChest` / `seeThroughHips` | The zone is veiled or lingerie. **Use these whenever the question is "can strangers see her nipples / pussy."** |
| `meshNoun` | 'mesh', 'fishnet' or 'sheer fabric' |
| `topFabric`, `bottomFabric`, `braFabric`, `pantiesFabric` | Fabric nouns |
| `topFabricAdj`, `bottomFabricAdj`, `braFabricAdj`, `pantiesFabricAdj` | Adjective form with a trailing space, or '' ("lace panties" vs just "panties") |

`cv.braless` and `cv.commando` keep their meaning. **A mesh bra is still a bra.**

### B4. Fabric helpers (Step 3)

- **`getFabricNoun(item)`:**
  - Registry items use their registry noun.
  - Otherwise it reads the `material:` tag: cotton, denim, lace, satin, silk, leather, velvet, wool, knit, mesh or fishnet.
  - synthetic, metal, unknown tags and a missing item all return 'fabric'.
- **`getFabricAdj(item)`:** returns 'lace ', 'mesh ', 'sheer ' (for 'sheer fabric') or ''.
- **`resolveFabricTokens(line, slot)`:** `{fabric}` resolves to the given slot's fabric. `{topFabric}`, `{bottomFabric}`, `{braFabric}` and `{pantiesFabric}` always resolve to their own slot.

### B5. Rates, gates and tables (global constants)

**`SHEER_PASSIVE_RATES`**, per hour as [thrill, corruption, mesh scratch arousal]:

| State | Thrill | Corruption | Scratch |
|---|---|---|---|
| framed | 1 | 1 | 0 |
| braless | 3 | 2 | 1 |
| commando | 4 | 3 | 0 |
| bare | 6 | 4 | 1 |
| lingerie_top / lingerie_bottom | 9 | 3 | 0 |
| lingerie | 12 | 4 | 0 |

- Thrill is multiplied by `weaveMult`.
- These **stack** with braless (+1/+1), commando (+2/+2) and both (+1/+1).
- Lingerie **replaces** the underwear-only +3/hr thrill.
- For reference, naked is +15/hr thrill and +5/hr corruption.
- **Verified per-hour totals** at 3000 corruption (thrill multiplier 1.0):

  | State | Corruption | Thrill |
  |---|---|---|
  | framed | 1 | 1 |
  | braless | 3 | 4 |
  | commando | 5 | 6 |
  | bare | 8 | 10 |
  | lingerie | 4 | 12 |

  30 two-minute ticks match one 60-minute tick exactly.

**Gates and venues:**
- `SHEER_STATE_GATES` / `getSheerStateGate(state)`: the gate column from B2.
- `getVisibleUnderwearGate()`: 0 when no underwear is visible; **8001** when both bra and panties are visible and every visible piece is sheer; 6001 otherwise. Used by `checkVisibleUnderwearCorruption()`.
- `SHEER_BRALESS_BLOCKED_VENUES`: the hospital, library, blood bank and dojang redirects.
- `SHEER_BARE_ALLOWED_VENUES`: Lucius, Hardy Bar, Urban Edge and the diner.

**Other tables:**
- `SHEER_EXPOSURE_LABELS`: stats panel labels.
- `SHEER_VIGNETTE_STATS`: Step 8 stats.
- `SHEER_BANNER_STYLE` / `SHEER_BANNER_TITLE_STYLE`: the smoky violet-black style.

### B6. Flags and state added so far (for the Step 21 migration)

| Flag / state | Purpose |
|---|---|
| `gameState.flags._passiveAccum` (object, created lazily) | Accrual remainders |
| `gameState.sheerLingerieStartTime` | In the initial gameState |
| `exhibitionStats.sheerMinutesTotal`, `sheerBareMinutesTotal`, `sheerLingerieMinutesTotal` | In the initial gameState |
| `flags._lastSheerExposureAmbientTime` | Step 7A stamp and cooldown |
| `flags._sheerDistrictAwarenessFired` | `{district: day}` |
| `flags._sheerNpcVignetteIndex` | Recent vignettes, last 4 |
| `flags._wetAwarenessJustFired` | `'district@minute'` |
| `flags._luciusSheerDoorDay` | Lucius doorman, once per day |
| `flags._rubySheerLineShift` | Ruby, once per shift |

---

## Part C — What's Done (every item approved by the user)

### Step 1 — Pre-existing bug fixes and the Noir dresses ✅

**1A (B1) Passive accrual**
- `accruePassive(key, perHourRate, minutes)` carries fractional gains across ticks. It adds a +1e-9 epsilon because 30 × 2 min at 1/hr summed to 0.9999 without it.
- `clearPassiveAccum(key)` zeroes a key on any tick where its state isn't active.
- Converted: braless, commando and both corruption and thrill; naked corruption; topless 6/hr, bottomless 9/hr and naked 15/hr thrill; wet thrill; commando friction 3/hr and braless friction 1.5/hr; the thrill→arousal feed (rate × 3/hr).
- **Thrill decay was converted too** (approved deviation). Otherwise gains would pile up with no decay.

**1B (B2) Exhibition Seduction reachable**
- `hunt_seduction` and `hunt_street_random` were removed from `exposureRestrictedBuildings`.
- `seduce_street_man` and `seduce_street_woman` had no garment assumptions. They now append one crude exposure line through `_seductExposureLine(g)` / `_seductWithExposure(g, base)` when she's visibly in underwear, topless, bottomless or naked.

**1C (B3, B4) Underwear and decay**
- Underwear-only earns +3/hr thrill in public, based on the *visible* level, so a full coat hides it.
- `_anyExhibitionState` includes underwear-only and any active sheer state.
- The guide's Thrill Sources line and the stat description were updated.

**1D (B5) Underwear banner.** The subtext is now "Just your bra and panties. You're walking around in your underwear." The doc's suggested "No top, no bottoms" breaks the No/No rule.

**1E (B6, B7) Stats rows and partial cover**
- The stats panel gained rows for Time braless, Time commando, and Time braless & commando.
- Partial concealment hides a sheer chest; this now lives in `getSheerState`.

**1F (B8, B9) Tags and the upscale list**
- `jaewon_mesh_top` is `material:mesh` in its definition. **Saved copies still need migrating in Step 21B.**
- The Noir upscale list swapped `designer_bodysuit` for both new dresses.

**1G New dresses**
- Added to the Noir `dresses` array and the image map. They also carry a harmless `sheer: true`.
- `grep -i bodysuit` returns 0.

**1H (B11) Underwear responses**
- All 9 underwear-only district responses were rewritten to the crude register.
- They branch on type (both / bra only / panties only) and use the real top or bottom name.

**1I (B12) Private scenes**
- `isPrivateExposureScene(sceneId)` returns true for `PRIVATE_UNEQUIP_SCENES` and any `jaewon_apartment_*`.
- It guards sheer thrill, underwear-only thrill and every Step 5 gain.
- In private, underwear and sheer don't hold off decay either.
- Braless and commando at home are deliberately left alone.

### Step 2 — Registry, `getSheerState()`, `cv` ✅

- **2A–2D:** built as described in Part B.
- **`getSheerZoneLine(zone, context)`:** returns a lowercase fragment with no terminal punctuation.
  - Body-branched on breastSize, buttSize and athletic.
  - Variants: fishnet, semi, stacked ("behind two layers of mesh"), lingerie ("behind your sheer mesh bra"), framed ("your black lace bra framed behind the mesh, every line of it on show").
  - Adds a wet-shine tail at arousal 60+. `'moving'` adds motion.
- **`getSheerSignatureLine(garmentId, voice)`:** pulls from `MESH_SIGNATURE_LINES`, 51 lines in total.
  - Each dress has chest and hips lines; the tops, bra, skirt and panties have one zone each, at low, mid and high.
  - It returns a line only for a zone where that garment currently shows bare skin (veiled or lingerie); otherwise it returns ''.
  - The voice defaults by corruption: under 2001 low, under 6001 mid, else high.
  - Per-zone dress lines were an approved deviation, so a nipple line never fires while a bra is framed behind the mesh.

### Step 3 — Fabric awareness pass (B10) ✅

About 110 generic "cotton" references to Kelsie's own garments were converted to `cv` fabric fields.

**Converted:**
- **20 exhibition events (74 hits):**
  - hem events → panties
  - puddle → skirt/dress (bottom)
  - button → bra
  - spill → top
- `exhibition_flash_skirt`, `exhibition_flash_coat`, `exhibition_masturbate_park_bench`.
- `checkAccidentalExposure()` (14 hits).
- The braless, both and commando friction handlers, plus the braless sidebar banner.
- `masturbate_braless_alley` and `masturbate_both_alley`. The commando alley scene had none.
- Street and slums harassment openers, gropes and flirt layers. "the rough cotton" became "the {fabric}".
- `district_exhibition_underwear`, `slums_exhibition_underwear`, `district_wet_exhibition`, `slums_wet_exhibition` (35 hits).

**Placeholders:** `{fabric}` in `BRALESS_AMBIENT_LINES` and `BOTH_AMBIENT_LINES`; `{pantiesFabric}` in `EXHIBITION_AMBIENT_LINES.underwear_only`.

**Post-Step 3 fixes (user-requested):**
- The `slums_exhibition_underwear` Deeply Corrupted opener names the worn bra and panties and their fabrics, not a hardcoded "black set."
- The puddle event no longer assumes lace or pale fabric (4 spots).
- 10 single em dash connectors were fixed in the bench, barstool and button events. A sentence-level scan finds 0 across the exhibition block.

**DEFERRED BY THE USER — the "could be converted" cotton pass, for a later session.** 273 "cotton" hits remain. Most are legitimately fixed: item definitions, SVG and image maps, material tables, the diner uniform, the dobok, jail clothes and Jaewon's wardrobe. The candidates for that later pass:
- The Jaewon bedroom and sleep-touch scenes (`jaewon_sleep_touch_*`, `masturbate_jaewon_bed`, `jaewon_movie_date`…).
- `rape_dream_*jaewon_bed_alone`.
- The wet gallery scenes: `wet_rain_masturbation`, `wet_crowd_press_sex`, `wet_umbrella_*`, `wet_doorway_sex`, `wet_car_slowdown_sex`, `rain_scene_sex`.
- `night_rape_main_female`.

**Not part of this overhaul.**

### Step 4 — Gates, police, venues, transit ✅

**4A Leave-house gates**
- `handleSheerDistrictEntry(districtId)` runs in all 7 district street `onEnter`s: commercial, residential, slums, medical, downtown, wendale and fitness.
- It sits right after the underwear block and is followed by `if (tickSheerLingeriePolice()) return;`.
- **Below the gate** (unless `clothesDestroyedInCombat`):
  - A "No Way" block. `getSheerBlockDesc(st)` names the garment and any sheer underwear under it.
  - The body reacts crudely, even at low corruption.
  - Returns to `previousScene`.
- **Past the gate:** it runs the Step 8 vignette.
- The visible-underwear block and both wardrobe warnings show `getVisibleUnderwearGate()`, and the sheer set has its own block line.
- `riverside_park` has **no gate**, the same as naked (approved).

**4B Lingerie police**
- `tickSheerLingeriePolice()` runs a 3-hour clock in `gameState.sheerLingerieStartTime`.
- It only starts once she's allowed out and resets when she leaves `sheer_lingerie`.
- `handleSheerPoliceArrive()` has 3 tiers, every one about the technicality. The high tier includes "It's see-through, Dave. That's the whole point of it."
- It then runs `handleNakedEscape()` unchanged.
- If another reaction fires on that street entry first, the check waits for the next entry, the same as the naked clock.

**4C Venues**
- `getSheerVenueBlockMessage(restriction)` runs in `showScene()` when the slot level is `clothed` and the scene is restricted.

  | State | Where it's blocked |
  |---|---|
  | framed | Nowhere |
  | braless | Hospital, library, blood bank, dojang |
  | commando / bare | Everywhere except Lucius, Hardy Bar, Urban Edge and the diner |
  | lingerie_top / lingerie_bottom | Everywhere (approved deviation: they read `clothed` by slot) |

- The mid-tier italic thought uses "my."
- `getExposureBlockMessage` says "in nothing but see-through underwear" for the sheer set.
- **Lucius doorman:** `getLuciusSheerDoorLine()` has **one line per state**, once a day in the evening or at night. It takes priority over the Beauty Obsessed bouncer line.
- **The diner** is allowed; Ruby's line is Step 8B.

**4D Transit**
- `getSheerTransitReaction(method)` is wired into `travelToLocation()`.
- **Lingerie top or bottom:** refused by both bus and Uber (approved deviation); Uber also costs −0.2 rating.
- **Bare or commando:** she boards, and the driver's reaction shows on arrival.
- The Uber line says "Nice dress" or "Nice outfit." The bus exhibition event path skips the line.

### Step 5 — Passive mechanics ✅

- **5A:** a sheer block in `advanceTime()` driven by `getSheerState()`.
  - It uses the accrual keys `sheerThrill`, `sheerCorruption` and `sheerScratch`.
  - It's private-guarded and multiplies thrill by the weave.
  - The scratch is skipped during post-orgasm clarity.
- **5B wet vs sheer:** only the higher thrill applies, **except** when exactly one zone is sheer and the other is opaque. Then the rain soaks the opaque zone and both count (approved interpretation). Example: mesh crop braless over wet jeans = braless 1 + mesh 3 + wet 4 = 8/hr.
- **5C:** lifetime sheer minutes, counted **in public only** (approved).
- **5D Sheer Showcase:** `getSheerShowcaseBonus()` gives 25% of `getNakedAppealBonus()` per veiled or lingerie zone, capped at 50%.
  - It's added in `calculateTotalAppeal()` after the set bonuses.
  - The sidebar shows "Sheer Showcase: +N" with a noun-aware note.
- **5E:** unchanged. New events and vignettes multiply by `getExhibitionDrawMultiplier()`.

### Step 6 — Banners and stats panel ✅

**6A Banners**
- `getSheerBannerInfo()` returns `{html, replacesBraless, replacesCommando}` to the clothed banner block in `updateAppearanceDisplay()`.
- Priority order:
  1. "✦ Sheer & Bare" (both zones)
  2. "✦ Sheer (Braless)" / "✦ Sheer (Commando)" (that zone only; the other zone's regular banner and the wet banner still show)
  3. "✦ Sheer" (framed)
- Each banner:
  - has 3 tiers
  - names the garments and handles stacked sheer underwear
  - ends with a rates line
- Optional art: `Images/body/sheer-chest.png`, `sheer-hips.png` and `sheer-lingerie.png`, each with an `onerror` fallback.

**6B Lingerie box.** `getSheerLingerieBoxInfo()` renders one of:
- SHEER LINGERIE ONLY
- SHEER BRA EXPOSED
- SHEER PANTIES EXPOSED
- SHEER BRA & PANTIES (sheer bra, opaque panties)
- BRA & SHEER PANTIES

**6C Stats panel** (`_buildAllStatsIntoTemp()`)
- Sheer exposure labels.
- Rows for Time in sheer, Time sheer & bare, and Time in sheer lingerie.

`SHEER_PASSIVE_RATES` was moved to global scope.

### Step 7 — Ambient interiority and friction ✅

**7A** `checkSheerExposureAmbientLine(sceneId)`:
- Hooked in `showScene()` **before** the both, braless and commando handlers.
- Street scenes only, 30-minute cooldown, guaranteed when eligible. It stamps `_lastSheerExposureAmbientTime`.
- **Pools**, over 4 tiers (low under 2001, mid under 4001, midHigh under 6001, high 6001+):

  | Pool | Lines |
  |---|---|
  | `SHEER_BRALESS_EXPOSURE_LINES` | 12 |
  | `SHEER_COMMANDO_EXPOSURE_LINES` | 12 |
  | `SHEER_BARE_EXPOSURE_LINES` | 8 |

- **Tokens:** `{top}`, `{bottom}`, `{outfit}`, `{noun}`.
- **Supplements mixed in:**
  - `SHEER_WEAVE_SUPPLEMENTS` (fishnet and semi, per zone)
  - `SHEER_STACKED_SUPPLEMENTS`
  - `SHEER_COLD_SUPPLEMENT`
  - a signature line at 1 in 3
- The old BO `SHEER_BRALESS/COMMANDO_AMBIENT_LINES` were folded in and **deleted**.
- **Zone ownership** (approved deviation) via `_sheerOwnsZone(zone)`:
  - The braless, commando and both ambient handlers stand down for any veiled zone.
  - They also yield on the same transition as the sheer line.
  - The other zone keeps its regular lines.

**7B Framed**
- `SHEER_AMBIENT_LINES` has 3 per tier.
- `checkSheerAmbientLine` reads the framed zones from `getSheerState()` and yields to 7A. It keeps its 45-minute cooldown.

**7C Lingerie**
- `EXHIBITION_AMBIENT_LINES.sheer_lingerie` has 3 lines per voice (innocent, experienced, corrupted).
- **Extra keys** (approved deviation): `sheer_lingerie_top` and `sheer_lingerie_bottom`, one line per voice.
- `checkExhibitionAmbientLine` routes lingerie states first and fills in `{bra}` and `{panties}`.

**7D Mesh friction**
- `MESH_FRICTION_LINES`: braless 4, commando 4 and both 2, split high (4001+) and low.
- `_meshFrictionZone(zone)` is true only when the zone is veiled, there's no sheer underwear under it, and the fabric is `'mesh'`. Fishnet and leopard keep the regular lines.
- The friction handlers swap in the mesh line and yield to the 7A stamp.

### Step 8 — District vignettes, Ruby, awareness ✅ (body-branched)

**`_sheerBodyCtx()`** returns fields every later prose step can reuse:

| Group | Fields |
|---|---|
| Sheer state | `st` |
| Body axes | `bs`, `butt`, `bt` |
| Garment names | `noun`, `top`, `bottom`, `outfit`, `same` |
| What shows | `seeChest`, `seeHips`, `shows` / `Shows` (what shows through, breast-size branched) |
| Body phrases | `tits`, `ass`, `hips`, `frame` |
| Garment words | `hipsWord` ('dress' / 'skirt'), `lookWord` |
| Framed state | `under`, `braF`, `panF`, `sheer` |
| Underwear names | `bra`, `panties` |

`_bt5(b, petite, athletic, curvy, thick, ordinary)` picks by body type. **Reuse both.**

**8A** `fireSheerDistrictVignette(districtId, st)`:
- **NPC vignettes:** 34 in `SHEER_NPC_VIGNETTES`: framed 4, braless 6, commando 6, bare 6, lingerie **10** (6 `set`, 2 `top`, 2 `bottom`; approved deviation from 8).
- **Kelsie's responses:** 42 in `SHEER_KELSIE_RESPONSES`. Framed has 3 tiers × 2; the other states 3 × 3. Tiers are low under 2001, mid under 6001, high 6001+.
- Shares the 20-minute `lastExhibStateEventMinute` cooldown and avoids repeating the last 4 vignettes.
- **`SHEER_VIGNETTE_STATS`:**

  | State | Arousal (low / mid / high) | MH | Suspicion | Thrill | Social heat |
  |---|---|---|---|---|---|
  | framed (fires 30% of the time) | 2 / 4 / 6 | none | 0 | 3 / 5 / 7 | 0 |
  | braless | 4 / 8 / 12 | −2 / 0 / +2 | +1 | 10 | +5 |
  | commando | 5 / 10 / 15 | −3 / 0 / +3 | +2 | 12 | +5 |
  | bare | 6 / 12 / 18 | −4 / 0 / +3 | +2 | 16 | +8 |
  | lingerie | 6 / 14 / 20 | −5 / 0 / +3 | +3 | 24 | +8 |

  - Thrill is multiplied by weave × draw; arousal by draw.
  - Social heat only applies by day, outside the slums and residential.
  - Every vignette counts toward `npcReactionsTriggered`.
- `handleUnderwearDistrictEntry` returns false for sheer lingerie once its gate passes, so lingerie lands here.

**8B Ruby.** `getRubySheerLine()`, intercepted in `showScene()` for `diner_entrance` and `diner_main_floor`.
- Fires when `hasDinerJob` is true and she isn't fired, on a weekday between 07:00 and 17:00, in `sheer_bare` or `sheer_commando`, once per day.
- Ruby's reaction depends on her friendship with Kelsie:
  - Under 20: exasperated.
  - 20–49: dry ("Bold choice for a Tuesday").
  - 50+: protective (a cardigan if bare, an apron tied round her waist if commando).
- Kelsie's inner line is corruption-tiered and butt-branched at high.

**8C** `getSheerDistrictAwareness(districtId)`:
- 18 lines: commercial, residential, downtown, slums, park and medical, × 3 tiers.
- Once per district per day, for braless, commando, bare and lingerie.
- Appended after `getWetDistrictAwareness` in 5 districts. Medical had no wet call, so it's added before `getFugitiveFlavor('medical')`.
- **Wet precedence:** `getWetDistrictAwareness` stamps `_wetAwarenessJustFired`, and sheer waits for the next district.

**QA:** 15,400 renders with zero errors and zero `undefined`; the longest paragraph was 240 characters.

---

## Part D — Findings, open items, and traps for the next steps

- **D1. Unfixed floor bug (out of scope).** Jaewon proximity arousal, companion proximity arousal and the desperation MH drain in `advanceTime()` still use `Math.floor(hoursPassedPartial * rate)`, which is 0 on short ticks. Each is a one-line fix with `accruePassive`. **Offer it; don't do it unasked.**
- **D2. Save migration still owed (Step 21B):**
  - bodysuit items in old saves
  - `jaewon_mesh_top` still tagged `material:synthetic` in saves
  - every flag in B6
- **D3. Step 9 must respect zone ownership.** `checkAccidentalExposure()` builds its braless and commando pools from raw `!o.bra` / `!o.panties`.
  - So today a mesh dress with no bra can draw opaque-fabric braless events ("nipples outlined through the {fabric}").
  - When you add the sheer pool, exclude veiled zones from the old pools using `_sheerOwnsZone(zone)`, the same approach as Step 7.
- **D4. Wet systems still describe fabric "going transparent" on mesh.** Step 13A owns fixing all of these:
  - the wet banner in `updateAppearanceDisplay` ("turns translucent")
  - `checkWetClothingInteriority`
  - the wet braless, commando and both interiority lines
  - `WET_NPC_*`
  - `getWetDistrictAwareness`
  - the harassment wet layer
- **D5. The Lucius doorman has 1 line per state.** Step 13C wants 3 variants per state. Extend `getLuciusSheerDoorLine()`.
- **D6. Inconsistencies in the original doc:**
  - Step 9 says its events lead into "Step 17 discovery scenes." They're **Step 18**.
  - Step 1F says to fix saves "in Step 20." Migration is **Step 21**.
  - Step 8A gives framed thrill as both 3/5/7 and 5. **3/5/7 was used.**
- **D7. Companion docs needed.** Steps 16D, 17, 18 and 19 require `VampireGirl_Sex_Standards.md` and `VampireGirl_Masturbation_Standards.md`, and **neither was provided this session.** Ask the user to upload them before starting Step 16D. What the spec says about them:
  - Gallery-eligible male sex scenes run two-part at about 45 paragraphs; female scenes are one extended scene at about 40.
  - Every gallery scene's `onEnter` calls `trackGalleryScene`.
  - The body branching standards (A3) apply throughout.
- **D8. Remaining systems still assume opaque clothing.** Each is covered by its own step:
  - harassment `exhibState` (Step 11)
  - evidence and Cruz (12)
  - style comments (13)
  - pose and flash (14)
  - seduction routing (15)
  - Jaewon (16)
- **D9. Braless/commando thrill at home stays unguarded** by design. Leave it unless the user asks.
- **D10. Style sweep done at handoff** (in the handoff commit). The whole diff against the original upload was scanned, and these were fixed:
  - "It's underwear the way a window's a wall" (Step 4) → "…the same as a window's a wall"
  - "all the way down your neck" (1H) → "down your neck and keeps going"
  - "all the way to the bar" (4C Lucius) → "every step to the bar"
  - two pre-existing bus-event lines touched by the cotton pass ("the rest of the way in" → "the rest of himself in," and "all the way through" → "right through you")
  - "on the way" (slums, post-Step 3) → "on the walk there"

  Now every added or touched line has **zero** hits for the banned phrases, and every em dash in them is paired or a cut-off.
- **D11. Match fabric words to the registry noun.** The mesh friction lines say "mesh" and "grid," so `_meshFrictionZone` only fires for 'mesh'. Any new prose that names the fabric should use the registry noun (fishnet diamonds, leopard spots) or `b.noun`.
- **D12. Gallery mode.** Nothing in Steps 1–8 reads `_galleryMode`; Steps 15C and 17–19 must. Use `flagOverrides` in the gallery registry, and never invent corruption flags.
- **D13. Exhibition vs exposure check order in `showScene()`:**
  1. Ruby intercept (8B)
  2. sheer venue block (4C)
  3. slot exposure block
  4. …ambient chain: arousal → exhibition ambient (7C routing) → evidence → wet chain → **sheer exposure (7A)** → both → braless → commando → framed (7B) → friction ×3

  New hooks should respect this order.
- **D14. District street `onEnter` order today:**
  1. `checkAndSetExposureState()`
  2. underwear/topless/bottomless block
  3. `handleSheerDistrictEntry` (gate + vignette)
  4. `tickSheerLingeriePolice`
  5. `checkAccidentalExposure` (only in commercial, residential, medical, downtown and wendale)
  6. hit squad
  7. `checkDistrictHarassment` → trigger
  8. Cruz
  9. random district event
  10. hero event
  11. `tryStyleComment`
  12. temperature
  13. degradation
  14. then the naked police check

  Slums runs its own daily harassment in `onEnter`.

---

## Part E — Handoff notes for each remaining step (read before the verbatim spec in Part F)

**Step 9 — New sheer accidental events.**
- Extend `checkAccidentalExposure()` (~22858). It currently returns early unless raw braless or commando.
- Add the sheer branch keyed on `getSheerState().state !== 'none'`.
- Apply zone ownership: drop the old braless events for a veiled chest, and the old commando events for veiled hips (D3).
- Build `{text, thrill, arousal}` like the existing events, and use `_sheerBodyCtx()` for body branching.
- Scale by `weaveMult` and state (framed ×0.5, braless/commando ×1.0, bare ×1.4), and by `getExhibitionDrawMultiplier()`.
- Every event rolls its NPC's gender at the start (60% male / 40% female) so Step 18 can branch.
- Events 1–3 (Backlight, Flash, Snag) must end with a choice at 6001+, "Let him/her follow." / "Keep walking.", which Step 18 fills in. Until Step 18 exists, either stub the choice or add it in Step 18. **Ask the user.**
- The Snag applies +8 damage to the garment; check how `item.damage` is stored (see `applyClothingDamage` or similar).
- The Flash adds +10 social media heat when he keeps the photo (Step 12C).
- Increment `exhibitionEventsCompleted` and track peak thrill.

**Step 10 — The 20 existing exhibition events.**
- They branch on `cv.hasPanties` / `cv.hasBra`.
- Add `getSheerUnderwearOverlay(stage)` at the trigger and hold stages when `cv.pantiesSheer` / `cv.braSheer` is true. Sheer underwear gets 75% of the `hold_bare` rewards.
- Hem events at 50% chance when the lifted garment is sheer (`cv.bottomSheer`), plus an overlay line. The same goes for top events (`cv.topSheer`).
- Suppress `exhibition_wet_trigger` on a sheer top.
- Give the bus event trigger a sheer overlay. The bus event is chosen in `travelToLocation` (~27837) at a 20% roll; it requires a skirt or dress.
- The fabric words in these events already use `cv.*Fabric`.
- Keep the new prose body-branched.

**Step 11 — Harassment, slums, night assault.**
- Scenes: `street_harassment_event` / `_grope` and `slums_harassment_event` / `_grope`.
- Grep `exhibState` to find the sub-state logic. Add the sheer states before the wet check.
- New routing targets `district_sheer_exhibition` / `slums_sheer_exhibition`. Those scenes are **built in Step 17**, so until then route to a safe fallback or build the stubs. **Ask the user.**
- Chance and risk code: `checkDistrictHarassment()` (~20550), `checkNightRapeEncounter()` (~28064), `getUnderwearVisibilityDesc()` (~39485) and `slums_passout_rape_main`.
- Night assault doesn't stack with wet: take the higher chance.
- Assault prose follows the assault register (visceral, invasive, her body seen from outside) and stays crude.

**Step 12 — Evidence, police, Cruz, social media.**
- `checkExhibitionEvidence()` (~36961).
- Grep Cruz's behavioral profile for the indecent-exposure pattern: the "3+" entry count and `exposureState`.
- Social media heat is `gameState.socialMediaHeat`, capped at 100. Step 8A already adds vignette heat, so don't double-add it for the same moment.

**Step 13 — Weather, style comments, venue flavor.**
- Wet mesh prose (D4) goes in every wet system listed there. Check `cv.topSheer` / `cv.bottomSheer` per zone.
- `checkStyleComment()` (~38060) detects leather, silk and velvet. Add mesh detection (a `material:mesh` tag or a registry hit) and pools keyed by sheer state.
- Expand the Lucius doorman to 3 variants per state (D5).
- Add the Hardy Bar line once per visit (`_hardySheerLineDay`). Grep `hunt_hardy_bar`.

**Step 14 — Deliberate sheer actions.**
- These are choices in the district choice lists, next to the flash, pose and wet choices. Grep `getWetExhibitionChoices`, `getPoseExhibitionChoice` (~23316) and `exhibition_flash_top` / `_skirt`.
- The cooldown flags are listed in the Step 21A notes.
- "Peel the mesh down" should branch per garment, reading the registry `shape` flags (offShoulder, oneShoulder, oRing, fishnet).

**Step 15 — Exhibition Seduction through the mesh.**
- Routing is in `hunt_seduction.choices` (~the "Exhibition Seduction — route" block). It's reachable now (1B).
- The exit scenes that must clear `_seductionSheer` are the `exhibition_seduce_*` chain endpoints; grep them.
- Overlays must return '' in `_galleryMode` unless a `flagOverrides` entry sets `_seductionSheer`.

**Step 16 — Jaewon.**
- `getJaewonBralessCommandoReaction()` (~26536) holds the existing gates.
- Use Jaewon's own corruption, stored at `gameState.relationships.jaewon.corruption`, to pick her tier.
- `corruptJaewonSlightly()` is the corruption helper.
- The gallery scene `jaewon_mesh_top_sex` needs the Sex Standards doc (D7).
- Grep for any existing window and face-sit scenes so it doesn't duplicate a position.

**Steps 17–19 — Gallery scenes.**
- Mirror the structure of `district_wet_exhibition` / `slums_wet_exhibition` and the `district_exhibition_*` / `slums_exhibition_*` scenes, and the existing Exhibitionism gallery category with its `flagOverrides`.
- Needs the companion docs (D7).
- **Gallery-mode default outfit:** the Off-Shoulder Mesh Mini Dress, braless and commando, set through the gallery's outfit handling.
- Garment damage (+40 outer, +60 panties, +30 for the snag scenes) goes through the item's `damage`.
- The masturbation variants in Step 19 are `masturbate_*_alley` (already fabric-aware) and `exhibition_masturbate_alley` / `_park_bench`, which gain a `sheer` branch plus `_alleyMastGalleryExposure` / `_parkBenchMastGalleryExposure === 'sheer'` gallery overrides.

**Step 20 — Tips & Guide.** The guide text currently in the file:
- The Exhibitionism entry has Thrill Sources and Decay lines, which Step 1C edited.
- The thrill stat description block has "Thrill Gain — Exposure States" and a Decay list.
- The Clothing Systems entry has "Sheer Pieces," which still claims sheer is "always street-legal" and **must be rewritten**, plus "Visible Underwear" and "Upscale Doors."

Grep `Sheer Pieces`, `Thrill Sources`, `Upscale` and `Going Braless`. Document **what's actually built**, deviations included:
- the lingerie top/bottom venue and transit refusals
- public-only sheer minutes
- the wet/sheer non-stacking rule and its one-zone exception
- the 8001 sheer-set gate
- Ruby and the Lucius doorman
- the vignette tiers

**Step 21 — Migration and sweep.** Grep for the Braless/Commando migration block: `bothMinutesTotal === undefined`, about 16767 before the edits.

Flag defaults to add:
- `_passiveAccum: {}`
- `_lastSheerExposureAmbientTime: 0`
- `_sheerDistrictAwarenessFired: {}`
- `_sheerNpcVignetteIndex: []`
- `sheerLingerieStartTime: null` (top level on gameState, not in flags)
- `_sheerStopCoveringDay: 0`, `_lastSheerLightMinute: 0`, `_lastSheerPeelMinute: 0`
- `_rubySheerLineShift: 0`, `_luciusSheerDoorDay: 0`, `_hardySheerLineDay: 0`
- `_jaewonMeshTopReactionDay: 0`, `_jaewonMeshTopSexDay: 0`
- `_sheerDiscoveryCondom: null`, `_seductionSheer: null`
- `_wetAwarenessJustFired: ''`
- `exhibitionStats.sheerMinutesTotal`, `sheerBareMinutesTotal`, `sheerLingerieMinutesTotal`

Item migration:
- Replace `designer_bodysuit` → `mesh_party_dress` and `lace_bodysuit` → `one_shoulder_club_dress` everywhere a saved item can live: `inventory.clothing`, every `gameState.wardrobes[*]`, `currentOutfit.dress` and `outfitPresets`. Keep `cleanliness`, `damage` and `wetness`, and show one notification if anything was swapped.
- Rewrite saved `jaewon_mesh_top` copies to `material:mesh`.
- No sheer flag migration is needed; the registry reads IDs.

The Step 21C test sweep is in Part F; use the A4 recipes.

---

## Part F — Remaining Steps 9–21, VERBATIM from the original overhaul doc

> Reproduced exactly so this handoff stands alone. **Part E's notes override anything here.** Line numbers (`~NNNNN`) are from the original upload and are stale; always `grep -n`.
> Known errata: in Step 9, "Step 17 discovery scenes" means **Step 18**.
> Original conventions that still apply:
> - one step per output, wait for confirmation, no bundling
> - short paragraphs, contractions, escaped apostrophes in all JS strings, `node --check` after every step
> - no AI-isms and none of the style guide's banned patterns
> - all prose 2nd person present, crude and anatomical at every corruption tier
> - no em dashes except paired parentheticals or dialogue cut-offs
> - a paragraph density scan (>300 visible chars) on every new or touched scene
> - "cum," never "come," for orgasm

## Step 9: New Sheer Accidental Events

#### 9A. Open the gate

`checkAccidentalExposure()` returns early unless braless or commando. Add a sheer branch: if `getSheerState()` isn't `none`, build the sheer pool below. If she's also braless/commando under opaque clothes on the other zone, both pools merge. Same 15% per transition and 60-minute shared cooldown.

#### 9B. Sheer pool (8 events)

Each event returns `{ text, thrill, arousal }` with 3 corruption tiers for Kelsie's response, same structure as the existing pool. Thrill and arousal scale with `weaveMult` and the sheer state (framed ×0.5, braless/commando ×1.0, bare ×1.4).

| # | Event | Requires | Base thrill / arousal |
|---|---|---|---|
| 1 | **The Backlight.** She walks past a bright shop window (day: the sun behind her). Light pours through the mesh and outlines everything. A man walking behind her gets her whole silhouette, then the details. | any sheer state | 10 / 7 |
| 2 | **The Flash.** A tourist's camera flash across the street. Flash photography goes straight through mesh. On his screen she's naked, sharper than the street ever showed. He looks from the phone to her and back. | any sheer state, dark or downtown/commercial | 12 / 8 |
| 3 | **The Snag.** Someone's watch, zipper, or a chain-link fence catches the mesh. It tears. A ragged hole opens over a nipple or a hip. Applies +8 damage to the garment (mesh is 1.5x damage, "Thin"). | outer sheer garment | 12 / 9 |
| 4 | **Diamond.** Her stiff nipple pushes through one of the fishnet holes. Bare, in the open air, and it stays there until she does something about it. | fishnet, braless | 12 / 10 |
| 5 | **The Ribbon.** One of the Mesh Party Dress's slit ties comes undone. The slit opens up her hip. Commando means the edge of her bare pussy shows in the gap, no mesh at all. | `mesh_party_dress` | 14 / 10 |
| 6 | **The String.** The One-Shoulder dress's halter string slips. The bare-shoulder side sags and the neckline drops under one nipple. | `one_shoulder_club_dress` | 12 / 9 |
| 7 | **The Reach.** Mesh crop top rides up when she reaches for something. Bare underboob under a see-through chest. | `mesh_crop` | 10 / 7 |
| 8 | **The Double Take.** A woman passing her does a slow second look, then says it out loud: "Oh my god, you're naked under that." Everyone nearby looks. | `sheer_bare`, `sheer_commando` | 14 / 10 |

Sample, The Flash, body:

> A flash goes off across the street. Some tourist shooting the lit-up storefronts.
>
> You're in the frame. He checks his screen and his face changes.
>
> The flash went straight through your {dress}. On his phone you're naked: nipples, navel, the bare slit of your pussy, sharper than the street ever showed.

Events 1, 2, and 3 are the doors into the Step 17 discovery scenes at 6001+.

#### 9C. Stats and tracking

Increment `exhibitionEventsCompleted`, track peak thrill, same as the existing pool.

---

### Step 10: The 20 Existing Exhibition Events Meet Mesh

The hem events (wind, staircase, bench, puddle, crowd, fitting, escalator, bus, barstool, photo) and top events (button, wet, reach, lean, fitslip, bra wind, mirror, neckline, spill, turnstile) all branch on `hasPanties` / `hasBra`. None of them know the underwear might be see-through, or that the skirt already was.

#### 10A. Sheer underwear routing

A mesh bra or mesh panties still routes to the `hold_panties` / `hold_bra` branches (they're wearing underwear). Add `getSheerUnderwearOverlay(eventStage)`: one paragraph inserted after the underwear reveal at the trigger and hold stages when `cv.pantiesSheer` / `cv.braSheer`. The overlay tells the truth: the panties hide nothing.

> Your mesh panties don't hide a thing. He can see your slit through the grid, your clit swollen against it, the dark wet patch where the mesh is sticking to your folds.

Bump arousal and thrill for sheer underwear to 75% of the `hold_bare` values (it's nearly bare). Fabric references in these branches already read `cv.pantiesFabric` after Step 3.

#### 10B. Sheer outer garments

When the garment being lifted, gapped, or blown is itself sheer:

- Hem events fire at 50% of their normal chance (there's less to reveal) and get a trigger overlay: "The mesh was already showing him the shape of you. The wind just takes the haze away." Corruption-tiered, one line each.
- Top events get the same treatment: "The gap shows him what the mesh was already showing, with nothing between."
- `exhibition_wet_trigger` (wet fabric) on a sheer top: suppressed entirely. Mesh doesn't go transparent when wet; it already is.

#### 10C. The bus event

`exhibition_bus_trigger` (bus-only) gets a sheer overlay at the trigger for any bare-behind-mesh state: the man pressed behind her doesn't need to reach under anything to know.

---

### Step 11: Harassment, Slums, Night Assault

#### 11A. `exhibState` gains sheer states

In `street_harassment_event`, `street_harassment_grope`, `slums_harassment_event`, `slums_harassment_grope`: when exposure is `clothed` or `underwear_only`, check `getSheerState()` first. New values: `sheer_framed`, `sheer_braless`, `sheer_commando`, `sheer_bare`, `sheer_lingerie`.

**Openers** (what they see), crude, one block per state. Sample `sheer_bare`:

> He doesn't have to guess. The mesh shows him your stiff nipples and the bare slit of your pussy under the streetlight.
>
> "She's naked under that," the stocky one says. "Dressed up to be naked."

**Grope** (what their hands find), one block per state. The sheer detail: nobody moves anything aside. Sample `sheer_braless`:

> His thumb finds your nipple through the mesh and grinds. The grid bites into it. He doesn't bother pushing your {top} up. He can see exactly what he's doing.

Sample, mesh panties: "The stocky one doesn't hook your panties aside. He rubs your clit right through the mesh until it's soaked and clinging."

`sheer_framed` gets the existing clothed opener plus one sheer line (they can see the bra through the top).

#### 11B. Routing

In the grope `choices`, before the wet check:
- `sheer_lingerie` → `district_sheer_exhibition` (street) / `slums_sheer_exhibition` (slums), all corruption tiers that currently route to exhibition scenes.
- `sheer_bare`, `sheer_braless`, `sheer_commando` → same scenes.
- `sheer_framed` → no change.
Arousal on grope: lingerie 25, bare 22, braless/commando 18. Corruption gain: lingerie and bare 25, others 20.

#### 11C. Harassment chance

`checkDistrictHarassment()`: `sheer_lingerie*` is already guaranteed (visible underwear). Add +0.20 for `sheer_bare`, +0.10 for `sheer_braless`/`sheer_commando`, +0.03 for `sheer_framed`.

#### 11D. Night assault and passout

- `checkNightRapeEncounter()`: add +0.10 `sheer_bare`, +0.07 `sheer_braless`/`sheer_commando`, +0.03 `sheer_framed`. Non-stacking with wet (take the higher, per 5B).
- `night_rape_ambush` opener: `getUnderwearVisibilityDesc()` gets a sheer variant ("in nothing but sheer mesh lingerie").
- `slums_passout_rape_main`: add sheer inserts alongside the naked/bottomless/topless inserts. Sample: "The mesh is still on you. It didn't slow them down. Someone tore the crotch of your panties open instead of pulling them off."

---

### Step 12: Evidence, Police, Cruz, Social Media

#### 12A. Evidence severity

`checkExhibitionEvidence()` reads `getSheerState()` when visible exposure is `clothed` or `underwear_only`:

| State | Severity | Chance multiplier |
|---|---|---|
| `sheer_framed` | none (no entry) | n/a |
| `sheer_braless`, `sheer_commando` | minor | ×`weaveMult` |
| `sheer_bare` | moderate | ×`weaveMult` |
| `sheer_lingerie*` | moderate (naked stays major) | ×1.0 |

Camera entries for sheer states get `exposureState: 'sheer_*'` and a ledger description reflecting the footage: "Street cam footage, subject in see-through clothing. Exposure visible in frame." Night + camera: the camera's IR or flash makes it worse: bump severity one level for sheer entries recorded at night on camera.

#### 12B. Cruz

Wherever Cruz's behavioral profile reads indecent exposure entries (the 3+ entry pattern flag), treat `sheer_*` `exposureState` as indecent exposure. Add two Cruz reference lines keyed to sheer entries, used when the majority of her exposure entries are sheer:

- "Your indecent exposure file is interesting. Most of it's technically clothed."
- "Three stills. Same see-through dress. You know exactly what the cameras see."

#### 12C. Social media

`sheer_bare` and `sheer_lingerie` in daytime busy districts add social media heat on the vignette (8A). The Flash event (9B #2) adds +10 heat on its own when he keeps the photo.

---

### Step 13: Weather, Style Comments, Venue Flavor

#### 13A. Wet mesh

When a zone's outer garment is sheer, the wet systems stop describing fabric "going transparent" for that zone:
- `checkWetClothingInteriority()`, the wet braless/commando/both interiority, `WET_NPC_*` layers, `getWetDistrictAwareness()`, and the harassment wet layer each check `cv.topSheer` / `cv.bottomSheer` and swap to a wet-mesh line for that zone. Rain hits skin directly through the grid. Water beads on her nipples behind the mesh. The mesh plasters flat and the fishnet holes fill with wet skin.
- 4 tiers × chest/hips = 8 wet-mesh lines, plus 2 for the harassment opener.
- Non-stacking thrill (5B) already covers the stats.

#### 13B. Style comments

`checkStyleComment()` gets mesh detection (`material:mesh` or registry hit) and a mesh comment pool keyed by sheer state:
- `sheer_framed`: 4 lines. Mostly admiring, a few judgmental. It reads as fashion.
- `sheer_braless` / `sheer_commando` / `sheer_bare`: 4 lines each. Scandal, stares, someone taking a photo, a woman telling her friend "that's a choice."
Mesh comments take priority over leather/silk/velvet comments when a sheer state is active. Slums still returns null.

#### 13C. Club venues

- Lucius' Lounge doorman line (from 4C), 3 variants by sheer state.
- Hardy Bar: when she walks in `sheer_bare` or `sheer_braless`, one bar-reaction line (the bartender comps a drink; a table goes quiet). Once per visit.

---

### Step 14: Deliberate Sheer Actions

Mesh needs its own deliberate verbs. Flash and pose assume there's something to remove. With mesh, the play is controlling the light and the veil.

#### 14A. "Stop covering up." (4001+)

The mid-corruption entry point, modeled on the wet "Stop fighting it." Any bare-behind-mesh state, thrill 30+, once per day. She drops her arms, straightens, walks slow. Thrill +10, arousal +8, corruption +25. Two tiers of prose (Experienced / Corrupted+).

#### 14B. "Step into the light." (6001+, thrill 50+)

Any bare-behind-mesh state. She stops under a streetlight at night, or in front of a lit window by day, and lets the mesh vanish. 60-minute cooldown. Thrill +18, arousal +12, corruption +20, all × `weaveMult` × draw multiplier. Two tiers (Corrupted / DC). The DC version turns slowly so the whole sidewalk gets both sides.

#### 14C. "Peel the mesh down." (8001+, thrill 70+)

Chest-veiled states only. She pulls the mesh off her tits in public: the off-shoulder dress slides down, the one-shoulder string gets untied, the party dress cups get pulled under her tits by the O-ring, the fishnet gets stretched under them. Momentarily topless without changing slots. Thrill +25, arousal +15, corruption +15, suspicion +2. Then she fixes it, or at DC she walks a block like that first. 45-minute cooldown.

#### 14D. Pose and Flash

- `getPoseExhibitionChoice()`: also accept `sheer_bare` and `sheer_lingerie`. The three pose scenes get a sheer paragraph variant (standing in the light, seated with the mesh riding up, walk-by).
- `exhibition_flash_top` / `_skirt`: when the lifted garment is sheer, a variant opening: he's already seen the shape through it, now he gets the color.

All choices appear in the district choice lists beside the existing flash/pose/wet choices.

---

### Step 15: Exhibition Seduction Through the Mesh

Depends on 1B.

#### 15A. Routing

In `hunt_seduction` choices, when slot exposure is `clothed` or `underwear_only`, read `getSheerState()` (same 6001+ / 80 appeal gate):

| Sheer state | Branch |
|---|---|
| `sheer_braless`, `sheer_lingerie_top` | top |
| `sheer_commando`, `sheer_lingerie_bottom` | btm |
| `sheer_bare` | nkd |
| `sheer_lingerie` | bra (with nkd-level rewards) |
| `sheer_framed` | none (regular seduction) |

Set `gameState.flags._seductionSheer = state` on routing; clear it at every exit scene.

#### 15B. Overlays

`getSheerSeductionOverlay(stage)` returns a paragraph when `_seductionSheer` is set:
- `approach`: what the target sees through the mesh.
- `proposition`: the target calling it out ("You know I can see everything, right?").
- `undress`: inserted before the first undressing paragraph of each `_sex_` scene. The branch prose assumes tits or pussy are already out; the overlay gets them out first. "He peels the mesh down off your tits and doesn't bother with the rest."

Eight sex scenes plus their `_sex_2_` chains. Only the first undressing moment needs the overlay. Scan each chain scene for contradictions after insertion.

#### 15C. Gallery mode

Overlays return empty in `_galleryMode` unless the registry entry sets `flagOverrides: { _seductionSheer: '...' }`. No new gallery entries this step.

---

### Step 16: Jaewon

#### 16A. Sheer reactions (GF)

Extend `getJaewonBralessCommandoReaction()` (~25782) with a sheer branch that fires when `cv.seeThroughChest` or `cv.seeThroughHips`. Braless under opaque clothes is something Jaewon notices. Braless under mesh is something she can't stop looking at.

Uses Jaewon's corruption (overhaul-added convention), 3 tiers (Innocent 0-4000 / Experienced 4001-8000 / DC 8001+), 3 states (braless / commando / both through mesh) × 2 variants = 18 lines. Same gates: GF, not angry, not asleep, mood allows touch, 120-minute cooldown, 30% rate.

#### 16B. "That's my top."

When Kelsie wears `jaewon_mesh_top`, the sheer branch draws from a dedicated pool instead: 3 tiers × 2 = 6 lines, doubled if braless under it.

- Innocent, braless: "Jaewon looks up from the couch and stops. \"That's my top.\" Her eyes drop to your nipples, dark behind the mesh. Her cheeks go red. \"You're not wearing anything under my top.\""
- DC, braless: "Jaewon pulls you into her lap by the hem of her own top. \"It never looked like this on me.\" Her thumb circles your nipple through the mesh until you squirm. \"Keep it. I want to watch you wear it out.\""

#### 16C. Friend-tier (not GF)

Borrowing needs friendship 60+, so a non-GF Jaewon can see Kelsie in her mesh top. Once per day, 3 flustered variants. Jaewon's side stays PG (no romance, no touch). Kelsie's interiority is crude if she's aroused.

#### 16D. Gallery scene: `jaewon_mesh_top_sex`

Trigger: an escalation choice under the 16B reaction, "Come take it back." GF, Jaewon corruption 4001+, Kelsie in `jaewon_mesh_top` with `cv.seeThroughChest`, once per day (`_jaewonMeshTopSexDay`). Living room or bedroom.

Two tiers (Jaewon's corruption):
- **Experienced (4001-8000):** Jaewon's mouth on Kelsie's nipple through the mesh, the grid wet from her tongue. She fingers Kelsie on the couch without taking the top off. It's hers, and she wants Kelsie to cum in it. Act 2: Kelsie returns it, Jaewon on her back, Kelsie's fingers and mouth.
- **DC (8001+):** Jaewon pins Kelsie against the living room window, backlit, the mesh gone see-through against the glass. She fingers her from behind and makes her watch their reflection. Act 2: "On your knees." Kelsie eats her out still wearing the top, Jaewon's fist in the mesh at her shoulder.

Sex Standards compliant, ~40 paragraphs, body branching, cv garments, crude throughout. onEnter: `trackGalleryScene`, `completeSexConsensualFemale`, relationship +3/+5, `corruptJaewonSlightly()`. Jaewon gallery category, 2 tiers. Must not duplicate existing positions (lazy couch tribbing, bedroom face-sit, window scenes if any exist: grep first).

---

### Step 17: Gallery Scenes: District & Slums Sheer Exhibition

The harassment routing payoff from 11B. Mirrors `district_wet_exhibition` / `slums_wet_exhibition` structure, length, onEnter, trauma handling, and three tiers.

#### 17A. `district_sheer_exhibition`

Two men, alley (park: behind the maintenance shed), same as the wet scene.
- **Innocent (0-4000):** assault framing, full trauma mechanics per the existing district exhibition scenes. They don't strip her. They tear the mesh, or fuck her through it. "Technically dressed" becomes their joke.
- **Experienced (4001-8000):** conflicted, wet, the mesh torn open at the crotch and her tits pulled through the neckline.
- **DC (8001+):** she wants it. She makes them keep the mesh on her.

Garment handling by `cv`: mesh dress (tear the crotch or hike it, tits through the torn neckline or peeled down), mesh skirt (never lifted; fucked through a torn hole), fishnet (fingers and cock through the widened diamonds), mesh lingerie (torn at the crotch, cups pushed under). After: the outer mesh garment takes +40 damage, mesh panties +60.

Gallery: Exhibitionism, "District: Sheer Exhibition," tiers Innocent / Experienced / DC. Gallery-mode default garment: the Off-Shoulder Mesh Mini Dress, braless and commando.

#### 17B. `slums_sheer_exhibition`

Same concept, slums cast and scale (match `slums_exhibition_*` for number of men and tone). Three tiers, same garment handling, same damage. Gallery: "Slums: Sheer Exhibition."

---

### Step 18: Gallery Scenes: Discovery Through the Mesh

Consensual encounters born from Step 9's events. Corruption 6001+, bare-behind-mesh state. The event ends with a choice ("Let him follow." / "Keep walking.") at 6001+. Gender decided by the event's NPC (each event rolls male or female at its start, 60/40). Male scenes run the condom choice via the existing transient-flag pattern (`_sheerDiscoveryCondom`, set → read → pass → null). Sex Standards: two-part male, extended single female. Tiers: Corrupted (6001-8000) / DC (8001+).

| Scene | From | Setting | Hinge |
|---|---|---|---|
| `sheer_backlight_sex_m` / `_f` | The Backlight | A lit shop doorway after close | The stranger saw her silhouette through the mesh from half a block back. She stops in the light and lets them look before anyone touches her. They fuck her in the doorway with the light still pouring through the mesh. |
| `sheer_flash_sex_m` / `_f` | The Flash | Parking structure stairwell | The photographer shows her the screen. She's naked in every frame. "One more." The shoot moves into the stairwell, shots between, and ends with sex. Keeping the photos is her choice: +10 social media heat if kept. |
| `sheer_snag_sex_m` / `_f` | The Snag | Elevator stuck between floors (m) / café restroom (f) | The hand that tore the mesh tries to hold the tear shut and finds bare skin. The tear keeps getting wider. The garment takes +30 damage. |

Six gallery entries in Exhibitionism. ~45 paragraphs male (two parts), ~40 female. Body branching and cv garment handling as in 17A.

---

### Step 19: Masturbation Integration

#### 19A. Alley relief scenes

`masturbate_braless_alley`, `masturbate_commando_alley`, `masturbate_both_alley`: add sheer branches to the opening and closing paragraphs when the relevant zone is see-through. She's been on display all day, not just rubbed raw. She doesn't need to lift anything to watch her fingers work under a mesh skirt. The mesh scratch replaces the cotton scrape (Step 3 already swapped the fabric words). No new gallery entries; these are variants.

`getBralessCommandoAlleyChoice()` labels get sheer variants: "Duck into an alley (everyone's been looking)."

#### 19B. Exhibition masturbation (alley + park bench)

`exhibition_masturbate_alley` and `exhibition_masturbate_park_bench` branch on exposure. Add a `sheer` branch (read `getSheerState()` when exposure is `clothed` or `underwear_only`, and `_alleyMastGalleryExposure` / `_parkBenchMastGalleryExposure === 'sheer'` in gallery mode). Two tiers, Corrupted / DC, matching the other branches. Masturbation Standards compliant.

The sheer hook: she touches herself through the mesh first. Only at the edge does she get her fingers under it.

Gallery entries: "Alley: Sheer," "Park Bench: Sheer," same tiers as their siblings. Gallery-mode garment defaults to the Off-Shoulder Mesh Mini Dress, braless and commando.

---

### Step 20: Tips & Guide

#### 20A. New subsection: "Mesh & Sheer"

Inside the Exhibitionism System entry, after Going Braless & Commando. Cover:
- The eleven sheer pieces and their weaves (sheer, semi, fishnet), with the two new Noir dresses.
- The five sheer states in plain terms, what each looks like to strangers, and the corruption each needs to walk out in.
- Passive thrill, corruption, the mesh scratch, the sheer showcase appeal bonus.
- Ambient interiority, vignettes, accidental events, how existing exhibition events change with mesh underwear.
- Deliberate actions (Stop covering up, Step into the light, Peel the mesh down, pose).
- Harassment, night risk, evidence severity, police on sheer lingerie (3-hour clock), venue rules, transit.
- Jaewon.
- Gallery scenes and their triggers.
- Strategy: framed is free and safe. Braless under mesh is the cheapest real exposure in the game. Sheer lingerie is naked with better odds with the police and a smaller thrill payout.

Crude, anatomical register throughout, matching the rest of the Exhibitionism entry.

#### 20B. Corrections and cross-references

- Clothing Systems, "Sheer Pieces": rewrite. Delete "Sheer never counts as visible underwear, so they're always street-legal."
- Clothing Systems, "Visible Underwear": add the 8001 gate for sheer lingerie.
- Clothing Systems, "Upscale Doors": replace the bodysuit, list the new Noir dresses.
- Exhibitionism, "Exposure States": add sheer.
- Exhibitionism, "Thrill Sources": add underwear-only (1C) and sheer rates.
- Exhibitionism, "Decay": still true now that 1C fixes it.
- Exhibitionism, "Arrest & Evidence," "Bus," "Exhibition Seduction" (now reachable, sheer routing).
- Wet Clothes: wet mesh behavior and non-stacking thrill.
- Braless/Commando: note the stats panel now shows the minute totals (1E) and that passive rates apply to short ticks (1A).

---

### Step 21: Save Migration & Final Sweep

#### 21A. Flag defaults

In the migration function beside the Braless/Commando block (~16755):
- `_passiveAccum: {}`
- `_lastSheerExposureAmbientTime: 0`, `_sheerDistrictAwarenessFired: {}`, `_sheerNpcVignetteIndex: []`
- `sheerLingerieStartTime: null`
- `_sheerStopCoveringDay: 0`, `_lastSheerLightMinute: 0`, `_lastSheerPeelMinute: 0`
- `_rubySheerLineShift: 0`, `_luciusSheerDoorDay: 0`, `_hardySheerLineDay: 0`
- `_jaewonMeshTopReactionDay: 0`, `_jaewonMeshTopSexDay: 0`
- `_sheerDiscoveryCondom: null`, `_seductionSheer: null`
- `exhibitionStats.sheerMinutesTotal`, `sheerBareMinutesTotal`, `sheerLingerieMinutesTotal`: 0

#### 21B. Item migration

- **Bodysuits:** any `designer_bodysuit` or `lace_bodysuit` in `inventory.clothing`, every `gameState.wardrobes[*]`, `currentOutfit.dress`, and `outfitPresets` gets replaced by a fresh copy of `mesh_party_dress` / `one_shoulder_club_dress` built from the Noir definition, keeping `cleanliness`, `damage`, and `wetness`. One notification on load if anything was swapped.
- **Jaewon's mesh top:** rewrite `material:synthetic` to `material:mesh` on every saved copy.
- No sheer flag migration needed: the registry reads IDs.

#### 21C. Test sweep

Walk every path:
- Each sheer state entered deliberately at each gate boundary (2000/2001, 4000/4001, 6000/6001, 8000/8001), including the combat-destruction exception.
- Full coat, partial coat, no coat over each state.
- Stacked sheer (mesh bra under mesh top, mesh panties under mesh skirt).
- Mesh top over opaque skirt commando (sheer chest + regular commando banner together).
- Passive accrual over a day of 2-minute ticks vs. one long tick (should match within rounding).
- Banners, stats panel, ambient yield order (sheer exposure > both > braless/commando > framed).
- Vignettes for every state in every district; Ruby; Lucius; Hardy Bar.
- All 8 sheer accidental events, the 20 existing events with mesh underwear and mesh outer garments.
- Harassment and slums routing for every state; night assault odds; passout inserts.
- Evidence severity for each state, night camera bump, Cruz lines.
- Deliberate actions at their gates and cooldowns.
- Exhibition Seduction from each sheer state (m and f).
- Jaewon: GF tiers, her top, friend tier, gallery scene.
- All new gallery entries in gallery mode, every tier.
- Wet mesh in rain; cold weather lines.
- A save from before the overhaul holding a bodysuit, Jaewon's mesh top, and a BO sheer dress.

#### 21D. `node --check`

Both script blocks pass. Style guide compliance scan on every new string: em dashes, contractions, "the way," "the kind of," Not/Not, negation-before-reveal, "genuinely," "A beat," paragraph density.

---

### (Original) Function & Method Summary — the Steps 1–8 rows are DONE

| Function | Step | Role |
|---|---|---|
| (new) `accruePassive()` | 1A | Fractional passive gain carry across short ticks |
| (fix) `exposureRestrictedBuildings` | 1B | Unblock Exhibition Seduction |
| (fix) `_anyExhibitionState`, underwear-only thrill | 1C | Decay and passive thrill for underwear/sheer |
| (fix) underwear banner, stats rows, partial concealment | 1D-1E | B5, B6, B7 |
| (fix) tags, Noir upscale list, Noir dresses | 1F-1G | B8, B9, bodysuit replacement |
| (rewrite) underwear-only responses | 1H | Rule 9 register |
| (new) `isPrivateExposureScene()` | 1I | Private-space thrill guard |
| (new) `MESH_GARMENTS`, `getMeshProfile()` | 2A | Registry |
| (new) `getSheerState()` | 2B | Zone + combined sheer state |
| (rewrite) `getVisibleSheerPieces()` | 2C | Registry-backed wrapper |
| (extend) `getSceneClothingVars()` | 2D | Sheer and fabric fields |
| (new) `getSheerZoneLine()`, `getSheerSignatureLine()` | 2E | Prose helpers |
| (new) `getFabricNoun()` + cotton pass | 3 | Fabric awareness |
| (new) `handleSheerDistrictEntry()` | 4A, 8A | Gates + vignettes |
| (extend) `checkVisibleUnderwearCorruption()` | 4A | 8001 for sheer set |
| (new) `handleSheerPoliceArrive()` | 4B | 3-hour sheer lingerie clock |
| (extend) `getExposureBlockMessage()` | 4C | Venue policy |
| (extend) bus/Uber checks | 4D | Transit |
| (rewrite) sheer passive block | 5A-5C | Rates, non-stacking wet, minutes |
| (extend) `calculateTotalAppeal()` | 5D | Sheer showcase |
| (new) sheer banners, lingerie box, stats labels | 6 | UI |
| (new) `checkSheerExposureAmbientLine()` + 3 pools | 7A | 32 lines + supplements |
| (extend) `SHEER_AMBIENT_LINES`, `EXHIBITION_AMBIENT_LINES` | 7B-7C | Framed + lingerie |
| (new) mesh friction variants | 7D | 10 lines |
| (new) sheer NPC pools + Kelsie responses | 8A | 30 vignettes |
| (new) Ruby line | 8B | Diner |
| (new) `getSheerDistrictAwareness()` | 8C | District text |
| (extend) `checkAccidentalExposure()` | 9 | 8 sheer events |
| (new) `getSheerUnderwearOverlay()` + outer overlays | 10 | 20 existing events |
| (extend) harassment + slums `exhibState` | 11A-11B | Openers, gropes, routing |
| (extend) harassment chance, night assault, passout | 11C-11D | Risk |
| (extend) `checkExhibitionEvidence()`, Cruz | 12 | Legal layer |
| (extend) wet systems, `checkStyleComment()`, venue lines | 13 | Weather + flavor |
| (new) Stop covering up / Step into the light / Peel | 14A-14C | Deliberate actions |
| (extend) Pose, Flash | 14D | Sheer variants |
| (extend) `hunt_seduction` routing + `getSheerSeductionOverlay()` | 15 | Seduction |
| (extend) `getJaewonBralessCommandoReaction()` | 16A-16C | Jaewon |
| (extend) three alley scenes, two exhibition masturbation scenes | 19 | Masturbation |
| (rewrite) Tips & Guide | 20 | Mesh & Sheer subsection + corrections |
| (extend) migration | 21 | Flags + item swaps |

### (Original) New Scene Summary — the Step 4, 7 and 8 rows are DONE

| Scene / beat | Step | Type | Trigger |
|---|---|---|---|
| Sheer "No Way" blocks (×6 states) | 4A | Repeatable | District entry below gate |
| Sheer lingerie police | 4B | Repeatable | 3 hours in `sheer_lingerie` |
| Sheer exposure ambient (×32) | 7A | Repeatable | Street transition, bare behind mesh |
| Framed ambient (+4) | 7B | Repeatable | Street transition, underwear behind mesh |
| Sheer lingerie ambient (×9) | 7C | Repeatable | Street transition, lingerie states |
| Mesh friction (×10) | 7D | Repeatable | Friction cycle, bare under mesh |
| Sheer NPC vignettes (×30) | 8A | Repeatable | District entry |
| Ruby's line | 8B | Once per shift | Arrive `sheer_bare`/`commando` |
| Sheer district awareness (×18) | 8C | Once per district per day | District text |
| The Backlight / Flash / Snag / Diamond / Ribbon / String / Reach / Double Take | 9B | Repeatable | District transition |
| Stop covering up | 14A | Once per day | 4001+, thrill 30+ |
| Step into the light | 14B | 60-min cooldown | 6001+, thrill 50+ |
| Peel the mesh down | 14C | 45-min cooldown | 8001+, thrill 70+ |
| Jaewon sheer reactions (×18 + 6 + 3) | 16A-16C | Repeatable | Apartment |
| `jaewon_mesh_top_sex` | 16D | Gallery (Jaewon, 2 tiers) | "Come take it back." |
| `district_sheer_exhibition` | 17A | Gallery (3 tiers) | Harassment routing |
| `slums_sheer_exhibition` | 17B | Gallery (3 tiers) | Slums harassment routing |
| `sheer_backlight_sex_m` / `_f` | 18 | Gallery (2 tiers each) | The Backlight, 6001+ |
| `sheer_flash_sex_m` / `_f` | 18 | Gallery (2 tiers each) | The Flash, 6001+ |
| `sheer_snag_sex_m` / `_f` | 18 | Gallery (2 tiers each) | The Snag, 6001+ |
| Alley: Sheer / Park Bench: Sheer | 19B | Gallery (2 tiers each) | Exhibition masturbation, sheer state |

**New gallery entries: 11** (1 Jaewon, 10 Exhibitionism).

---

### (Original) Implementation Notes — still apply

- **Step 1 is load-bearing.** B1 alone means half the exhibition economy has been running at a fraction of its designed rate. Fix it first, playtest a day, and only then tune sheer rates on top. Building sheer rates on a broken accrual would mean tuning numbers against a bug.

- **Step 2 is the foundation for everything after it.** Every later step asks one question: what can strangers see right now? `getSheerState()` answers it. If a later step reads `item.sheer`, `outfit.dress.name`, or a hardcoded ID list instead, it's wrong.

- **Step 3 is unglamorous and essential.** A mesh dress whose prose says "cotton" breaks the fantasy harder than any missing feature. It also fixes every satin, lace, and leather outfit in the game while it's at it.

- **Sheer lingerie is the headline.** Nathan's brief: counts as naked, but somewhat different. The difference is legible everywhere: an 8001 gate like naked, a 3-hour police clock instead of 2, moderate evidence instead of major, thrill just under naked, half the naked appeal bonus, vignettes about the technicality, the bra seduction branch with naked rewards, and harassers who tear it instead of stripping it. Every system should feel like naked's close cousin.

- **Framed is the quiet win.** A mesh dress over a good bra is the most wearable piece of exhibition in the game: street-legal, venue-legal, a small passive thrill with zero risk. It's how most players will meet the system. Its prose should make that look feel deliberate and good.

- **Bare behind mesh is the heart.** Braless and commando are secrets. Mesh turns the secret into a display without taking the clothes off. The prose should live in that tension: she's dressed, and every stranger can see her nipples and her slit anyway.

- **Garment signature lines are where it sings.** Eleven pieces with real, visible details: the O-ring, the underbust window, the single string on the bare shoulder, the bell sleeves, the fishnet diamonds, Jaewon's top smelling like Jaewon. Use them. They make the prose read like it's about *this* dress.

- **Keep the prose crude and anatomical throughout.** Nipples, tits, pussy, clit, cunt, slit, folds, slick, wet. Mesh is a visual system, which makes the crude language more precise: the reader should know exactly what shows through the grid at every corruption tier. Low corruption is shame with a body that won't stop reacting. It's never soft.


---

## Part G — Original reference sections, VERBATIM

> The original doc's system map and bug table, kept for completeness. Line numbers are stale; use `grep -n`.
> - Bugs B1–B9, B11 and B12 are **fixed** (Part C, Step 1).
> - B10 is **done** for the priority areas (Step 3). The rest is the "could be converted" pass the user deferred.

### Existing Systems This Connects To (verified in the current file)

Exposure core (~20938-21060):
- `isEffectivelyNaked()`, `isEffectivelyTopless()`, `isEffectivelyBottomless()`: slot-based. A mesh bra counts as a bra.
- `getExposureLevel()`: returns `naked` / `topless` / `bottomless` / `underwear_only` / `clothed`. Mesh lingerie alone returns `underwear_only`.
- `getVisibleExposureLevel()`: applies outerwear concealment. Used by evidence, ambient interiority, stats modal.
- `isBralessAndCommando()` (Braless Step 9C).
- `getSceneClothingVars()` (~38000): the `cv` object. No sheer or fabric fields yet.

Beauty Obsessed sheer layer (Step 2E):
- `getVisibleSheerPieces()` (~37949): reads `item.sheer`. Full coat hides both zones. Partial coat is ignored (bug, see 1E).
- Passive thrill block in `advanceTime()` (~32891): +1/hr, +1 more when bare behind.
- `SHEER_BRALESS_AMBIENT_LINES`, `SHEER_COMMANDO_AMBIENT_LINES` (~34536): one line per tier, pushed into the braless/commando/both pools.
- `SHEER_AMBIENT_LINES` + `checkSheerAmbientLine()` (~34552): underwear showing through sheer, 45-min cooldown.
- `normalizeBOItem()` (~49459): rewrites `sheer` from BO definitions on load.
- Tips & Guide "Sheer Pieces" paragraph: claims sheer is "always street-legal." This overhaul changes that.

Exhibition system:
- `EXHIBITION_THRILL_TIERS`, `applyExhibitionThrillGain()` (~33721-33830): corruption-scaled gain/decay.
- `handleNakedDistrictEntry()` (~20420), `getNakedNPCVignette()` (12 NPCs), `checkNakedPolice()` (2-hour clock), `handlePoliceArrive()`, `handleNakedEscape()`.
- `handleUnderwearDistrictEntry()` (~21959): hard block below 6001 via `checkVisibleUnderwearCorruption()` (~38486), topless/bottomless/underwear vignettes and Kelsie responses.
- `checkAccidentalExposure()` (~22104): 15%/transition, 60-min cooldown, returns `{text, thrill, arousal}` events.
- `buildDistrictEventPool()` (~20607): 20 `exhibition_*_trigger` scenes (10 hem, 10 top), each with escape / hold_panties or hold_bra / hold_bare / perform branches (~164631-168870). Outerwear concealment scales their chance.
- Flash (6001+, thrill 70+), Pose (8001+, thrill 75+, overt states only), exhibition masturbation (alley, park bench), Watcher encounter.
- `checkExhibitionEvidence()` (~35814): 30% camera districts, 15% witness districts, severity minor/moderate/major.
- `checkExhibitionAmbientLine()` + `EXHIBITION_AMBIENT_LINES` (~34050, ~34397): keyed by visible exposure, 15-min cooldown.
- `exposureRestrictedBuildings` + `getExposureBlockMessage()` (~198397-198482), enforced at the top of `showScene()`.
- Bus refusal / Uber cancel on any non-clothed exposure (~27099-27125).
- `checkNightRapeEncounter()` (~27290): visible underwear guaranteed; wet +5/+10%.
- `checkDistrictHarassment()` (~20535): visible underwear or overt exposure guaranteed.
- Street harassment (`street_harassment_event` / `_grope`, ~106003) and slums harassment (~107620): `exhibState` sub-states, wet layer, routing to `district_exhibition_*` / `slums_exhibition_*` / `district_wet_exhibition` gallery scenes.
- `slums_passout_rape_main` (~175063): exposure inserts.
- Exhibition Seduction (`hunt_seduction` choices, ~63655): four exposure branches, 64 scenes (currently unreachable, see 1A).
- Sidebar outfit display (~54535-54710): naked / topless / bottomless / underwear warning boxes; braless / commando / both / wet banners.
- Stats modal thrill panel (~32143).
- `checkStyleComment()` (~37125 area): detects leather, silk, velvet. No mesh.

Wet Clothing Erotic Overhaul (the closest structural template):
- `getWetDistrictAwareness()` (~35489): per-district text appended to district `text:`.
- `WET_NPC_REACTION_EVENTS` + braless/commando/both layers, `checkWetNpcReaction()` (~35601).
- Wet thrill tier-crossing prose, deliberate wet choices (`getWetExhibitionChoices()`, ~33872).
- Harassment "the rain did the rest" opener layer and grope layer, `district_wet_exhibition` / `slums_wet_exhibition` routing at wetness 60+.

Braless & Commando Overhaul (complete): ambient pools, friction lines, both-state bonuses, alley masturbation (3 scenes, gallery Masturbation category), Jaewon reactions (`getJaewonBralessCommandoReaction()`, ~25782), minute tracking, migration block (~16755).

Scene Gallery Exhibitionism category (~44812): tiered entries with `flagOverrides`.

---


### Pre-Existing Bugs Found During Research

These get fixed in Step 1 before any mesh work lands, because several of them would silently eat the new system.

| # | Bug | Where | Impact |
|---|---|---|---|
| B1 | **Passive per-hour gains floor to zero on short ticks.** `hoursPassedPartial = minutes / 60` inside `advanceTime()`, and every passive gain does `Math.floor(hoursPassedPartial * rate)` with no remainder carry. A 2-minute walk, a 10-minute scene, a 30-minute errand all yield 0. The same applies to the `Math.floor(minutes / 20)` blocks under 20 minutes. | `advanceTime()` ~32706-33000 | Braless/commando/both corruption and thrill, sheer thrill, wet thrill, naked corruption, topless/bottomless/naked thrill, thrill-to-arousal feed, commando/braless friction arousal: all of these only accrue on long single advances (sleep, shifts, waits). Most of the exhibition economy has been leaking for as long as it's existed. |
| B2 | **Exhibition Seduction is unreachable.** `hunt_seduction` and `hunt_street_random` sit in `exposureRestrictedBuildings`. Any non-clothed exposure gets bounced to `search_for_prey` before the branch routing in `hunt_seduction` ever runs. | ~198457 | All four branches (bra, top, btm, nkd), m and f, 64 scenes, 8 gallery entries: dead in normal play. |
| B3 | **Thrill decays while exposed.** `_anyExhibitionState` counts naked/topless/bottomless/braless/commando/wet, but not `underwear_only` and not sheer. Walking around in underwear, or in a sheer piece over underwear, decays thrill at -10/hr. The sheer block's +1/hr loses to the decay every time. The guide says thrill doesn't decay in any exposure state. | ~32942 | Sheer and underwear states bleed thrill. |
| B4 | **Underwear-only earns no passive thrill.** Every other exposure state has a passive rate. Bra-and-panties on a public street has none. | ~32864 | Inconsistent with every neighboring state. |
| B5 | **"BRA & PANTIES ONLY" banner hardcodes "Your dress is gone."** Fires identically for separates. | ~54607 | Clothing awareness miss. |
| B6 | **Braless/commando/both minutes are tracked but never displayed.** The guide says they're visible in the exhibition stats panel. They aren't. | ~32143 | Guide/UI mismatch. |
| B7 | **Partial outerwear doesn't hide sheer chest pieces.** `getVisibleSheerPieces()` only checks `full`. A hoodie over a mesh top still fires sheer thrill and sheer interiority. | ~37949 | Concealment inconsistency. |
| B8 | **Wrong material tags.** `jaewon_mesh_top` is `material:synthetic` (a `mesh` material exists at ~36511: 1.5x damage, "Thin"). | ~42587 | Durability and future material checks read wrong. |
| B9 | **Noir upscale list references `designer_bodysuit`** and never listed `lace_bodysuit`. Both are being replaced. | ~42104 | New dresses won't pass Lucius/Regency doors unless added. |
| B10 | **Hardcoded "cotton."** 69 instances of "cotton" inside the 20 exhibition random events alone (~164600-168900), 342 file-wide. Many describe Kelsie's current garment generically ("cotton panties," "scraping cotton," "thin cotton"). She could be in satin, lace, leather, or mesh. The Clothing Awareness overhauls fixed garment *names* but never fabric. | exhibition events, braless/commando pools, alley masturbation scenes, wet systems | Direct contradiction whenever she's wearing anything but cotton, and mesh is the worst case. |
| B11 | **Underwear-only corrupted district responses are euphemistic.** "The attention settles on you like a second skin. Warm. Specific." violates Rule 9. The mid and low tiers are also soft. | `handleUnderwearDistrictEntry()` ~22040 | Register miss in a system mesh lingerie will share. |
| B12 | **Sheer thrill fires in private spaces outside the unequip list.** The check excludes `PRIVATE_UNEQUIP_SCENES` only, so Jaewon's bedroom and other non-public scenes still tick sheer thrill. | ~32894 | Thrill from "exposure" with nobody around. |

---


---

## Part H — Original spec text for completed material, VERBATIM (reference only)

> Everything here is **already implemented.** Part C records exactly what was built and where it deviates, with user approval. It's included so this handoff contains the whole original document, and so a later audit can check the built work against the spec.

### (Original title) Mesh & Sheer Clothing System: Overhaul

---

### How to Read This Document

Organized into **21 sequential steps**. One step per output. Wait for confirmation before proceeding. Don't bundle steps.

Same conventions as all prior overhauls: short paragraphs, contractions, escaped apostrophes in all JS strings, `node --check` after every step, no AI-isms, no banned style-guide patterns. All new prose is 2nd person present tense. Crude anatomical language throughout, at every corruption tier. No em dashes except paired parentheticals or dialogue cutoffs. Paragraph density scan (>300 visible chars) on every new or touched scene. "Cum," never "come," for orgasm.

**Line numbers below are approximate.** They're from the pre-edit upload. Nathan has since renamed the crop top image key and is replacing the bodysuits, so everything shifts slightly. Always `grep -n` before editing.

**Standing lessons carried forward:**
- Body branching ternaries break the string with UNESCAPED quotes; after `: '')` or `: 'text')` you MUST add `+ '` to reopen the string.
- Scene Gallery sets `gameState.corruption` directly. Never invent `_galleryCorruption` style flags. Use `flagOverrides` in the registry for state that isn't corruption (the alley masturbation entries show the pattern).
- Deeply Corrupted is always 8001.
- Braless/commando checks go through `cv.braless` / `cv.commando`, never raw outfit checks. This overhaul adds sheer fields to `cv` and the same rule applies to them.
- Static `text:` properties that reference `cv` must be converted to `text: function()`.

---

### The Mesh Wardrobe (verified in file, plus two incoming)

| # | ID | Name | Slot | Store | Weave | Notes |
|---|---|---|---|---|---|---|
| 1 | `bo_off_shoulder_mesh_mini_dress` | Off-Shoulder Mesh Mini Dress | dress | Beauty Obsessed | sheer (black) | Slides off both shoulders into short sleeves, geometric openings, sheer everywhere. `upscale: true`, `sheer: true` already. |
| 2 | `bo_mesh_mini_skirt` | Sheer Mesh Mini Skirt | bottom | Beauty Obsessed | sheer (black) | Ruched sides, solid waistband. `sheer: true` already. |
| 3 | `bo_rainbow_halter_mini_dress` | Rainbow Fishnet Mini Dress | dress | Beauty Obsessed | fishnet (rainbow) | Halter, open back, pink trim, wide diamond holes. `sheer: true` already. Skin sits bare inside every hole. |
| 4 | `jaewon_mesh_top` | Jaewon's Sheer Mesh Top | top | Jaewon's wardrobe | sheer | `owner: "jaewon"`, borrowing needs friendship 60+ (~42720). Tagged `material:synthetic` (bug, see 1F). |
| 5 | `mesh_club_dress` | Mesh Club Dress | dress | Urban Edge ($1000) | semi-sheer | Passes the upscale door check (`urbanEdgeDresses`, ~42089). |
| 6 | `mesh_tease_bra` | Sheer Mesh Bra | bra | Urban Edge ($12) | sheer | Half of the Mesh Tease Set (+8 appeal, ~37150). |
| 7 | `mesh_tease_panties` | Sheer Mesh Panties | panties | Urban Edge ($6) | sheer | Other half of the set. |
| 8 | `mesh_crop` | Mesh Crop Top | top | Urban Edge ($260) | sheer | Nathan fixed the image key (`mesh_crop_top` to `mesh_crop`). Crop: bare midriff, underboob on a reach. |
| 9 | `mesh_party_dress` *(proposed ID)* | Mesh Party Dress | dress | Noir Boutique | sheer (black) | Replaces `designer_bodysuit`. Image `Images/clothing/noirboutique/mesh-party-dress.png`. |
| 10 | `one_shoulder_club_dress` *(proposed ID)* | One-Shoulder Mesh Club Dress | dress | Noir Boutique | sheer (chocolate) | Replaces `lace_bodysuit`. Image `Images/clothing/noirboutique/one-shouldered-club-dress.png`. |
| (11) | `bo_leopard_halter_mini_dress` | Sheer Leopard Mini Dress | dress | Beauty Obsessed | sheer (leopard) | **Carried forward.** Not on Nathan's mesh list, but BO Step 2E already flags it `sheer: true` and it's wired into every existing sheer consumer. Dropping it would regress BO. It joins the registry as a sheer piece. |

**The two new Noir dresses, read from the images:**

- **Mesh Party Dress** (image 1): black sheer mesh on thin spaghetti straps. Sheer sweetheart cups gather at the center into an O-ring hung from a chain halter around the neck. A cutout under the bust leaves a strip of bare skin between the cups and the waist panel. Ruched side seams. Short slits at the hem closed with ribbon ties. Every inch that isn't cutout is see-through, the cups included. Signature hooks: the O-ring, the underbust window, the ribbon ties that can come loose.
- **One-Shoulder Mesh Club Dress** (image 2): chocolate-brown sheer mesh, asymmetric neckline off one shoulder with a thin halter string on the bare side, long flared bell sleeves, a drawstring that ruches the skirt up one hip, mini hem. The product shot shows a bra clearly through it, which is exactly the "framed" state below. Signature hooks: the bare shoulder and its single string, the bronze haze over skin, the drawstring that hikes the hem.

If Nathan's replacement uses different IDs, swap the keys in `MESH_GARMENTS` (Step 2A) and in the Noir upscale list (Step 1F). Nothing else in this plan hardcodes them.

**Out of scope, noted for later:** `sheer_lace_bra`, `sheer_lace_panties`, `sheer_silk_top` (Noir), `lace_dress`, `lace_cami` (Urban Edge). All of them are semi-transparent by description. The registry makes adding them a one-line change each.

---


### The Sheer State Model (reference for every step)

Mesh is evaluated per zone, then combined. The existing slot-based states (naked, topless, bottomless) keep ownership whenever they apply. Sheer states only exist when getExposureLevel says `clothed` or `underwear_only`.

**Zone status** (chest and hips, computed separately):

| Status | Meaning |
|---|---|
| `opaque` | An opaque outer garment covers the zone. Underwear doesn't matter. |
| `framed` | A sheer outer garment over opaque underwear. The underwear is on display. |
| `veiled` | Bare skin visible through a sheer outer garment (nothing under it, or sheer underwear under it). |
| `lingerie` | Sheer underwear with no outer garment over the zone. |
| `underwear` | Opaque underwear with no outer garment (existing visible-underwear state). |
| `bare` | Nothing at all (existing topless/bottomless). |

Outerwear: `full` concealment forces both zones `opaque`. `partial` forces chest `opaque` (fixes B7).

**Combined state** (`getSheerState()`, priority top to bottom):

| State | Condition | Reads as | Leave-house gate |
|---|---|---|---|
| `sheer_lingerie` | chest `lingerie` AND hips `lingerie` | Naked with a technicality | 8001 (matches naked) |
| `sheer_lingerie_top` | chest `lingerie`, hips anything but `lingerie` | Topless-lite in underwear | 6001 (matches visible underwear) |
| `sheer_lingerie_bottom` | hips `lingerie`, chest anything but `lingerie` | Bottomless-lite in underwear | 6001 |
| `sheer_bare` | chest `veiled` AND hips `veiled` | Naked in a dress | 4001 |
| `sheer_commando` | hips `veiled` | Bare pussy on display through the mesh | 4001 |
| `sheer_braless` | chest `veiled` | Bare tits on display through the mesh | 2001 |
| `sheer_framed` | any zone `framed` | A deliberate, legal showcase | none |
| `none` | no sheer involvement | | |

Secondary flags returned alongside the state, for prose: `stacked` (sheer underwear under a sheer outer: two grids and her nipples still show), `framedChest` / `framedHips` (the other zone is framed, e.g. mesh dress braless over panties), `weave` (sheer / semi / fishnet), `tint`, and `garments` (the actual items per zone).

**Weave multiplier** (applies to passive thrill, event thrill, and evidence rolls): semi 0.7, sheer 1.0, fishnet 1.1 (the skin inside each hole is literally bare).

**The gate ladder rationale:** framed stays street-legal, exactly as BO promised for that look. Nipples through mesh is a fashion edge case, so it unlocks early. Visible pussy is a real line, so it sits with Experienced. Sheer underwear with nothing over it follows the rules for the underwear it is, and the full set alone follows naked.

---


### Step 1: Pre-Existing Bug Fixes & The New Noir Dresses

Fix everything in the bug table except B10 (Step 3 owns it) before touching mesh.

#### 1A. Passive accrual remainder carry (B1)

Add a fractional accumulator:

```javascript
// Carries fractional passive gains across short advanceTime() ticks.
function accruePassive(key, perHourRate, minutes) {
    if (!gameState.flags._passiveAccum) gameState.flags._passiveAccum = {};
    var acc = gameState.flags._passiveAccum;
    var total = (acc[key] || 0) + (perHourRate * minutes / 60);
    var whole = Math.floor(total);
    acc[key] = total - whole;
    return whole;
}
```

Convert every passive exposure gain in `advanceTime()` to it: braless/commando/both corruption, braless/commando/both thrill, the sheer block (Step 5 replaces it anyway), topless/bottomless/naked thrill (convert "+2 per 20 min" to 6/hr, etc.), naked corruption, wet thrill, the thrill-to-arousal feed, and both friction arousal blocks. When the underlying state isn't active that tick, zero its key so a remainder can't carry across unrelated sessions.

**Balance warning:** these gains have effectively been off for short-tick play. After the fix, braless all day actually earns its +1/hr. Playtest one in-game day in each state and compare against the guide's stated rates. The rates don't change; they finally apply.

#### 1B. Exhibition Seduction reachability (B2)

Remove `hunt_seduction` and `hunt_street_random` from `exposureRestrictedBuildings`. Hunting on the street while exposed is the entire premise of Exhibition Seduction. Verify the other seduction entry points (`seduce_street_man`, `seduce_street_woman`) handle exposed states without text contradictions; the exposed branches route away from them at 6001+/80 appeal, and below that the plain street seduction scenes fire. Scan those two scenes for "your top"/"your skirt" assumptions and add a one-line exposure acknowledgment where needed.

#### 1C. Thrill decay and underwear-only thrill (B3, B4)

Add `underwear_only` and every sheer state except `none` to `_anyExhibitionState`. Add underwear-only passive thrill at 3/hr (via `accruePassive`). Update the guide's Thrill Sources line.

#### 1D. Underwear banner fix (B5)

"BRA & PANTIES ONLY" subtext branches: dress removed vs. separates removed. Use the last-removed info if tracked; otherwise write it neutrally: "No top, no bottoms. You're walking around in your underwear."

#### 1E. Stats panel rows and partial concealment (B6, B7)

Add "Time braless," "Time commando," "Time braless & commando" rows to the thrill panel lifetime stats. In `getVisibleSheerPieces()`, `partial` concealment nulls `res.chest`. (Step 2 replaces this function's internals, but fix it now so nothing regresses in between.)

#### 1F. Tags and the Noir upscale list (B8, B9)

- `jaewon_mesh_top`: `material:synthetic` to `material:mesh`. Also update the matching entry in any Jaewon wardrobe seeding function and run the fix on saved copies in Step 20.
- Noir upscale list: replace `designer_bodysuit` with `mesh_party_dress` and `one_shoulder_club_dress` in the Dresses row.

#### 1G. The two Noir dresses (verify Nathan's replacement)

Nathan is replacing the bodysuits himself. This sub-step verifies the replacement and fills gaps. Suggested definitions if any field is still open:

```javascript
{ id: "mesh_party_dress", name: "Mesh Party Dress", appeal: 96, price: 2200,
  description: "Black mesh on thin straps. The cups gather into an O-ring on a chain halter, a cutout bares the skin under your bust, and ribbon ties close the slits at the hem. You can see through every inch of it, the cups included.",
  effect: [{ type: "seduction_bonus", value: 0.05, desc: "+5% Seduction" }, { type: "xp_bonus", stat: "charisma", value: 0.03, desc: "+3% Charisma XP" }],
  tags: ["glamorous", "provocative", "fit:tight", "material:mesh", "occasion:club"] },
{ id: "one_shoulder_club_dress", name: "One-Shoulder Mesh Club Dress", appeal: 102, price: 2600,
  description: "Chocolate mesh cut off one shoulder, a single halter string on the bare side, long bell sleeves, and a drawstring that ruches the skirt up your hip. Sheer from neckline to hem.",
  effect: [{ type: "seduction_bonus", value: 0.04, desc: "+4% Seduction" }, { type: "thirst_reduction", value: 0.05, desc: "-5% Thirst" }],
  tags: ["glamorous", "edgy", "fit:tight", "material:mesh", "occasion:club"] }
```

Check every place the bodysuit IDs lived: the Noir `dresses` array (~50145), the image map (~49093, ~49139), the SVG map if either had one, the upscale list, any ensemble or smart-suggestion table. `grep -n "bodysuit"` should return zero hits when done. Old saves holding a bodysuit are handled in Step 20.

#### 1H. Underwear-only register pass (B11)

Rewrite the three corrupted, three mid, and three low responses in `handleUnderwearDistrictEntry()`'s underwear branch to the Rule 9 register. Low still carries shame, but the body reacts in anatomical terms: nipples stiff against the cups, pussy damp against the panties, clit throbbing under the stares. Sheer lingerie gets its own pools in Step 8; this fixes the opaque-underwear path it would otherwise fall back to.

#### 1I. Private-space thrill guard (B12)

Add `isPrivateExposureScene(sceneId)`: true for `PRIVATE_UNEQUIP_SCENES` plus any `jaewon_apartment_*` scene. Every passive exposure thrill gain (sheer now, and the new blocks in Step 5) checks it. Braless/commando thrill at home is a separate balance question; leave it alone unless Nathan asks.

---

### Step 2: Foundation: The Mesh Registry & Sheer-Aware `cv`

#### 2A. `MESH_GARMENTS` registry

An ID-keyed table. The registry is the source of truth. Saved items never need re-stamping and store items never need a flag copied.

```javascript
// Mesh & Sheer Overhaul, Step 2A. Source of truth for see-through garments.
// zones: which zones the garment shows through. weave: sheer | semi | fishnet.
// shape flags feed garment-specific prose hooks.
var MESH_GARMENTS = {
    bo_off_shoulder_mesh_mini_dress: { zones: ['chest','hips'], weave: 'sheer',   tint: 'black',     noun: 'mesh',    shape: { offShoulder: true, cutouts: true, mini: true } },
    bo_mesh_mini_skirt:              { zones: ['hips'],         weave: 'sheer',   tint: 'black',     noun: 'mesh',    shape: { ruched: true, solidWaistband: true, mini: true } },
    bo_rainbow_halter_mini_dress:    { zones: ['chest','hips'], weave: 'fishnet', tint: 'rainbow',   noun: 'fishnet', shape: { halter: true, openBack: true, mini: true } },
    bo_leopard_halter_mini_dress:    { zones: ['chest','hips'], weave: 'sheer',   tint: 'leopard',   noun: 'sheer fabric', shape: { halter: true, mini: true } },
    jaewon_mesh_top:                 { zones: ['chest'],        weave: 'sheer',   tint: null,        noun: 'mesh',    shape: { jaewons: true } },
    mesh_club_dress:                 { zones: ['chest','hips'], weave: 'semi',    tint: null,        noun: 'mesh',    shape: { mini: true } },
    mesh_crop:                       { zones: ['chest'],        weave: 'sheer',   tint: null,        noun: 'mesh',    shape: { crop: true } },
    mesh_tease_bra:                  { zones: ['chest'],        weave: 'sheer',   tint: null,        noun: 'mesh',    shape: {} },
    mesh_tease_panties:              { zones: ['hips'],         weave: 'sheer',   tint: null,        noun: 'mesh',    shape: {} },
    mesh_party_dress:                { zones: ['chest','hips'], weave: 'sheer',   tint: 'black',     noun: 'mesh',    shape: { oRing: true, underbustCutout: true, slitTies: true, mini: true } },
    one_shoulder_club_dress:         { zones: ['chest','hips'], weave: 'sheer',   tint: 'chocolate', noun: 'mesh',    shape: { oneShoulder: true, bellSleeves: true, drawstring: true, mini: true } }
};
function getMeshProfile(item) { return (item && MESH_GARMENTS[item.id]) || null; }
```

Tints only appear where the art confirms them. `null` tint means prose says "mesh" or "sheer" with no color.

#### 2B. `getSheerState()`

Implements the State Model above. Returns:

```javascript
{ state: 'sheer_bare', chest: 'veiled', hips: 'veiled',
  chestGarment: item, hipsGarment: item, chestUnder: item|null, hipsUnder: item|null,
  stacked: false, framedChest: false, framedHips: false,
  weave: 'sheer', weaveMult: 1.0, tint: 'black' }
```

When two sheer layers disagree on weave (stacked), use the more transparent one. When zones use different garments (mesh crop over a mesh skirt), `weave` is the chest garment's and a `hipsWeave` field carries the other.

#### 2C. `getVisibleSheerPieces()` becomes a wrapper

Rewrite its internals to read the registry through `getSheerState()` and keep its return shape (`{ chest, hips }`) so every BO consumer keeps working unchanged. The `item.sheer` flag stays on BO items (normalizeBOItem maintains it) but nothing reads it anymore.

#### 2D. `cv` extension

Add to `getSceneClothingVars()`:

| Field | Value |
|---|---|
| `sheerState` | `getSheerState().state` |
| `topSheer`, `bottomSheer` | the outer garment covering that zone is in the registry |
| `braSheer`, `pantiesSheer` | the worn bra/panties are in the registry |
| `seeThroughChest`, `seeThroughHips` | zone status is `veiled` or `lingerie` (strangers can see bare skin there) |
| `meshNoun` | `'mesh'`, `'fishnet'`, or `'sheer fabric'` for the most visible garment |
| `topFabric`, `bottomFabric`, `braFabric`, `pantiesFabric` | material noun from the item's `material:` tag, see Step 3 |

`cv.braless` and `cv.commando` keep their current meaning (no bra/panties under a covering garment). A mesh bra is still a bra. Any system that needs "can strangers see her nipples" reads `cv.seeThroughChest`.

#### 2E. Helpers for prose

- `getSheerZoneLine(zone, context)`: body-branched fragment of what shows through. Uses breast size / butt size / body type exactly like `exhibitionCrowdFixation()`. Chest, sheer, large: "your heavy tits behind the mesh, nipples dark and stiff and pressing the grid." Hips, fishnet: "your bare pussy in the diamonds of the fishnet, a slice of slit in every hole."
- `getSheerSignatureLine(garmentId, corruptionVoice)`: one garment-specific line for the eleven registry pieces. Examples of the hooks: the Mesh Party Dress O-ring sitting between tits you can see straight through; the underbust window; the One-Shoulder dress's single string being the only thing on that side of her chest; Jaewon's top smelling like Jaewon; the crop top leaving her stomach bare under a see-through chest; the fishnet's holes printing diamonds of pressure on her nipples.

---

### Step 3: Fabric Awareness Pass (B10)

Mesh can't land while the prose keeps calling every garment cotton.

#### 3A. Fabric nouns

`getFabricNoun(item)` reads the `material:` tag: cotton, denim, lace, satin, silk, leather, velvet, wool, synthetic ("fabric"), mesh ("mesh"), fishnet (via registry), lace. Unknown or missing tag falls back to "fabric." Feeds the four `cv.*Fabric` fields.

#### 3B. Classify and convert

Run `grep -n "cotton"` and classify each hit:

1. **Kelsie's current garment referenced generically** ("scraping cotton," "thin cotton of your top," "your cotton panties"): convert to the matching `cv.*Fabric` variable. Grammar check each one; "scraping lace" and "scraping mesh" both work, "the leather of your panties" doesn't exist in the wardrobe so it won't come up, but "the denim" needs "your" phrasing checks.
2. **Fixed garments** (diner uniform, a specific NPC's shirt, a named item that really is cotton): leave.
3. **Flashback / CC / story text**: leave.

Priority order for the pass: the 20 exhibition random events (~69 hits), the braless/commando ambient and friction pools and banners, the three `masturbate_*_alley` scenes, the wet interiority and NPC layers, the harassment grope layers. Everything else is optional and can be logged for a later sweep.

#### 3C. Mesh-specific friction wording

When the fabric is mesh, the friction itself is different: rougher, a grid pressing into skin, faint crosshatch marks left on nipples after hours. Step 7 writes the lines. This sub-step only ensures the pools have a `{fabric}` placeholder where the old text said cotton, resolved in the consumer like `{top}`.

---

### Step 4: Exposure Rules: Gates, Police, Venues, Transit

#### 4A. Leave-house gates

New `handleSheerDistrictEntry(districtId)`, called in every district street `onEnter` right after the underwear/topless/bottomless block and before `checkAccidentalExposure()`: commercial (~102512), residential (~102677), slums (~102861), medical (~108217), downtown (~108309), wendale (~108451), fitness (~177984). Also check `riverside_park` (~135516), which handles exposure itself.

If the sheer state's gate (State Model table) isn't met and `clothesDestroyedInCombat` isn't set, show a "No Way" block in the same style as the underwear block and return to `gameState.previousScene`. Block text per state, low voice, crude and specific: she looks down and sees her nipples through the mesh, or her slit through the skirt, and she's not walking out like that.

`sheer_lingerie_top` / `_bottom` / `sheer_lingerie` are also `underwear_only` by slot, so `checkVisibleUnderwearCorruption()` already fires first. Update it: if both visible underwear pieces are sheer, require 8001 instead of 6001. Its block text gets a sheer variant.

The gate passes → the rest of `handleSheerDistrictEntry()` runs the vignette (Step 8).

#### 4B. Sheer lingerie police clock

`sheer_lingerie` gets its own clock: `gameState.sheerLingerieStartTime`, set on first street entry in the state, cleared when the state ends. Police arrive at **3 hours** (naked is 2). `handleSheerPoliceArrive()` reuses `handlePoliceArrive()`'s structure with a sheer script at all three corruption tiers. The officer's problem is the technicality. Sample (high corruption):

> The cruiser pulls up and the officer gets out with his notepad already open. He looks at you anyway. Your nipples dark behind the mesh bra. The bare slit of your pussy behind the mesh panties.
>
> "Ma'am, is that..." He stops and tries again. "Is that underwear?"
>
> "Lingerie."
>
> "I can see your..." He doesn't finish. His ears go red.

Escape flows through `handleNakedEscape()` unchanged.

#### 4C. Venue policy

Extend `getExposureBlockMessage()` with a sheer tier. `sheer_lingerie*` states are already blocked everywhere (underwear_only). New rules:

| State | Blocked at | Allowed |
|---|---|---|
| `sheer_framed` | nowhere | everywhere |
| `sheer_braless` | hospital, library, blood bank, dojang | everywhere else |
| `sheer_commando` / `sheer_bare` | everything in `exposureRestrictedBuildings` except the allowed list | Lucius' Lounge, Hardy Bar, Urban Edge (it sells the mesh), the diner (see below) |

Block prose names the state accurately: "your nipples are right there through the mesh" rather than "completely naked."

**Diner:** Kelsie arrives, changes into the uniform. No block. Step 8 adds a one-time-per-shift line from Ruby when she walks in `sheer_bare` or `sheer_commando` (Ruby's reaction scales with Kelsie's standing, not her corruption).

**Lucius' Lounge:** add a doorman entry line when she arrives in a sheer state at night. The mesh dresses already pass the upscale check. At `sheer_bare` he waves her through without checking anything but her tits.

#### 4D. Transit

Bus and Uber refuse `underwear_only`, which covers all lingerie states. For `sheer_bare` and `sheer_commando`: boarding is allowed, with a one-line driver reaction (bus) or a rating-neutral driver comment (Uber). The bus exhibition event (`exhibition_bus_trigger`, fires only via bus travel) gets a sheer overlay in Step 10.

---

### Step 5: Passive Mechanics

#### 5A. Replace the BO sheer block

Delete the BO Step 2E passive block (~32891) and replace it with a sheer block driven by `getSheerState()`. All gains go through `accruePassive()` (1A), skip `isPrivateExposureScene()` (1I), and multiply thrill by `weaveMult`.

| State | Thrill | Corruption | Arousal | Stacks with |
|---|---|---|---|---|
| `sheer_framed` | +1/hr | +1/hr | none | nothing (underwear is on) |
| `sheer_braless` | +3/hr | +2/hr | +1/hr mesh scratch | braless +1/+1 and braless friction |
| `sheer_commando` | +4/hr | +3/hr | none extra | commando +2/+2 and commando friction |
| `sheer_bare` | +6/hr | +4/hr | +1/hr mesh scratch | both-state +4/+4 and both friction |
| `sheer_lingerie_top` / `_bottom` | +9/hr | +3/hr | none extra | underwear-only +3/hr thrill (1C) is replaced, not stacked |
| `sheer_lingerie` | +12/hr | +4/hr | none extra | replaces underwear-only |

For reference, naked is +15/hr thrill and +5/hr corruption. `sheer_bare` totals +10/hr thrill and +8/hr corruption with the both-state stack; `sheer_lingerie` sits just under naked. That's the "somewhat different from naked" gap: close, deliberately lower on thrill, and routed through its own content.

The mesh scratch arousal is physical. Nylon mesh is rougher than cotton on bare nipples, and hours of it leave them swollen.

#### 5B. Wet and sheer don't double-dip

Wet see-through and mesh see-through describe the same thing: strangers can see skin. When both apply to a zone, passive thrill uses the higher of the two rates, not the sum. Wet mesh doesn't get more transparent; it clings. Step 13 handles the prose.

#### 5C. Minute tracking

In the lifetime tracking block, add `exhibitionStats.sheerMinutesTotal` (any sheer state), `sheerBareMinutesTotal` (`sheer_bare`), `sheerLingerieMinutesTotal` (`sheer_lingerie*`). Display in the stats panel (Step 6).

#### 5D. Appeal: the sheer showcase

In `calculateTotalAppeal()`, when a zone is `veiled` or `lingerie`, add a partial naked body bonus: 25% of `getNakedAppealBonus()` per visible zone, capped at 50%. `sheer_lingerie` therefore gets half the naked body bonus on top of the underwear's own appeal. Show it in the sidebar as a "Sheer Showcase" line. Good bodies look good through mesh.

#### 5E. Exhibition draw

`getExhibitionDrawMultiplier()` is untouched, but every new sheer event and vignette multiplies its thrill and arousal by it, matching the existing events.

---

### Step 6: Sidebar Banners & Stats Panel

#### 6A. Sheer banners replace braless/commando banners when the bare zone is behind mesh

The current braless banner says "your nipples press against the inside of your top, stiff and visible." Behind mesh that's wrong: nothing is pressing through, they're simply on display. Priority in the clothed-state banner block:

1. `sheer_bare`: one combined banner, "✦ Sheer & Bare" (replaces the both banner)
2. `sheer_braless` / `sheer_commando`: "✦ Sheer (Braless)" / "✦ Sheer (Commando)" (replaces the single braless/commando banner for that zone; the other zone's regular banner still shows if it applies, e.g. mesh top braless over a denim skirt commando)
3. `sheer_framed`: "✦ Sheer" showcase banner (new)

Colors: a smoky violet-black border to separate it from the pink braless and purple commando boxes. Images: reuse the existing breast-size and commando images for now. Optional new art: `Images/body/sheer-chest.png`, `Images/body/sheer-hips.png`, `Images/body/sheer-lingerie.png` with `onerror` fallback to the existing images.

Three corruption tiers each, garment named via the registry item, crude at every tier. Sample, `sheer_bare`, high:

> Nothing under the mesh. Your nipples sit dark and stiff behind your {dress} and your bare cunt's a clean shadow below it. You're dressed and everyone can see all of you. Your pussy hasn't stopped dripping since you left.

Sample, `sheer_framed`, mid:

> Your {under} shows through your {sheer}, every line of it framed on purpose. Strangers read it from across the street. Your nipples tighten in the cups every time one of them does.

#### 6B. Sheer lingerie warning box

`sheer_lingerie*` states currently render the pink "BRA & PANTIES ONLY" box. Add a sheer variant: label "SHEER LINGERIE ONLY" (or "SHEER BRA EXPOSED" / "SHEER PANTIES EXPOSED"), subtext that names what shows through, same slot list below it, plus the Sheer Showcase appeal line from 5D.

#### 6C. Stats panel

- Exposure label: add `Sheer`, `Sheer (Braless)`, `Sheer (Commando)`, `Sheer & Bare`, `Sheer Lingerie` with their own colors. Show the sheer label when the sheer state isn't `none` and slot exposure is `clothed` or `underwear_only`.
- Lifetime rows: the three B6 rows (from 1E) plus "Time in sheer," "Time sheer & bare," "Time in sheer lingerie."

---

### Step 7: Ambient Interiority & Friction

#### 7A. `checkSheerExposureAmbientLine(sceneId)`

One handler for all bare-behind-mesh states, fired from the `showScene()` hook chain **before** the both/braless/commando handlers. When it fires, it stamps `_lastSheerExposureAmbientTime` and the braless/commando/both handlers yield if that stamp equals `_now` (same yield pattern the both handler uses today). 30-minute cooldown, guaranteed fire on eligible street transitions, suppressed by concealment through `getSheerState()`.

Pools (4 corruption tiers: low 0-2000, mid 2001-4000, midHigh 4001-6000, high 6001+):

| Pool | Lines | Focus |
|---|---|---|
| `SHEER_BRALESS_EXPOSURE_LINES` | 12 (3/tier) | Bare tits on display through the mesh, strangers' eyes landing on nipples, mesh scraping them |
| `SHEER_COMMANDO_EXPOSURE_LINES` | 12 (3/tier) | Bare pussy visible through the skirt or dress, the outline of her slit, slick showing at the right angle |
| `SHEER_BARE_EXPOSURE_LINES` | 8 (2/tier) | Naked in a dress, the whole body read at once |

Mixed into every draw: one weave line (fishnet, semi), one garment signature line from `getSheerSignatureLine()` at 1-in-3, and a cold-weather line when temperature is cold/cool (nipples rigid and fully visible through the mesh). The existing `SHEER_BRALESS_AMBIENT_LINES` / `SHEER_COMMANDO_AMBIENT_LINES` supplements get folded into these pools and deleted from the braless/commando handlers.

Samples:

- high, braless: "Your bare tits are right there under your {top}, nipples dark and stiff behind the mesh. Everyone you pass gets the full shape of them. Your clit throbs every time someone's eyes drop and stay."
- low, braless: "There's nothing under your {top}, and the mesh hides nothing. Your nipples are stiff and dark behind it and the whole street can see them. You fold your arms over your chest. Your pussy clenches anyway."
- mid, commando: "Your bare pussy shows through your {bottom} with every step, slit and folds and the wet shine on them. You press your thighs together. It only gives them a better outline."
- high, bare: "You're naked in a dress. Nipples stiff behind the mesh, your bare cunt a dark slit through the {bottom}. Every stare lands somewhere specific, and every one of them makes you wetter."
- fishnet supplement: "Your left nipple's pushed through one of the diamonds in your fishnet. Bare and stiff, right out in the open air."
- stacked supplement: "Two layers of mesh and it doesn't matter. Your nipples show through both, darker where the grids overlap."

#### 7B. Expand `SHEER_AMBIENT_LINES` (framed)

The underwear-behind-sheer pool goes from 8 lines (2/tier) to 12 (3/tier). The existing 8 are good; add one per tier. Keep `checkSheerAmbientLine()` as the framed handler, keep its 45-minute cooldown, and have it yield to 7A on the same transition.

#### 7C. Sheer lingerie in `EXHIBITION_AMBIENT_LINES`

Add a `sheer_lingerie` key (innocent / experienced / corrupted, 3 each) and route `checkExhibitionAmbientLine()` to it when `getSheerState()` returns a lingerie state (otherwise it keeps using `underwear_only`). The hook: she's technically dressed.

- corrupted: "Mesh bra, mesh panties, and nothing hidden under either. Your nipples push the grid and your slit's right there behind the panties. Technically you're dressed. Your dripping pussy says otherwise."
- innocent: "You're wearing underwear. You keep telling yourself that. Your nipples show through it and so does your pussy, and a woman at the crosswalk covers her son's eyes."

#### 7D. Mesh friction lines

When the fabric under friction is mesh (`cv.topFabric`/`cv.bottomFabric` is mesh and the zone is bare under it), the braless/commando/both friction handlers draw from mesh variants: 4 braless, 4 commando, 2 both, tiered high/low. The grid is rougher than cotton, it scrapes, and after an hour it leaves faint crosshatch marks on swollen nipples.

- braless, high: "The mesh grid scrapes your bare nipples with every step, rougher than any cotton. By the next block they're swollen and aching, and the ache runs straight down to your clit."
- commando, low: "The mesh presses a grid into your bare folds when you sit. You can feel every little square of it on your clit. You stand up fast and you're wetter than when you sat down."

---

### Step 8: District Entry Vignettes & District Awareness

#### 8A. `handleSheerDistrictEntry(districtId)` vignettes

After the gate (4A) passes, fire an NPC vignette plus Kelsie's response. Shares the 20-minute `lastExhibStateEventMinute` cooldown with naked/underwear entries so only one exposure reaction fires per window.

| State | NPC pool | Kelsie responses | Stats (low / mid / high corruption) | Suspicion |
|---|---|---|---|---|
| `sheer_framed` | 4 | 3 tiers × 2 | Arousal +2/+4/+6, thrill +3/+5/+7. Fires at 30% only (it's a look, not an incident) | 0 |
| `sheer_braless` | 6 | 3 tiers × 3 | Arousal +4/+8/+12, MH -2/0/+2 | +1 |
| `sheer_commando` | 6 | 3 tiers × 3 | Arousal +5/+10/+15, MH -3/0/+3 | +2 |
| `sheer_bare` | 6 | 3 tiers × 3 | Arousal +6/+12/+18, MH -4/0/+3 | +2 |
| `sheer_lingerie*` | 8 | 3 tiers × 3 | Arousal +6/+14/+20, MH -5/0/+3 | +3 |

Thrill: apply through `applyExhibitionThrillGain()` scaled by `weaveMult` and the draw multiplier: framed 5, braless 10, commando 12, bare 16, lingerie 24. Social media heat +5 (braless/commando), +8 (bare/lingerie) in daytime busy districts.

The NPC pools share one theme: strangers negotiating what they're allowed to call it. With naked, there's no argument. With mesh, somebody always says "she's wearing something" and somebody else says "I can see her pussy."

Samples:

- `sheer_bare`: "Two guys at a bus shelter stop talking. The closer one squints at your {dress}, then at what's under it.\n\n\"Bro. She's got nothing on under that.\"\n\n\"It's see-through.\"\n\n\"I know it's see-through. That's what I'm saying.\""
- `sheer_lingerie`: "A barista wiping tables outside a café looks up and freezes, rag in hand.\n\nHer eyes go to your chest, where your nipples show dark through the mesh bra. Then lower, to the mesh panties and the bare slit behind them.\n\n\"Is that... are you wearing anything?\"\n\nShe answers herself before you can. \"You're wearing something. I can see right through it.\""
- `sheer_framed`: "A woman outside a boutique looks you over the slow, professional way women look at other women's outfits. Your {under}, framed in the {sheer}. She nods once like you passed something. Her boyfriend doesn't nod. He just stares at your tits."

Kelsie responses follow the existing topless/bottomless response functions: low is shame with the body betraying her in anatomical terms, mid is conflicted and wet, high is deliberate and hungry. Every tier names nipples, pussy, clit.

#### 8B. Ruby's diner line

One-time-per-shift intercept at shift start when Kelsie walks in `sheer_bare` or `sheer_commando` (she changes into the uniform after). Ruby's reaction, three variants: exasperated, protective, dry. No corruption scaling on Ruby; Kelsie's inner line after it scales with corruption.

#### 8C. `getSheerDistrictAwareness(districtId)`

Mirrors `getWetDistrictAwareness()`. Appended to each district street `text:` right after the wet awareness call. Fires for `sheer_braless`/`commando`/`bare`/`lingerie*`, once per district per day. Six districts (commercial, residential, downtown, slums, park, plus medical as a variant of residential) × 3 corruption tiers.

- commercial, low: "You catch yourself in a storefront window. The mesh turns to nothing in the glass. Nipples, navel, the dark slit between your thighs, all of it printed on the reflection for everyone walking behind you. You cross your arms and walk faster."
- downtown, high: "Neon hits the mesh and it glows. The crowd sees your tits in color. You slow down and let it."

Wet takes precedence when both would fire in the same district the same day; the sheer version fires on the next district instead.

---

