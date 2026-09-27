# Trigrave PD: Department Expansion

**Target implementer:** Claude Opus
**File:** `Vampire_Girl.html`
**Scope:** Trigrave PD is supposed to be the most formidable police department anyone's ever built. The city has no military because TPD fills that role: patrol, motors, K9, plainclothes, narcotics, forensics, SWAT, armored vehicles, a drone fleet, armed helicopters, and a Detective Bureau run by a woman who's never lost a case. Every officer survived a vetting and training pipeline harder than most special forces selection. Nobody in the department takes bribes. Nobody can be seduced out of doing their job. The only thing that gets past a TPD officer is something that reaches inside their head: Kelsie's compulsion, or the Mothers' psychic reach when Rufus calls in a favor. Gabriella Cruz is immune to both.

In the current file, most of that exists only in dialogue. Mechanically, TPD is a reaction to a number. The police show up when suspicion is high, they chase, they lose, and the case only lands on Kelsie if she's already been booked or her face is on a sketch. A careless, gloveless murder in the Slums with nobody watching costs about five suspicion and never gets solved. The department that's supposed to make Trigrave's criminals nervous is the easiest opponent in the game.

This overhaul turns TPD into an institution the player can feel working. It gives the city a baseline police presence that doesn't wait for Kelsie to get hot. It gives every crime a case file that investigators actively work, with leads that develop over days: canvasses, camera traces, K9 tracks, lab results, tip lines. It closes the "never booked, never matched" loophole with a lawful, very Cruz-shaped answer. It adds the units the lore promises (drones, helicopters, motors, K9, decoys, armored warrant service) as real encounter content. It introduces Chief Hugo Lars, the man who built all of it. And it puts the stealth stat at the center of everything, because stealth is how Kelsie commits a crime cleanly *and* how she covers her tracks afterward.

The current system has six structural problems:

1. **Investigation only happens after a booking.** `processPendingForensics()` matches prints only when `fingerprintsInSystem` is true, and faces only when `mugshotTaken` is true (or facial recognition hits 70). Until Kelsie's been arrested once, prints on a body are "unmatched evidence" forever. A homicide unit with a Crime Scene Unit, a camera network, K9 teams, and Cruz can't solve a murder unless the killer walks into Central Booking on her own. That's backwards for the department the lore describes.

2. **Murder is cheap.** `getBaseKillSuspicion('slums')` is 12, multiplied by the Slums' 0.4 suspicion multiplier: 5 suspicion for leaving a drained body in an alley. The pre-warrant ceiling (`SUSPICION_CAP_NO_WARRANT = 40`) means no amount of unsolved killing ever blocks anything. The user-facing promise ("it should be exceptionally difficult to get away with murder unless you have very high stealth and gloves") currently has no mechanical backing at all.

3. **TPD has no presence until Kelsie is hot.** `getMuggingPoliceChance()` starts at 5% at zero suspicion. The violent hunt police interrupt is 0% below suspicion 20. There's no baseline patrol density, no response time, no sense that the city's policed around the clock whether Kelsie's wanted or not.

4. **The department is a patrol car and a detective.** Encounters are almost always "a cruiser pulls up, flee/fight/kill/surrender." Motors, K9, drones, helicopters, armored vehicles, and plainclothes units appear only in a handful of set pieces (Cruz's SWAT raid, the TAD capture, undercover mugging targets). Fleeing with vampire speed always ends the encounter, clean.

5. **Stealth stops mattering after the crime ends.** The stealth stat drives detection during burglary, pickpocketing, blood theft, and violent hunts. Once Kelsie walks away, stealth does nothing. There's no concept of a *cover-up*: of how cleanly she exits, whether her route home is on camera, whether a dog can follow her, how much trace she left behind. Stealth should be the difference between a professional and an amateur on both sides of the crime.

6. **Some existing prose undercuts TPD's discipline.** Several exhibition police stops have officers openly leering. A hero-path officer lets Kelsie go "before my captain hears this." Exhibition bookings skip fingerprints and the mugshot entirely. None of it's corruption. All of it's sloppiness the department wouldn't tolerate.

When this is done, a clumsy Kelsie who kills without gloves should get a knock on the door within the week, however quiet the alley was. A Phantom-tier Kelsie in gloves who takes the rooftops home, hides the body, and never shows her face should be able to keep killing, with Cruz's file on "Profile Zero" getting thicker and her next hunting ground getting more crowded with plainclothes every time. The difference between those two players should be the stealth stat and the choices they make, and nothing else.

---

## How to Read This Document

Organized into **17 sequential steps**. One step per output. Wait for confirmation before proceeding. Don't bundle steps.

Same conventions as every prior overhaul: short paragraphs, contractions, escaped apostrophes in all JS strings, `node --check` after every step, no AI-isms, no banned style-guide patterns. All new prose is 2nd person present tense, always contracted ("she's," "you're," never "she is," "you are"). No paragraph exceeds ~4 lines. No em dashes in prose except for a parenthetical aside or dialogue that gets cut off.

**Canon this overhaul establishes (and every new line of prose must respect):**

- **No TPD officer is corrupt.** Bribery and seduction never work on an officer, in any scene, at any stat level. An attempt is documented and becomes evidence.
- **Two things get past TPD, and both are supernatural.** Kelsie's compulsion works on any officer except Cruz. The Mothers (an all-female criminal organization of powerful psychics) can erase crimes from every mind that holds them, TPD included, when Rufus calls in a favor. Cruz is immune to both.
- **Rufus's services are underworld work, never TPD corruption.** His lesser services run through his own criminal connections outside the department. His heaviest services run through the Mothers.
- **The underworld survives because it's in a league of its own.** The Vajaros cartel, the Boruski Syndicate, and the Mothers have wealth, compartmentalization, and (in the Mothers' case) powers TPD can't answer. TPD arrests their street layer constantly and never reaches the top. Everyone else gets caught.
- **Crime still happens.** Trigrave has muggers, burglars, and small crews like any city. TPD catches almost all of them, usually fast. The crime-fighting system exists because the streets are live; the arrest blotter exists because TPD is good.
- **Kelsie can live anywhere, or nowhere.** Varhall Apartments (her own unit, or with Jaewon), a room at the Timeless Hotel, a room at Oracle Inn, a room at the Lurasi Motel, or the street. Wherever she's staying is where TPD comes for her. The one exception is the Lurasi: TPD stays clear of Rufus and his motel, and it's the one roof in the city they won't go under. If she's homeless, there's no door to knock on, so TPD uses the cameras and takes her on the street. If she lives with Jaewon, Jaewon's in the blast radius.

**Existing systems this overhaul connects to (verified in the current file):**

Suspicion core:
- `modifyCitySuspicion(amount, reason, districtId, witnessQuality)`: master suspicion function. Applies trait/alibi/clothing reductions, the case ceiling, witness weighting (`drunk 0.5, civilian 1.0, professional 1.5, cop 2.0`), camera footage rolls against `DISTRICT_CAMERA_DENSITY`, district heat (+1.5× amount), social media heat for daytime crimes outside the Slums.
- `SUSPICION_CAP_NO_WARRANT = 40`, `SUSPICION_CAP_BY_WARRANT_CLASS`, `SUSPICION_FLOOR_BY_WARRANT_CLASS`, `getWarrantClass()`, `getSuspicionCap()`, `getSuspicionFloor()`, `enforceSuspicionCap()`: the "heat follows the case" ceiling, with `heldHeat` pouring in when a warrant raises the cap.
- `calculateWantedTier()`: Tier 1 (20+) through Tier 4 (80+). Supernatural evidence 50+/80+ and 3+ bodycam incidents escalate independently, capped by the case ceiling. Sets `violentHuntsBlocked` (Tier 4), `crimesBlocked` and `bloodTheftBlocked` (Tier 3).
- `getDistrictHeat(districtId)`, `suspicionData.districtHeat`, `hotDistricts`.
- `openCaseOnKelsie()`: sets `investigationActive`, `detectiveAssigned`, `gabriellaCruzAssigned`, `gabriellaIntroductionPending`, `caseOpenedDay`.
- `checkForSuspicionEvent()`: the one-per-day narrative queue (warrant issued, Cruz post-warrant reflection, bodycam leak, Cruz intro, servant gang events, composite sketch, news report, random recognition).
- `hasCurrentAlibi`: set from being with Jaewon, on a diner shift, or at a public job. Reduces suspicion gains by 30%.

Evidence ledger (the foundation this overhaul builds on):
- `createLedgerEntry(params)`: entries carry `type`, `day`, `district`, `timeOfDay`, `linkedToKelsie`, `chargeStatus`, `masked`, `maskSignature`, `evidence{witness, cameraFootage, forensic, circumstantial, supernatural}`, `witnesses[]`, `forensic{fingerprintsLeft, fingerprintsMatched, biteMarks}`, `camera{cameraType, cameraQuality, facialRecognitionHit, masked}`, `additionalCameras[]`, `forensicProcessed`, `bodyDiscovered`, `bodyDiscoveryDay`.
- Call sites (all verified): blood theft (three sites), fleeing, police assault, hunt-interrupt crimes (`logHuntPoliceCrime`), indecent exposure, mugging (six variants), burglary, hunt assault (partial/full detection), hunt murder (`processBodyDisposal`), arrest fight, escape, pickpocketing.
- `linkCrimeToSuspect(crimeId, method)`: methods `caught_in_act`, `fingerprint`, `facial_recognition`, `witness_id`, `pattern`, `mask_match`. Calls `openCaseOnKelsie()`.
- `processPendingForensics()`: daily. Marks entries processed after 1 day (murders wait for discovery). Matches prints only if `fingerprintsInSystem`, faces only if `mugshotTaken`. Public ID at facial recognition 70. Series linking: 3+ same type in the same district. Mask-signature grouping.
- `checkWarrantConditions()`: murder or cop-killing linked = instant warrant; 2+ linked crimes = warrant; 1 linked + FR 70 = warrant.
- `issueArrestWarrant()`, `checkForNewCharges()`, `deriveWarrantCrimesFromLedger()`, `calculateWarrantSeverityFromLedger()`, `getHighestSeverityCrime()`, `processPostEscapeConsequences()`, `recordArrestHistory()`.
- `linkAllMaskSignatureCrimes()` (the booking bomb), `unlinkMaskSignatureCrimes()` (Rufus + the Mothers).

Violent hunt pipeline (already overhauled, NOT to be rewritten):
- `rollHuntDetection()`, `calculateHuntSuspicion()`, `rollHuntCameraFootage()`, `applyDistrictHuntHeat()`, `buildCasingText()`, `getCasingCameraBlock()`, `getCasingWitnessBlock()`, `getCasingEscapeBlock()`.
- `processBodyDisposal(method, district)`: `left` / `hidden` / `water` / `vampspeed`. Creates the murder ledger entry with `fingerprintsLeft: !woreGloves`, `biteMarks: true`.
- `applyFingerprintEvidence(woreGloves, cleanedScene)`: instant warrant if prints are in the system.
- `addPendingBodyDiscovery()` / `processBodyDiscoveries()`: discovery delays by method.
- `getBaseKillSuspicion(district)`, `DISTRICT_HUNT_PROFILES` (includes `dockyard`, `fitness`, `wendale`).
- The `ambush_strike` onEnter police interrupt: `policeChance = (citySuspicion - 20) / 120` at suspicion 20+.

Stealth system:
- `gameState.stealth`, `getStealthLevel()` (Clumsy 1-5, Careful 6-15, Quiet 16-30, Silent 31-50, Ghost 51-99, Phantom 100), `getStealthBonus()` (0.0-0.60), `gainStealth()`, `calculateStealthCheck(context)`, `getCrimeStealthBodyMod()`, `getFaceWitnessMultiplier()`, `applyVampireSpeedCrimeCosts()`, `hasLeatherGloves()`, `rollCameraAvoidance()`.

Police encounters (all existing):
- Hunt interrupt tree: `police_interrupt_ambush`, `police_interrupt_surrender`, `police_flee_vampire_speed`, `police_flee_aftermath`, `police_attack_nonlethal`, `police_assault_aftermath`, `police_kill_decision`, `police_kill_execution`, `police_kill_aftermath`.
- Mugging: `mugging_police_arrive`, `mugging_flee_cops`, `mugging_knockout_cops`, `mugging_kill_cops`. `getMuggingPoliceChance(location)`.
- Warrant/arrest: `warrant_issued_event`, `rand_event_warrant_police`, `arrest_surrender`, `arrest_flee`, `arrest_fight`, `arrest_compel`, `arrest_booking`, `arrest_cell`, `arrest_cell_wait`, `arrest_escape_cell`, `arrest_cell_post_interrogation`.
- Exhibition: `tickIndecencyPolice()`, `handleIndecencyPoliceArrive()`, `handleSheerPoliceArrive()`, `offerExhibPoliceChoices()`, `doExhibPoliceCompel()`, `doExhibPoliceSurrender()`, `exhib_arrest_surrender`, `exhib_arrest_booking`, `exhib_arrest_cell`, `exhib_arrest_release`. Officer Pruitt at the property desk.
- Pickpocketing: `street_pickpocket_cop_arrest`, `street_pickpocket_cop_arrest_flee`. Off-duty cop targets (5-8%).
- Blood theft: `resolveBloodTheftCaught()`, `BLOOD_THEFT_WATCHERS` (Officer Reyes at Trigrave General).
- Drugs: `getDrugHeatLevel()`, `modifyDrugHeat()`, `drug_patrol_encounter`, `drug_raid_home`, `drug_raid_not_home`, `drug_raid_surrender`.
- Cartel war: `cruz_stakeout*`, `cruz_swat_ambush*`, `cruz_confrontation_1` through `_3c` (Operation Phantom).
- Hero path: `vigilante_police_explain`, `vigilante_police_flee`, `hero_mayor_press_conference`, `hero_mayor_first_meeting`, `mayors_office*`. Captain Alan Driscoll (TPD spokesperson). Mayor Maxine Renalds.
- Servants: `servant_gang_pattern_linked`, `servant_task_force_formed`, `servant_cruz_gang_awareness`, `servant_cruz_operation_paused`, capture/breakout systems, `recalcServantHeatMultiplier()`.
- Naked escape: `checkNakedPolice()`, `handlePoliceArrive()`, `handleNakedEscape()` (bodycam chance `0.3 + 0.15 × wantedTier`).

