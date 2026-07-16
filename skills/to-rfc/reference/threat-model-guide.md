# Writing the threat model (threat-model.md) — generation guide

Every RFC gets a threat model — no exception for changes that don't feel "security-flavored" on the surface (a database migration still gets a real STRIDE pass: noisy-neighbor DoS, cross-tenant exposure, tampering via bulk load, etc.). **Never skip this file or leave it as the blank template** — a missing/unfilled threat model is the single most common real defect found in review, and it's a hard requirement, not optional.

Fill in `templates/threat-model-template.md`'s 7 sections using the conversation's architecture/design content. Section-specific guidance:

## 1. Application Summary
Plain-language description, owners (ask if unclear who the business/technical owner is), environments covered, and — importantly — an explicit **Out of scope** line. Stating what's deliberately excluded prevents reviewers from assuming a gap is an oversight.

## 2. Architecture, Data Flow, and Dependencies
Reuse the RFC's architecture diagram, ideally with trust-zone `subgraph` blocks added (Public/Trusted/External or similar — match whatever zone names you use in the README's diagram so a reviewer can map the two at a glance). List every external dependency with an owner and a stated security assumption ("OIDC tokens must be validated by the application").

## 3. Assets, Data, and Trust Levels
List sensitive data, credentials, and critical capabilities with a Classification (Public/Internal/Confidential/Restricted) and which of Confidentiality/Integrity/Availability actually matters for it. Include service-account/non-human identities alongside human roles.

## 4. Entry Points, Exit Points, and Trust Boundaries
Every API, form, upload, webhook, queue, admin surface, background job, third-party integration — as entry and/or exit — with required trust level and touched assets. **Specifically check**: any client-supplied scoping identifier (tenant ID, org ID, account ID) that is NOT derived from authenticated server-side context is an IDOR-class risk — call it out as a threat row in section 5 if the design has this shape.

## 5. Threats, Risks, and Mitigations (STRIDE table)

```markdown
| STRIDE Category | Threat / Abuse Scenario | Affected Asset(s) | Entry / Flow | Existing Controls | Risk | Mitigation / Requirement | Owner | Status |
```

Use STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) as the prompt for each entry/exit point and each asset from sections 3-4. Focus on threats specific to *this* system's actual shape, not a generic checklist recited regardless of architecture.

Risk rating:
| Risk | Guidance |
|---|---|
| High | Likely/easy to exploit; could expose restricted/confidential data, escalate privilege, affect many customers, or compromise a critical system |
| Medium | Plausible, moderate impact; compensating controls may exist |
| Low | Limited impact, hard to exploit, or already strongly controlled |

Every High/Medium row needs a real mitigation, a real owner, and a status.

**The "Accepted" pattern — use it whenever the design knowingly deviates from a spec or best practice.** If the conversation surfaced a deliberate tradeoff (e.g. "we're not validating the audience claim because X" or "we're forwarding the token upstream because the two systems share one trust domain"), don't hide it and don't leave it vaguely `Open`. Write it as its own threat row, rate it honestly, and set Status to `Accepted` with the specific reasoning already given in the conversation. An examined, justified deviation is a strength; a silently-glossed-over gap is a real defect reviewers will find.

Example rows (for calibration on depth/specificity expected — write your own from the actual architecture, don't reuse these):
> Spoofing — "Attacker attempts to reuse or forge an authentication token to access another customer's account" — High — "Validate issuer, audience, expiration, and signature on every API request"

> Elevation of Privilege — "Token passthrough to upstream API" — Low (downgraded from High via an accepted deviation) — "Accepted deviation: [system A] and [system B] share one trust domain, and the forwarded token is a first-party JWT carrying the real user's identity and scopes, so the confused-deputy scenario the spec guards against doesn't apply here." — Status: Accepted

## 6. Open Risks and Risk Acceptance
```markdown
| Risk ID | Description | Risk Level | Reason Not Mitigated | Approver | Expiration / Review Date |
```
Any accepted risk needs a named, accountable Approver (a real person/role — ask if unclear) and an expiration/review date so it doesn't become permanent by default. **High risks should not be accepted without explicit leadership approval** — flag this to the user rather than quietly setting Approver on a High risk yourself.

## 7. Review Checklist and Maintenance
Fill in honestly based on what you've actually done in this pass — don't mark "Yes" to a check (e.g. "Security reviewer has reviewed the model") that hasn't actually happened. Leave reviewer sign-off items as "No" / blank until a human has actually done that step.

## Cross-referencing
Every mitigation named here that also appears in the README's ROAM table or the readiness review should use the *same wording*. See `conventions.md` → "Cross-document consistency" — this is actively checked in real review and is the most common thing that goes stale.
