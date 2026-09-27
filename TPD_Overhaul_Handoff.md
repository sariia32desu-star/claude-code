# Trigrave PD Overhaul: Handoff (Steps 1–10 Done, Next Up: Jaewon Pass, Then Steps 11–17)

This doc stands on its own. It replaces the original `Trigrave_PD_Expansion.md` for everything still left to build. It covers what the overhaul is, the canon, the writing rules, the workflow, what Steps 1–10 actually built (with the real function and flag names), and the full spec for every remaining step.

---

## 0. Start Here (First Thing in the New Chat)

1. **Get the latest file.** The working file is `Vampire_Girl.html` at the root of the repo `sariia32desu-star/claude-code`, on branch **`claude/trigrave-pd-overhaul-3f1jh4`**. The last game commit is `af8e705` (Step 10). This handoff doc is committed right after it.
   - If the user uploads a `Vampire_Girl.html` in the new chat, work on that upload. Their rule: a new file from them wins.
   - Otherwise, check out the branch and work on `Vampire_Girl.html` in the repo. **Never** copy an older file over it, and never work from an earlier version. Losing progress that way is the worst possible failure.
2. **First task: the Jaewon staccato pass** (Section 5). Its own commit, then report and wait.
3. **Then Step 11.** Build one step per output, then wait for the user's go-ahead before starting the next. Never bundle steps.
4. **Standing instruction from the user:** "Fix any [style violations] you come across from now on." Fix style violations wherever you touch or read nearby prose, and mention them in the report.

---

## 1. What This Overhaul Is

Trigrave PD (TPD) is meant to be the most formidable police department anyone's ever built. The city has no military, so TPD fills that role: patrol, motors, K9, plainclothes, narcotics, forensics, SWAT, armored vehicles, a drone fleet, armed helicopters, and a Detective Bureau run by Gabriella Cruz, who's never lost a case.

Before the overhaul, TPD was a reaction to a suspicion number. Cases only landed on Kelsie after a booking, murder cost about five suspicion, and fleeing with vampire speed always ended things clean. The overhaul makes TPD an institution the player can feel working:

- A baseline police presence in every district that doesn't wait for Kelsie to get hot.
- A case file on every crime, with leads that develop over days.
- A lawful, Cruz-shaped way to get Kelsie's prints and saliva on file without a booking.
- The units the lore promises, built as real encounter content.
- Chief Hugo Lars, the man who built the department.
- Stealth at the center of everything: it decides how cleanly Kelsie commits a crime and how well she covers it up afterward.

**Target feel:** a clumsy Kelsie who kills without gloves gets a knock on the door within the week. A Phantom-tier Kelsie in gloves who takes the rooftops home, hides the body, and never shows her face can keep killing, but Cruz's "Profile Zero" file keeps growing and her next hunting ground keeps getting more crowded with plainclothes. The only things separating those two players are the stealth stat and the choices they make.

---

## 2. Canon (Every New Line Must Respect It)

- **No TPD officer is corrupt.** Bribery and seduction never work on an officer, in any scene, at any stat level. An attempt gets documented and becomes evidence. Never add a success branch, a charisma check, or a corruption-gated exception. The failure is the content.
- **Two things get past TPD, and both are supernatural.**
  - Kelsie's compulsion works on any officer except Cruz. Never add a resist roll. Compulsion costs thirst and leaves the echo trail.
  - The Mothers are an all-female criminal organization of powerful psychics. When Rufus calls in a favor, they can erase crimes from every mind that holds them, TPD included.
  - Cruz is immune to both.
- **Rufus's services are underworld work, never TPD corruption.** His lesser services run through his own criminal connections outside the department. His heaviest ones run through the Mothers.
- **The underworld survives because it's in a league of its own.** TPD arrests the street layer constantly and never reaches the top:
  - The Vajaros cartel is compartmentalized, rich, and lawyered.
  - The Boruski Syndicate is old-world and military-grade.
  - The Mothers are psychic. After what happened to Jésus's army, nobody at TPD wants to find out what an armed raid on them looks like.
- **Crime still happens.** Trigrave has muggers, burglars, and small crews. TPD catches almost all of them, fast.
- **Kelsie can live anywhere, or nowhere.**
  - Her options: Varhall Apartments (her own unit, or with Jaewon), the Timeless Hotel, Oracle Inn, the Lurasi Motel, or the street.
  - Wherever she's staying is where TPD comes for her.
  - **The Lurasi is absolute sanctuary.** No system ever sends TPD into the Lurasi: no warrant service, raids, decoys, plainclothes, K9 corner, or Cruz scene set inside it. Before placing any new TPD content at a residence, check `getKelsieResidence().sanctuary`.
  - Homeless Kelsie has no door to knock on, so TPD uses the cameras and takes her on the street.
  - If she lives with Jaewon, Jaewon's in the blast radius.
- **Residence is free-roam.** Never assume Varhall. Any line that mentions where Kelsie sleeps reads `getKelsieResidence()`, and any scene that raids her home branches on the residence id.
- **Cruz is lawful.** She never plants evidence, never threatens Jaewon, and never breaks procedure. Her menace comes from being thorough, patient, and right. If a draft has her bending a rule, rewrite it so she gets the same result legally, and faster.
- **Suspicion and the case file are separate.**
  - Suspicion is the city's temperature. It drives tiers, blocks, and presence.
  - The case file is TPD's progress toward Kelsie specifically.
  - Don't feed one into the other beyond what the spec says; presence reads both. The existing ceiling, floor, and held-heat logic stays untouched.
- **The Slums stay the Slums.** They're low on presence, cameras, and suspicion, but a body there still gets Homicide and Cruz. Never raise Slums presence to "fix" anything.
- **Don't rewrite the violent hunt system.** It's finished. The overhaul only adds exits, decoys, wash-the-wound, and the interrupt formula.
- **Keep the TAD cycle and Operation Phantom as written.**
- **Stealth should be felt.** The first time each stealth threshold matters, the prose shows Kelsie doing the professional thing.
- **Jaewon's scenes carry the emotional cost.** Write them with the same care as the romance scenes, and branch on what she knows (`jaewonKnowsYoureVampire`) and whether they're girlfriends (`officialGirlfriends`). **The user's direction for Jaewon:** she cares about Kelsie no matter what and always stands beside her, whether or not they're girlfriends. She never judges. She makes it clear she's with Kelsie.

### Department lore (for prose and the Step 16 guide)

**Chief Hugo Lars.**
- Late fifties. A former army colonel who commanded a combined-arms brigade.
- Twelve years ago, Maxine Renalds won her first mayoral race on public safety and recruited him personally. The two rebuilt TPD from the budget line up.
- Tall and gray at the temples. He moves like the joints hurt, and he's decided that's nobody's business.
- He talks in short, complete sentences and never raises his voice. Where Cruz is amused, Lars is patient.
- He went looking for the best investigator alive, found Cruz in federal service, and made her an offer nobody else could match.

**The Academy.**
- Eighteen months, the longest police academy in the country.
- Curriculum: criminal law, forensic science, crisis negotiation, tactical medicine, and combat, plus a physical program built by Lars's old instructors.
- Psych evaluations at entry, at graduation, and every six months for the rest of an officer's career.
- About 80% wash out. Those who graduate are disciplined past what most soldiers reach, trained to protect and serve as efficiently as a human being can.

**Professional Standards.**
- Integrity testing is constant and unannounced: sting offers of cash, favors, and seduction.
- Anyone who bites is fired and prosecuted, publicly, and nobody bites anymore.
- Every officer is trained to document any attempt in the report.

**Accountability.**
- Bodycams run for the full shift. An officer can switch one off (Reyes does, under compulsion, in the hospital blood theft scene), but the switch-off logs itself and pings a supervisor.
- Officers report every contact.

**TPD is the military.**
- Armored Response, Air Support, and SWAT exist so the city doesn't need one.
- Raptor One (the helicopter) flies armed.
- The Bear is a tracked armored vehicle that can take a rifle round and keep rolling.

### Named NPCs

