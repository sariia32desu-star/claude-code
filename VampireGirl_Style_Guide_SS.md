# Vampire Girl — Writing Style Guide

## Purpose
This document defines every writing rule for the Vampire Girl interactive fiction project. Paste this into any new chat with Claude when writing new scenes, editing existing ones, or continuing development. **Every rule here is mandatory and non-negotiable.** Deviation from any rule is considered an error.

Note: The game is strictly 2nd person present tense. Any scenes written in any style or POV that deviates from this is an error that must be rectified above all.
---

## 1. Em Dashes

**Rule:** No em dashes anywhere in the file UNLESS they meet one of two exceptions.

**Exception 1 — Paired parenthetical asides:**
An em dash is allowed when used in PAIRS to insert an aside into the middle of a sentence, like parentheses.

✅ `His voice—low and careful—cut through the noise.`
✅ `The blood—warmer than she expected—settled in her throat.`

**Exception 2 — Dialogue cut-offs:**
An em dash is allowed when a character's speech is interrupted or trails off abruptly.

✅ `"I can't just—"`
✅ `"Kelsie, you can tell me any—"`

**Everything else is WRONG:**
❌ `She looked at you — really looked.` (spaced em dash as connector)
❌ `His blood was warm—rich and slow.` (single unspaced em dash as connector)
❌ `The room was quiet — just the two of you.` (spaced em dash as connector)

**What to use instead:** commas, periods, semicolons, or colons depending on context.

---

## 2. Contractions (Formal Language Ban)

**Rule:** Always use contractions in prose and dialogue. Formal/uncontracted language is banned.

**This applies to ALL prose and ALL dialogue. No exceptions for "serious" or "dramatic" moments.**

| ❌ WRONG | ✅ RIGHT |
|----------|----------|
| She is | She's |
| He is | He's |
| It is | It's |
| You are | You're |
| There is | There's |
| I am | I'm |
| We are | We're |
| does not | doesn't |
| do not | don't |
| is not | isn't |
| will not | won't |
| cannot | can't |
| has not | hasn't |
| have not | haven't |
| was not | wasn't |
| were not | weren't |
| could not | couldn't |
| would not | wouldn't |
| should not | shouldn't |
| I have | I've |
| You have | You've |
| We have | We've |

**Exceptions (do NOT contract):**
- JavaScript code comments (`//`)
- Actual code logic (`if`, `const`, `function`, etc.)
- HTML tag attributes
- `<video>` fallback text: "Your browser does not support video playback"
- UI system labels and game guide text (settings panel, stat descriptions)

---

## 3. "The Kind Of" / "The Kind That" Formula

**Rule:** Never use "the kind of [noun] that [verb]" or "the kind that [verb]" formula.

❌ `The kind of silence that fills a room.`
❌ `The kind of person who decides to like you.`
❌ `The kind that keeps you up at night.`

**Fix by rewriting directly:**

✅ `A silence that fills a room.`
✅ `Someone who decides to like you.`
✅ `One that keeps you up at night.`

Or restructure entirely. "The kind of" is an AI writing crutch and always has a more direct replacement.

---

## 4. "A Beat" / "A Pause"

**Rule:** Never use "A beat." or "A pause." as a standalone sentence or sentence fragment.

❌ `A pause. "I was wrong about you."`
❌ `A beat. Just the two of you in the quiet.`
❌ `She holds your gaze a beat longer.`

**Fix with character action, body language, or silence description:**

✅ `She exhales. "I was wrong about you."`
✅ `His jaw shifts. "I was wrong about you."`
✅ `She swallows. "I was wrong about you."`
✅ `She holds your gaze a moment longer.`
✅ `She holds your gaze a second longer.`
✅ `Silence.` (if standalone is needed)

---

## 5. Not/Not and No/No Patterns

**Rule:** Do not use paired negative inventory patterns.

❌ `Not the hardest hitter. Not the tallest.`
❌ `No pretense. No composure. Just want.`
❌ `Not hurt. Not angry. Curious.`
❌ `No explanation. No apology. Just rejection.`
❌ `Nothing that kills. Nothing that won't heal.`
❌ `Not in a bad way. In a warm way.`