Detective Cruz (all existing):
- `checkCruzEncounters(returnScene)`: post-escape Day+1 confrontation (`rand_event_cruz_fugitive`), Day+2+ 50% tactical capture (`cruz_tactical_encounter`, the TAD).
- `cruz_introduction_event`, `rand_event_cruz_spotted` (five variants by context), `cruz_compulsion_attempt`, `cruz_approach`, `rand_event_cruz_direct_question`, `cruz_question_deflect/lie/gang_aware`, `cruz_gang_deflect/misdirect`, `cruz_interrogation_tactical`, `cruz_interrogation`, `cruz_interrogation_speed/compulsion/why/end`, `cruz_post_warrant_reflection`, `cruz_fugitive_compulsion_fail`.
- Hero-path Cruz: `checkCruzHeroTriggers()`, `hero_cruz_vigilante_aware`, `hero_cruz_suspects_identity`, `hero_cruz_admission`, `hero_cruz_walk`, `hero_cruz_probing`.
- Flags: `gabriellaCruzAssigned`, `sawCruzIntro`, `gabriellaCompulsionFailed`, `cruzTacticalCaptureCount`, `cruzKnowsIdentity`, `cruzVigilanteAware`, `cruzSpottedToday`, `cruzQuestionToday`.
- Established characterization: 28, warm brown skin, dark braid over one shoulder, blazer over a white tee, shoulder holster. Army, then CIA, then two TPD divisions. Runs the Detective Bureau AND SWAT, handles narcotics operations, does her own forensics when the lab's too slow. Built the TAD (Targeted Acoustic Disruptor). Calm, dry, faintly amused, bone-certain. "Whoever this is, they've been careful. Good for them. I'm better."

Rufus and the Mothers (existing, reframed in text only by Step 15):
- `rufus_service_warrant` / `_paid`: warrant cleared over 7 days (`rufusWarrantClearing`).
- Mask clearing via the Mothers (`rufusMaskClearing`), 5 days, price escalates 20% per use. Cruz immune.
- Lose the Paperwork, Handle the Witnesses, Cool a District, Scrub the Digital Trail, servant clean release, "clean up your crew."

Daily tick (verified order in `onNewDay`): district heat decay, camera footage −5, suspicion −2 on crime-free days, social media heat −5, `processBodyDiscoveries()`, `processPendingForensics()`, `checkWarrantConditions()`, `checkForNewCharges()`, `enforceSuspicionCap()`, then warrant/fugitive consequences and Rufus clearing timers.

Other connections:
- `DISTRICT_CAMERA_DENSITY` (downtown 0.90, medical 0.80, commercial 0.70, fitness 0.40, residential 0.20, park 0.15, slums 0.05). Dockyard and Wendale aren't in this table; they fall back to defaults. Step 2 fixes that.
- `gameState.weather.isRaining`, `gameState.weather.rainIntensity` (30-80 when raining).
- `getTimeOfDay()`, `isDark()`, `getThirstCap()`, `getActiveClothingEffects()` (`crime_stealth`), `getFootwearStats()` (noise).
- `checkRandomDistrictEvent(district)` / `triggerRandomDistrictEvent(eventId, returnScene)`: the district-travel event hook every new street encounter plugs into.
- Stats modal: `sections.push({ label: 'City Suspicion', ... })` inside the Current Stats builder; `openStatFromSidebar()`, `showStatDetail()`.
- Tips & Guide: "Violent Hunts" and "Crime & Suspicion" sections (BASICS, THE STEALTH STAT, MUGGING, BURGLARY, PICKPOCKETING, BLOOD THEFT, CAMERA FOOTAGE & DIGITAL EVIDENCE, DISTRICT HEAT, POLICE ENCOUNTERS, DETECTIVE CRUZ, REDUCING SUSPICION, SUSPICION EVENTS, WARRANT & ARREST, STRATEGY TIPS, STEALTH BUILDS). Also the Hero System's DETECTIVE CRUZ and VIGILANTISM IS ILLEGAL entries, and the Vampire Servant Operations CAPTURE entry.

---

## Step 1: The Department

TPD needs to exist as a thing in the code, and as a thing in the prose, before any system can lean on it. This step builds the department's data object, establishes its canon, and introduces its chief.

### 1A. The `TPD` data object

A single constant that every later step reads from. Names, callsigns, and unit labels live here so prose and UI never drift.

```javascript
// ===== TRIGRAVE POLICE DEPARTMENT =====
// The city has no military. TPD is the closest thing it has.
var TPD = {
    chief:  { name: 'Hugo Lars', title: 'Chief of Police' },
    mayor:  { name: 'Maxine Renalds' },
    pio:    { name: 'Alan Driscoll', title: 'Captain' },
    hq:     'TPD Headquarters, Fourth Street',     // Central Booking shares the block
    units: {
        patrol:     { label: 'Patrol Division' },
        motors:     { label: 'Motor Unit' },
        k9:         { label: 'K9 Unit', handler: 'Officer Dale Brenner', dog: 'Juno' },
        air:        { label: 'Air Support Unit', heli: 'Raptor One', drones: 'Kestrel drones' },
        tactical:   { label: 'SWAT', commander: 'Gabriella Cruz' },
        armored:    { label: 'Armored Response', vehicle: 'the Bear' },
        narcotics:  { label: 'Narcotics Division' },
        plainclothes: { label: 'Special Investigations Section' },
        csu:        { label: 'Crime Scene Unit' },
        rtcc:       { label: 'Real-Time Crime Center', liaison: 'Detective Lena Varga' },
        detective:  { label: 'Detective Bureau', commander: 'Gabriella Cruz' },
        standards:  { label: 'Professional Standards Bureau' }
    }
};
```

### 1B. Department canon

This is the lore every new scene draws from. It should surface in prose gradually, through what officers do and how the city talks about them, and in a condensed "Trigrave PD" entry in the Tips & Guide (Step 16).

**Chief Hugo Lars.** Late fifties. Former army colonel who commanded a combined-arms brigade before he retired. Twelve years ago, Maxine Renalds won her first mayoral race on a public-safety platform and recruited him personally. The two of them rebuilt TPD from the budget line up. He's tall, gray at the temples, and moves like the joints hurt and he's decided that's none of anybody's business. He talks in short, complete sentences and never raises his voice. Where Cruz is amused, Lars is patient. He's the reason Cruz is in Trigrave: he went looking for the best investigator alive, found her in federal service, and made her an offer nobody else in the country could match.

**The Academy.** Eighteen months, the longest police academy in the country. Criminal law, forensic science, crisis negotiation, tactical medicine, combat, and a physical program built by Lars's old instructors. Psychological evaluations at entry, at graduation, and every six months for the rest of an officer's career. The washout rate is around 80%. What's left is disciplined past the point most soldiers ever reach, and the whole thing's designed to leave an officer with one thought: protect and serve, as efficiently as a human being can.

**Professional Standards.** Integrity testing is constant and unannounced. Professional Standards runs sting offers (cash, favors, and seduction) against officers at random. Anyone who bites is fired and prosecuted, publicly. Nobody bites anymore. Every officer knows the woman flirting with them at a traffic stop might be Professional Standards, and every officer's trained to document the attempt in the report. That's why bribery and seduction don't work in Trigrave: it's a habit drilled in until it's reflex.

**Accountability.** Bodycams run for the full shift. An officer *can* switch one off (Reyes does, under compulsion, in the hospital blood theft scene), but the switch-off logs itself and pings a supervisor. Officers report every contact. That paperwork is what lets Step 9's "compulsion echo" exist.

**Why the underworld survives.** TPD arrests Vajaros corner dealers, Boruski runners, and freelance crews every night. It's never touched the people at the top. The Vajaros are compartmentalized, rich, and lawyered to the teeth. The Boruskis are old-world and military-grade. The Mothers are psychic, and after what happened to Jésus's army, nobody at TPD is eager to find out what an armed raid on them looks like. Everyone below that tier gets caught.

**TPD is the military.** Trigrave has no standing force of its own. TPD's Armored Response, Air Support, and SWAT exist so it doesn't need one. Raptor One flies armed. The Bear is a tracked armored vehicle that can take a rifle round and keep rolling.

### 1C. Named NPC roster

Existing characters keep their established voice and roles. New ones are recurring faces, so the department feels staffed by people and not by "an officer."

| Name | Role | Status | Voice notes |
|---|---|---|---|
| Hugo Lars | Chief of Police | **New** | Clipped, patient, never raises his voice. Speaks in finished sentences. |
| Gabriella Cruz | Detective Bureau + SWAT | Existing | Calm, dry, faintly amused. Never hurries. |
| Alan Driscoll | Captain, public information officer | Existing (hero path) | Press-conference cadence. Careful. |
| Officer Reyes | Patrol, Trigrave General substation | Existing (blood theft) | Steady hands, radio discipline. |
| Officer Pruitt | Property desk, Central Booking | Existing (exhibition arrests) | Remembers everyone. Dry. |
| Det. Sgt. Nadia Osei | Homicide lead under Cruz | **New** | Methodical, warm with families, cold with suspects. |
| Det. Lena Varga | Detective Bureau liaison to the RTCC | **New** | Fast talker, lives in the camera web. |
| Det. Andre Tolliver | Property & Robbery | **New** | Burglary and mugging cases. Patient. Owns a lot of cardigans. |
| Officer Dale Brenner + Juno | K9 Unit | **New** | Brenner talks to Juno more than to people. Juno's a Belgian Malinois. |
| Sgt. Ruth Calloway | Patrol sergeant, field interviews | **New** | Twenty-two years on the street. Polite, immovable. |

These new names should appear sparingly: Osei and Varga mostly through Cruz's dialogue and news items, Brenner and Juno in K9 scenes, Calloway as the recurring face of Step 9's field interviews.

### 1D. Prose register for TPD

Every officer in every new scene should read the same way: calm, procedural, courteous, and completely unmovable. They say "ma'am." They narrate what they're doing ("I'm going to ask you to keep your hands where I can see them"). They don't get flustered by exposure, flirting, cash, or threats. Their only visible reaction to something supernatural is a pause, a radio call, and a note in the report.

Sample (field interview opener):

> A motor officer rolls to the curb ahead of you and kills the engine. She pulls off one glove, then the other, and sets them on the tank.
>
> "Afternoon, ma'am. Sergeant Calloway, TPD." She doesn't step off the bike yet. "You mind if I ask you a couple of questions?"
>
> Her voice is friendly. Her right hand's resting on her thigh, a few inches from her holster, and it stays there.

---

## Step 2: Presence & Response Engine

TPD should always be on the street. This step gives every district a baseline police presence that exists whether Kelsie's wanted or not, then layers suspicion, heat, and active investigations on top.

### 2A. `getTPDPresence(district)`

Returns a 0-100 presence score for a district right now. Every police-chance calculation in the game routes through this.

```javascript
// Baseline patrol density per district. TPD staffs every shift evenly:
// the night shift is as heavy as the day shift, which is exactly why
// officers stand out more at 3 AM.
var TPD_BASE_PRESENCE = {
    downtown: 70, medical: 55, commercial: 50, fitness: 40,
    residential: 35, wendale: 35, park: 30, dockyard: 22, slums: 15
};

function getTPDPresence(district) {
    var p = TPD_BASE_PRESENCE[district];
    if (p == null) p = 35;
    var sd = gameState.suspicionData || {};
    var tier = sd.wantedTier || 0;

    // The city responds to its own heat
    p += [0, 5, 10, 20, 35][Math.min(tier, 4)];

    // District heat pulls extra patrols into the area
    var heat = getDistrictHeat(district);
    if (heat >= 80) p += 20;
    else if (heat >= 50) p += 10;

    // An open serious case in this district (Step 3) means more cars
    if (typeof countOpenCasesInDistrict === 'function') {
        p += Math.min(15, countOpenCasesInDistrict(district, 'serious') * 5);
    }

    // Cruz's forecast (Step 12): she staffed the district she expects you in
    if (gameState.flags.cruzForecastDistrict === district &&
        gameState.flags.cruzForecastDay === gameState.time.day) p += 15;

    // A warrant puts her face in every briefing
    if (gameState.flags.warrantIssued) p += 10;

    return Math.max(5, Math.min(100, p));
}
```

The Slums sit low because they're Vajaros turf and TPD patrols them in pairs, less often. Low presence in the Slums doesn't mean low *investigation*: a body in the Slums still gets Homicide (Step 3). The Slums are safer to act in and exactly as dangerous to be careless in.

### 2B. Fill the missing districts

Add `dockyard: 0.25` and `wendale: 0.30` to `DISTRICT_CAMERA_DENSITY` (the Dockyard has port-authority cameras on the gates and almost nothing on the water; Wendale is a mid-density residential zone). Every function that reads density currently falls back to defaults for these two.

### 2C. `getTPDResponseChance(district, crimeType)`

The chance TPD reaches a crime *while it's in progress*. Replaces the flat suspicion formulas.

```javascript
// How loud each crime is to the city: alarms, screams, 911 calls.
var TPD_CRIME_NOISE = {
    pickpocketing: 0.10, blood_theft: 0.30, burglary: 0.35,
    mugging: 0.55, hunt_partial: 0.25, hunt_full: 0.60,
    exposure: 0.40, drug_deal: 0.20, assault: 0.60
};

function getTPDResponseChance(district, crimeType) {
    var presence = getTPDPresence(district) / 100;
    var noise = TPD_CRIME_NOISE[crimeType] || 0.4;
    var chance = presence * noise;
    // A professional picks her moment. Stealth cuts the odds TPD is close enough to matter.
    chance *= (1 - getStealthBonus());
    return Math.max(0.02, Math.min(0.95, chance));
}
```

Reference values at zero suspicion, no heat:

| District | Mugging, stealth 1 | Mugging, stealth 50 | Hunt (full detection), stealth 1 | Hunt (full), stealth 50 |
|---|---|---|---|---|
| Downtown | n/a (no mugging) | n/a | 42% | 30% |
| Commercial | 27% | 20% | 30% | 21% |
| Residential | n/a | n/a | 21% | 15% |
| Slums | 8% | 6% | 9% | 6% |

At Tier 3 with district heat 80+, Commercial mugging at stealth 1 climbs to 49%. That's the intended feel: a clumsy mugger in a policed district gets interrupted about a quarter of the time with no heat at all, and half the time once the city's looking.

### 2D. Wire it into existing call sites

