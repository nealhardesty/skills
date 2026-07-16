# Writing the readiness review (readiness-review.md) — generation guide

Where the threat model asks "is this secure," the readiness review asks "is this actually ready," across five lenses: business, engineering, security/compliance, operational, support/rollout. Fill in `templates/readiness-review-template.md`.

## Status vocabulary

Every check row uses exactly one of: **Not Started / In Progress / Ready / Blocked**. Be honest, not optimistic — if the conversation never discussed monitoring/alerting, that row is `Not Started`, not `Ready`. A readiness review that marks everything `Ready` on a first draft is a red flag, not a good sign.

## Business Readiness

- "Sponsor has approved the direction" is `Blocked` whenever Metadata's Sponsor is `TBD` — always keep these two facts in sync.
- "Success metrics are defined" should map back to the RFC's Goals and Motivation — if goals aren't stated as testable outcomes, this can't be `Ready`.

## Engineering Readiness

- "Rollback plan is documented" must match whatever the RFC's Implementation Plan actually says — if the RFC has no rollback plan, this is `Blocked`, not skipped.

## Security and Compliance Readiness

- "Threat model is complete" — check the actual state of `threat-model.md` you just wrote/are writing, don't assume `Ready` because the file exists. If any High/Medium STRIDE row lacks a mitigation or owner, this is `In Progress` at best.
- Note any privacy/compliance angle the conversation raised (e.g., data leaving to a third-party processor) even if full legal review hasn't happened yet — `In Progress` with a clear note is more honest than skipping the row.

## Operational Readiness / Support and Rollout Readiness

Fill from whatever the conversation said about monitoring, runbooks, on-call, rollout communication. Where nothing was discussed, mark `Not Started` and add a corresponding row to Risks and Blockers rather than leaving it silently blank.

## Risks and Blockers / Launch Criteria

These tables should restate (not duplicate independently) whatever is `Blocked` or `Not Started` above, each with a named owner and a resolution plan. A blocker with no owner or no resolution plan is a common, real gap.

## Decision field — allow nuance, don't force a binary

Real, well-calibrated decisions distinguish *what* is and isn't ready rather than giving one verdict for everything:
- `Ready with Follow-ups (staging pilot); Not Ready (production)` — when the design is fine to pilot but has open items gating full production.
- `Not Ready (feasibility proven; go-live gated)` — when a POC validates the engineering approach but launch has specific unmet gates.
- `Ready with Follow-ups` — general case: proceed, with named follow-ups and owners.
- `Not Ready` — say this plainly when it's true, with a one-line reason (e.g. "proceed only through design/prototyping until blockers are resolved").

Pick whichever phrasing actually reflects the state of the RFC you just wrote — don't default to "Ready" to make the draft look more finished than it is. It is normal and expected for a freshly-drafted RFC's readiness review to say `Not Ready` or `Ready with Follow-ups` — that's the honest, common case, not a failure of the process.

## Cross-referencing

Any status/blocker here that traces back to a decision made (or still open) in the README or threat model must use consistent language and must be updated together if that decision changes later. See `conventions.md` → "Cross-document consistency."
