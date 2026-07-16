---
name: humanize
description: Rewrite prose to remove AI-generated tells, compress it for density, and fix errors — while preserving every fact, the author's voice, and any mandated structure or compliance terminology (RFC 2119, ISO clauses, legal boilerplate). Works on any prose — docs, RFCs, READMEs, PR descriptions, commit messages, emails. Use when the user asks to "humanize," "de-AI-ify," "de-slop," compress, tighten, proofread, or clean up a draft.
disable-model-invocation: true
---

# Humanize

Make the target read as if a sharp human wrote it: dense, plain, and in the author's own voice — without losing a fact or inventing one.

## Governing principles

These override every specific rule below when they conflict.

- **Minimal diff.** Make the smallest change that fixes the problem. Preserve sentences, paragraph breaks, and ordering that are already fine so the diff stays reviewable. You are editing, not rewriting from scratch.
- **Preserve the author's voice.** Match how *this* author writes (from the surrounding text or their other work if you can see it). Don't impose a house style. Don't trade one uniform rhythm for another — mechanically choppy sentences are just a different robot. Invisible editing is the goal.
- **Never add or sharpen.** Don't introduce facts, claims, hedges, or transitions that weren't there. Never turn a vague statement into a specific one ("fast" → "sub-100ms") — that's fabrication, not editing.
- **Don't edit for the sake of it.** If a passage is already tight and human, leave it and say so. A tool that always finds something to change earns distrust.

## Step 1: Target, intensity, and what's frozen

Identify the text. If the user named a file, `Read` it — don't ask them to paste it. If they pasted inline, use that. If it's ambiguous, ask.

Pick an intensity (default to **copyedit** unless the user says otherwise or the input is genuinely bad):
- **Proofread** — errors and obvious tells only; structure and wording otherwise untouched.
- **Copyedit** (default) — tells, redundancy, filler, and errors; preserve structure and voice.
- **Rewrite** — free to restructure and re-voice; use only when the user asks or the draft is beyond copyediting.

Scan for anything that must survive **exactly as-is**:
- **Compliance keywords.** RFC 2119 terms (MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY, OPTIONAL) — never touch wording or capitalization.
- **Mandated structure.** Required section headers, ordering, and boilerplate from a named standard, template, or house style. Edit the prose *inside* required sections; never merge, reorder, or delete one, even if it looks redundant.
- **Verbatim content.** Code, command output, log lines, error strings, direct quotations, table cell values, citations, contractual clauses, and any user-supplied string. Edit the prose around these, never their content.

If unsure whether something is mandated boilerplate or just verbose, treat it as frozen and ask.

## Step 2: Inventory the facts

Scale the rigor to the input. For a long or technical document, list every discrete claim — numbers, metrics, named tools/versions, ordered procedural steps, dates, owners, logical dependencies ("X requires Y before Z") — as a checklist to diff against in Step 6. For a short message, hold the facts in mind. Either way, know what must survive before you cut. Silent information loss during compression is the failure this step exists to prevent.

## Step 3: Cut the AI tells

The strongest modern tells are structural, not vocabulary. Watch for:

- **Summary sandwich.** Opening that restates what's coming; closing that restates what was said; a mini-summary sentence ending every section. Delete them — *unless* a required structure calls for an Abstract/Summary/Conclusion, in which case tighten that section instead.
- **Negation-reversal parallelism.** "It's not just X — it's Y," "not only… but also." Cut to the actual claim.
- **Reflexive rule-of-three.** "Fast, reliable, and scalable" strings where the third item is padding. Keep the items that carry weight.
- **Bullet lists where every item is a `**bold lead-in:** explanation`** by default, or where all items are forced into identical shape. Use that pattern only where it earns its place.
- **Em-dash and colon as a drama device.** Occasional em-dashes are fine (and natural); a steady drumbeat of "— and here's the thing" reveals is a tell. Reduce frequency; don't ban.
- **Hedge and booster filler.** "It's worth noting that," "it's important to understand," "can help to," "may potentially," "in today's fast-paced world," "let's dive in."
- **Vocabulary crutches** (secondary to the above): delve, tapestry, beacon, testament, symphony, bustling, landscape, underscore, illuminate, leverage (verb), robust, seamless, and crucial/vital/essential/imperative as intensifiers. Keep any of these when used in a precise technical sense.
- **Voice.** Prefer active voice with a concrete subject ("the system analyzes the logs," not "the logs are analyzed") — unless passive is genuinely more accurate. Let sentence length vary the way the content naturally demands; don't engineer variance for its own sake.

## Step 4: Compress and deduplicate

- Merge paragraphs that restate one concept from different angles into one tighter paragraph.
- Reduce a verbose bullet to its core fact or action — a list item is a fragment, not a paragraph.
- Cut words that don't change meaning: "very," "really," "basically," "in order to" → "to," "the fact that."
- If two *required* sections genuinely duplicate each other, flag it in the report (see Output) rather than unilaterally restructuring a mandated skeleton.

## Step 5: Fix errors while you're in there

This is a proofread, not only a style pass:
- Spelling, grammar, punctuation.
- Inconsistent terminology for one concept — pick a term and use it throughout (unless an external glossary mandates otherwise).
- Numbers or units that disagree across sections for the same fact ("200ms" here, "0.3s" there).
- Broken Markdown: skipped header levels, tables with mismatched columns, unclosed code fences, links to an anchor/section that doesn't exist.
- Acronyms used before they're defined; dangling references ("as discussed above" pointing at nothing, "see Section 4" after Section 4 moved).

Flag anything you can't confidently fix (a claim that looks wrong but you can't verify) rather than guessing at a correction.

## Step 6: Verify nothing was lost — and nothing was added

Diff the result against Step 2. Every number, name, step, and dependency must still be present and unaltered in meaning. Equally: confirm you introduced no new claim, hedge, or specificity the source didn't have. Restore or remove before finishing.

## Output

- **Edited a file:** apply the change with `Edit`/`Write`. Report concisely — word count before/after, intensity used, what you treated as frozen, errors fixed, anything flagged but left alone. Don't re-paste the whole document; the user reads the diff.
- **Edited pasted inline text:** return the edited text, then a 2–4 sentence note on structural changes and flags. Not a section-by-section changelog.
- **Already clean:** say so plainly and make only the few changes that are genuinely warranted. Don't manufacture edits.