- **`getMuggingPoliceChance(location)`**: body becomes `return getTPDResponseChance(location, 'mugging');`. Keep the function name so every caller still works.
- **`ambush_strike` onEnter police interrupt**: replace `(citySuspicion - 20) / 120` with: clean detection = 0; partial = `getTPDResponseChance(district, 'hunt_partial')`; full = `getTPDResponseChance(district, 'hunt_full')`. The interrupt now depends on whether anyone saw the hunt, which is what the police can actually respond to. Clean hunts are invisible to TPD at any suspicion level.
- **Exhibition police clocks** (`tickIndecencyPolice`, `tickSheerLingeriePolice`, `checkNakedPolice`): scale the per-tick arrival chance by `getTPDPresence(district) / 50` so Downtown's clock runs about 40% faster than the Commercial District's and the Slums' runs at a third of it.
- **Drug patrol encounter**: multiply the drug-heat patrol percentage by `getTPDPresence(district) / 20` (Slums baseline 15 keeps it close to current values; heat and tier push it up).

### 2E. Presence in the prose

District street scenes get one ambient line about TPD, chosen by presence band. The line's always there, so the player learns to read the city's police weather at a glance. Sample pool (write 3-4 per band per district, time-of-day aware):

- **Low (under 25), Slums, night:** "A cruiser crawls the far end of the block with two officers in it. It doesn't stop. It never stops here, and it never skips a night either."
- **Moderate (25-49), Residential, afternoon:** "A patrol car idles outside the elementary school, windows down, the officer inside eating a sandwich with his eyes on the crosswalk."
- **High (50-74), Commercial, evening:** "Two officers on foot at the corner of Ninth, walking the block the way they walk it every hour. One of them nods at a shop owner locking up. He nods back like it's a ritual."
- **Heavy (75+), Downtown, night:** "There's a Kestrel drone parked in the air above the intersection, red light blinking, and a second one working the next avenue over. A motor unit's parked at the curb. Its rider's standing beside it, reading every face that passes."

The heavy band should feel oppressive on purpose. That's what Tier 3 and 4 look like from the sidewalk.

---

## Step 3: The Investigation Engine

The mechanical spine. Every ledger entry gets a case file, and TPD works that case every day until it's solved or goes cold. Identification no longer waits for a booking.

### 3A. The case file

Attach an `investigation` object to every ledger entry at creation (inside `createLedgerEntry`, after the entry's built):

```javascript
entry.investigation = {
    unit: assignInvestigatingUnit(entry),   // 'patrol' | 'property' | 'detective' | 'homicide' | 'cruz'
    progress: 0,                             // 0-100. 100 = identified.
    leads: [],                               // { type, strength, developDay, developed, pointsAtKelsie }
    status: 'open',                          // 'open' | 'cold' | 'identified'
    lastLeadDay: entry.day,
    poiRaised: false
};
seedCaseLeads(entry, params);                // Step 3C
```

The case always identifies Kelsie when it hits 100, because Kelsie's the one who did it. The engine models how fast TPD closes the gap, and what she can do to keep it from closing.

### 3B. Unit assignment (triage)

TPD triages. Petty crime gets a report; murder gets everything.

| Crime type | Unit | Multiplier | Goes cold after |
|---|---|---|---|
| `pickpocketing`, `indecent_exposure` | Patrol (report only) | ×0.5 | 5 days without a new lead |
| `burglary`, `blood_theft`, `fleeing` | Property & Robbery (Tolliver) | ×0.8 | 10 days |
| `mugging`, `assault` | Detective Bureau | ×1.0 | 14 days |
| `murder` | Homicide (Osei) | ×1.4 | 30 days |
| `murder` or `assault` with bite evidence, any `police_assault`, `police_killing`, `escape` | Cruz, personally | ×1.8 | Never (see 3F) |

`assignInvestigatingUnit(entry)` reads `entry.type` and `entry.forensic.biteMarks`. Cruz takes anything anomalous because she does her own forensics, and two unexplained punctures in a throat are exactly the kind of anomaly she collects.

### 3C. The lead catalog

Leads are seeded at crime time and **develop** on a schedule. A lead adds its strength (times the unit multiplier, times the cover-up modifier where noted) to `progress` on the day it develops. Develop days count from the crime, or from body discovery for murders.

| Lead | Strength | Develops | Seeded when | Cover-up applies? | Points at Kelsie? |
|---|---|---|---|---|---|
| `witness_face` | 25 | Day 0 | Civilian witness saw an unmasked face | No | Yes |
| `witness_face_cop` | 45 | Day 0 | Officer saw an unmasked face | No | Yes |
| `witness_desc` | 8 | Day 0 | Someone saw something (no face, or masked) | Yes | No |
| `victim_statement` | 20 | Day 1 | Surviving victim who wasn't compelled | No | Yes |
| `canvass` | 10 | Day 1 | Rolled: `footTraffic / 100`, times the cover-up modifier | Yes | No |
| `camera_clear` | 30 | Day 1 | Clear footage, unmasked | No | Yes |
| `camera_partial` | 15 | Day 1 | Partial footage, or clear but masked | No | No |
| `camera_blur` | 4 | Day 1 | Vampire-speed blur (also +supernatural evidence) | No | No |
| `camera_trace` | 25 | Day 2 | RTCC reconstructs the exit route (Step 6) | Yes | No |
| `trace_residence` | +25 | Day 2 | The camera trace ends where she's staying (Step 6E; +10 and no name at the Lurasi) | Yes | Yes (No at the Lurasi) |
| `k9_track` | 15 | Day 0 | Fresh scene (Step 10D) | Yes | No |
| `k9_residence` | +20 | Day 0 | The track ends where she's staying (Step 6E; +8 and no name at the Lurasi) | Yes | Yes (No at the Lurasi) |
| `prints_unmatched` | 8 | Day 2 | Ungloved crime, prints not on file | No | Converts (Step 8) |
| `profile_zero` | 8 | Day 3 | Unsealed bite, Kelsie's saliva recovered (Step 5) | No | Converts (Step 8) |
| `trace_evidence` | 6 | Day 3 | Hair, fiber, footwear impression | Yes | No |
| `outfit_match` | 12 | On trigger | Field interview matches a crime description (Step 9) | No | Yes |
| `tip_line` | 20 | Day 3-7 | Stills released; rolled (3D) | No | Yes |
| `series_bonus` | +6 per linked case, cap +30 | On link | Case joins a series (3G) | No | No |

"Points at Kelsie" leads are the ones that name a specific person. They're what Step 8 uses to decide when Kelsie becomes a Person of Interest.

### 3D. The daily tick: `advanceInvestigations()`

Runs in `onNewDay()` right after `processPendingForensics()` and before `checkWarrantConditions()`.

```javascript
function advanceInvestigations() {
    var ledger = gameState.evidenceLedger || [];
    var today = gameState.time.day || 0;
    for (var i = 0; i < ledger.length; i++) {
        var e = ledger[i];
        var inv = e.investigation;
        if (!inv || inv.status === 'identified' || e.linkedToKelsie) continue;
        // Murders don't get worked until somebody finds the body
        if (e.type === 'murder' && !e.bodyDiscovered) continue;

        var anchor = (e.type === 'murder') ? (e.bodyDiscoveryDay || e.day) : e.day;
        var mult = TPD_UNIT_MULT[inv.unit] || 1.0;
        var developedToday = false;

        for (var j = 0; j < inv.leads.length; j++) {
            var L = inv.leads[j];
            if (L.developed || today < anchor + L.developDay) continue;
            L.developed = true;
            developedToday = true;
            inv.progress += L.strength * mult * (L.coverUp != null ? L.coverUp : 1);
        }

        // Tip line: released stills bring calls from people who know her face
        if (!inv.tipRolled && today >= anchor + 3 && caseHasReleasableStill(e)) {
            inv.tipRolled = true;
            var tipChance = 0.25 + ((gameState.socialMediaHeat || 0) / 200) +
                            (((gameState.suspicionData || {}).facialRecognition || 0) / 200);
            if (Math.random() < tipChance) {
                inv.leads.push({ type: 'tip_line', strength: 20, developDay: 0, developed: true, pointsAtKelsie: true });
                inv.progress += 20 * mult;
                developedToday = true;
            }
        }

        if (developedToday) inv.lastLeadDay = today;
        inv.progress = Math.min(100, inv.progress);

        if (inv.progress >= 100) {
            inv.status = 'identified';
            linkCrimeToSuspect(e.id, 'investigation');
            fireIdentificationBeat(e);                 // 3E
        } else if (inv.unit !== 'cruz' && today - inv.lastLeadDay > TPD_COLD_DAYS[inv.unit]) {
            inv.status = 'cold';
        }
        checkPersonOfInterest(e);                       // Step 8
    }
}
```

Add `'investigation'` and `'dna'` (Step 8) to the `linkCrimeToSuspect` method list and to any UI that labels link methods ("Identified by investigation").

### 3E. Identification beats

When a case closes on Kelsie, the method that tipped it decides the flavor. `fireIdentificationBeat(entry)` picks the strongest developed lead with `pointsAtKelsie` and queues a notification plus, for serious cases, a one-time street or home beat:

- **`trace_residence` / `k9_residence`:** a line keyed to where she's staying. Varhall: "A cruiser's parked across from Varhall when you come home. It's still there an hour later." Timeless Hotel: "There's a man in the lobby who isn't reading his newspaper." Oracle Inn: "An unmarked sedan's backed into the space facing your window." Never at the Lurasi (6E).
- **`tip_line`:** a news item: "Police credit a tip line call for a break in the [district] [crime] investigation."
- **`witness_face`:** Tolliver or Osei (by case unit) mentions it in a later Cruz scene: "Your neighbor across the courtyard picked you out of a photo array."
- **`outfit_match`:** "The jacket. That's what did it."

These should be short. The real consequence is the warrant pipeline, which already exists.

### 3F. Cold cases, and Cruz's cases

A cold case stops progressing. It reopens (status back to `open`, `lastLeadDay` reset) when:
- Kelsie's prints or saliva go on file (booking or Step 8's elimination sample): unmatched prints and Profile Zero convert to instant identification.
- The case joins a series (3G).
- Kelsie's field-interviewed in a matching outfit (Step 9).

Cruz's cases never go cold. They stall. She keeps them open on her desk with the same patience the whole department's known for, and they wake up the moment anything new touches them.

### 3G. Signature series (cross-district)

The existing series system links 3+ crimes of the same type in the same district. Keep it, and add **signature series** that ignore district:

- **Bite series ("Profile Zero"):** every case with a recovered bite profile links to every other one, anywhere in the city. Cruz owns it.
- **Mask series:** the existing mask-signature grouping becomes a real series with `series_bonus` progress.
- **MO series:** 3+ crimes of the same type within 10 days whose casing context matches (same time-of-day band and same approach method) link as one offender, regardless of district.

Each case in a series gets `series_bonus` (+6 per other linked case, capped at +30). A series never identifies Kelsie by itself, since the cap keeps pure-series progress under 100 at Cruz's multiplier (Step 7 shows the math). What a series does is feed Cruz's profile (Step 12), pull presence into the districts it touches, and put a single development away from closing every case in it at once.

---

## Step 4: The Stealth Spine

Stealth decides how cleanly Kelsie commits a crime *and* how well she covers it up afterward. Right now it only does the first half. This step makes it the dominant stat in every interaction with TPD.

### 4A. The cover-up modifier

```javascript
// How much of the soft evidence she leaves behind. Applies to every lead
// marked "cover-up applies" in Step 3C.
function getCoverUpMult() {
    var s = gameState.stealth || 1;
    var m;
    if (s >= 100) m = 0.25;
    else if (s >= 75) m = 0.35;
    else if (s >= 50) m = 0.50;
    else if (s >= 30) m = 0.70;
    else if (s >= 15) m = 0.85;
    else m = 1.0;
    // Crime-stealth clothing helps her disappear into the city afterward too
    var fx = getActiveClothingEffects();
    if (fx.crime_stealth > 0) m *= (1 - fx.crime_stealth * 0.5);
    return m;
}
```

A Clumsy Kelsie leaves every soft lead at full strength. A Ghost leaves half. A Phantom leaves a quarter. That's the single biggest lever in the whole overhaul.

### 4B. Where stealth touches TPD

Every stealth threshold below should appear in the Tips & Guide, and each one should be *felt* in the prose the first time it matters.

| Stealth | Committing the crime | Covering it up | Reading TPD |
|---|---|---|---|
| 1-9 | Base detection. | Full-strength soft leads. Heads straight home by default. | Sees officers only when they're in front of her. |
| 10+ | Existing camera/lighting reads. | **Sweep the scene** (+3 min): trace evidence ×0.5. | Casing shows TPD presence band for the district. |
| 15+ | Careful mugging, lure. | Cover-up modifier 0.85. | Spots marked cars before they spot her (field interview chance −25%). |
| 20+ | Pickpocket awareness, distraction lifts. | Knows to change her look: outfit-match window drops from 7 days to 3. | **Spots plainclothes**: undercover targets get a subtle tell in casing. |
| 25+ | Kill-site cleanup (existing). | Cleanup also zeroes `trace_evidence`. | Reads patrol rhythm: casing tells her when the next pass is due. |
| 30+ | Escape routes in casing. | Cover-up modifier 0.70. **Break the trail** exit option (Step 6C). | Notices drone lanes: casing flags Kestrel coverage. |
| 35+ | Rooftop drop. | **Rooftop exit**: no camera trace, no K9 track. | |
| 40+ | Exact detection percentages. | Camera trace chance ×0.5 on any exit. | **Reads decoys**: decoy targets get an explicit tell. |
| 50+ | | Cover-up modifier 0.50. **Wash the wound** on kills (Step 5C). | Sees Cruz's forecast: casing mentions the extra staffing. |
| 60+ | | Camera trace ×0.25. Elimination sample chance halved (Step 8). | Knows the RTCC's blind spots city-wide. |
| 75+ | | Cover-up modifier 0.35. | Pursuit escape bonus doubles (Step 10). |
| 100 | | Cover-up modifier 0.25. | Field interviews almost never fire (chance ×0.2). |

### 4C. Stealth XP from getting away with it

Crimes already grant stealth XP at the moment of the crime. Add XP for the cover-up, paid when a case goes cold:

| Case outcome | Stealth XP |
|---|---|
| Petty case goes cold | +2 |
| Property/detective case goes cold | +5 |
| Homicide case goes cold | +10 |
| A Cruz case stalls 30 days without a new lead | +15 (one-time per case) |

Show it as a quiet notification: "The [district] case went cold. Nobody's knocking." This closes the loop: getting away with a crime counts as practice too.

