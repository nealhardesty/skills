# Shared conventions and guardrails

Cross-cutting rules that apply across the RFC, threat model, and readiness review. Read this once per RFC-building session, alongside whichever per-document guide(s) you're actively using.

## Diagrams

Use Mermaid for all diagrams — it renders natively in GitHub Markdown. House style, drawn from real examples:

- **`flowchart` for architecture** — use `subgraph` blocks to group components by system or trust zone (e.g. `Public Zone`, `Trusted Application Boundary`, `External Boundary`). Reuse the *same* zone names between the RFC's architecture diagram and the threat model's diagram so a reviewer can map one onto the other at a glance.
- **`sequenceDiagram` for interactions** — optionally use `box` to visually group participants by owning subsystem, `alt` blocks for conditional branches, and `Note over X: ...` for inline annotations that don't need their own arrow.
- **`gantt` for implementation timelines** — chain phases with `after`, mark a hard cutover/decommission point with `milestone`.
- Mermaid is the default, not the only option ever — an SVG or other image is fine for something Mermaid genuinely can't express well (e.g. a detailed simulation chart), but don't reach for that as a first choice.
- **A diagram must never imply a control that doesn't actually exist in the design.** If a decision changes (e.g. "we're not validating audience after all"), go back and fix every diagram label that implied otherwise — a stale diagram is worse than no diagram, because reviewers who skim the diagram instead of the prose will be actively misled.

## Supplemental files — one purpose each, never a dumping ground

Only add these if there's real material to justify them — don't create an empty one just to look thorough:

| File | Purpose | Convention |
|---|---|---|
| `diagrams.md` | Source of truth for diagrams too numerous to keep solely inline in the README | Link once from Supporting Materials, and again at point of use (e.g. in Implementation Plan) |
| `references.md` | Bibliography / data provenance for any measured number or external fact cited | Cite the **measurement date**, not just the number — e.g. "Current size (measured 2026-07-08): ..." |
| `protobufs.md` / `*.proto` | API/schema contract, kept out of the README so it doesn't bloat the narrative | Link once at the point the contract is introduced |
| `poc-findings.md` | Empirical results of a prior spike/POC, positioned as authoritative over an earlier concept doc | State explicitly that it governs where it and any earlier doc diverge; use a claim-vs-verdict table (Affirmed/Refined/Corrected/Open) if there was a prior assumption being tested |
| `assets/` | Images, exported diagrams, screenshots | — |

**De-identification for sensitive real data**: if supporting evidence touches real customer/vendor identifiers, use a de-identified version (fabricated IDs, real analysis/numbers) in the committed file, and note explicitly where the authoritative copy with real identifiers lives instead (e.g. "held out of git, shared out-of-band on request").

## Cross-document consistency (the most commonly-missed thing)

The single most common real defect in RFC review is a decision that changes in one file while a table row, diagram label, or prose reference elsewhere still describes the old state. Before considering the package done:

- Any mitigation named in the README's ROAM table must use the *same wording* in the threat model's STRIDE table and, where relevant, the readiness review.
- Any question resolved in prose must have its "Questions to Answer" row (and any corresponding readiness-review row) updated from `Open` to reflect the resolution — in the same pass, not "later."
- Any diagram label must match the current, not superseded, decision.
- When you (or the user) change a decision mid-session, explicitly re-grep all three files plus any `diagrams.md` for every other mention of that decision before declaring the update complete.

## Sponsor requirement

The sponsor must be an architect, VP, or another person with clear technical authority — this is enforced at review time, and a missing/implausible sponsor is treated as a hard blocker. Never fabricate a name that "seems plausible" for this field. If the user hasn't told you who it is: write `TBD` in Metadata, ask the user directly if they want to supply one now, and reflect the gap as a `Blocked` row in the readiness review's Business Readiness section regardless of their answer.

## Grounding claims in evidence, not assertion or memory

Any statement about what a specific technology/vendor/product actually does (its HA model, consistency guarantees, sharding granularity, license terms) is a claim, not a fact, until verified. If you have live web access in this session and the claim is load-bearing for a real architectural decision, verify it before stating it as settled (quote a license clause rather than paraphrasing from memory; check current docs for a mechanism-of-action claim rather than relying on training data, which can be stale or approximately-but-not-exactly right). If you cannot verify a claim in this session, say so explicitly in the RFC (as an open question or a flagged assumption) rather than presenting it with unearned confidence — an RFC that asserts an unverified vendor capability as fact is a well-documented real failure mode this process is designed to catch (see the `/rfc-review` skill, which exists specifically to fact-check this after the fact — better to not need that catch in the first place).

Similarly: every rejected alternative and every cost comparison needs a specific, falsifiable reason or number behind it. "X doesn't scale as well" with nothing else is an assertion, not an analysis.

## Never leave template placeholder text

`YYYY-MM-DD`, blank required cells, or the template's own instructional prose ("Describe the selected solution") left unedited is not a lightweight draft — it's an unstarted section masquerading as a complete one. If you genuinely don't have content for a section yet, mark it visibly (`TODO: needs input from <person> on <topic>`) rather than leaving generic template instructions in place, so a reviewer can tell "unfinished" from "finished but shallow."

## Every table row needs a real owner

Risks, questions, threats, blockers — every one needs a named person or role, not a permanently-blank cell and not a rubber-stamped default (e.g. auto-assigning everything to the RFC owner regardless of who'd actually own it). If genuinely unknown, write `TBD — needs assignment` rather than leaving it blank or guessing a name.

## Asking questions: when and how

Don't run this as a blind, exhaustive interview — synthesize everything the conversation already gives you first. Then batch the *specific* gaps into one round of targeted questions rather than trickling them out one at a time. The recurring, real gaps worth asking about explicitly when the conversation doesn't cover them:

1. Who is the sponsor (architect/VP/technical authority), and is the business case stated in outcome terms?
2. Is there a real rollback plan, or is that still open?
3. Are there known accepted deviations from a spec/best-practice that should be threat-modeled as `Accepted` rather than silently under-implemented?
4. Who owns each open risk/question/blocker — a real name or role for each?
5. Where should the RFC package be written (which repo/path) if it isn't obvious from context?

If the user says "just mark it TBD" or "figure it out yourself" for something like a sponsor name, honor that literally — write `TBD`, don't substitute a guessed name.