| Name | Role | Voice |
|---|---|---|
| Hugo Lars | Chief of Police | Clipped, patient, never raises his voice. Finished sentences. |
| Gabriella Cruz | Detective Bureau + SWAT | Calm, dry, faintly amused, bone-certain. Never hurries. Details below. |
| Alan Driscoll | Captain, public information officer (hero path) | Press-conference cadence. Careful. |
| Maxine Renalds | Mayor (defined on the hero path) | |
| Officer Reyes | Patrol, Trigrave General substation | Steady hands, radio discipline. |
| Officer Pruitt | Property desk, Central Booking | Remembers everyone. Dry. |
| Det. Sgt. Nadia Osei | Homicide lead under Cruz | Methodical. Warm with families, cold with suspects. |
| Det. Lena Varga | Detective Bureau liaison to the RTCC (Real-Time Crime Center) | Fast talker who lives in the camera web. |
| Det. Andre Tolliver | Property & Robbery (burglary, mugging) | Patient. Owns a lot of cardigans. Offers a thermos of tea she can't drink. |
| Officer Dale Brenner + Juno | K9 Unit (Juno's a Belgian Malinois) | Brenner talks to Juno more than to people. |
| Sgt. Ruth Calloway | Patrol sergeant, field interviews | Twenty-two years on the street. Polite, immovable. |

**Cruz in detail:**
- 28, warm brown skin, a dark braid over one shoulder, a blazer over a white tee, a shoulder holster.
- Her career: Army, then CIA, then two TPD divisions.
- She runs the Detective Bureau and SWAT, handles narcotics operations, and does her own forensics when the lab's too slow.
- She built the TAD (Targeted Acoustic Disruptor).
- Signature line: "Whoever this is, they've been careful. Good for them. I'm better."

Keep the new names sparse:
- Osei and Varga appear mostly through Cruz's dialogue and news items.
- Brenner and Juno appear in K9 scenes.
- Calloway is the face of field interviews.

### TPD prose register

Every officer reads the same way: calm, procedural, courteous, and completely unmovable.
- They say "ma'am" and narrate what they're doing ("I'm going to ask you to keep your hands where I can see them").
- Exposure, flirting, cash, and threats don't fluster them.
- Their only visible reaction to something supernatural is a pause, a radio call, and a note in the report.

Reference sample:

> A motor officer rolls to the curb ahead of you and kills the engine. She pulls off one glove, then the other, and sets them on the tank.
>
> "Afternoon, ma'am. Sergeant Calloway, TPD." She doesn't step off the bike yet. "You mind if I ask you a couple of questions?"
>
> Her voice is friendly. Her right hand's resting on her thigh, a few inches from her holster, and it stays there.

---

## 3. Writing Rules (Mandatory; Any Deviation Is an Error)

These come from the user's style guide and their personal preferences. They apply to every line of prose, and to code comments where noted.

1. **2nd person, present tense.** Always.
2. **Em dashes** are allowed only in two cases:
   - paired parenthetical asides (`His voice—low and careful—cut through.`);
   - dialogue cut-offs (`"I can't just—"`).

   Nothing else, and that includes code comments. Use commas, periods, semicolons, or colons instead.
3. **Contractions always,** in prose and dialogue alike: "she's," "you're," "it's," "doesn't," "I'm." Never "She is" / "You are." (Code logic is exempt.)
4. **Banned outright:**
   - "the kind of" / "the kind that";
   - "A beat." / "A pause." (and "a beat longer");
   - "Not a question.";
   - "genuinely" modifying a state (allowed sparingly in dialogue);
   - "Something X. Something Y." pairs;
   - AI vocabulary: delve, tapestry, testament, multifaceted, nuanced.
5. **"The way" and "In a way" are banned in all forms.** The user is strict, so also avoid "all the way," "out of the way," "on the way out," "the same way," and "the way X does Y." That applies in comments too.
6. **No Not/Not, No/No, or Nothing/Nothing pairs** ("Not the hardest hitter. Not the tallest blocker."). A single "Not" is fine, as are panic-thought italics.
7. **No negation-then-reveal** ("Not because X. Because Y." / "It wasn't anger. It was exhaustion." / "She didn't stop because of fear. She stopped because..."). Just say what it is.
8. **No stanza formula** ("You're not the star. / You're the girl coaches trust / to be in the right place.").
9. **No staccato.** Chains of one- and two-word sentences ("Heat. Breath. Racing pulse." / "She looks at you. Doesn't stand.") are an error. Keep paragraphs short and sentences full; let sentences run with commas and detail.
10. **Two-adjective pairs** ("Quiet. Deliberate.") are allowed once per passage at most.
11. **Paragraph density:** 2–3 sentences, roughly 300 visible characters maximum, split with `\n\n`. The game is read on phones.
12. **Erotic scenes use crude, anatomical prose:** nipples, pussy, clit, cunt. Never euphemisms.
13. **Orgasm is "cum"/"cums"/"cumming."** Past tense stays "came."
14. **Dialogue sounds spoken,** never like speeches. Prose reads terse, modern, and casual, never literary or AI-polished.

### Technical rules

- All prose lives in **single-quoted JS strings**, so every apostrophe must be `\'`. Watch for `\\'` double-escapes, which crash the game.
- **Never use sed with `\x27`.** Edit with Python or the Edit tool.
- **Check after every batch:** run `node --check` on both script blocks. Find the lines with `grep -n '<script>\|</script>' Vampire_Girl.html`. As of `af8e705`, block 1 is lines 5934–5998 and block 2 is lines 13032–234531 (the inside of the second `<script>`). The lines shift as the file grows.
- **Sweep your own diff for banned patterns before committing:** em dashes, "the way", "kind of", "A beat", "genuinely", "Not a question", formal "She is"/"You are", and staccato.

---

## 4. Workflow That's Worked

- **How to edit:** write a Python script with an exact-match `rep(old, new, count=1)` helper that asserts the match count, and apply it to the file. Write prose as plain text and run it through a `js()` escaper when generating strings.
- **Verify:**
  - `node --check` on both blocks.
  - Harness tests: extract real functions from the file by brace matching, stub `gameState` and the helpers, and run them in node.
  - Harnesses need a varying `Math.random` or a patched `Date.now`, because otherwise ledger IDs collide.
- **Gotchas:**
  - `getCharismaLevel()` returns a label. Use `gameState.charisma` for numbers.
  - `policeEncounterToday` only resets under a warrant. Use `lastPoliceEncounterDay` for "police already met her today."
  - Scene lookup regexes should require `: {\n`, because scene names also appear in the coordinates table.
- **Commit:** after each step, commit with a descriptive message ending in:
  ```
  Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
  Claude-Session: https://claude.ai/code/session_015C8MSQdsxhf9L89GFLSHxr
  ```
  (Use the new session's own link in the new chat.) Push with `git push -u origin claude/trigrave-pd-overhaul-3f1jh4`, send the file to the user, and report briefly: what was built, any calls made, style fixes found, and anything deferred. Then wait.
- **Don't open a PR** unless the user asks.
- **Content boundary:** explicit ravage passages with compelled, non-consenting victims weren't rewritten in the hunt staccato pass. Only their non-sexual framing lines were edited, and the user was told.

---

## 5. NEXT TASK: The Jaewon Staccato Pass

The user asked: "Please give that Jaewon scene its own pass."

**Scope:** six scenes in `Vampire_Girl.html`. Line numbers are as of `af8e705` and will shift; find each scene with `grep -n "^            <scene>: {"`.

| Scene | Approx. line | What it is |
|---|---|---|
| `jaewon_arrest_reaction` | 142244 | Kelsie comes home after her mugshot hits the news. Jaewon's on the couch with red eyes. Branches on `gf` (girlfriends). |
| `jaewon_fugitive_morning` | 142674 | A morning coffee ritual. She asks about the assault charges. Choices set `motive`: justified / honest / erosion / demonic (karma ≤ −1000) / deflected. |
| `jaewon_fugitive_morning_response` | 142777 | Jaewon's response to each motive. |
| `jaewon_fugitive_evening` | 142885 | Dinner at the counter: "If you could undo all of it... would you?" Choices: Yes / Some of it / No (sets `jaewonKnowsRegret` / `jaewonKnowsComplexity`). |
| `jaewon_fugitive_evening_response` | 142935 | Her response to each answer. |
| `jaewon_fugitive_comfort` | 143006 | 3 AM on the couch. Escalates by `comfortCount` (0 / 1 / 2+), branches on `gf`. |

**The job:**
- Rewrite the staccato fragment chains into full, flowing sentences while keeping every beat, image, and line of dialogue's meaning.
- Fix any other banned pattern while you're in there (see the list below).
- Don't touch the logic, choices, flags, or branch structure. Step 10 already fixed 7 negation-then-reveal and stacked-negative lines in these scenes.

**Jaewon's voice (keep it):** steady, present, and patient, with no judgment. She has a coffee ritual; she pours Kelsie a cup without asking. She keeps her phone face-down at meals as a small refusal of the world. She always leaves room on the couch. Signature lines to keep verbatim or near it:
- "I don't care."
- "I don't care who comes to that door."
- "I'm not going anywhere."
- "Whatever this is, we'll figure it out."
- "Eat your rice before it gets cold."
- "I sleep better when you're there."

**Known problem lines to fix (not exhaustive, so read every line):**

`jaewon_arrest_reaction`:
- Staccato: "She looks at you. Doesn't stand."; "Controlled. Held in place by effort you can hear in the consonants."; "Your mugshot is on the screen. Shared across three different platforms."; "Face-down again. Hiding it from herself."
- Staccato (gf): "She stands up. Crosses the room to you. Takes your face in both hands."; "Simply. Without decoration."; "Together. Like we figure everything out."; "Arms around you. Tight. Her heartbeat against your chest."
- Staccato (non-gf): "Simply. Flatly."; "Holds on."
- "as she always does" is fine, but check that "the way" doesn't appear anywhere.
- Formal "is": "The apartment is quiet", "Jaewon is on the couch", "The TV is off", "the screen is still warm". Narrative "is" before an adjective is acceptable, but contract where natural ("Jaewon's on the couch").

`jaewon_fugitive_morning`:
- Staccato: "Then you remember." (fine alone) and "She sets the mug on the counter across from her. Sits down."
- "The assumption of coffee. The assumption that you'll be here." is a paired pattern; rework it.
- Run-on/density: the long gf paragraph ("For three seconds before your brain finishes booting up...") and the sleep-shirt paragraph exceed 300 characters. Split them.
- "The kitchen faucet drips. Once. Twice."
- "She waits. Patient. How she always waits."

`jaewon_fugitive_morning_response`:
- Staccato: "Picks it up again."; "Testing the shape of it. Turning it over like a stone..."; "She nods. Slow."
- Staccato: "Understanding landing in a place that was waiting for it. A shelf she cleared..."
- The demonic branch: "She stands. Walks to the window. Stands there with her back to you. Her hands on the sill. Her shoulders rise once. Fall."
- The deflected branch: "Jaewon nods. Once."; "Light. Easy."
- The closing line "...apartments hold people who've just said something that can't be unsaid: carefully. With the specific quiet..."

`jaewon_fugitive_evening`:
- Staccato: "Dinner. She cooked."; "Shoulder to shoulder."; "A small ritual of refusal. The world can wait. Dinner can't."; "Fork paused. Not looking at you."; "Turns on her stool to face you."
- The Yes branch: "Holds it there."; "Soft. Certain."
- The Some branch: "Processing."; "Slow. Tasting each word."; "Eats a bite. Chews. Swallows."; "The look of a woman who's seen the weight..."
- The No branch: "She takes a breath. Lets it out."; "Before the warrant. Before any of this." (a paired pattern); "Picks up her fork."
- Negation-then-reveal to rewrite: "She's not asking to trap you. She's not asking to judge. She's trying to find you." This is a Not/Not plus reveal and it's banned.

`jaewon_fugitive_comfort`:
- Staccato: "Careful. Slow. Lifting her arm..."; "She murmured something. Shifted. Pulled the pillow closer. Didn't wake."; "Hair tangled. Eyes half-closed."; "Slow. Trusting."; "Slow. Careful."; "Content. Warm."; "Steady. Slow."; "Eyes closed. Already drifting. Already somewhere between..."
- "How you learn a language..." (a fragment).
- Closing: "The warrant doesn't go away. Cruz doesn't stop building her case. The city doesn't forget your face." This is a triple negative stack; rework it. "But this is warm. And it's 3 AM. And someone chose to be here." is staccato; rework it.

**Deliver:** run `node --check`, sweep for style, then commit ("Rewrite staccato prose in Jaewon's fugitive scenes" plus the attribution), push, send the file, and report. Then wait for the go-ahead on Step 11.

---

## 6. What Steps 1–10 Built (Real Names in the File)

Commits on the branch: Step 1 `5eaae99`, Step 2 `40a36cf`, Step 3 `ab184f1`, Step 4 `d28e29d`, Step 5 `a03e03c`, Step 6 `0337b72`, hunt staccato pass `b019c1b`, Step 7 `2e76708`, Step 8 `5ed7165`, Step 9 `6d9ec27`, Step 10 `af8e705`.

The TPD engine lives in one block, roughly lines 33600–37100 (as of `af8e705`). Scenes are further down.

### Step 1: The department
- `var TPD = { chief, mayor, pio, hq, units{...}, people{...} }` with a canon comment block. Units:
  - patrol, motors;
  - k9 (Brenner/Juno);
  - air (Raptor One, Kestrel drones);
  - tactical (SWAT, Cruz);
  - armored (the Bear);
  - narcotics, plainclothes (Special Investigations Section), csu;
  - rtcc (Varga);
  - detective (Cruz), standards (Professional Standards Bureau).
- HQ is TPD Headquarters, Fourth Street; Central Booking shares the block. Stray chief lines in existing prose were fixed.

### Step 2: Presence & response
- **Baselines:** `TPD_BASE_PRESENCE` = downtown 70, medical 55, commercial 50, fitness 40, residential 35, wendale 35, park 30, dockyard 22, slums 15.
- **`getTPDPresence(district)`** adds:
  - tier +[0, 5, 10, 20, 35];
  - district heat 80+ → +20, 50+ → +10;
  - open serious cases, +5 each (cap +15);
  - `cruzForecastDistrict`/`cruzForecastDay` +15 (already read, but nothing sets them yet; Step 12 does);
  - warrant +10.

  The result clamps to 5–100.
- **Helpers:**
  - `getTPDPresenceBand` (low <25, moderate 25–49, high 50–74, heavy 75+).
  - `TPD_CRIME_NOISE` and `getTPDResponseChance(district, crimeType)` = presence × noise × (1 − stealthBonus), clamped to 0.02–0.95.
  - `getTPDClockDistrict`/`getTPDClockLimit`.
- **Ambient lines:** `TPD_AMBIENT_LINES` plus `getTPDAmbientLine(district)`, used in all 9 street scenes.
- **Cameras:** `DISTRICT_CAMERA_DENSITY` gained dockyard 0.25 and wendale 0.30.
- **Rewired:**
  - `getMuggingPoliceChance` now delegates.
  - The `ambush_strike` interrupt runs after the detection roll: clean = 0, partial/full use the response chance.
  - Exhibition clocks get the district passed in.
  - Drug patrol is × presence/20.
- **Deferred to Step 13 by the user:** Kestrel drone lines currently appear in any district's heavy band (75+). The doc's 13D table says Tier 0 has drones Downtown only, and Tier 2 has drones in every district with presence 30+. Settle this in Step 13.

### Step 3: Investigation engine
- **Tables:** `TPD_UNIT_MULT` (patrol 0.5, property 0.8, detective 1.0, homicide 1.4, cruz 1.8), `TPD_COLD_DAYS` (5 / 10 / 14 / 30 / never), `TPD_LEAD_CATALOG`.
- **Case-file functions:**
  - `assignInvestigatingUnit(entry)`, `addCaseLead(entry, type, opts)` (opts support strength, pointsAtKelsie, residence), `seedCaseLeads`.
  - `markCaseIdentified`, `reopenCase`, `countOpenCasesInDistrict(district, 'serious')`.
  - `caseHasReleasableStill`, `districtHasActiveSeries(district, crimeType)`, `caseHasLead(e, type)`.
- **Daily tick and series:**
  - `linkSignatureSeries()` handles bite/mask/MO series.
  - `advanceInvestigations()` runs in `onNewDay` after `processPendingForensics`. It handles tip lines, going cold, cold-case stealth XP, and the 30-day Cruz stall XP.
- **Identification beats:** `fireIdentificationBeat(entry)`, `tpdStreetBeats`/`tpdHomeBeats` queues, and Cruz ID mentions (`cruzIdMentions`).
- `createLedgerEntry` sets `entry.investigation`, `entry.moKey`, `k9Fresh`, and `exitPending`.
- The link methods `'investigation'` and `'dna'` were added.
- The Mothers' `unlinkMaskSignatureCrimes()` wipes the case file.
- **Lead strengths:**
  - witness_face 25, witness_face_cop 45, witness_desc 8;
  - victim_statement 20, canvass 10;
  - camera_clear 30, camera_partial 15, camera_blur 4, camera_trace 25;
  - trace_residence +25 (+10 and no name at the Lurasi);
  - k9_track 15, k9_residence +20 (+8 and no name at the Lurasi);
  - prints_unmatched 8, profile_zero 8, trace_evidence 6;
  - outfit_match 12, tip_line 20;
  - series_bonus +6 per linked case (cap +30);
  - air_track +10.

  The leads that name Kelsie (`pointsAtKelsie`): witness_face, witness_face_cop, victim_statement, camera_clear, trace/k9_residence (except at the Lurasi), outfit_match, tip_line.

### Step 4: Stealth spine
- `getCoverUpMult()`: 1.0 / 0.85 at 15+ / 0.70 at 30+ / 0.50 at 50+ / 0.35 at 75+ / 0.25 at 100, times the crime-stealth clothing.
- `getTPDCasingBlock(district, stealth)`, with helpers `tpdDronesOver` and `tpdNumberWord`, is used in hunt `buildCasingText`, mugging, pickpocket, and burglary casing. What it shows by stealth:
  - 10+: presence band;
  - 25+: patrol rhythm;
  - 30+: drone lanes;
  - 50+: Cruz forecast, once it exists.
- `noteStealthSweep`: at 10+, sweeping the scene halves trace. At 25+, cleanup removes trace.
- **Not built yet:** the stealth 20+ plainclothes tell and the 40+ decoy tell belong to Step 11.

### Step 5: Forensics / Profile Zero
- Prints are lifted at more sites (`fingerprintsLeft: !hasLeatherGloves()`). The hunt assault entry has `biteMarks: false` unless the feed was interrupted.
- `markHuntFeedInterrupted()` covers police interrupts and tension or companion aborts. It sets the cruz unit and an 80% profile_zero lead.
- Disposal recovery rolls: left 100%, hidden 90%, water 30%, vampspeed 100%.
- **Wash the Wound:** stealth 50+, +5 minutes, recovery ×0.6.
- `onProfileZeroProcessed` adds +5 supernatural evidence and `modifyCruzProfile(10)`.
- `modifyCruzProfile(amount, reason)` already exists and clamps 0–100. It **doesn't yet fire threshold beats**; Step 12 adds those.

### Step 6: RTCC & residence
- **Residence:**
  - `getDistrictReturnScene(district)`.
  - `getKelsieResidence()` returns an id (varhall/timeless/oracle/lurasi/street) plus label, scene, withJaewon, sanctuary, and homeless.
  - `recordRestLocationFromScene` in `sleepAdvanceTime` sets `lastRestLocation`.
  - `rollHotelDeskRecognition()` runs daily: Timeless 40%, Oracle 30%, halved at stealth 50+.
  - `TPD_HOME_SCENES` maps scene id to residence id.
  - `queueTPDHomeBeat`, `buildTraceNewsBeat`.
- **Exits:**
  - `rollCameraTrace(entry, exit)`.
  - The exitPending system, including `getExitPendingEntries` and `clearExitPending`. It expires after 240 minutes and seeds K9 then.
  - `TPD_NO_EXIT_TYPES` = fleeing, escape, indecent_exposure, bribery_attempt, pickpocketing.
  - `applyCrimeExit(exit, opts)`, `isCrimeExitDestination`.
  - **`tpdSceneIntercept(sceneId)`**, the showScene hook. When a pending crime exists and she navigates to a street scene, `city_exploration`, `hunting_options`, or `riverside_park`, it shows the `crime_exit_choice` scene. Walking straight into her residence scene counts as `walk_home`. The same hook serves warrant service and home beats.
  - The exits: walk_home ("Head back to [residence]" / "Walk off into the city" if homeless), go_to_ground, break_trail (30+), rooftops (35+, not in the Park), and vampspeed.
- **Street takedown:** `checkStreetTakedown(district)` plus the `tpd_street_takedown` scene.
- `tpdKnownResidence` is set at warrant issue (own Varhall lease) and at booking.
- The surveillance hub rework: `modifyCruzProfile(5, 'surveillance tampering')`.
- The Lurasi ambient line.
- The Park return bug was fixed; arrests used to return to a nonexistent `park_district_street`.

### Step 7: Homicide rule
- The murder entry absorbs the hunt assault entry's witnesses and camera. Scene camera footage is partial only when the kill was detected (blur for vampspeed). Clean kills get no scene footage.
- `seedK9Leads(entry, exit)` runs from `applyCrimeExit` and on pending expiry.
- The doc's scenario table was verified in a harness and matched.

### Step 8: POI & elimination sample
- `checkPersonOfInterest(entry)`: progress 50+ with a developed lead that names her. Sets `kelsiePOI`/`kelsiePOIDay` and calls `openCaseOnKelsie()`.
- `convertForensicMatches` and `acquireEliminationSample(source)`.
- `rollEliminationSample()` runs in onNewDay after `checkForNewCharges`: 15%, +15% at profile 70+, +5% on a diner job, halved at stealth 60+.
- `checkCruzSampleMatch` and the `cruz_sample_match` scene.
- The coffee offer in `cruz_approach` (`_cruzCoffeeOffer`): drinking gives the sample; refusing gives +5 profile.
- `arrest_booking` Station Two gets a cheek swab (`profileZeroMatched`).
- Flags: `eliminationSampleTaken`, `eliminationSampleSource`, `eliminationSampleDay`, `sampleMatchPending`, `sampleMatchDna`, `sampleMatchCount`, `sawSampleMatch`.
- **Not built yet:** the standalone `cruz_coffee` street scene is Step 12C.

### Step 9: Field interviews
- **Outfits:** `TPD_OUTFIT_SLOTS`, `snapshotOutfit`, `findOutfitMatchCase` (7 days, or 3 at stealth 20+), `clearRecentOutfitRisk` (from the hunt's "Change clothes").
- `bumpCaseProgress`, `getFieldInterviewCase`, `checkFieldInterview(district)`. The last uses `lastPoliceEncounterDay`: ×0.75 at stealth 15+, ×0.2 at 100.
- **`tpd_field_interview` (Calloway):**
  - cooperate (composure check);
  - voluntary interview (+10 case progress, +5 profile);
  - detained? (+5);
  - compel;
  - run (pursuit);
  - bribe (the `bribery_attempt` ledger type, +5 profile);
  - flirt (+5 profile).
- **Echoes:** `recordCompulsionEcho(district)` adds +3 supernatural evidence, and +5 profile once the pattern's been seen. It's hooked into:
  - arrest_compel;
  - doExhibPoliceCompel;
  - hospital blood theft;
  - drug raid compel;
  - `drug_deal_undercover` compel;
  - field interview compel.
- `checkCruzEchoPattern` plus the `cruz_echo_pattern` scene (+15 profile, one-time).
- **District travel order:** checkCruzEncounters → checkCruzSampleMatch → checkCruzEchoPattern → checkStreetTakedown → checkFieldInterview → random district events.

### Step 10: Pursuit & warrant service
- **Pursuit engine:**
  - `startPursuit(ctx)`, where ctx = `{ district, severity: 'minor'|'serious'|'violent', next, droneLocked, headStart }`.
  - `resolvePursuit(ctx)` (air × thermal by thirst × rain × stealth, with exit mods).
  - `rollK9Corner`, `getTodaysOpenCases`, `applyPursuitOutcome`, `getPursuitProse`.
  - `runPursuit(exit)`, and `resolveSilentPursuit(ctx)` for the naked escape.
- **Outcomes:** lost, tracked_district, tracked_home, tracked_lurasi, cornered.
- **The cold-thermal line** (the user asked for thirst instead of "two days"): "The thirst is a raw ache behind your ribs, and there isn't anything warm left in you to give away."
- **Warrant service:**
  - `queueWarrantService(resId, sameDay)` and `checkWarrantServiceMorning()` (onNewDay).
  - `getWarrantServiceState(sceneId)` via the intercept.
  - Flags: `warrantServicePending`, `warrantServiceDay`, `warrantServiceLocation`, `warrantServiceImmediate` (Tier 4), `warrantServiceCooldownUntil` (3 days).
- **Scenes:**
  - `tpd_pursuit`, `tpd_k9_corner`.
  - `tpd_warrant_service`, with branches:
    - Varhall own (Cruz with the TAD if she has a tactical capture on record);
    - Varhall with Jaewon. It's morning, so Jaewon's home. It branches on `officialGirlfriends` and `jaewonKnowsYoureVampire`, and Jaewon always stands by Kelsie;
    - Timeless;
    - Oracle.
  - `tpd_warrant_service_not_home`.
- **Rewired flees:** police_flee_vampire_speed, police_attack_nonlethal, police_kill_execution, mugging_flee_cops, arrest_flee (droneLocked for takedowns), and the field interview run.

---

## 7. Remaining Steps (Full Spec)

Build one step per output, then wait.

### Step 11: Undercover & Decoy Operations

TPD's Special Investigations Section puts plainclothes officers where crime's happening. The existing undercover mugging target and off-duty cop pickpocket targets are the seed; this step makes plainclothes work systemic.

**11A. `getUndercoverChance(district, crimeType)`** (new)
```javascript
function getUndercoverChance(district, crimeType) {
    var p = getTPDPresence(district) / 100;
    var c = 0.03 * p * 2;   // 2% in the Slums, 4% Downtown at baseline
    if (districtHasActiveSeries(district, crimeType)) c += 0.10;   // a detail's been assigned
    if ((gameState.flags.cruzProfile || 0) >= 50 && crimeType === 'violent_hunt') c += 0.06;
    return Math.min(0.30, c);
}
```
Apply it in four places. (The doc's comment says 2% Slums / 4% Downtown; the formula actually gives about 1% / 4.2% at baseline. Keep the formula and fix the comment.)
- **Mugging:** replace the flat check at about line 120434 (`const isUndercover = tier >= 2 && Math.random() < 0.10;`, which sets `gameState.flags.muggingTargetCop`).
- **Pickpocketing targets:** `generatePickpocketTarget(district)` at about 226700. The `PICKPOCKET_TARGETS` pools include `type: 'cop'` "Off-duty cop" entries, and `isCop` is set at about 226743. Blend in the undercover chance.
- **Drug buyers:** `checkUndercoverCop()` at about 15537 (base 5%, plus heat tiers) and the matching `badBuyerChance` at about 15048. Step 14 reuses this.
- **Violent hunt targets:** 11B.

**Also in Step 11: rework the pickpocket cop tell** (4B: "Stealth 20+ spots plainclothes"). Today the casing warning at about line 75076 (`if (target.isCop) { desc += 'Something about this one...' }`) shows at any stealth, and the cop's own pool description carries a tell too. Gate the explicit tell at stealth 20+ and give it the professional read. Below 20, keep the description neutral, so she's working blind. Apply the same gate to the mugging undercover hint.

**11B. Hunt decoys.** When a bite series is active (2+ Profile Zero cases) or Cruz's profile is 50+, violent hunt targets can be decoys. A decoy is an officer playing drunk, lost, or distracted in the district Cruz expects Kelsie to hunt next, with a cover team a block away.
- Roll `getUndercoverChance(district, 'violent_hunt')` in `generateViolentHuntTarget(district, timeOfDay)` (about line 225162; it stores `gameState.flags._huntTarget`). Mark `target.isDecoy`.
- Decoys are never set at the Lurasi.

Casing tells, added in `buildCasingText(target, env, stealth)` at about 225687:
- **Stealth 20+:** "He's drunk, sure. His feet aren't."
- **Stealth 40+:** "He's weaving, and every time he stumbles he ends up facing the alley. Nobody's that unlucky. He's bait."
- **Below 20:** no tell. She's hunting blind.

Use a female-target variant as needed; the target has `gender`.

**11C. The sting: `hunt_decoy_sting`** (new scene). If Kelsie strikes a decoy, the grab lands on a trained officer with a vest under his shirt and a panic button in his hand. The cover team arrives in about twenty seconds.
- **Flee:** always possible. Pursuit at `violent` severity (use `startPursuit` → the `tpd_pursuit` flow). Unless she's masked, the decoy's bodycam got her face at arm's length: a new `police_assault` ledger entry with a cop witness who saw her face (`witness_face_cop`, +45).
- **Compel the decoy:** it works, because he's a regular officer. It buys ten seconds and creates a compulsion echo (`recordCompulsionEcho`), and the cover team's still coming.
- **Feed anyway:** demonic karma only (karma ≤ −1000). Twenty seconds buys one bite and a few swallows. She gets his blood, and he's left with an open wound on a cop (a `profile_zero` lead). The team's on the corner when she lifts her head, and it goes straight to pursuit.
- **Surrender:** the arrest pipeline, `caught_in_act`.

Sample opener:
> Your hand closes on his collar and he moves before you do.
>
> He twists inside your grip, fast and trained, and something clicks in his fist. Somewhere down the block, a car door opens. Then three more.
>
> "TPD!" he shouts, right into your face. "Down! Get down!"
>
> The drunk's gone. He was never there.

**11D. Pickpocket details and narcotics buys.**
- **Pickpocket detail:** 3+ `pickpocketing` ledger entries in one district within 7 days assigns plainclothes there for 10 days (`gameState.flags.pickpocketDetails = { district: expiresDay }`). That district gets +10% undercover chance, and every lift there rolls a "plainclothes spotted you" check that turns into `street_pickpocket_cop_arrest` (about line 75810; `_flee` variant at about 75847).
- **Narcotics buys:** at drug heat Warm+, each sale rolls `getUndercoverChance(district, 'drug_deal')`.
  - An undercover buy ends as a **controlled purchase**: product and money go into evidence, and the buyer walks.
  - The case gets `witness_face_cop` and `camera_clear` (the buyer wore a wire camera). The arrest comes later, through the warrant, just like a real narcotics case.
  - Stealth 20+ gets a tell: "He knows the price. Nobody who needs it this bad knows the price."
  - The existing `drug_deal_undercover` scene (about 110430) plays today as an immediate bust with run/compel. Rework it or branch it into the controlled buy; the compel option already records an echo.
  - The bulk-deal undercover bust (about 110596, `ucChance`) should follow the same logic.

### Step 12: Detective Cruz & the Detective Bureau

**12A. Cruz's profile.** `gameState.flags.cruzProfile` runs 0–100. It measures how well Cruz understands Kelsie's habits. It never decays.

| Source | Gain | Status |
|---|---|---|
| Each Profile Zero sample processed | +10 | Done (Step 5) |
| Each Cruz-owned case opened | +3 | **To wire:** when `assignInvestigatingUnit` returns 'cruz' at creation |
| Failed compulsion on Cruz (`gabriellaCompulsionFailed`) | +15, one-time | **To wire** |
| Compulsion echo pattern | +15 one-time, then +5 per echo | Done (Step 9) |
| Surveillance hub tampering | +5 | Done (Step 6) |
| Bribery/seduction attempt on any officer | +5 | Done (field interview). Also apply to any new bribe/flirt option. |
| Refusing her coffee | +5 | Done (`cruz_approach`). Also in the new `cruz_coffee`. |
| Every Cruz street encounter | +2 | **Partial:** only `cruz_sample_match` has it. Add it to `rand_event_cruz_spotted`, `cruz_approach`, `rand_event_cruz_direct_question`, `rand_event_cruz_fugitive`, `cruz_echo_pattern`, and the new Cruz scenes. |
| Mothers erasure (12E) | +10 | **To wire** |

Thresholds (`modifyCruzProfile` should detect a crossing for any one-time beats):

| Profile | What she does |
|---|---|
| 20+ | Reads Kelsie's time-of-day habits. District presence during Kelsie's most common crime hour gets +5; derive the hour from ledger `timeOfDay`. |
| 30+ | **Forecast.** Each morning, Cruz predicts the district Kelsie's most likely to act in next, using `getRotationForecast()`. She sets `cruzForecastDistrict` and `cruzForecastDay` for +15 presence there all day. Presence and the 50+ casing line already read these. |
| 50+ | **Decoys.** Hunt decoys enabled (11B), and +6% undercover chance on hunts. |
| 70+ | **Patience.** Elimination sample chance +15% (already in `rollEliminationSample`). She starts showing up where Kelsie lives and works (12C). |
| 90+ | **Waiting for you.** Any violent hunt casing in the forecast district has a 10% chance that Cruz is already there: `cruz_scene_arrival`. The hunt ends. The conversation doesn't. |

**`getRotationForecast()`** reads `_lastViolentHuntDistrict`, `_huntDistrictStreak`, and the last ten ledger entries' districts. It picks the district Kelsie's used least recently among the ones she uses at all. Players who rotate predictably get predicted. Players who break pattern, or read the forecast at stealth 50+, stay ahead of her.

**12B. The Detective Bureau as a presence.**
- **Osei** runs Homicide. The body-found news item names her: "Detective Sergeant Nadia Osei, lead investigator."
- **Varga** runs the camera side. Cruz quotes her: "Varga's got you on nine cameras between the alley and the bus stop. She's working on ten."
- **Tolliver** handles burglaries and muggings. Voluntary interviews on property cases are with him: patient and courteous, with a thermos of tea he offers and she can't drink.

**12C. New Cruz scenes.** All are in Cruz's voice: calm, precise, faintly amused, never cruel for sport, never in a hurry.
- **`cruz_coffee`** (POI, no sample yet, district travel, 25%): Cruz is outside a coffee shop with two cups and hands Kelsie one.
  - Accept and drink: the sample's taken (`acquireEliminationSample('coffee')`).
  - Accept and carry it off: Kelsie can ditch it. At stealth 30+ she notices why she was handed it.
  - Refuse: +5 profile.
  - Add it to the district travel checks.
- **`cruz_diner_visit`** (POI, Kelsie working at the diner, profile 70+, one-time): Cruz takes a booth in Kelsie's section, orders pie, tips 30%, and asks Ruby three questions on her way out, which Ruby tells Kelsie about later. If Ruby's friendship/romance milestone is met, Ruby's loyal in that retelling: "I told her you're the best server I've got and she should mind her business."
- **`cruz_jaewon_interview`** (one-time). Trigger: POI plus living with Jaewon plus profile 70+, or after a warrant service at Varhall with Jaewon.
  - Kelsie comes home to Jaewon at the kitchen table with Cruz's card in front of her.
  - Jaewon's reaction depends on `jaewonKnowsYoureVampire` and the relationship level: frightened and loyal, frightened and angry, or quietly furious at Cruz.
  - This is the scene where the police system reaches into the part of the game the player cares about most. Write it with romance-scene care.
  - Per the user, Jaewon always stands beside Kelsie, so even "angry" stays loyal.
  - Cruz stays lawful in Jaewon's retelling. She asks questions and leaves a card, with no threats.
  - It also sets `tpdKnownResidence = 'varhall'`.
- **`cruz_scene_arrival`** (profile 90+, forecast district, hunt casing, 10%): Cruz is sitting on the bus bench across the street. She doesn't draw. She tells Kelsie the target's name, and that his daughter's picking him up in ten minutes. The hunt's over for the night. Never at the Lurasi.

Sample (`cruz_coffee`):
> Cruz is leaning on the rail outside the coffee shop on Ninth with a cup in each hand. When you get close enough, she holds one out.
>
> "Oat milk," she says. "You look like an oat milk person."
>
> Her face is friendly. Her eyes are on your hand, waiting to see whether it takes the cup.

**12D. Cruz stays lawful.** She never plants evidence, never threatens Jaewon, and never breaks procedure. If a line has her bending a rule, rewrite it.

**12E. The Mothers, and the file Cruz keeps by hand.** `cruz_paper_file` fires once, the first time a Mothers clearing completes while Cruz is assigned. The mask clearing finishes in the onNewDay block at about line 50040 (`rufusMaskClearing`), and the warrant clearing uses `rufusWarrantClearing`; check which ones route through the Mothers.

> Cruz finds you outside the pharmacy. She's got a manila folder under one arm, thick with paper, held shut with two rubber bands.
>
> "Strangest week of my career," she says. "Osei doesn't remember your case. Varga doesn't. The database says there never was one." She taps the folder. "I've been printing everything since the first time you tried to get in my head. I write it all down twice now."
>
> She tucks the folder back under her arm.
>
> "Whoever you've got cleaning up after you, they're very good. I'm better."

Afterward:
- +10 profile.
- Every future Mothers clearing leaves Cruz's own cases (unit 'cruz') at 50% progress instead of zero, because she rebuilds from her notes. Today `unlinkMaskSignatureCrimes()` wipes the case file; adjust it.
- The Mothers still clear the warrant and everyone else's memory, so the service keeps its value. It just can't make her forget.
- Flag: `cruzPaperFileSeen`.

### Step 13: Chief Lars, Mayor Renalds, and a City That Shows Its Police

**13A. Lars on the criminal path.** He appears mostly through news.
- **`tpd_lars_briefing`** (one-time): queue it in `checkForSuspicionEvent()` (about line 34773) after the first signature series links 3+ cases or Tier 3, whichever comes first. Kelsie sees the press conference on a TV in a shop window, a bar, or at home (use `getKelsieResidence()`).
- **`tpd_lars_manhunt_address`** (one-time, Tier 4): Lars announces the manhunt. Raptor One flies nightly, the Bear's staged at district borders, and there are checkpoints on the bridges.

Sample (`tpd_lars_briefing`). Pull the numbers from the ledger: attacks in the last 30 days, bodies discovered.
> The chief doesn't use the podium. He stands beside it with his hands folded in front of him, and the room goes quiet without anyone asking it to.
>
> "Four people have been attacked in this city since the first of the month," Hugo Lars says. "One of them died. I want to speak to the person responsible."
>
> He looks straight into the camera, dead center, the same way Cruz does.
>
> "My department's closed eleven hundred cases this year. Yours is going to be one of them. When that happens, I'd prefer you were alive for it."

(The sample has "the same way Cruz does." That violates the "the way" rule; reword it, e.g. "dead center, just like Cruz.")

**13B. Lars on the hero path.**
- At `hero_mayor_press_conference` (about 116412) and `hero_mayor_first_meeting` (about 116461), Lars stands behind Renalds, silent, his uniform pressed. One line of description: he doesn't clap.
- **`hero_lars_rules`** (one-time, the first visit to the Mayor's Office after legalization; the `mayors_office` scene is at about 60678):
  - Lars is waiting in Renalds's office with a one-page document of rules of engagement: hand every suspect to TPD, give a statement on scene, never touch evidence, never enter a building TPD has secured.
  - He's courteous. He built a department where nobody breaks rules, and now the mayor's handed him someone who can fly.
  > "The mayor made you legal," Lars says. "That was her call, and she's usually right."
  >
  > He slides the page across the desk.
  >
  > "Now you're working in my city. So you'll work the way my people work."

  ("the way my people work" is dialogue from the doc, but the user's rule is strict. Reword it, e.g. "So you'll work like my people work.")
  - Afterward, suited crime-fighting that ends with Kelsie handing the suspect over grants +2 extra hero reputation ("TPD commends cooperation"). Leaving before officers arrive grants none. Flag: `larsRulesSeen`.

**13C. Renalds on the criminal path.** She appears once in the news at Tier 4, standing beside Lars: "This department has my full support and every resource this city can give it." That's all she says.

**13D. Wanted tiers made visible.** The existing tier blocks stay. Add what the city looks like:

| Tier | World changes |
|---|---|
| 0 | Baseline presence. Kestrel drones Downtown only. |
| 1 | Presence +5. Casing blocks mention "a patrol car that's been past twice." |
| 2 | Presence +10. Plainclothes in Downtown and Commercial (undercover +3%). Drones in every district with presence 30+. |
| 3 | Presence +20. Checkpoints on the bridges and the Dockyard gate: vampire-speed travel across them rolls bodycam risk. Raptor One flies every night from 10 PM. |
| 4 | Presence +35. The Bear staged at district borders. SWAT on standby (warrant service happens the same day when she's tracked home; already built). Motor units at every major intersection. Hotel desks call in on sight (desk recognition ×1.5). Lars and Renalds on every channel. |

Presence bonuses by tier are already in `getTPDPresence`. Also settle the deferred drone question here: tie the ambient drone lines and `tpdDronesOver` to this table.

**13E. The TPD blotter.** `getTPDBlotterLine()` returns one line from a pool of 20+. Use it in newsstand scenes, background TVs, Jaewon's phone during apartment scenes, and radio chatter during police encounters. Samples:
- "TPD arrests two men 38 minutes after a jewelry store smash-and-grab on Fifth. Both identified through store cameras and a K9 track."
- "Commercial District burglary suspect arrested at home the next morning. Police say he left a single print on a window latch."
- "Three arrested in Residential car-theft ring. Detective Andre Tolliver credits residents' doorbell cameras."
- "Man who fled a traffic stop on foot located by drone in under four minutes."
- "Armed robbery suspect surrenders to SWAT after a two-hour standoff in Wendale. No injuries."
- "Pickpocketing crew dismantled after a week-long plainclothes operation Downtown."
- Occasionally, the underworld limit: "Police seize a Boruski weapons shipment at the Dockyard. No arrests above the level of the drivers."

The player should know from background noise alone that ordinary criminals in Trigrave get caught fast.

### Step 14: Crime-by-Crime Integration

Most of these are small once Steps 2–11 exist. Audit each one; several are already partly done.

- **14A. Mugging.**
  - Police arrival is done.
  - The undercover target comes from Step 11.
  - A mugged victim who isn't compelled gives `victim_statement` (+20); check the mugging `createLedgerEntry` call sites.
  - Exit choices after every mugging outcome that doesn't end in custody are done via the intercept; verify.
- **14B. Burglary.**
  - **Monitored alarms:** 30% of Residential houses, 5% in the Slums. Casing reveals the panel at stealth 30+. At stealth 45+ she gets "Bypass the panel" (+5 minutes, +4 stealth XP).
  - A tripped alarm starts a response clock: rooms before TPD arrives = `max(1, round((1 − presence/100) × 5))`. Staying past the clock triggers a police arrival in the house, a new scene ("Burglary alarm response").
  - CSU lifts prints on every burglary; gloves prevent the lead.
  - Occupied houses get K9 if reported within 6 hours (`k9Fresh` is already set for occupied-house burglaries).
  - **Doorbell cameras:** an unmasked face on one is `camera_clear` (+30). Disabling the cam first stays the right call.
  - The burglary casing TPD block is at about line 121546.
- **14C. Pickpocketing.** Patrol-level case: ×0.5, cold in 5 days (done). Pickpocket details come from 11D. Off-duty cop targets stay unchanged.
- **14D. Blood theft.** Trigrave General has a TPD substation where Reyes works; Medical's baseline already reflects it. Compelling Reyes creates an echo (done). The camera trace applies to the exit from the hospital and the blood bank; verify the exit intercept covers them.
- **14E. Violent hunts.**
  - The interrupt is done.
  - Patrol tension events should be weighted by presence instead of suspicion.
  - Decoys come from 11B–C. Wash the wound is done.
  - Exit choices go before the summary card in `after_ambush_hunt` and the kill aftermath; verify the intercept covers them.
  - Clean, completed feeds on living victims leave nothing behind: no bite, no case. The best hunters never open a file.
- **14F. Drugs.**
  - Patrol encounters scale with presence (done). Narcotics controlled buys come from 11D.
  - Drug raids at Burning+ become SWAT operations, with the Bear when drug heat is Nuclear. This is a prose upgrade to `drug_raid_home` (about 110932).
  - Cruz's existing narcotics involvement at Hot stays as written.
- **14G. Exhibition.** Presence-scaled clocks (done), professional officers (15A), and exhibition bookings that record prints and a mugshot (15C).
- **14H. Vampire servants.**
  - Multiply the base capture chance by `getTPDPresence(district) / 40`. Capture rolls are at about lines 50679 and 211160.
  - The servant task force (`servant_task_force_formed`) is Cruz's Bureau working the pattern; its scene text should name Varga's camera work.
  - Rufus's clean release is a Mothers service (15D).
- **14I. Hero path.**
  - Crimes Kelsie finds on patrol show a TPD response estimate in the prose ("Sirens, maybe three minutes out"). If she hesitates past it, TPD resolves the crime without her: no karma, no reputation.
  - `hero_lars_rules` and the cooperation bonus come from 13B.
  - The vigilante police encounters get the professionalism pass (15B).
- **14J. Cartel war.** Operation Phantom stays as written. An optional prose upgrade: Raptor One overhead during `cruz_swat_ambush`, and the Bear at the warehouse door.
- **14K. Rufus & the Mothers.**
  - Existing prices, cooldowns, and effects stay. The only mechanical changes are Cruz's profile gains on Mothers clearings and her paper file (12E).
  - The Lurasi is sanctuary: no warrant service, raids, or plainclothes in the lot. Traces and tracks that end there never name her (done in the engine). That's the price of TPD's arrangement with the Slums, and it's why a hunted Kelsie might trade a Timeless suite for a Lurasi room.

### Step 15: Consistency & Professionalism Pass

Fix existing prose that contradicts the canon, each in its own scene, and keep everything that isn't the officer's conduct.

**15A. Exhibition police stops.**
- **Where:** `handleIndecencyPoliceArrive()`, `handleSheerPoliceArrive()`, `tickSheerLingeriePolice()`, the corruption-tiered stop texts nearby, `offerExhibPoliceChoices()` callers, and **`exhib_arrest_surrender`**.
- **The problem:** officers leer. One "doesn't even pretend to look at your face," another's "eyes go straight to your bare pussy," another's "jaw works" looking through mesh, and another has been "driving at walking speed for half a block, looking at" her.
- **The rule:** officers keep their eyes on her face, speak procedurally, and offer the emergency blanket every TPD cruiser carries. Kelsie's own corruption-tiered interiority (her arousal, shame, and thrill) stays untouched, in crude prose per the style guide. The heat lives in her head and in being seen by the street; the officers are the one thing in the scene that stays cool.
- **Example rewrite** (the Deeply Corrupted naked stop):
  - Before: "The officer gets out, and his eyes go straight to your bare pussy."
  - After: "The officer gets out with a folded gray blanket already in his hand. He keeps his eyes on yours, the whole way over, like that's a skill he practiced. It probably is."
  - "the whole way over" violates the user's "the way" strictness; reword it, e.g. "all through his walk over."

**15B. Hero-path officers.**
- **Where:** `vigilante_police_explain` and its branches. The line at about 114884 reads `'"Get out of here, ' + name + '. Before my captain hears this."'`.
- **The rule:** TPD officers exercise lawful discretion on the record. The victim's statement goes on the bodycam, the officer documents the intervention, and he releases her pending a detective's follow-up.
  - After: "Victim statement's on my camera." He clicks his pen. "You're free to go, [name]. A detective'll want to talk to you. I'd take the call."
- Update the Tips & Guide "Moral challenge" line to: "The victim speaks for you on camera. Officers document it and release you on the spot."

**15C. Exhibition bookings skip identification.**
- **Where:** `exhib_arrest_booking` onEnter (about line 140254).
- **The fix:** set `fingerprintsInSystem = true` and `mugshotTaken = true`, then run `processPendingForensics()` and the Step 8 conversion (`convertForensicMatches` or equivalent). Don't touch facial recognition unless it's already 50+.
- Add a Station Two line: "Ten fingers, ten scans. The technician doesn't care what you came in wearing." Add a notification: "Your fingerprints are on file."
- **Balance:** this is the biggest consequence in the pass. An exhibitionist Kelsie who's ever been booked has her prints on file, and every gloveless crime converts. That's intended, and the Tips & Guide should say so plainly.

**15D. Rufus's service text.**
- **Where:** the Tips & Guide REDUCING SUSPICION and Vampire Servant Operations CAPTURE entries, plus every `rufus_service_*` scene. As of `af8e705` they're at about 151060–151332: `rufus_service_paperwork`, `_witnesses`, `_district`, `_digital`, `_crew_clean`, `_warrant`, `_warrant_paid`, `_mask_clearing`, `_mask_clearing_ask`, `_mask_clearing_paid`.
- **The problem:** lines like "Evidence goes missing, arresting officer gets reassigned" and "Misfiled evidence, broken chain of custody" read as TPD corruption.
- **The fix:** state the mechanism in each line.
  - **Servant clean release:** "Rufus calls in a favor with the Mothers. Nobody at the precinct remembers the arrest, and the paperwork follows them."
  - **Lose the Paperwork:** route it outside TPD (the contract lab courier, the court clerk's office, city records) or through the Mothers. Audit for any line implying a TPD employee was paid or turned.
  - **Kill the Warrant:** the DA's office reclassification stays, since the DA isn't TPD. Add one Cruz line at her next encounter noting the "administrative error," plus +5 profile.

**15E. Tips & Guide statements that are now wrong** (rewrite them in Step 16):
- "Police can interrupt a violent hunt once suspicion reaches 20..."
- "Nothing lands on you until something identifies you: your fingerprints or face matched after a booking..."
- "Fleeing always uses vampire speed." It still does, but pursuit now continues after it.
- "Undercover Cops: At Wanted Tier 2+, there is a 10% chance..." (about line 7984)
- "The moment the evidence ledger ties a crime to you (or a warrant goes out), Detective Gabriella Cruz..." POI now assigns her too.
- "Officers look the other way." (15B)

### Step 16: UI, Stats Modal & Tips & Guide

**16A. Stats modal: Trigrave PD subsection**, under the existing City Suspicion section (`sections.push({ label: 'City Suspicion', ... })` / the stat-section html at about line 40046). It shows:
- **TPD presence here:** the band label for the current district (Light / Steady / Heavy / Saturated), plus the number at stealth 40+.
- **Cases in the news:** counts of public cases by type (bodies found, assaults reported, burglaries). Public knowledge only; Kelsie can't see TPD's internal progress.
- **Person of Interest:** shown once Kelsie's had her first POI-era Cruz beat ("Cruz has your name").
- **Cruz's attention,** labeled from `cruzProfile`: Distant (0–19), Curious (20–49), Focused (50–69), Fixated (70–89), "She knows how you think" (90+).
- **Compulsion lapses on record:** shown once `cruz_echo_pattern` has fired.
- **Prints on file / Profile Zero matched:** yes/no, once true.

**16B. Reading case warmth** in-world:
- **Scene tape:** at stealth 40+, walking through a district with an open serious case adds a line: "The tape's still up on the alley off Ninth" (open) or "Somebody finally took the tape down" (cold).
- **Servant scouts at skill 75+** (the existing scout intel tier): the daily report names each open serious case as Cold / Cooling / Warm / Hot. They watch where detectives go.

**16C. Post-crime summary card.** The existing consequence cards (hunt summary, mugging, burglary) gain two lines:
- the exit taken;
- "Case opened: [unit]" in the unit's color: gray patrol, blue property, amber detective, red homicide, violet Cruz.

**16D. Tips & Guide.** Add a new top-level **Trigrave PD** section before Crime & Suspicion, with these entries:
- **THE DEPARTMENT:** Lars, Renalds, the Academy, Professional Standards, why bribery and seduction never work, and the two supernatural exceptions.
- **UNITS:** patrol, motors, K9, air, SWAT, armored, narcotics, plainclothes, CSU, RTCC, Detective Bureau.
- **PRESENCE:** district baselines, what raises them, the ambient lines.
- **HOW TPD WORKS A CASE:** units, leads, cold cases, series, identification.
- **PERSON OF INTEREST:** the sample, gloves, the time bomb.
- **PURSUIT:** air, thermal and thirst, rain, K9, exits.
- **WHERE YOU SLEEP:** Varhall, the Timeless, Oracle Inn, the Lurasi's sanctuary, the known-address loop, hotel desks, homeless street takedowns.
- **COVERING YOUR TRACKS:** the full stealth table (Section 8).

Rewrite these in Crime & Suspicion:
- BASICS (the case vs. the city's temperature);
- POLICE ENCOUNTERS;
- DETECTIVE CRUZ (profile, forecast, decoys, the paper file);
- STRATEGY TIPS;
- STEALTH BUILDS (The Ghost gains "never becomes a POI"; The Brute gains "the helicopter always finds you warm").

Also fix every 15E line, the Hero System's DETECTIVE CRUZ and VIGILANTISM IS ILLEGAL entries, and the Vampire Servant Operations CAPTURE entry.

The existing Crime & Suspicion sections: BASICS, THE STEALTH STAT, MUGGING, BURGLARY, PICKPOCKETING, BLOOD THEFT, CAMERA FOOTAGE & DIGITAL EVIDENCE, DISTRICT HEAT, POLICE ENCOUNTERS, DETECTIVE CRUZ, REDUCING SUSPICION, SUSPICION EVENTS, WARRANT & ARREST, STRATEGY TIPS, STEALTH BUILDS. (Guide and UI text are exempt from contractions per the style guide, but the rest of the rules apply.)

New strategy tips, in the guide's voice:
- "Hunt hungry if you plan to run. Raptor One sees a fed vampire from a mile up."
- "Never walk straight home from anything."
- "The Lurasi's the one roof TPD won't go under. Every other room in the city is a door they can knock on."
- "Switching hotels under a warrant buys time. The desk at the next one is already looking at your face."
- "If you've got no home, you've got no door to kick in, and every camera in the city is looking for you instead. Stay out of Downtown."
- "Gloves are cheap. A single print is a time bomb."
- "Rain covers a lot. Dogs can't track through it and drones don't fly in it."
- "If Cruz hands you a coffee, think about why."
- "Rotate districts unpredictably. Once Cruz can predict you, she'll be there first."

### Step 17: Save Migration & New gameState Fields

The file already has migration functions to follow as a pattern: `migrateLivieData`, `migrateHeroData`, `migrateWarrantData`, `migratePickpocketData`, `migrateBankData` (about line 13897+). Add something like `migrateTPDData()` alongside them, and add defaults to the initial `gameState.flags` (about line 13960 is inside it).

**17A. Fields.** Include everything the overhaul actually uses. Grep the TPD block for `F.` / `gameState.flags.` names. From the doc:
```javascript
// Department & presence
cruzForecastDistrict: '', cruzForecastDay: 0,
fieldInterviewToday: false, _fieldInterviewCase: null,
// POI & sample (Step 8)
kelsiePOI: false, kelsiePOIDay: 0, eliminationSampleTaken: false,
eliminationSampleSource: '',   // 'coffee' | 'discard' | 'diner'
profileZeroMatched: false, sawSampleMatch: false,
// Cruz (Step 12)
cruzProfile: 0, cruzEchoPatternSeen: false, cruzPaperFileSeen: false, cruzCoffeeRefused: 0,
// Compulsion echo (Step 9E)
compulsionEchoes: 0, compulsionEchoLog: [],   // { day, district }
// Lars & the city (Step 13)
larsBriefingSeen: false, larsManhuntSeen: false, larsRulesSeen: false,
// Residence (Step 6E-6F)
lastRestLocation: '', tpdKnownResidence: '', streetTakedownToday: false,
// Warrant service (Step 10E)
warrantServicePending: false, warrantServiceDay: 0, warrantServiceLocation: '',
// Details (Step 11D)
pickpocketDetails: {}          // district: expiresDay
// Per ledger entry: investigation{unit, progress, leads, status, lastLeadDay, poiRaised, tipRolled},
// outfit[], exit, seriesKeys[], moKey, k9Fresh, exitPending
```

Fields the build added beyond the doc (include them too):
- warrantServiceImmediate, warrantServiceCooldownUntil;
- tpdStreetBeats, tpdHomeBeats, cruzIdMentions;
- eliminationSampleDay, sampleMatchPending, sampleMatchDna, sampleMatchCount;
- profileZeroSamples, profileZeroLabSeen;
- k9CornersTotal, stealthSweepFelt, lastPoliceEncounterDay;
- _pursuit, _pursuitResult, _crimeExitDest, _warrantServiceReturn, _takedownDistrict, _cruzCoffeeOffer.

**17B. Save migration.**
- **Existing ledger entries** get an `investigation` object on load:
  - unit from `assignInvestigatingUnit()`, progress 0;
  - status `cold` if the entry's older than its unit's cold window, otherwise `open`;
  - leads seeded from the booleans already on the entry (witnesses, camera, forensic);
  - entries already `linkedToKelsie` get status `identified`.

  Set `exitPending: false`. No `exit` and no `outfit`, since K9 and outfit leads can't seed retroactively.
- **`cruzProfile`** initializes from history: +15 if `gabriellaCompulsionFailed`, +2 per `cruzFugitiveEncounters`, +3 per existing murder/assault entry with `biteMarks`, capped at 60.
- **`kelsiePOI`** initializes true if `gabriellaCruzAssigned` is already true.
- **If `fingerprintsInSystem` is already true:** `eliminationSampleTaken` stays false (she was booked, so the sample's moot). `profileZeroMatched` initializes true only if `mugshotTaken` (a full booking includes the cheek swab, which Step 8 already added to `arrest_booking`).
- **Exhibition-only bookings in existing saves** stay as they are. The 15C fix applies only to future bookings.
- **`lastRestLocation`** initializes from what she holds: Varhall first, then the Timeless, Oracle Inn, and the Lurasi, or '' if she holds nothing.
- **`tpdKnownResidence`** initializes to 'varhall' if a warrant's already active and `hasOwnApartment` is true (her lease is in her name). Everyone else starts unknown, so an existing fugitive in a hotel room or on the street gets found the new way.
- The `DISTRICT_CAMERA_DENSITY` additions need no save data.

---

## 8. Reference Tables (For Steps 11–17 and the Guide)

### Stealth thresholds (the "COVERING YOUR TRACKS" table)

| Stealth | Committing the crime | Covering it up | Reading TPD |
|---|---|---|---|
| 1–9 | Base detection. | Full-strength soft leads. Heads straight home by default. | Sees officers only when they're in front of her. |
| 10+ | Existing camera/lighting reads. | Sweep the scene (+3 min): trace evidence ×0.5. | Casing shows the district's TPD presence band. |
| 15+ | Careful mugging, lure. | Cover-up 0.85. | Spots marked cars first (field interview chance −25%). |
| 20+ | Pickpocket awareness, distraction lifts. | Outfit-match window drops from 7 days to 3. | Spots plainclothes: undercover targets get a subtle tell (**Step 11**). |
| 25+ | Kill-site cleanup. | Cleanup also zeroes trace_evidence. | Reads patrol rhythm. |
| 30+ | Escape routes in casing. | Cover-up 0.70. Break the trail exit. | Notices drone lanes. |
| 35+ | Rooftop drop. | Rooftop exit: no camera trace, no K9 track. | |
| 40+ | Exact detection percentages. | Camera trace ×0.5 on any exit. | Reads decoys: explicit tell (**Step 11**). |
| 50+ | | Cover-up 0.50. Wash the wound on kills. | Sees Cruz's forecast in casing (**Step 12** sets it). |
| 60+ | | Camera trace ×0.25. Elimination sample chance halved. | Knows the RTCC's blind spots city-wide. |
| 75+ | | Cover-up 0.35. | Pursuit escape bonus doubles. |
| 100 | | Cover-up 0.25. | Field interviews almost never fire (×0.2). |

### Stealth XP from cold cases (done)
- Petty case goes cold: +2.
- Property or detective case: +5.
- Homicide: +10.
- A Cruz case stalls 30 days: +15, one-time per case.

The notification reads: "The [district] case went cold. Nobody's knocking."

### Residence behavior (done in the engine; new scenes must respect it)

| Residence | Trace / K9 lead | Names her | How TPD learns it | Warrant service |
|---|---|---|---|---|
| Varhall, own unit | +25 / +20 | Yes | Trace, K9, or the warrant itself (lease) | Yes |
| Varhall, with Jaewon | +25 / +20 | Yes | Trace, K9, the Jaewon interview | Yes; Jaewon's there |
| Timeless Hotel | +25 / +20 | Yes | Trace, K9, desk recognition (40%/day under a warrant) | Yes; security hands SWAT a key card |
| Oracle Inn | +25 / +20 | Yes | Trace, K9, desk recognition (30%/day) | Yes |
| Lurasi Motel | +10 / +8 | No | Never | **Never** |
| Homeless | None | n/a | No address | Street takedown instead |

### New scenes still to build
`hunt_decoy_sting` (11), `cruz_coffee`, `cruz_diner_visit`, `cruz_jaewon_interview`, `cruz_scene_arrival`, `cruz_paper_file` (12), `tpd_lars_briefing`, `tpd_lars_manhunt_address`, `hero_lars_rules`, the Renalds Tier 4 news beat (13), and the burglary alarm response (14B).

### New functions still to build
`getUndercoverChance` (11A), `getRotationForecast` (12A), threshold beats in `modifyCruzProfile` (12A), `getTPDBlotterLine` (13E), and a TPD save migration (17).

---

## 9. Report Format the User Likes

The report should be short and plain:
- what the step built;
- any judgment calls, flagged as "One call for you";
- style fixes found in existing prose;
- anything deferred.

End with "Ready for Step N when you are." Send the updated `Vampire_Girl.html` with each step.