### 4D. The tells in casing

Every existing casing function (`buildCasingText`, the mugging, burglary, and pickpocket casing scenes) gets an optional TPD block. Samples:

- **Stealth 10+, presence band:** "Two cruisers have rolled through since you got here. This block gets watched."
- **Stealth 25+, rhythm:** "The patrol loops back every eleven minutes. The last one passed four minutes ago."
- **Stealth 30+, drones:** "There's a Kestrel lane along the avenue. The drones fly it on a schedule, and the alley's under the edge of it."
- **Stealth 50+, Cruz's forecast:** "More uniforms than this district usually gets. Somebody expected you here tonight."

---

## Step 5: Forensics: the Crime Scene Unit and Profile Zero

TPD's lab should be the best in the country, and Cruz should be the best person in it.

### 5A. The Crime Scene Unit processes everything above petty

Every `burglary`, `mugging`, `assault`, `murder`, `police_assault`, and `blood_theft` ledger entry gets CSU processing. That means:
- **Prints are always lifted** when Kelsie wasn't wearing gloves. Several current call sites (hunt assaults, police fights with no `fingerprintsLeft` param) should pass `fingerprintsLeft: !hasLeatherGloves()` so the lead gets seeded.
- **Trace evidence** is seeded on every processed scene unless Kelsie swept it (stealth 10+) or cleaned it (stealth 25+).

### 5B. Profile Zero (the bite)

Vampire saliva seals punctures closed with no scar (established in the file: "Vampire saliva. It seals the punctures."). That gives a clean forensic rule:

- **A completed feed on a living victim** ends with the wound sealed. Nothing to swab. Clean non-lethal feeds leave no bite evidence at all.
- **A feed that's interrupted** (tension event abort, police interrupt, victim wrenches away) leaves an open wound. The victim goes to the hospital, the ER swabs it, and CSU recovers Kelsie's saliva.
- **A kill** leaves open punctures, because sealing needs living tissue to heal. Every body carries Kelsie's saliva.

The lab can't classify the profile. The markers don't match any human reference. Cruz calls it **Profile Zero**, and she links every recovered sample into one signature series (Step 3G). Each recovered sample adds +5 supernatural evidence the first time it's processed (the lab's "unclassifiable" finding), and +10 to Cruz's profile (Step 12).

Wire it: in `processBodyDisposal()`, seed `profile_zero` with recovery chance from 5C. In the hunt assault ledger block inside `ambush_strike`, set `biteMarks` only when the feed was interrupted (the current code sets it on every partial/full detection).

### 5C. Recovery chance and the cover-up

| Situation | Saliva recovered |
|---|---|
| Body left or poorly hidden | 100% |
| Body well hidden (found 3-5 days later) | 90% |
| Body in the harbor (if it surfaces) | 30% |
| Vampire-speed disposal (if traced) | 100% |
| Stealth 50+ **Wash the wound** (+5 min, kill scenes only) | ×0.6 |
| Interrupted feed, victim reaches a hospital | 80% |

**Wash the wound** is a new kill-scene option at stealth 50+. Prose should be clinical and short; it's the moment a demonic Kelsie proves she's a professional. Sample:

> You tip the last of his water bottle over the punctures and work it in with your thumb, the way you'd scrub a stain out of a shirt. Then again with the hem of his own jacket. A good lab'll still find something. They'll have to work for it.

### 5D. Fingerprints on file

Prints on file happen three ways: booking (existing), an exhibition booking (Step 15 fixes this), or Step 8's elimination sample. Once they're on file, every `prints_unmatched` lead in the ledger converts instantly to identification, open or cold. `applyFingerprintEvidence()` keeps its instant-warrant behavior for bodies found after that point.

---

## Step 6: The Real-Time Crime Center

Downtown's 90% camera density means something only if somebody's watching. The RTCC is TPD's camera command center on the top floor of headquarters, and it can rebuild a person's route across the city frame by frame.

### 6A. The camera trace

When a case has any camera lead, or the crime happened in a district with density 0.40+, the RTCC tries to reconstruct Kelsie's exit. `rollCameraTrace(entry, exit)`:

```javascript
function rollCameraTrace(entry, exit) {
    // exit: 'walk_home' | 'go_to_ground' | 'break_trail' | 'rooftops' | 'vampspeed'
    if (exit === 'rooftops') return null;                 // stealth 35+: cameras don't point up
    var density = DISTRICT_CAMERA_DENSITY[entry.district] || 0.2;
    var chance = density;
    if (exit === 'break_trail') chance *= 0.4;            // stealth 30+: doubled back, changed streets
    if (exit === 'go_to_ground') chance *= 0.6;           // the trail ends in a dead zone
    if (exit === 'vampspeed') chance *= 0.3;              // too fast to follow...
    var s = gameState.stealth || 1;
    if (s >= 60) chance *= 0.25; else if (s >= 40) chance *= 0.5;
    if (isDark()) chance *= 0.85;
    if (Math.random() >= chance) return null;
    // Only a straight walk back leads the cameras to where she sleeps (6E).
    // Homeless Kelsie has nowhere to lead them.
    var res = getKelsieResidence();
    var reachesHome = (exit === 'walk_home') && !res.homeless;
    return { trace: true, residence: reachesHome ? res.id : null };
}
```

Vampire speed is the one exit that trades one problem for another: the trace almost always fails, and the blur adds `camera_blur` plus supernatural evidence through `applyVampireSpeedCrimeCosts()`.

### 6B. Exit choices

Every crime that currently ends with a flat "you leave" gets an exit choice, filtered by stealth. This is where the cover-up becomes a decision:

- **"Head back to [residence]."** Always available, labeled with wherever she's staying ("Head back to Varhall," "Head back to the Timeless," "Head back to the Lurasi"). Fastest in real terms, and the one that ends a trace at her door. Homeless Kelsie gets **"Walk off into the city"** instead: same speed, no door at the end of it, and a successful trace only adds +10 heat to the district she drifts into.
- **"Go to ground first."** Always available. +30-60 minutes, +3 thirst. Kelsie waits somewhere dark before moving on. Trace ×0.6, and a trace that succeeds ends at the hiding spot.
- **"Break the trail."** Stealth 30+. +15 minutes. Doubles back, cuts through a building, changes streets under the camera gaps she read in casing. Trace ×0.4.
- **"Take the rooftops."** Stealth 35+, not in the Park. +10 minutes. No trace, no K9 track.
- **"Vampire speed."** Always available. Existing costs. Trace ×0.3, but supernatural evidence.

Store the chosen exit on the ledger entry (`entry.exit`) so Step 10's K9 rules can read it.

### 6C. The surveillance hub, reworked

