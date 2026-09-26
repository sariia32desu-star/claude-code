# Crime & Suspicion System: Audit and Rework

## What the playtest found (before)

A headless harness ran 17 crime paths day by day through the real game functions (kills with each disposal method, pickpocketing, mugging masked and unmasked, burglary, blood bank runs, witnessed feeds, hunt-interrupt police encounters, booking, Rufus clearance).

- **Suspicion and the case against Kelsie were disconnected.** One downtown kill put suspicion at 68, assigned Cruz and blocked crime, with no warrant. A cop-killer sat at 100 suspicion with nothing in the evidence ledger and never got a warrant. Blood bank runs pushed suspicion to 88 with an empty ledger.
- **Cruz was assigned at suspicion 50** (the Tips & Guide was accurate to the code), whether or not anything tied a crime to Kelsie.
- **Hidden, dumped and speed-moved bodies could never be linked.** `addPendingBodyDiscovery` dropped the ledger id, so those murders never reached forensics.
- **Hunt-interrupt police encounters (flee, assault, kill) created no ledger entries**, and the warrant fallback checked different flag names than those scenes set.
- **Pattern linking pinned crimes on Kelsie with no identifying evidence.** Three clean pickpockets nobody saw counted as "linked to Kelsie."
- **Booking never re-checked old crimes.** Forensics ran once per crime, so a later mugshot or prints never matched earlier footage or prints.
- **A Rufus clearance was undone the next morning.** The warrant check re-issued it from the charges still on file.
- **The post-escape 3-day crime block was wiped** by the tier recalculation.
- **46 scenes wrote suspicion directly**, skipping traits, thresholds and tier updates.
- **Witnessed feeds, the core vampire crime, never went into the ledger.** Unmasked muggings added no facial recognition, despite the guide saying going maskless builds it.
- **`rand_event_cruz_spotted` was never triggered**, so the coffee-shop compulsion attempt, `cruz_approach`, `hero_cruz_walk` and `hero_cruz_probing` were unreachable.
- **The news report and guide event thresholds ran off legacy counters and suspicion numbers** that didn't match the code.

## The model now

**Heat follows the case.**

| State | Suspicion ceiling | Max tier |
|---|---|---|
| No warrant | 40 | 2 (never blocks crime or hunting) |
| Warrant, minor charges (theft, pickpocketing, burglary, exposure, fleeing) | 60 | 3 |
| Warrant, serious (mugging, aggravated assault, police assault, escape) | 80 | 4 |
| Warrant, violent (murder, police killing) | 100 | 4 |

A warrant severity of 60+ bumps the class up one step. When a warrant is cleared, suspicion drops back under 40. The numbers are constants at the top of the cap helpers (`SUSPICION_CAP_NO_WARRANT`, `SUSPICION_CAP_BY_WARRANT_CLASS`).

**How a case is built.**
1. Every crime goes into the evidence ledger.
2. Three crimes of the same type in one district become a series with an unknown suspect.
3. A crime is tied to Kelsie only by identifying evidence: prints or mugshot matched after a booking, a witness ID, being caught in the act, a mask match, or facial recognition reaching 70 (someone recognizes her from the sketch).
4. Once one crime in a series is tied to her, the whole series is.
5. Two linked charges, or one murder or police killing, and a warrant goes out.
6. Evidence doesn't expire. On her first booking, old prints and footage match.

**Cruz** is assigned when the case first opens on Kelsie (first linked crime, or any warrant). Her street sightings and questioning happen only while she's building the case, before a warrant exists.

**Pressure before a warrant** comes from hunt interrupts (police chance starts at suspicion 20, about 17% at 40) and facial recognition building toward identification.

## Playtest after the rework (end state of each path)

| Path | Result |
|---|---|
| Unmasked witnessed feeds | FR climbs, identified on day 8, warrant (serious), Cruz assigned |
| Pickpocket, detected and fleeing | FR reaches 70, whole series linked, warrant (minor) |
| Masked muggings, then booked | anonymous until booking, then all 5 linked through the mask |
| Slums killer with prints on file | warrant (violent), ceiling 100; Rufus clearance holds and suspicion drops to 40 |
| Masked feeds, hidden bodies, clean pickpockets | stay anonymous; heat tops out at 40 |

## Follow-up: surrender and cop-killer consistency

- **Hunt interrupts now offer surrender.** It's caught in the act: an aggravated assault linked on the spot, the mask comes off, and she goes to booking (mugshot, prints, mask cascade).
- **Killing police builds facial recognition from the bodycams** (+40 unmasked), plus supernatural evidence and bodycam incidents either way. Unmasked, she's identified the next day. Masked, she stays anonymous until booked with the mask.
- **The cop-killer aftermath is rewritten** to say what actually happens, with separate masked and unmasked versions.
- **The permanent blood-theft block is gone.** Assaulting or killing police used to set `bloodBagTheftBlocked` forever; the wanted tier decides now.
- **Held heat and warrant floor.** Heat above the ceiling is held back and released when a warrant raises the ceiling. An active warrant sets a floor (20 minor, 40 serious, 60 violent).

## Follow-up: blood theft by method

| Method | Result |
|---|---|
| Compulsion | Clean getaway: no suspicion, no ledger entry. |
| Vampire speed | Ledger entry with a blurred camera frame (can never identify her), supernatural evidence, heat. |
| Stealth, every check passed | Cameras evaded. +2 suspicion when the stock comes up short, no ledger entry. |
| Stealth, early slips, storage passed | Out with the bags; staff sighting or a blurred camera frame goes on the books. |
| Stealth failed at storage, or two slips in a row | **Caught on camera**: new scene per site (hospital: Trigrave PD Officer Reyes; clinic and blood bank: security guards). |

Caught choices: run at vampire speed, empty-handed (face on camera: FR +15, theft entry, plus fleeing at the hospital); compel them to wipe the recording and take the bags (clean, +15 thirst); knock them out and take the bags (theft plus assault on camera, assault on law enforcement at the hospital); surrender (theft tied to her on the spot, booking).

## Still open (design calls, not bugs)

- **Hero-only players never meet Cruz.** Her vigilante awareness requires her to be assigned to Kelsie's criminal case.