**Fix by restructuring:**

✅ `Every pretense stripped away. Just want.`
✅ `She isn't hurt or angry. Curious.`
✅ `Without explanation or apology. Just rejection.`
✅ `Nothing that kills or won't heal.`
✅ `Warmly. His eyes soften.`

**Note:** A single "Not" or "No" is fine. The problem is the PAIRED or TRIPLE repetitive pattern. Also, panic-thought italics like `<em>Not now. Not in front of her.</em>` and natural dialogue like "Not bad for your first day" are acceptable.

---

## 6. Negation-Before-Reveal Formula

**Rule:** Never negate first and then state the real thing. Just say what it is. The core problem is beating around the bush: telling the reader what something *isn't* before telling them what it *is*. Every variation of this is banned.

❌ `Not because she was afraid. Because she was tired.`
❌ `He lets you—not because you could hold him, but because the touch makes him go slow.`
❌ `She didn't stop because of fear. She stopped because she was done.`
❌ `It wasn't anger. It was exhaustion.`

All of these waste words circling the point. Just say it:

✅ `She was tired. That was all.`
✅ `He lets you. The touch makes him go slow.`
✅ `She was done. She stopped.`
✅ `Exhaustion settled over her.`

If the sentence works without the negation, cut the negation. The reader doesn't need to be told what something isn't before they're told what it is.

---

## 7. "It's Not X, It's Y" / "You're Not X, You're Y" Stanza Formula

**Rule:** Do not use the stanza structure where lines build via negation then reveal.

❌ `You're not the star. / You're the girl coaches trust / to be in the right place.`

This includes any variant where multiple lines negate before a reveal. Restructure into direct, active prose.

---

## 8. "Genuinely" / "Honestly" / "Straightforward"

**Rule:** Do not use "genuinely" as an adverb modifying emotions or states. It's an AI tell.

❌ `She looks genuinely surprised.`
❌ `genuinely dangerous`
❌ `genuinely happy`
❌ `genuinely curious`

✅ `She looks surprised.` (the context makes it real)
✅ `dangerous`
✅ `happy`
✅ `curious`

