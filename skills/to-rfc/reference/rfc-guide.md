# Writing the RFC document (README.md) — generation guide

Fill in `templates/rfc-template.md` section by section. This guide tells you what good input looks like for each section and what to do when the conversation doesn't supply it.

## Metadata table

- **RFC Owner**: a named individual (the user, unless they say otherwise). Never a team name.
- **Sponsor / Sponsor Role**: MUST be an architect, VP, or someone else with clear technical authority. If the conversation hasn't named one, do not invent one — write `TBD` and add a corresponding `Blocked` row in the readiness review's Business Readiness section (see readiness-review-guide.md). It's acceptable to ship a draft with `TBD` here as long as it's tracked honestly, not silently.
  - Split-sponsor pattern: if one person owns the architectural call and a different person owns a specific sign-off (e.g. data governance, legal), name both explicitly: `Sponsor: <Name>, <Role> (sponsor of record; <Other Name> for <specific> sign-off)`.
- **Business Case**: one to two sentences, outcome-oriented. Pattern: *problem → what this RFC replaces it with → the concrete business consequence*. Not a restatement of the feature. E.g. "Manually provisioning a container per vendor blocks per-customer onboarding and caps revenue growth. This control plane replaces that with multi-tenant, API-driven, self-service provisioning, eliminating the manual bottleneck." If the conversation only discussed the technical problem, ask the user for the business consequence rather than inventing one — this is a common, real gap and reviewers treat a missing/weak business case as a genuine defect.
- **Status**: `Draft` until merged. Don't write `Approved`/`Accepted` yourself — that only happens at PR merge.
- **Created / Last Updated**: today's date. **Review Date**: ask the user if they know the target RFC review date (many orgs run these on a fixed recurring cadence); otherwise write `TODO (next scheduled RFC review)`.

## Required Review Artifacts

Always include, with working relative links:
```markdown
- [Threat model](threat-model.md)
- [Readiness review](readiness-review.md)
```

## Executive Summary

State the problem and the solution so someone who reads only this paragraph understands the decision. If the conversation surfaced explicit design bets (e.g. "we decided X because Y"), front-load them here as a short bulleted list — don't make the reader hunt for the key decisions later.

## Problem Statement

Structure as: **what exists today → what it cannot do → what this RFC specifically addresses**. Be concrete — quantify pain points if the conversation gave numbers; avoid vague claims like "doesn't scale well" without a specific reason.

## Goals and Motivation

Outcomes, not implementation — "self-service API," "eliminate manual provisioning," not "we'll use gRPC." Each goal should be something the readiness review could later test as a success metric.

## Known Requirements

Pre-existing constraints only (compliance, existing commitments, observability standards) — **this is not a PRD substitute**. Pull these from anything the conversation established as a hard constraint (e.g. "must be SOC2 compliant," "must not require downtime"). If none were discussed, ask briefly rather than inventing generic filler like "must be secure."

## Proposed Solution

The bulk of the document — technology, architecture, boundaries, data flow, scaling, security, ops, cost. Synthesize this from whatever architecture/design decisions were already made in the conversation; do not re-derive or re-litigate them.

Strong-RFC techniques to apply:
- **State explicitly what the system does NOT do** — a scope/boundary table or paragraph. This preempts most reviewer questions.
- **Justify any technology/vendor choice with real numbers or a verified fact, not assertion.** If the conversation included a cost comparison, benchmark, or POC result, use it verbatim with numbers. If a technology claim (a product's HA/consistency/clustering behavior, a license's terms) hasn't been verified and you're not confident of it from firsthand research in this conversation, say so as an open question rather than asserting it — do not state vendor/product capabilities from memory as settled fact. See `conventions.md` → "Grounding claims in evidence."
- **If a POC/spike was discussed**, cite its findings concretely and let them govern over any earlier assumption; note explicitly where they diverge from the original plan.

### Architecture Diagram / Interaction Diagram

Always include both, in Mermaid (`flowchart` + `sequenceDiagram` at minimum). See `conventions.md` → "Diagrams" for the house style (subgraph trust zones, box groupings, gantt milestones, etc.) — read it before drafting diagrams.

## Impacts and Risks (ROAM table)

```markdown
| Risk | Impact | ROAM Status | Owner | Mitigation or Notes |
```
ROAM = Resolved / Owned / Accepted / Mitigated. Every row needs a real owner (a name or role, never blank, never a permanent "TBD"). Reuse the exact same mitigation wording you use in the threat model's STRIDE table and in the readiness review — see `conventions.md` → "Cross-document consistency."

## Questions to Answer

```markdown
| Question | Owner | Status | Notes |
```
Only genuinely open questions — don't list something already resolved elsewhere in the doc. If the conversation resolves a question you'd otherwise have listed here, don't add it as Open in the first place.

## Implementation Plan

Phases, rollout, rollback, operational handoff, plus a `gantt` diagram if there's a real timeline (use `after` dependencies and a `milestone` for a hard cutover point). Always include a rollback plan even in an early draft — "no rollback plan documented" is a real, commonly-flagged readiness gap. If timeline isn't known yet, say so rather than inventing dates.

## Alternatives Considered

```markdown
| Alternative | Summary | Why It Was Not Chosen |
```
Every rejected alternative needs a specific, falsifiable reason — not "X is worse." If the conversation discussed real competing options, use those with their actual pros/cons. If the conversation didn't explore alternatives, ask the user briefly what else was considered before filling this in with something generic.

## Supporting Materials

Link POCs, related PRs/issues, cost estimates, design docs — whatever the conversation referenced. If a spike/prototype was built, link its actual PR(s).

## Review Notes

```markdown
| Date | Note | Owner |
```
Leave this with a single seed entry noting the RFC was drafted (date, owner) — it becomes a running log of what changes during review; don't pre-fill it with speculative future entries.