The existing "Infiltrate the Surveillance Hub" choice lets Kelsie overwrite 20-30 camera footage. The RTCC keeps a mirrored backup at headquarters, so tampering with the Traffic Management Center now:
- Still reduces `cameraFootage` by the existing amount (the public-facing feeds and the city's traffic archive).
- Removes `camera_trace` leads that haven't developed yet on cases from the last 48 hours (the trace hasn't been built, and the raw feed's gone).
- Leaves developed camera leads alone (the RTCC already pulled them).
- Creates a `tampering` note that raises Cruz's profile by +5 each time. She notices when the city's cameras keep losing the same hours.

### 6D. Sample prose (identification by trace)

> The news runs it at eleven, between the weather and a story about a water main.
>
> Grainy stills, stitched together. A small figure leaving an alley in the Commercial District. The same figure at a crosswalk on Ninth. At a bus shelter. At the corner of your street.
>
> The last frame's the lobby door of Varhall Apartments.
>
> The anchor says police are "following up on a promising lead." You turn the TV off and sit in the dark and listen to your own pulse, which is quieter than it ought to be.

Key the last frame to her residence: "the lobby door of Varhall Apartments," "the revolving door of the Timeless Hotel," "the side entrance of Oracle Inn." At the Lurasi the stills end at the motel's lot, and the anchor says the suspect "entered a Slums address police declined to name."

### 6E. Where the trail ends: Kelsie's residence

Kelsie's a free-roam character. She can hold her own unit at Varhall, share Jaewon's, keep a room at a hotel, or have nothing. Every trace, K9 track, pursuit, and warrant service reads one function:

```javascript
// Where Kelsie's sleeping right now: wherever she last rested, if she still
// holds it, otherwise the strongest claim she holds, otherwise the street.
function getKelsieResidence() {
    var F = gameState.flags;
    var R = {
        varhall:  { id: 'varhall',  label: 'Varhall Apartments', holds: !!(F.hasOwnApartment || F.stayingWithJaewon), withJaewon: !!F.stayingWithJaewon },
        timeless: { id: 'timeless', label: 'the Timeless Hotel', holds: !!F.hasHotelRoom,      scene: 'luxury_hotel_lobby' },
        oracle:   { id: 'oracle',   label: 'Oracle Inn',         holds: !!F.hasParkSideRoom,   scene: 'parkside_inn_entrance' },
        lurasi:   { id: 'lurasi',   label: 'the Lurasi Motel',   holds: !!F.hasCheapMotelRoom, scene: 'cheap_motel_entrance', sanctuary: true }
    };
    var last = F.lastRestLocation;   // new: set by every sleep/rest handler at these locations
    if (last && R[last] && R[last].holds) return R[last];
    var order = ['varhall', 'timeless', 'oracle', 'lurasi'];
    for (var i = 0; i < order.length; i++) if (R[order[i]].holds) return R[order[i]];
    return { id: 'street', label: 'the street', homeless: true };
}
```

Set `gameState.flags.lastRestLocation` in every sleep and rest handler at Varhall (both arrangements), the Timeless Hotel, Oracle Inn, and the Lurasi Motel. Clear it when she loses the place (lease terminated, room checked out, eviction).

**How each residence plays against TPD:**

| Residence | Trace / K9 lead | Points at Kelsie | How TPD learns the address | Warrant service |
|---|---|---|---|---|
| Varhall, own unit | +25 / +20 | Yes | Trace or K9 ends there; her lease is in her name, so a warrant finds it the day it issues | Yes (10E) |
| Varhall, with Jaewon | +25 / +20 | Yes | Trace or K9 ends there; Cruz's Jaewon interview (12C) | Yes, and Jaewon's there for it |
| Timeless Hotel | +25 / +20 | Yes (the desk pulls the key-card log) | Trace or K9; the front desk recognizes her from the bulletin (40% per day she holds the room under a warrant) | Yes. Hotel security hands SWAT a key card |
| Oracle Inn | +25 / +20 | Yes (guest registry) | Trace or K9; the desk recognizes her (30% per day under a warrant) | Yes |
| Lurasi Motel | +10 / +8 | No | Never confirmed. TPD won't go in to ask | **Never** |
| Homeless | None | n/a | There's no address to learn | None. Street takedown instead (6F) |

**Known address.** New flag `gameState.flags.tpdKnownResidence` holds the residence id TPD currently believes is hers. It's set by a developed `trace_residence` or `k9_residence` lead, by a hotel desk recognition, by the warrant itself for her own Varhall lease, by the Jaewon interview, and at booking (the key card or keys in her personal effects). Warrant service (10E) fires only when a warrant's active and `tpdKnownResidence` matches where she's actually staying. When she moves, the known address goes stale until the next trace, track, or desk call finds her again. That's the fugitive loop: every new room buys her time, and every careless walk home spends it.

The existing `hotelsBanned` flag already stops new check-ins under a warrant while letting her keep a room she already holds. The desk-recognition roll is what makes keeping that room a gamble. `rollHotelDeskRecognition()` runs in `onNewDay()` after `enforceSuspicionCap()`: with a warrant active and a room held at the Timeless (40%) or Oracle Inn (30%), a hit sets `tpdKnownResidence` to that hotel. Stealth 50+ halves it (she uses the side entrance and keeps her face down), and the Tier 4 multiplier from 13D stacks on top.

**The Lurasi.** TPD stays clear of Rufus and his motel. That's established, and this overhaul keeps it absolute: no warrant service, no raid, no SWAT, no plainclothes in the lot, and trace or K9 leads that end there never name her. The sanctuary covers the motel and nothing else. The moment she steps out into the Slums, every normal warrant encounter applies (at Slums presence, which keeps them rare). Ambient line for a Lurasi resident under a warrant: "A cruiser idles at the far corner of the block, as close to the Lurasi as TPD ever gets. It's been there since you checked in."

### 6F. Homeless: the street takedown

With no door to knock on, TPD uses the thing Trigrave has everywhere: cameras. While a warrant's active and `getKelsieResidence().homeless` is true, `checkStreetTakedown(district)` runs on district travel, after `checkCruzEncounters()` and before field interviews:

```javascript
function checkStreetTakedown(district) {
    var F = gameState.flags;
    if (!F.warrantIssued || F.currentlyInCustody || F.policeEncounterToday) return false;
    if (!getKelsieResidence().homeless) return false;
    var density = DISTRICT_CAMERA_DENSITY[district] || 0.2;
    // The RTCC runs her face against every live feed; units get vectored in.
    var chance = density * 0.6 + getTPDPresence(district) / 200;
    chance *= (1 - getStealthBonus());
    var sd = gameState.suspicionData || {};
    if ((sd.facialRecognition || 0) < 50) chance *= 0.6;   // no mugshot-grade face yet
    if (Math.random() >= Math.min(0.80, chance)) return false;
    triggerRandomDistrictEvent('tpd_street_takedown', getDistrictReturnScene(district));
    return true;
}
```

With a warrant at Tier 2 (presence +20) and facial recognition 50+, Downtown hits the 80% cap at stealth 1 and runs about 70% at stealth 50. The Slums run about 20% and 14%. A homeless fugitive who stays in the camera-thin districts can last a while. One who wanders Downtown won't.

**`tpd_street_takedown`:** the RTCC gets a hit, and units converge before she's crossed the street. Motor units cut off the corners, two cruisers box the crosswalk, a Kestrel drone drops to rooftop height and hangs there. Choices mirror the arrest tree: surrender, flee (pursuit at `serious` severity; the drone's already on her, so air starts locked unless she's cold), fight, or compel the nearest officers (buys a gap at +20 thirst, compulsion echo). If Cruz has a tactical capture on record, 25% of takedowns are Cruz with the TAD instead (`cruz_tactical_encounter`).

Sample opener:

> The light at Fifth turns red and you stop with everybody else.
>
> A motor unit rolls up on your left and doesn't stop at the line. Another comes in from the right. Across the intersection, a cruiser noses into the crosswalk and parks there, blocking it.
>
> Something whirs overhead. You look up into the lens of a drone, twenty feet above you, holding perfectly still.
>
> "Kelsie Summers." The motor officer's already off his bike. "Hands where I can see them."

---

## Step 7: The Homicide Rule

The user-facing promise: getting away with murder should be exceptionally hard unless Kelsie has very high stealth and wears gloves. This step calibrates the numbers from Steps 3-6 so that promise holds, and documents the math for testing.

### 7A. Scenario table

Unit: Cruz (×1.8), because every kill leaves a bite. Cover-up modifier from Step 4A. Progress rounds down.

| # | Setup | Leads (after cover-up) | Progress | Outcome |
|---|---|---|---|---|
| 1 | Slums, stealth 5, no gloves, direct strike, partial detection, body left, walks back to Varhall or a hotel room, dry night | witness_desc 8, k9_track + k9_residence 35, prints 8, profile_zero 8, trace 6 = 65 | 117 | **Identified by day 3.** The dog walked her home. |
| 2 | Same as #1 with gloves | 57 | 102 | **Identified.** Gloves alone don't save a clumsy killer. |
| 3 | Same as #2, but raining | k9 gone: 22 | 39 | Stalls. Profile Zero on file. |
| 4 | Slums, stealth 30, gloves, follow and isolate, clean detection, body well hidden, cleanup | profile_zero 8 | 14 | Stalls. Series grows. |
| 5 | Commercial, stealth 30, gloves, direct, partial detection, walks home | witness_desc 5.6, camera_partial 15, camera_trace + residence 35, profile_zero 8, trace 4.2 = 67.8 | 122 | **Identified** when the trace rolls (about 60% at night at this stealth). Without the trace: 52, no identity lead, stalls. |
| 6 | Same as #5, rooftops home (stealth 35) | 32.8 | 59 | Stalls, no identity lead, no POI. |
| 7 | Downtown, stealth 60, gloves, follow, clean, vampire-speed disposal | Body found 30%: profile_zero 8 | 14 | Stalls, +supernatural evidence. |
| 8 | Commercial, stealth 45, **no gloves**, clean, hidden, cleanup, rooftops | prints 8, profile_zero 8 | 28 | Stalls. **Time bomb**: the day her prints go on file, this closes. |
| 9 | Any of #4/#6/#7 after five more kills | +30 series cap | 68 max | Never closes on series alone. Cruz's profile climbs, decoys start (Step 11). |

Rows 1, 2, and 5 assume she walks back to Varhall, the Timeless, or Oracle Inn. If she walks back to the Lurasi, the residence leads drop to +8/+10 with no name, and rows 1 and 2 stall at about 95 and 81 with no lead that names her, so no Person of Interest. That's as close as a case gets without closing: one field interview in the same outfit, or one print going on file, finishes it. If she's homeless, there's no residence lead at all, and the danger moves to the street takedown once a warrant exists.

The design reads cleanly off this table. Gloves protect against the time bomb. Stealth protects against everything that follows her home. Clean detection keeps faces out of it. Rain and patience cover a lot of mistakes. And no amount of care makes the series go away: it keeps Cruz interested, and her interest keeps making the city harder.

### 7B. Discovery timing matters now

`addPendingBodyDiscovery()` already delays bodies. Add: when a body's found within 6 hours of the kill (left in the open, or poorly hidden in a high-traffic district), K9 is deployed (Step 10D). Bodies found later have a cold scent. Hiding a body is now worth more than the suspicion reduction: it's what beats the dog.

### 7C. Suspicion stays the city's temperature

Leave `getBaseKillSuspicion()` and the pre-warrant ceiling alone. Suspicion measures how nervous the city is. The case file measures how close TPD is to Kelsie specifically. They're separate on purpose: a careful killer can keep suspicion low while Cruz's file gets thicker, and a careless one gets identified long before the city's in a panic. The moment a murder case identifies her, `checkWarrantConditions()` fires the instant warrant and `enforceSuspicionCap()` pours the held heat in, both already implemented.

---

## Step 8: Person of Interest & the Elimination Sample

The loophole in the current system: prints and faces only match after a booking. TPD shouldn't need Kelsie to walk into Central Booking. It needs a lawful way to get her prints and saliva on file, and Cruz is exactly the person to do it.

### 8A. Becoming a Person of Interest

`checkPersonOfInterest(entry)` runs after every case update. Kelsie becomes a POI when any open case reaches progress 50 **and** has at least one developed lead with `pointsAtKelsie`. Pure forensics (unmatched prints, Profile Zero) never make her a POI by themselves; they need a name to attach to.

On first POI:
- `gameState.flags.kelsiePOI = true`, `kelsiePOIDay = today`.
- `openCaseOnKelsie()` fires (Cruz assigned, intro pending). This moves Cruz's arrival earlier than the first formal link, which fits her: she shows up while the case is still being built.
- No warrant, no ceiling change. A POI can walk around freely. She's just being watched.

### 8B. The elimination sample

Once Kelsie's a POI, the Detective Bureau tries to get her prints and saliva lawfully. Anything she discards in public is fair game. Each day as a POI:

```javascript
function rollEliminationSample() {
    if (!gameState.flags.kelsiePOI || gameState.flags.eliminationSampleTaken) return;
    var chance = 0.15;
    if ((gameState.flags.cruzProfile || 0) >= 70) chance += 0.15;
    if (gameState.flags.workingAtDiner) chance += 0.05;      // the mug she drinks from on break
    if ((gameState.stealth || 1) >= 60) chance *= 0.5;         // she's learned to take her trash with her
    if (Math.random() < chance) acquireEliminationSample('discard');
}
```

Some Cruz scenes offer the sample directly. In `cruz_approach` and the new `cruz_coffee` beat (Step 12), Cruz hands Kelsie a coffee. Accepting it and drinking puts the sample on file that day. Refusing costs nothing but raises Cruz's profile +5 ("She didn't want the coffee. Noted.").

`acquireEliminationSample(source)` sets `fingerprintsInSystem = true` and `profileZeroMatched = true`, then runs the conversion: every `prints_unmatched` and `profile_zero` lead in the ledger converts to an instant identification (`linkCrimeToSuspect(id, 'fingerprint')` or `'dna'`). A careful player with one gloveless mistake six weeks ago finds out today.

### 8C. The match scene: `cruz_sample_match`

Fires the next time Kelsie enters a district after a sample converts at least one case. Cruz has the cup in an evidence bag.

> Cruz is sitting on the bench outside Manny's store when you come around the corner. She's got a coffee in one hand and a paper evidence bag in the other, folded over twice and sealed with red tape.
>
> She holds the bag up so you can see it. Inside, there's a paper cup with a lid. The lid's from the shop on Ninth. You drank out of it yesterday.
>
> "Thanks for the coffee," she says.
>
> She sets the bag on the bench beside her and pats it, once, the way you'd pat a dog that did a good job.
>
> "The lab called your spit 'unclassifiable.' I've been waiting eight months for somebody to give me a word like that." She sips her own coffee. "You should get a lawyer, Kelsie. A creative one."

Tone the line to the actual history: if the sample came from a discard, she holds up whatever it was (the diner mug, a water bottle from the park).

### 8D. Countermeasures

- **Gloves** keep prints off crime scenes, so there's nothing for the sample to match.
- **Stealth 60+** halves the daily discard chance.
- **Never becoming a POI** is the real answer, and it's what high stealth and clean exits buy.
- **The Mothers** can still clear crimes through Rufus (existing), and their erasure removes the sample from TPD's minds and systems too. Cruz keeps her own copy (Step 12E).

---

## Step 9: Street Encounters: Field Interviews & Professional Conduct

TPD stops people. Politely, lawfully, and constantly. This step adds the everyday police encounter: an officer who wants a word, for a reason.

### 9A. `checkFieldInterview(district)`

Runs on district travel, before `checkRandomDistrictEvent()`. Once per day at most, never while in custody, never on the same day as another police encounter.

```javascript
function checkFieldInterview(district) {
    var F = gameState.flags;
    if (F.currentlyInCustody || F.policeEncounterToday || F.fieldInterviewToday) return false;
    if (F.warrantIssued) return false;   // warrant encounters keep their own system
    var sd = gameState.suspicionData || {};
    var match = 0;
    if ((sd.facialRecognition || 0) >= 30) match += 0.15;
    if (sd.hasCompositeSketch) match += 0.25;
    if (F.kelsiePOI) match += 0.25;
    var outfitHit = findOutfitMatchCase(district);   // 9B
    if (outfitHit) match += 0.40;
    if (match <= 0) return false;
    var chance = (getTPDPresence(district) / 100) * match;
    var s = gameState.stealth || 1;
    if (s >= 100) chance *= 0.2; else if (s >= 15) chance *= 0.75;
    if (Math.random() >= chance) return false;
    F.fieldInterviewToday = true;
    F._fieldInterviewCase = outfitHit ? outfitHit.id : null;
    triggerRandomDistrictEvent('tpd_field_interview', getDistrictReturnScene(district));
    return true;
}
```

### 9B. Outfit matching

When a ledger entry is created with a witness or camera lead, snapshot the distinctive visible items Kelsie wore (outerwear, top/dress, headwear, footwear ids) onto `entry.outfit`. `findOutfitMatchCase(district)` returns an open case in the same district, within the last 7 days (3 at stealth 20+), where at least two of those items are equipped right now. The violent hunt aftermath's "Change clothes" choice, currently prose-only, clears the risk for that crime.

If the interview fires from an outfit match and the officer runs the description, the case gets an `outfit_match` lead (+12, points at Kelsie).

### 9C. The scene: `tpd_field_interview`

Sgt. Calloway on a motor, or a foot-patrol pair downtown. The officer states a reason ("You match the description of someone we're looking to talk to about an incident on Ninth"), asks where Kelsie's headed, and asks for ID.

Choices:

- **"Cooperate."** Composure check: `stealth + charisma/2 + 20` vs `40 + match × 60`. Success: she's released, the officer logs the contact. If the stop came from an outfit match, the lead still lands (the description matched; the officer wrote it down). Failure: "I'm going to ask you to come down to the station and give a statement. You're not under arrest." Kelsie can go (a voluntary interview scene with Osei or Tolliver, +10 progress on the matched case, +5 Cruz profile) or decline and walk (legal, logged, +5 progress).
- **"Ask if you're being detained."** Always available without a warrant. Calloway answers honestly ("No, ma'am. You're free to go") and lets her walk. The refusal goes in the report: +5 progress to any matched case. TPD respects the law. It also remembers.
- **"Compel her."** Illusion unlocked. +10 thirst. Always works on a regular officer. Creates a **compulsion echo** (9E).
- **"Run."** Starts a pursuit (Step 10). If she's a POI, creates a `fleeing` ledger entry.
- **"Offer her money."** Available when Kelsie has $20+. Always fails. See 9D.
- **"Get closer. Soften her up."** Available at corruption 4001+. Always fails. See 9D.

Sample (cooperate, success):

> "Where are you headed tonight?"
>
> "Home."
>
> Calloway looks at you for a while. Then at your hands, then your shoes. She writes something on the pad clipped to her handlebar.
>
> "Get home safe, ma'am."
>
> She means it. She also wrote down what you're wearing.

### 9D. Bribery and seduction always fail

Every TPD encounter that currently offers compel/flee/fight/surrender may gain a money or flirt option where it fits the scene, and **every one of them fails the same way**: calmly, on camera, into the report. Mechanically:

- New ledger type `bribery_attempt` (charge label "Attempted Bribery of an Officer"), linked to Kelsie on the spot if she's been identified in the stop (`caught_in_act`), unlinked otherwise.
- +5 suspicion (`witnessQuality: 'cop'`), +5 Cruz profile.
- Seduction attempts create no charge but the same report line and profile gain.

Samples:

> You fold two twenties between your fingers and hold them low, below where the bodycam sits.
>
> Calloway looks at the money. Then she looks at you, and her face stays exactly as friendly as it was.
>
> "Ma'am, I'm noting that you just offered me cash." She says it like she's reading back an order at a drive-through. "Put it away, please."
>
> She writes it down while you watch.

> You step in closer than you need to and let your voice go soft.
>
> Calloway takes one step back, which puts her exactly out of reach, and keys her shoulder mic. "Twenty-two, can I get a second unit to Ninth and Carver."
>
> Then she's polite again. "Let's keep a little space between us, ma'am."

Prose rule for these: the officer never gets flustered, never flirts back, never threatens. The failure should feel like walking into a wall that's been expecting you.

### 9E. The compulsion echo

Compulsion always works on a regular officer. That's canon, and it stays that way. What changes is that TPD's paperwork notices what compulsion leaves behind: a bodycam switched off for no reason, a radio gap, an officer who can't account for four minutes of a shift and is required to report it.

- Every compulsion of an on-duty officer (field interview, `arrest_compel`, `doExhibPoliceCompel`, blood theft at the hospital, burglary/hunt encounters with officers) increments `gameState.flags.compulsionEchoes` and records `{ day, district }`.
- Each echo adds +3 supernatural evidence.
- At 3+ echoes with Cruz assigned, fire the one-time `cruz_echo_pattern` beat and +15 Cruz profile. After that, each new echo adds +5 Cruz profile.

Sample (`cruz_echo_pattern`, Cruz on the street):

> "Three officers this month filed lapse reports." Cruz turns a page on her tablet without looking at it. "Good officers. Clean evals. Each one lost about four minutes, and each one lost them standing next to a small brunette."
>
> She looks up.
>
> "Whatever you're doing to my officers, it leaves a hole in their shift. I've started collecting the holes."

---

## Step 10: Pursuit: Motors, Air Support, and K9

Vampire speed wins any footrace in Trigrave. That's established and it stays. What TPD has is everything that doesn't need to win a footrace.

### 10A. Pursuit resolution

Every flee choice (`police_flee_vampire_speed`, `mugging_flee_cops`, `arrest_flee`, field interview "Run," decoy sting flight, the naked escape) routes through `resolvePursuit(context)` after the existing escape prose. The ground chase always breaks. Then:

```javascript
function resolvePursuit(ctx) {
    // ctx: { district, severity: 'minor'|'serious'|'violent', exit }
    var presence = getTPDPresence(ctx.district) / 100;
    var tier = (gameState.suspicionData || {}).wantedTier || 0;

    // Air: drones everywhere presence is 30+, Raptor One for serious crimes or Tier 2+
    var heli = (ctx.severity !== 'minor') || tier >= 2;
    var air = presence * (heli ? 0.9 : 0.5);

    // Thermal: a vampire runs as warm as her last meal
    var thirst = gameState.thirst || 0;
    var thermal = thirst <= 40 ? 1.0 : thirst <= 70 ? 0.6 : 0.25;
    air *= thermal;

    if (gameState.weather && gameState.weather.isRaining) air *= 0.5;   // drones ground in rain
    air *= (1 - getStealthBonus() * ((gameState.stealth || 1) >= 75 ? 2 : 1));
    if (ctx.exit === 'rooftops') air *= 1.2;          // exposed to the sky
    if (ctx.exit === 'go_to_ground') air *= 0.5;      // under concrete
    if (ctx.exit === 'break_trail') air *= 0.7;

    if (Math.random() >= air) return 'lost';
    if (ctx.exit === 'walk_home') {
        var res = getKelsieResidence();
        if (res.homeless) return 'tracked_district';   // no door at the end of it
        if (res.sanctuary) return 'tracked_lurasi';    // they watch her go in, and stop
        return 'tracked_home';
    }
    // Tracked to where she stopped. K9 decides the rest.
    return rollK9Corner(ctx) ? 'cornered' : 'tracked_district';
}
```

Before rolling, the pursuit scene offers the exit choices from Step 6B (head back to her residence or walk off into the city, go to ground, break the trail, rooftops). Vampire speed's already in use.

### 10B. Outcomes

| Outcome | Effect |
|---|---|
| `lost` | Nothing further. The existing aftermath scene plays. |
| `tracked_district` | District heat +15. Every open case from today gains +10 progress (`air_track`, points at Kelsie: no). |
| `tracked_home` | Every open case from today gains `trace_residence` (+25, points at Kelsie). `tpdKnownResidence` updates to where she's staying. If a warrant's active: warrant service there the next morning (10E). |
| `tracked_lurasi` | Raptor One watches her walk into the Lurasi lot and peels off. Every open case from today gains +10 (no name). Nothing else follows. |
| `cornered` | The K9 team finds her hiding spot: `tpd_k9_corner` scene (flee again at +8 thirst with air still up, compel the handler, or surrender). |

### 10C. Sample prose

Warm (fed recently):

> You're six blocks out before the first siren finishes winding up. Nobody on the ground's catching that.
>
> Then the air over the rooftops starts to throb.
>
> Raptor One comes in low over the Commercial District with its spotlight dark. It doesn't need the light. Somewhere in its belly, a thermal camera's painting the city in grays, and you're the warmest thing moving on it. You fed twenty minutes ago.

Cold (thirst 71+):

> Raptor One sweeps the block twice. Its searchlight stays off. You stand in the mouth of a parking garage with the rotor wash tugging at your hair and watch it hunt.
>
> You haven't fed in two days. On that camera, you're the same temperature as the brick.
>
> It banks away toward the river.

### 10D. K9

Officer Brenner and Juno deploy in two situations:

- **At scenes:** murders found within 6 hours, burglaries of occupied homes, and any police assault. Seeds `k9_track` (+15). If the recorded exit was `walk_home` and she has a residence, add `k9_residence` (+20, or +8 with no name at the Lurasi). A homeless Kelsie's track just ends wherever she bedded down. Rooftop and vampire-speed exits leave no track. Rain (`isRaining`) wipes the track entirely. The cover-up modifier applies.
- **In pursuit:** `rollK9Corner(ctx)` fires only for `serious`/`violent` severity, not raining: `0.5 × (1 − getStealthBonus())`, ×0.5 if `go_to_ground`.

Juno can track Kelsie. She doesn't like what she finds at the end. That detail should appear every time, because it's a small supernatural data point TPD writes down:

> Juno works the alley with her nose an inch off the asphalt, never lifting it, pulling Brenner along at the end of the lead.
>
> She stops ten feet from the dumpster you're crouched behind. Her hackles go up. She sits down hard and won't take another step.
>
> Brenner crouches beside her with a hand on her ribs. "What've you got, girl?"
>
> She whines, low, and doesn't take her eyes off the dark behind the dumpster.

Each K9 corner adds +3 supernatural evidence ("K9 refused final approach" in the report).

### 10E. Warrant service where she's staying

Each morning, if a warrant's active, `tpdKnownResidence` matches `getKelsieResidence().id`, and the residence isn't the Lurasi, queue `tpd_warrant_service` at that location. (A pursuit that ends `tracked_home` sets the known address and queues it for the next morning; at Tier 4 it's the same day.) Model it on the existing `drug_raid_home` / `drug_raid_not_home` pair. The scene branches by residence:

- **Varhall, own unit:** SWAT stack at the door, the Bear in the courtyard, Raptor One overhead. Cruz leads it personally if she's had a tactical capture before (TAD in hand). Choices mirror the arrest tree: surrender, flee (through a window, straight into air support), fight (the Bear, twelve officers, bodycams everywhere), compel (can't compel the whole stack; buys a head start at +25 thirst). Not home: she comes back to a broken door frame, a search warrant on the counter, and her things bagged.
- **Varhall, with Jaewon:** the same raid, with Jaewon home for it or coming home to it. Either way Jaewon's detained and interviewed (Step 12C). This should hit hard. It's the cost of being careless while someone else sleeps in the next room.
- **Timeless Hotel:** no battering ram. Plainclothes in the lobby, SWAT in the service elevator, hotel security walking them up with a master key card. The first Kelsie hears is the lock clicking green. Flee means a window many floors up or a hallway full of rifles. Not in the room: the manager's cleared her things to a storage room, and `hasHotelRoom` is revoked.
- **Oracle Inn:** two cruisers in the parking lot, a motor unit at the exit, and a knock from an officer standing to the side of the door frame the way the Academy teaches. Smaller stack, same choices. Not in the room: room revoked, things held at the desk "for the police."
- **Lurasi Motel:** never. See 6E.
- **Homeless:** no warrant service. The street takedown (6F) replaces it.

Losing a hotel room to warrant service also clears `lastRestLocation` and `tpdKnownResidence`, and the existing `hotelsBanned` flag keeps her from checking back in. After a raid at Varhall with her own lease, `processPostEscapeConsequences()`'s existing eviction logic takes over.

---

## Step 11: Undercover & Decoy Operations

TPD's Special Investigations Section puts plainclothes officers where crime's happening. The existing undercover mugging target (10% at Tier 2+) and off-duty cop pickpocket targets are the seed. This step makes plainclothes work systemic.

### 11A. `getUndercoverChance(district, crimeType)`

```javascript
function getUndercoverChance(district, crimeType) {
    var p = getTPDPresence(district) / 100;
    var c = 0.03 * p * 2;   // 2% in the Slums, 4% Downtown at baseline
    if (districtHasActiveSeries(district, crimeType)) c += 0.10;   // a detail's been assigned
    if ((gameState.flags.cruzProfile || 0) >= 50 && crimeType === 'violent_hunt') c += 0.06;
    return Math.min(0.30, c);
}
```

Replace the mugging's flat 10% (Tier 2+) with this. Apply it to pickpocketing targets, drug buyers (Step 14), and violent hunt targets (11B).

### 11B. Hunt decoys

When a bite series is active (2+ Profile Zero cases) or Cruz's profile is 50+, violent hunt targets can be decoys: an officer playing drunk, lost, or distracted in the district Cruz expects Kelsie to hunt next, with a cover team a block away.

Casing tells (added to `buildCasingText`):

- **Stealth 20+:** "He's drunk, sure. His feet aren't."
- **Stealth 40+:** "He's weaving, and every time he stumbles he ends up facing the alley. Nobody's that unlucky. He's bait."
- **Below 20:** no tell. She's hunting blind.

### 11C. The sting: `hunt_decoy_sting`

If Kelsie strikes a decoy, the grab lands on a trained officer with a vest under his shirt and a panic button in his hand. The cover team arrives in about twenty seconds.

- **Flee:** always possible. Pursuit at `violent` severity. The decoy's bodycam got her face at arm's length unless masked: `witness_face_cop` (+45) on a new `police_assault` entry.
- **Compel the decoy:** works (he's a regular officer), buys ten seconds, compulsion echo, and the cover team's still coming.
- **Feed anyway:** demonic karma only. Twenty seconds buys one bite and a few swallows. She's got his blood and an open wound on a cop (`profile_zero`), and the team's on the corner when she lifts her head. Straight to pursuit.
- **Surrender:** arrest pipeline, `caught_in_act`.

Sample opener:

> Your hand closes on his collar and he moves before you do.
>
> He twists inside your grip, fast and trained, and something clicks in his fist. Somewhere down the block, a car door opens. Then three more.
>
> "TPD!" he shouts, right into your face. "Down! Get down!"
>
> The drunk's gone. He was never there.

### 11D. Pickpocket details and narcotics buys

- **Pickpocket detail:** 3+ pickpocketing ledger entries in one district within 7 days assigns plainclothes there for 10 days. Undercover chance +10% in that district, and every lift there rolls a "plainclothes spotted you" check that turns into `street_pickpocket_cop_arrest`.
- **Narcotics buys:** at drug heat Warm+, each sale rolls `getUndercoverChance(district, 'drug_deal')`. An undercover buy ends with a controlled purchase: product and money go into evidence, the buyer walks, and the case gets `witness_face_cop` and `camera_clear` (the buyer wore a wire camera). The arrest comes later, through the warrant, the way real narcotics cases work. Stealth 20+ gets a tell ("He knows the price. Nobody who needs it this bad knows the price.").

---

## Step 12: Detective Cruz & the Detective Bureau

Cruz is already the best-written presence in the police system. What she's missing is a way for the player to *feel her work*: a sense that she's learning, adapting, and closing distance even when she's offscreen.

### 12A. Cruz's profile

New stat: `gameState.flags.cruzProfile` (0-100). How well Cruz understands the way Kelsie operates. It never decays. It's a model in her head.

| Source | Gain |
|---|---|
| Each Profile Zero sample processed | +10 |
| Each Cruz-owned case opened | +3 |
| Failed compulsion on Cruz (existing flag) | +15 (one-time) |
| Compulsion echo pattern (9E) | +15 one-time, then +5 per echo |
| Surveillance hub tampering (6C) | +5 |
| Bribery/seduction attempt on any officer | +5 |
| Refusing her coffee | +5 |
| Every Cruz street encounter | +2 |
| Mothers erasure (12E) | +10 |

| Profile | What she does |
|---|---|
| 20+ | Reads Kelsie's time-of-day habits. District presence during Kelsie's most common crime hour +5. |
| 30+ | **Forecast.** Each morning, Cruz predicts the district Kelsie's most likely to act in (the one her rotation history points to next) and staffs it: `cruzForecastDistrict`, +15 presence there all day. |
| 50+ | **Decoys.** Hunt decoys enabled (11B). Undercover chance +6% on hunts. |
| 70+ | **Patience.** Elimination sample chance +15% (Step 8). She starts showing up where Kelsie lives and works (12C). |
| 90+ | **Waiting for you.** 10% chance on any violent hunt casing in the forecast district that Cruz is already there: `cruz_scene_arrival`. The hunt ends. The conversation doesn't. |

`getRotationForecast()` reads `_lastViolentHuntDistrict`, `_huntDistrictStreak`, and the last ten ledger entries' districts, then picks the district Kelsie's used least recently among the ones she uses at all. Players who rotate predictably get predicted. Players who break pattern (or read the forecast at stealth 50+) stay ahead of her.

### 12B. The Detective Bureau as a presence

Cruz's people should show up in her dialogue and in the world, so the Bureau feels like a staffed floor:

- **Osei** runs Homicide. When a body's found, the news item names her: "Detective Sergeant Nadia Osei, lead investigator."
- **Varga** runs the camera side. Cruz quotes her: "Varga's got you on nine cameras between the alley and the bus stop. She's working on ten."
- **Tolliver** handles burglaries and muggings. Voluntary interviews on property cases are with him: patient, courteous, a thermos of tea he offers and she can't drink.

### 12C. New Cruz scenes

All in Cruz's established voice: calm, precise, faintly amused, never cruel for sport, never in a hurry.

- **`cruz_coffee`** (POI, no sample yet, district travel, 25%): Cruz is outside a coffee shop with two cups and hands Kelsie one. Accept and drink: sample taken. Accept and carry it off: Kelsie can ditch it (stealth 30+ notices why she was handed it). Refuse: +5 profile.
- **`cruz_sample_match`**: Step 8C.
- **`cruz_diner_visit`** (POI, Kelsie working at the diner, profile 70+): Cruz takes a booth in Kelsie's section, orders pie, tips 30%, and asks Ruby three questions on the way out that Ruby tells Kelsie about later. If Ruby's friendship/romance milestone is met, Ruby's loyal in that retelling ("I told her you're the best server I've got and she should mind her business.").
- **`cruz_jaewon_interview`** (POI, living with Jaewon, profile 70+, or after a warrant service at Varhall): Kelsie comes home to Jaewon at the kitchen table with Cruz's card in front of her. Jaewon's reaction depends on `jaewonKnowsYoureVampire` and relationship levels: frightened and loyal, frightened and angry, or quietly furious at Cruz. It should be the scene where the police system reaches into the part of the game the player cares about most.
- **`cruz_scene_arrival`** (profile 90+): Kelsie's casing a hunt and Cruz is sitting on the bus bench across the street. She doesn't draw. She tells Kelsie the target's name and that his daughter's picking him up in ten minutes. The hunt is over for the night.
- **`cruz_echo_pattern`**: Step 9E.

Sample (`cruz_coffee`):

> Cruz is leaning on the rail outside the coffee shop on Ninth with a cup in each hand. When you get close enough, she holds one out.
>
> "Oat milk," she says. "You look like an oat milk person."
>
> Her face is friendly. Her eyes are on your hand, waiting to see whether it takes the cup.

### 12D. Cruz stays lawful

Cruz never plants evidence, never threatens Jaewon, never breaks procedure, and never needs to. Her power is that she's thorough and patient and right. Every new scene should make that the source of the menace. If a line ever has her bending a rule, rewrite it so she gets the same result the legal way, faster.

### 12E. The Mothers, and the file Cruz keeps by hand

Rufus's Mothers services wipe the crimes out of TPD's minds and systems (existing). Cruz is immune (existing). What's missing is the scene where she notices.

**`cruz_paper_file`** fires once, the first time a Mothers clearing completes while Cruz is assigned:

> Cruz finds you outside the pharmacy. She's got a manila folder under one arm, thick with paper, held shut with two rubber bands.
>
> "Strangest week of my career," she says. "Osei doesn't remember your case. Varga doesn't. The database says there never was one." She taps the folder. "I've been printing everything since the first time you tried to get in my head. I write it all down twice now."
>
> She tucks the folder back under her arm.
>
> "Whoever you've got cleaning up after you, they're very good. I'm better."

After this: +10 profile, and every future Mothers clearing leaves Cruz's own cases (her profile, her personally owned case files) at 50% progress instead of zero. She rebuilds from her notes. The Mothers still clear the warrant and everyone else's memory, so the service keeps its value; it just can't make her forget.

---

## Step 13: Chief Lars, Mayor Renalds, and a City That Shows Its Police

### 13A. Chief Lars on the criminal path

Lars appears mostly through news, the way a chief should. His appearances mark the city's escalation.

- **`tpd_lars_briefing`** (one-time, queued in `checkForSuspicionEvent` after the first signature series links 3+ cases, or Tier 3, whichever comes first): Kelsie sees the press conference on a TV in a shop window, a bar, or at home.
- **`tpd_lars_manhunt_address`** (one-time, Tier 4): Lars announces the manhunt. Raptor One nightly, the Bear staged at district borders, checkpoints on the bridges.

Sample (`tpd_lars_briefing`):

> The chief doesn't use the podium. He stands beside it with his hands folded in front of him, and the room goes quiet without anyone asking it to.
>
> "Four people have been attacked in this city since the first of the month," Hugo Lars says. "One of them died. I want to speak to the person responsible."
>
> He looks straight into the camera, dead center, the same way Cruz does.
>
> "My department's closed eleven hundred cases this year. Yours is going to be one of them. When that happens, I'd prefer you were alive for it."

Pull the numbers from the ledger (attacks in the last 30 days, bodies discovered).

### 13B. Chief Lars on the hero path

- At `hero_mayor_press_conference` and `hero_mayor_first_meeting`: Lars stands behind Renalds, silent, uniform pressed. One line of description. He doesn't clap.
- **`hero_lars_rules`** (one-time, first visit to the Mayor's Office after legalization): Lars is waiting in Renalds's office with a one-page document. Rules of engagement. Hand every suspect to TPD. Give a statement on scene. Never touch evidence. Never enter a building TPD has secured. He stays courteous. He built a department where nobody breaks rules, and now the mayor's handed him someone who can fly.

> "The mayor made you legal," Lars says. "That was her call, and she's usually right."
>
> He slides the page across the desk.
>
> "Now you're working in my city. So you'll work the way my people work."

After this scene, suited crime-fighting that ends with Kelsie handing the suspect over grants +2 hero reputation extra ("TPD commends cooperation"). Leaving before officers arrive grants none.

### 13C. Mayor Renalds on the criminal path

Renalds is defined on the hero path. On the criminal path, she appears once in news, at Tier 4, standing beside Lars: "This department has my full support and every resource this city can give it." That's all she needs. The mayor who built TPD with Lars has no reason to say more.

### 13D. Wanted tiers made visible

The existing tier effects (blocks) stay. Add what the city *looks* like:

| Tier | World changes |
|---|---|
| 0 | Baseline presence (Step 2). Kestrel drones Downtown only. |
| 1 | Presence +5. Casing blocks mention "a patrol car that's been past twice." |
| 2 | Presence +10. Plainclothes in Downtown and Commercial (undercover +3%). Drones in every district with presence 30+. |
| 3 | Presence +20. Checkpoints on the bridges and the Dockyard gate (vampire-speed travel across them rolls bodycam risk). Raptor One flies every night from 10 PM. |
| 4 | Presence +35. The Bear staged at district borders. SWAT on standby (warrant service happens the same day when tracked home). Motor units at every major intersection. Hotel desks call in on sight (desk recognition ×1.5). Lars and Renalds on every channel. |

### 13E. The TPD blotter

The city's full of small criminals, and TPD catches almost all of them. Show it. `getTPDBlotterLine()` returns one line from a pool of 20+, used by newsstand scenes, TVs in background prose, Jaewon's phone during apartment scenes, and radio chatter during police encounters. Samples:

- "TPD arrests two men 38 minutes after a jewelry store smash-and-grab on Fifth. Both identified through store cameras and a K9 track."
- "Commercial District burglary suspect arrested at home the next morning. Police say he left a single print on a window latch."
- "Three arrested in Residential car-theft ring. Detective Andre Tolliver credits residents' doorbell cameras."
- "Man who fled a traffic stop on foot located by drone in under four minutes."
- "Armed robbery suspect surrenders to SWAT after a two-hour standoff in Wendale. No injuries."
- "Pickpocketing crew dismantled after a week-long plainclothes operation Downtown."

Occasional lines should mention the underworld and TPD's limit: "Police seize a Boruski weapons shipment at the Dockyard. No arrests above the level of the drivers."

This matters for tone. The player should know, from background noise alone, that ordinary criminals in Trigrave get caught fast.

---

## Step 14: Crime-by-Crime Integration

Every crime system gets its TPD hooks. Most of these are small once Steps 2-11 exist.

### 14A. Mugging
- Police arrival: `getTPDResponseChance(location, 'mugging')` (2D).
- Undercover target: `getUndercoverChance()` (11A) replaces the flat 10% at Tier 2+.
- A mugged victim who isn't compelled gives `victim_statement` (+20).
- Exit choices (6B) after every mugging outcome that doesn't end in custody.

### 14B. Burglary
- **Monitored alarms:** 30% of Residential houses, 5% in the Slums. Casing reveals the panel at stealth 30+. At stealth 45+, "Bypass the panel" (+5 min, +4 stealth XP). A tripped alarm starts a response clock: rooms before TPD arrives = `max(1, round((1 − presence/100) × 5))`. Staying past the clock triggers a police arrival in the house.
- CSU lifts prints on every burglary (gloves prevent the lead). Occupied houses get K9 if reported within 6 hours.
- Doorbell cameras: an unmasked face on a doorbell cam is `camera_clear` (+30). Disabling the cam first stays the right call.

### 14C. Pickpocketing
- Patrol-level case, ×0.5, cold in 5 days: single lifts almost never come back.
- Pickpocket details (11D) make repeating a district dangerous.
- Off-duty cop targets unchanged.

### 14D. Blood theft
- Trigrave General has a TPD substation (Officer Reyes already works it). Presence in the Medical District's baseline already reflects it.
- Compelling Reyes creates a compulsion echo (her bodycam switch-off is already in the prose; now it's logged).
- Camera trace applies to the exit from the hospital and the blood bank.

### 14E. Violent hunts
- Police interrupt reworked (2D). Patrol tension events weighted by presence instead of suspicion.
- Decoys (11B-C). Wash the wound (5C). Exit choices (6B) added to `after_ambush_hunt` / kill aftermath before the summary card.
- Clean, completed feeds on living victims leave nothing: no bite, no case. The best hunters never open a file.

### 14F. Drugs
- Patrol encounters scale with presence (2D). Narcotics controlled buys (11D).
- Drug raids at Burning+ become SWAT operations with the Bear when drug heat is Nuclear (prose upgrade to `drug_raid_home`).
- Cruz's existing narcotics involvement at Hot stays as written.

### 14G. Exhibition
- Presence-scaled clocks (2D).
- Professional officers (Step 15A).
- Exhibition bookings record prints and a mugshot (Step 15C).

### 14H. Vampire servants
- Base capture chance multiplied by `getTPDPresence(district) / 40`.
- The servant task force (`servant_task_force_formed`) is Cruz's Bureau working the pattern. Its scene text should name Varga's camera work.
- Rufus's clean release is a Mothers service (Step 15D).

### 14I. Hero path
- Crimes Kelsie finds on patrol show a TPD response estimate in the prose ("Sirens, maybe three minutes out"). If she hesitates past it, TPD resolves the crime without her (no karma, no reputation).
- `hero_lars_rules` (13B) and cooperation bonus.
- Vigilante police encounters get the professionalism pass (15B).

### 14J. Cartel war
- Operation Phantom stays as written. Optional prose upgrade: Raptor One overhead during `cruz_swat_ambush`, the Bear at the warehouse door.

### 14K. Rufus & the Mothers
- Existing prices, cooldowns, and effects stay. Cruz's profile gains on Mothers clearings and her paper file (12E) are the only mechanical changes.
- The Lurasi Motel is sanctuary (6E): no warrant service, no raids, no plainclothes in the lot, and traces or tracks that end there never name her. That's the price of TPD's arrangement with the Slums, and the reason a hunted Kelsie might trade a Timeless suite for a Lurasi room.

---

## Step 15: Consistency & Professionalism Pass

Existing prose that contradicts the canon from Step 1. Fix each one in its own scene, and keep everything that isn't the officer's conduct.

### 15A. Exhibition police stops

**Where:** `handleIndecencyPoliceArrive()`, `handleSheerPoliceArrive()`, `tickSheerLingeriePolice()`, the corruption-tiered stop texts nearby, and `offerExhibPoliceChoices()` callers.

**Problem:** Several stops have officers leering: an officer who "doesn't even pretend to look at your face," one whose "eyes go straight to your bare pussy," one whose "jaw works" looking through mesh, one who's been "driving at walking speed for half a block, looking at" her.

**Rule:** Officers keep their eyes on her face, speak procedurally, and offer the emergency blanket every TPD cruiser carries. Kelsie's own corruption-tiered interiority (her arousal, her shame, her thrill) stays untouched. The heat of these scenes lives in her head and in being seen by the street; the officers are the one thing in the scene that stays cool.

Example rewrite (the Deeply Corrupted naked stop):

> *Before:* "The officer gets out, and his eyes go straight to your bare pussy."
>
> *After:* "The officer gets out with a folded gray blanket already in his hand. He keeps his eyes on yours, the whole way over, like that's a skill he practiced. It probably is."

### 15B. Hero-path officers

**Where:** `vigilante_police_explain` and its branches.

**Problem:** "Get out of here, [name]. Before my captain hears this." reads as an officer breaking protocol as a favor. The Tips & Guide says "Officers look the other way."

**Rule:** TPD officers exercise lawful discretion on the record. The victim's statement goes on the bodycam, the officer documents the intervention, and he releases her pending a detective's follow-up. Same outcome, all by the book.

> *Before:* "Get out of here, [name]. Before my captain hears this."
>
> *After:* "Victim statement's on my camera." He clicks his pen. "You're free to go, [name]. A detective'll want to talk to you. I'd take the call."

Update the Tips & Guide "Moral challenge" line to: "The victim speaks for you on camera. Officers document it and release you on the spot."

### 15C. Exhibition bookings skip identification

**Where:** `exhib_arrest_booking` onEnter.

**Problem:** A full booking (`arrest_booking`) sets `mugshotTaken`, `fingerprintsInSystem`, facial recognition 100, and runs `processPendingForensics()`. An exhibition booking processes Kelsie through the same Central Booking building and skips all of it. TPD books everyone the same way.

**Fix:** Exhibition bookings set `fingerprintsInSystem = true` and `mugshotTaken = true` and run `processPendingForensics()`. Leave facial recognition alone unless it's already 50+ (a mugshot for indecent exposure doesn't make the evening news). Add one line to the booking prose at Station Two ("Ten fingers, ten scans. The technician doesn't care what you came in wearing.") and a notification: "Your fingerprints are on file."

**Balance note:** this is the single largest consequence in the pass. An exhibitionist Kelsie who's ever been booked has prints on file, and every gloveless crime she's committed converts. That's intended, and the Tips & Guide should say it plainly.

### 15D. Rufus's service text

**Where:** the Tips & Guide REDUCING SUSPICION and Vampire Servant Operations CAPTURE entries, and every `rufus_service_*` scene's description text.

**Problem:** Lines like "Evidence goes missing, arresting officer gets reassigned" and "Misfiled evidence, broken chain of custody" can read as TPD corruption. They aren't: Rufus works through his own underworld connections, and through the Mothers when things are dire.

**Fix:** State the mechanism in each line so it's never ambiguous.
- Servant clean release: "Rufus calls in a favor with the Mothers. Nobody at the precinct remembers the arrest, and the paperwork follows them."
- Lose the Paperwork: route it outside TPD (the contract lab courier, the court clerk's office, city records), or through the Mothers. Audit the scene text for any line implying a TPD employee was paid or turned.
- Kill the Warrant: the DA's office reclassification stays (the DA isn't TPD). Add one Cruz line on her next encounter noting the "administrative error," +5 profile.

### 15E. Tips & Guide statements that change

Every line below is now wrong and gets rewritten in Step 16:
- "Police can interrupt a violent hunt once suspicion reaches 20..."
- "Nothing lands on you until something identifies you: your fingerprints or face matched after a booking..."
- "Fleeing always uses vampire speed." (It still does. Pursuit now continues after it.)
- "Undercover Cops: At Wanted Tier 2+, there is a 10% chance..."
- "The moment the evidence ledger ties a crime to you (or a warrant goes out), Detective Gabriella Cruz..." (POI now assigns her too.)
- "Officers look the other way." (15B)

---

## Step 16: UI, Stats Modal & Tips & Guide

### 16A. Stats modal: Trigrave PD subsection

Add under the existing City Suspicion section:

- **TPD presence here:** band label for the current district (Light / Steady / Heavy / Saturated), plus the number at stealth 40+.
- **Cases in the news:** counts of public cases by type (bodies found, assaults reported, burglaries). Public knowledge only: Kelsie can't see TPD's internal progress.
- **Person of Interest:** shown once Kelsie's had her first POI-era Cruz beat ("Cruz has your name").
- **Cruz's attention:** label from `cruzProfile`: Distant (0-19), Curious (20-49), Focused (50-69), Fixated (70-89), "She knows how you think" (90+).
- **Compulsion lapses on record:** shown once `cruz_echo_pattern` has fired.
- **Prints on file / Profile Zero matched:** yes/no, once true.

### 16B. Reading case warmth

Kelsie can't see case progress directly. Two in-world ways to read it:
- **Scene tape.** At stealth 40+, walking through a district with an open serious case adds a line: "The tape's still up on the alley off Ninth" (open) or "Somebody finally took the tape down" (cold).
- **Servant scouts, skill 75+** (existing scout intel tier): the daily report names each open serious case as Cold / Cooling / Warm / Hot. They watch where detectives go.

### 16C. Post-crime summary card

The existing consequence cards (hunt summary, mugging, burglary) gain two lines: the exit taken, and "Case opened: [unit]" in the unit's color (gray patrol, blue property, amber detective, red homicide, violet Cruz).

### 16D. Tips & Guide

Add a new top-level **Trigrave PD** section before Crime & Suspicion:
- THE DEPARTMENT (Lars, Renalds, the Academy, Professional Standards, why bribery and seduction never work, the two supernatural exceptions)
- UNITS (patrol, motors, K9, air, SWAT, armored, narcotics, plainclothes, CSU, RTCC, Detective Bureau)
- PRESENCE (district baselines, what raises them, the ambient lines)
- HOW TPD WORKS A CASE (units, leads, cold cases, series, identification)
- PERSON OF INTEREST (the sample, gloves, the time bomb)
- PURSUIT (air, thermal and thirst, rain, K9, exits)
- WHERE YOU SLEEP (Varhall, the Timeless, Oracle Inn, the Lurasi's sanctuary, the known-address loop, hotel desks, homeless street takedowns)
- COVERING YOUR TRACKS (the full stealth table from 4B)

Rewrite in Crime & Suspicion: BASICS (the case vs. the city's temperature), POLICE ENCOUNTERS, DETECTIVE CRUZ (profile, forecast, decoys, the paper file), STRATEGY TIPS, STEALTH BUILDS (The Ghost gains "never becomes a POI"; The Brute gains "the helicopter always finds you warm").

New strategy tips, in the guide's existing voice:
- "Hunt hungry if you plan to run. Raptor One sees a fed vampire from a mile up."
- "Never walk straight home from anything."
- "The Lurasi's the one roof TPD won't go under. Every other room in the city is a door they can knock on."
- "Switching hotels under a warrant buys time. The desk at the next one is already looking at your face."
- "If you've got no home, you've got no door to kick in, and every camera in the city is looking for you instead. Stay out of Downtown."
- "Gloves are cheap. A single print is a time bomb."
- "Rain covers a lot. Dogs can't track through it and drones don't fly in it."
- "If Cruz hands you a coffee, think about why."
- "Rotate districts unpredictably. Once Cruz can predict you, she'll be there first."

---

## Step 17: Save Migration & New gameState Fields

### 17A. New fields

```javascript
// Department & presence
gameState.flags.cruzForecastDistrict = '';     // Step 12A
gameState.flags.cruzForecastDay = 0;
gameState.flags.fieldInterviewToday = false;   // Step 9, reset daily
gameState.flags._fieldInterviewCase = null;

// Person of interest & sample (Step 8)
gameState.flags.kelsiePOI = false;
gameState.flags.kelsiePOIDay = 0;
gameState.flags.eliminationSampleTaken = false;
gameState.flags.eliminationSampleSource = '';  // 'coffee' | 'discard' | 'diner'
gameState.flags.profileZeroMatched = false;
gameState.flags.sawSampleMatch = false;

// Cruz (Step 12)
gameState.flags.cruzProfile = 0;
gameState.flags.cruzEchoPatternSeen = false;
gameState.flags.cruzPaperFileSeen = false;
gameState.flags.cruzCoffeeRefused = 0;

// Compulsion echo (Step 9E)
gameState.flags.compulsionEchoes = 0;
gameState.flags.compulsionEchoLog = [];        // { day, district }

// Lars & the city (Step 13)
gameState.flags.larsBriefingSeen = false;
gameState.flags.larsManhuntSeen = false;
gameState.flags.larsRulesSeen = false;

// Residence (Step 6E-6F)
gameState.flags.lastRestLocation = '';        // 'varhall' | 'timeless' | 'oracle' | 'lurasi'
gameState.flags.tpdKnownResidence = '';       // the address TPD believes is hers ('' = none)
gameState.flags.streetTakedownToday = false;  // reset daily

// Warrant service (Step 10E)
gameState.flags.warrantServicePending = false;
gameState.flags.warrantServiceDay = 0;
gameState.flags.warrantServiceLocation = '';  // residence id the raid was queued for

// Details (Step 11D)
gameState.flags.pickpocketDetails = {};        // district: expiresDay

// Per ledger entry (added in createLedgerEntry)
// entry.investigation = { unit, progress, leads, status, lastLeadDay, poiRaised, tipRolled }
// entry.outfit = [itemIds]
// entry.exit = 'walk_home' | 'go_to_ground' | 'break_trail' | 'rooftops' | 'vampspeed'
// entry.seriesKeys = []                       // signature series memberships
```

### 17B. Save migration

- Existing ledger entries get an `investigation` object on load: unit from `assignInvestigatingUnit()`, `progress` 0, status `cold` if the entry's older than its unit's cold window, otherwise `open`, with leads seeded from the booleans already on the entry (`witnesses`, `camera`, `forensic`). Entries already `linkedToKelsie` get status `identified`.
- Existing entries get no `exit` and no `outfit`. K9 and outfit leads can't retroactively seed.
- `cruzProfile` initializes from history: +15 if `gabriellaCompulsionFailed`, +2 per `cruzFugitiveEncounters`, +3 per existing murder/assault entry with `biteMarks`, capped at 60. A save deep into a Cruz arc shouldn't start her at zero.
- `kelsiePOI` initializes true if `gabriellaCruzAssigned` is already true.
- If `fingerprintsInSystem` is already true, `eliminationSampleTaken` stays false (she was booked; the sample's moot) and `profileZeroMatched` initializes true only if `mugshotTaken` (a full booking includes a cheek swab going forward; add a line to `arrest_booking` Station Two saying so).
- Exhibition-only bookings in existing saves (arrest history with only exhibition entries, `fingerprintsInSystem` false) stay as they are. The Step 15C fix applies to future bookings.
- `DISTRICT_CAMERA_DENSITY` gains `dockyard` and `wendale`; no save data depends on the table.
- `lastRestLocation` initializes from what she holds (Varhall first, then the Timeless, Oracle Inn, the Lurasi), or '' if she holds nothing.
- `tpdKnownResidence` initializes to 'varhall' if a warrant's already active and `hasOwnApartment` is true (her lease is in her name). Everyone else starts unknown: an existing fugitive in a hotel room or on the street gets found the new way.

---

## New Function Summary

| Function | Step | Purpose |
|---|---|---|
| `TPD` (constant) | 1A | Department names, units, callsigns |
| `getTPDPresence(district)` | 2A | 0-100 police presence per district, all modifiers |
| `getTPDResponseChance(district, crimeType)` | 2C | Chance TPD reaches a crime in progress |
| (rewrite) `getMuggingPoliceChance(location)` | 2D | Delegates to `getTPDResponseChance` |
| `assignInvestigatingUnit(entry)` | 3B | Triage: patrol / property / detective / homicide / cruz |
| `seedCaseLeads(entry, params)` | 3C | Seeds leads at ledger creation |
| `advanceInvestigations()` | 3D | Daily case tick: develop leads, tip line, identify, cold |
| `caseHasReleasableStill(entry)` | 3D | Whether TPD can release a still for the tip line |
| `fireIdentificationBeat(entry)` | 3E | Flavor beat by identifying lead |
| `countOpenCasesInDistrict(district, level)` | 2A | Presence modifier from open serious cases |
| `linkSignatureSeries()` | 3G | Bite / mask / MO series across districts |
| `getCoverUpMult()` | 4A | Stealth-driven soft-lead multiplier |
| `rollCameraTrace(entry, exit)` | 6A | RTCC route reconstruction |
| `getKelsieResidence()` | 6E | Where she's staying: Varhall, the Timeless, Oracle Inn, the Lurasi, or the street |
| `rollHotelDeskRecognition()` | 6E | Daily desk call-in for a room held under a warrant |
| `checkStreetTakedown(district)` | 6F | Homeless fugitive located through the camera network |
| `offerCrimeExitChoices(ctx)` | 6B | Walk home / go to ground / break trail / rooftops / speed |
| `checkPersonOfInterest(entry)` | 8A | POI threshold check |
| `rollEliminationSample()` | 8B | Daily discard sample roll |
| `acquireEliminationSample(source)` | 8B | Puts prints + Profile Zero on file, converts leads |
| `checkFieldInterview(district)` | 9A | Stop trigger on district travel |
| `findOutfitMatchCase(district)` | 9B | Crime-outfit description match |
| `recordCompulsionEcho(district)` | 9E | Logs officer compulsion lapses |
| `resolvePursuit(ctx)` | 10A | Air, thermal, rain, stealth, exits |
| `rollK9Corner(ctx)` | 10D | K9 finds her hiding spot |
| `getUndercoverChance(district, crimeType)` | 11A | Plainclothes / decoy chance |
| `getRotationForecast()` | 12A | Cruz's predicted next district |
| `modifyCruzProfile(amount, reason)` | 12A | Profile gains, threshold beats |
| `getTPDBlotterLine()` | 13E | Background arrest news |
| `getTPDAmbientLine(district)` | 2E | Presence-band street line |

## New Scene Summary

| Scene / beat | Step | Type | Trigger |
|---|---|---|---|
| Crime exit choice | 6B | Repeatable | After any crime not ending in custody |
| Identification beats | 3E | Repeatable (by method) | A case closes on Kelsie |
| `cruz_sample_match` | 8C | One-time | First sample conversion |
| `tpd_field_interview` | 9C | Repeatable | Description match + presence |
| Voluntary interview (Osei / Tolliver) | 9C | Repeatable | Failed cooperate check, Kelsie agrees |
| Bribery / seduction failure | 9D | Repeatable | Money or flirt option in any TPD encounter |
| `cruz_echo_pattern` | 9E | One-time | 3+ compulsion echoes with Cruz assigned |
| Pursuit: air (warm / cold variants) | 10C | Repeatable | Any flee |
| `tpd_k9_corner` | 10D | Repeatable | K9 finds her hiding spot |
| `tpd_warrant_service` (Varhall own / Varhall with Jaewon / Timeless / Oracle; home and not-home variants) | 10E | Repeatable | Warrant active and TPD knows where she's staying (never the Lurasi) |
| `tpd_street_takedown` | 6F | Repeatable | Homeless, warrant active, district travel |
| Lurasi ambient line | 6E | Repeatable | Staying at the Lurasi under a warrant |
| `hunt_decoy_sting` | 11C | Repeatable | Strike on a decoy |
| `cruz_coffee` | 12C | Repeatable until sample | POI, no sample |
| `cruz_diner_visit` | 12C | One-time | POI, diner job, profile 70+ |
| `cruz_jaewon_interview` | 12C | One-time | POI, living with Jaewon, profile 70+ or warrant service |
| `cruz_scene_arrival` | 12C | Repeatable (rare) | Profile 90+, forecast district, hunt casing |
| `cruz_paper_file` | 12E | One-time | First Mothers clearing while Cruz assigned |
| `tpd_lars_briefing` | 13A | One-time | First 3-case signature series or Tier 3 |
| `tpd_lars_manhunt_address` | 13A | One-time | Tier 4 |
| `hero_lars_rules` | 13B | One-time | First Mayor's Office visit after legalization |
| Renalds Tier 4 statement | 13C | One-time | Tier 4 news |
| Burglary alarm response | 14B | Repeatable | Alarm tripped, clock expires |

---

## Implementation Notes

- **Build one step. Output the file. Wait for confirmation.** This overhaul touches the ledger, the daily tick, every crime system, Cruz, the hero path, and the Tips & Guide. A bundled step is a debugging disaster.

- **Steps 3 and 4 are the spine.** The investigation engine is what makes TPD formidable, and the cover-up modifier is what makes stealth decide whether it works. If the lead values are off, Step 7's promise collapses in one direction (murder's free again) or the other (every careful player gets caught by math). Run the Step 7 scenarios by hand with `cheatSetStealth()` and the existing hunt cheats after Step 7 lands, and don't move on until each row comes out as the table says.

- **Suspicion and the case file are different things. Keep them separate.** Suspicion is the city's temperature: it drives tiers, blocks, and presence. The case file is TPD's progress toward Kelsie specifically. Don't feed case progress into suspicion or the reverse, beyond what's specified (presence reads both). The existing ceiling, floor, and held-heat logic stays untouched.

- **Compulsion always works on a regular officer.** Never add a resist roll. The cost of compulsion is thirst and the echo trail. Cruz is the only exception, and she's already written.

- **Bribery and seduction never work.** Never add a success branch, a charisma check, or a corruption-gated exception. The failure's the content.

- **Cruz is lawful.** Every new Cruz scene gets its menace from thoroughness, patience, and being right. If a draft has her threatening someone or bending procedure, rewrite it.

- **Stealth should be felt, every time.** The first time each Step 4B threshold matters, the prose should show Kelsie doing the professional thing (reading the patrol rhythm, taking the rooftops, washing the wound). Players who invested in stealth should see the investment paying off in the fiction, beyond the numbers.

- **The Slums stay the Slums.** Low presence, low cameras, low suspicion. A body there still gets Homicide and Cruz. The Slums are forgiving to act in and exactly as unforgiving to be careless in. Don't raise the Slums' presence to "fix" this; the investigation engine already does.

- **The Lurasi is absolute.** No system in this doc sends TPD into the Lurasi Motel: no warrant service, no decoys, no plainclothes, no K9 corner, no Cruz scene set inside it. If a new system needs a location, check `getKelsieResidence().sanctuary` first.

- **Residence is free-roam.** Never assume Varhall. Every line that mentions where Kelsie sleeps reads `getKelsieResidence()`, and every scene that raids it branches on the residence id.

- **Don't rewrite the violent hunt system.** It's already overhauled. This doc adds exits, decoys, wash-the-wound, and the new interrupt formula. Everything else in the hunt pipeline (casing, approaches, feeding, tension events, disposal, summary card) stays as implemented.

- **Keep the TAD cycle and Operation Phantom as written.** They're finished systems. Steps 10E and 12 layer around them.

- **Jaewon's scenes carry the emotional cost.** `cruz_jaewon_interview` and the warrant service with Jaewon home are where TPD reaches into the relationship the player's invested most in. Write them with the same care as the Jaewon romance scenes, and branch on what she knows.

- **Writing rules for every new line.** 2nd person present, contractions always, short paragraphs, escaped apostrophes, no em dashes except a parenthetical aside or cut-off dialogue, no "A beat," no negation-then-correction formulas, no stacked "Not this. Not that." fragments. Officers sound like the Step 1D sample. Cruz sounds like Cruz.
