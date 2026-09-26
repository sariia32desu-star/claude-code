# Detective Gabriella Cruz: Consistency Rewrite Plan

## Canon sheet (the voice every scene gets rewritten to)

Taken from the compulsion-fail scene (`cruz_fugitive_compulsion_fail`) and the TAD capture scenes (`cruz_tactical_encounter`, `cruz_interrogation_tactical`).

- **Who she is:** Detective Gabriella Cruz, 28. Runs Trigrave PD's Detective Bureau and SWAT. Ex-CIA special agent. Army special operations before that, two deployments before 22. Builds her own gear (the TAD, the filtered earpieces). Colombian, sister in Medellín.
- **Look:** dark braid over one shoulder, dark brown eyes with amber flecks, lean and athletic, blazer open over a white tee, badge on the belt, shoulder holster.
- **Attitude:** nonchalant arrogance. She believes she's better than everyone, Kelsie included, and says it like a weather report. She's never afraid of Kelsie, never flustered, never tearful, never kept up at night. Her pulse never moves.
- **Voice:** calm, level, clipped, a little amused. "Miss Summers" when she's needling. She asks questions she already knows the answers to. She never raises her voice or pleads.
- **Compulsion:** can't be compelled. When Kelsie tries, she feels something push at her and treats it as data. She doesn't know it's compulsion or what it does, and she doesn't care. She never asks how many people Kelsie made forget.
- **Time:** no stated time spans ("months", "weeks", "eleven years in Trigrave"). The game advances minute by minute, so scenes don't claim how long it's been.
- **Hero track:** she still protects Kelsie's hero identity, but as a calculated call made from above ("the suit's useful to my city"), with no sentiment or fear.
- **War arc:** she still lets the Phantom finish the Vajaros, but because she chooses to, from strength. No red eyes, no cracking voice, no "you scared me".

## Scene map and batches

| Batch | Scenes | Main problems found |
|---|---|---|
| 1. Introduction and sightings | guide entries, `cruz_introduction_event`, news quotes in `suspicion_news_report_event` / `servant_task_force_formed`, `rand_event_cruz_spotted`, `cruz_compulsion_attempt`, `cruz_approach` | "Mid-thirties" (she's 28), "Major Crimes" title, warm "pleased to see you" smile, soft compulsion reactions |
| 2. Street questioning and gang arc | `rand_event_cruz_direct_question`, `cruz_question_deflect`, `cruz_question_lie`, `getCruzSheerExposureLine`, `servant_cruz_gang_awareness`, `cruz_question_gang_aware`, `cruz_gang_deflect`, `cruz_gang_misdirect` | "I'm scared, Kelsie", "keeps me up", "eleven years", fooled too easily by lies |
| 3. Custody and interrogation | `arrest_cell_wait` (her arrival), `cruz_interrogation_tactical`, `cruz_interrogation`, `cruz_interrogation_speed`, `cruz_interrogation_compulsion`, `cruz_interrogation_why`, `cruz_interrogation_end`, echoes in `arrest_cell_post_interrogation` | the "There was a moment" beat (fear, "months", "how many people have you made forget"), "holding the word up to the light", warm/soft Cruz, "I don't think you're a bad person" |
| 4. Fugitive cycle | `rand_event_cruz_fugitive`, `cruz_fugitive_compulsion_fail`, `cruz_tactical_encounter`, `cruz_post_warrant_reflection` | "mapped your routes months ago", "eleven years", stated time spans, style violations inside the reference scenes |
| 5. Hero track | `hero_cruz_vigilante_aware`, `hero_cruz_suspects_identity`, `hero_cruz_admission`, `hero_cruz_walk`, `hero_cruz_probing`, Cruz bits in the hero ceremony and Maxine chats | sentimental Cruz, "warmth is the weapon", Maxine quoting a soft Cruz |
| 6. War arc | `cruz_confrontation_1` through `3c`, `cruz_stakeout*` (5), `cruz_swat*` (5), war milestone notices | "mid-thirties", "Homicide", "thirteen years", "What the FUCK was that", "Cruz is going to be furious", red eyes, cracking voice, "You scared me" |
| 7. Drug arc | `drug_raid_home` (including both compel outcomes), `drug_raid_surrender`, Jesus/Samira mentions | the compel succeeds on Cruz ("Cruz is the last to go"), which breaks her immunity canon |
| 8. Final sweep | every scene above, plus passing mentions (Rufus, Jaewon, street descriptions) | Full style-guide scan and `node --check` |

## Process for each batch

1. Rewrite each scene completely in the canon voice, keeping every flag, stat change, choice and scene link working.
2. Run the style scan on the new text: em dashes, contractions, "the kind of", "A beat", Not/Not, negation-before-reveal, "genuinely", "the way", "in a way", stated time spans, paragraph length, staccato chains.
3. Run `node --check` on both script blocks.
4. Commit.

## Status

All eight batches are done.

- Batches 1 to 7 rewritten; the final sweep fixed the leftover title, "captain" and pattern slips.
- New flag `cruzCompulsionScene` (`window`, `table`, `street`, `raid`) records where Kelsie first tried to compel Cruz, so the interrogation's "There was a moment" line refers back to the right place.
- Interrogation beat 3 choices are now: stay silent, try the compulsion again (+10 thirst, -5 mental health), or deny it was you.
- The drug raid compel no longer affects Cruz. On a success her team goes under, and she calls the raid off herself.
- `node --check` passes on both script blocks. A headless-browser smoke test rendered every rewritten scene across 2,880 flag combinations with no errors.