**Exception:** "Genuinely" is acceptable in character dialogue when it sounds like natural speech: `"I am genuinely great right now."` — but use sparingly. "Honestly" is acceptable in dialogue as a speech habit: `"Honestly? No."` "Straightforward" as an adjective describing a thing (not a person's manner) is fine.

---

## 9. Crude prose for erotic scenes (CRITICAL RULE)

**Rule:** Even for non-gallery erotic scenes, even for scenes where Kelsie or another character is simply feeling aroused, the prose should be crude, erotic, and anatomical all throughout. No euphemisms or watered down terms whatsoever. Use "nipples," "pussy," "clit," and "cunt". This is the game's established style. Write to it for consistency.

---

## 10. "Something X. Something Y." Paired Patterns

**Rule:** Do not use paired "Something" sentences.

❌ `Something shifted. Something broke.`

Restructure into a single sentence or use varied phrasing.

---

## 11. Two-Adjective Paired Patterns

**Rule:** When two short sentences follow the same AI rhythm pattern, vary the structure.

❌ `Nothing serious, nothing competitive.`
❌ `Quiet. Deliberate.` followed by `Slow. Real.` in the same paragraph.

One pair per passage is fine. The problem is when the whole passage is built from these pairs.

---

## 12. General Prose Style

- **Short paragraphs.** Terse prose. Let white space do work.
- **Casual modern contractions throughout.** "She's" not "She is." "You're" not "You are." Always.
- **No AI-sounding language.** No "delve," "tapestry," "testament," "multifaceted," "nuanced." No purple prose. No poetry mode.
- **No stanza formatting.** Prose, not verse. Don't break sentences into line-by-line reveals.
- **Dialogue should sound spoken.** People clip words, trail off, interrupt themselves. Nobody gives speeches.

---

## 13. Paragraph Density (No Walls of Text)

**Rule:** No single paragraph should exceed roughly 2–3 sentences of prose. If a paragraph runs longer than ~300 characters of visible text, break it into smaller paragraphs separated by `\n\n`.

This game is read on mobile screens. Long, dense paragraphs become unreadable walls of text that players will skim or skip entirely. White space is a design tool. Use it.

**Gold standard — the Trigrave arrival scene:**
Each image or thought gets its own paragraph of one to three full sentences, then a break. Short paragraphs don't call for short sentences: let sentences run with commas and detail, and save the fragment for the rare moment it earns. Chains of one- and two-word sentences ("Heat. Breath. Racing pulse.") read as staccato AI filler and are an error.

✅
```
The city hits you all at once, and it hits hard.

Horns, sirens, a jackhammer chewing up asphalt somewhere down the block. Exhaust and fryer grease and a hundred different perfumes, stacked on top of each other until you can taste them. And under all of it, drowning everything else out, the heartbeats.

Thousands of them. Every person on this street is carrying a drum in their chest, and you can hear every single one, thudding out of sync until the whole sidewalk seems to shake.

You stumble and grab a lamppost to keep your feet. The metal's cold under your palm. Your fangs ache in your gums, and for one bad second you're not sure you can hold them back.

You close your eyes and make yourself breathe, slow and deliberate, in through your nose and out through your mouth.

Then you pick one sound and hold onto it, a street musician's guitar on the corner, and let everything else slide in behind it. The heartbeats sink down to a low hum you can think over.

When you open your eyes, Trigrave's still there. Glass towers cutting up the sky, streets packed curb to curb, people pouring past you in a current that won't slow down for anyone.
```

❌ (wall of text)
```
The city hits you all at once, and it hits hard. Horns, sirens, a jackhammer chewing up asphalt somewhere down the block. Exhaust and fryer grease and a hundred different perfumes, stacked on top of each other until you can taste them. And under all of it, drowning everything else out, the heartbeats. Thousands of them. Every person on this street is carrying a drum in their chest, and you can hear every single one. You stumble and grab a lamppost to keep your feet.
```

❌ (staccato)
```
You stumble. Grab a lamppost for support. Too much. It's all too much.

Focus. Breathe. You can do this.

You concentrate. The sounds dampen. Fade to background noise. Better. Manageable.
```

**Applies to everything players read:**
- Location descriptions (first visit and return)
- Character appearance descriptions
- Narrative prose in scene text
- Dialogue and dialogue-heavy scenes
- Sex scenes and intimate scenes
- Any `desc +=` or `text:` line that produces visible paragraphs

**Does NOT apply to:**
- HTML/UI markup

**When in doubt:** If a block of text would fill more than half a phone screen without a break, it needs splitting.

---

## 14. Technical Rules for the HTML File

### The Single Most Important Rule

**Every apostrophe inside a single-quoted JavaScript string MUST be escaped with a backslash.**

The game is a single HTML file with all prose inside JavaScript string literals. Nearly all strings use single quotes (`'...'`). Any unescaped apostrophe inside a single-quoted string will terminate the string early and crash the game with a `SyntaxError: Unexpected identifier` error.

✅ `'She doesn\'t look away.'`
❌ `'She doesn't look away.'` ← **Crashes the game.**

This applies to ALL apostrophes regardless of what follows them:
- Contractions: `doesn\'t`, `can\'t`, `she\'s`, `you\'re`, `it\'s`, `won\'t`, `I\'m`, `I\'ve`, `we\'re`, `they\'re`, `he\'s`, `that\'s`, `there\'s`, `hasn\'t`, `haven\'t`, `wasn\'t`, `weren\'t`, `couldn\'t`, `wouldn\'t`, `shouldn\'t`, `isn\'t`, `don\'t`, `aren\'t`
- Possessives: `Lisa\'s`, `Kelsie\'s`, `Jensen\'s`
- Contractions before ANY character including em dashes: `That\'s—` not `That's—`
- Any other apostrophe in prose: `o\'clock`, `ma\'am`, etc.

**The character after the apostrophe does not matter.** Whether it's followed by a letter, a space, a period, an em dash, a quotation mark — if it's inside `'...'`, escape it.

### How to safely write new content

**Option A — Write in the str_replace tool or create_file tool:**
When writing new scene text directly, always include `\'` for every apostrophe:

```javascript
text: 'She doesn\'t look at you. She can\'t. Her hands haven\'t stopped shaking since you told her.',
```

**Option B — Write prose naturally, then escape before inserting:**
Write the prose without escaping, then do a find-and-replace of `'` → `\'` on the prose content ONLY (not the surrounding JS syntax quotes) before placing it in the file.

### Never use sed with \x27

Using `sed` with `\x27` to insert apostrophes creates literal `'` characters, not escaped `\'`. This will break JavaScript strings. Always use `str_replace`, Python, or direct file editing instead of sed when inserting apostrophes into JS strings.

### Double-escaping danger

When editing lines that already contain `\'`, be careful not to create `\\'`. That's an escaped backslash followed by a string-terminating apostrophe — it also crashes the game. After any edit, verify with:

```bash
node --check extracted_script.js
```

To extract and check both script blocks:

```bash
sed -n '3759,3823p' Vampire_Girl.html > /tmp/script1.js && node --check /tmp/script1.js
sed -n '6200,87869p' Vampire_Girl.html > /tmp/script2.js && node --check /tmp/script2.js
```

**The line numbers for script extraction will shift as the file grows. Always verify with:**

```bash
grep -n '<script>' Vampire_Girl.html
grep -n '</script>' Vampire_Girl.html
```

### Other technical notes

- The working file is a single HTML file with embedded JavaScript.
- All prose lives inside JavaScript string literals (single-quoted `'...'` or template literals).
- When editing with `str_replace`, always match the exact content including escape characters.
- Code comments (`//`), `gameState` references, `function` declarations, `if/else` blocks, and HTML attributes are code, not prose — do not apply contraction rules to them.
- After ANY batch of edits, always run the `node --check` verification before declaring work complete.

---

## 15. "The Way" / "In a Way" Patterns

**Rule:** Both phrases are banned entirely — no exceptions.

`The way` as a sentence or clause opener is a lazy atmospheric crutch that delays meaning:

❌ `The way he looked at her made her stomach drop.`
❌ `The way it felt — she didn't have words for it.`
❌ `The way the light hit the room.`

All of these bury the actual beat. State it directly:

✅ `His look dropped her stomach.`
✅ `She had no words for it.`
✅ `Light fell across the room at a low angle.`

`In a way` is a hedging qualifier that softens writing into vagueness:

❌ `In a way, she'd always known.`
❌ `In a way, it made sense.`

Cut it entirely and commit to the sentence:

✅ `She'd always known.`
✅ `It made sense.`

---

## Quick Reference Checklist

Before declaring any writing work complete, verify:

- [ ] No spaced em dashes (` — `) except paired parentheticals or dialogue cut-offs
- [ ] No single unspaced em dashes used as connectors (word—connector—word with only one dash)
- [ ] All contractions used (no formal "does not," "She is," "You are," etc.)
- [ ] No "the kind of" or "the kind that" formulas
- [ ] No "A beat." or "A pause." as standalone beats
- [ ] No paired Not/Not, No/No, or Nothing/Nothing patterns
- [ ] No negation-before-reveal formulas ("not because X, but because Y" or any variant — just state it directly)
- [ ] No "genuinely" modifying emotions/states
- [ ] No "The way" as a sentence or clause opener (e.g. "The way he looked at her")
- [ ] No "In a way" as a hedging phrase — banned in all forms
- [ ] Crude prose for erotic scenes
- [ ] No wall-of-text paragraphs — break any prose block over ~300 characters into shorter paragraphs
- [ ] No staccato chains of one- and two-word sentences — short paragraphs, full sentences
- [ ] All apostrophes properly escaped in JS strings (`\'` not `'`) — including before em dashes and all other characters
- [ ] `node --check` passes on extracted script blocks with zero errors
- [ ] Prose reads terse, modern, casual — not literary or AI-polished
