---
name: rfc-review
description: Deep critical review of an RFC in this repo's rfcs/ directory — verifies technology claims against real-world research, audits the architecture/schema/API for inconsistencies, and lists what a good architect would flag as missing. Use when the user asks to review, critique, or "do a deep review" of an RFC, or asks "what's wrong with this RFC" / "what's missing from this RFC".
---

# RFC Review

Reproduces a thorough, skeptical architecture review of an RFC: understand it completely, verify every non-trivial technical claim against outside research (don't trust the RFC's or your own training-data recollection of a product's capabilities), and report findings organized for a software/product/data engineering audience.  It is critical that you verify all claims.

This is not a rubber stamp. Default posture is skeptical — the goal is to find what's wrong, unsubstantiated, inconsistent, or missing, not to summarize the doc back to the author.

## Step 0: Identify the target

Args may name an RFC folder or path (e.g. `/rfc-review graph-correlation-service`). If not given:
- If exactly one folder exists under `rfcs/`, use it.
- If multiple exist, list them and ask which one (or ask if the user means the RFC tied to the currently open/edited file).
- If the user names a PR instead of a folder, use `gh pr diff`/`gh pr view` to find the changed RFC folder.

## Step 1: Read the project's own rules first, then the RFC folder

Before reading the target RFC, (re-)read the repo's top-level `README.md` and `templates/rfc-template.md` in full, even if you've read them before in this session — they define what this process is *for* and what "done" looks like, and the review is meaningless without that context. Also skim `templates/threat-model-template.md` and `templates/readiness-review-template.md` so you know what those documents are supposed to contain. Pay particular attention to:
- The stated purpose of an RFC (`README.md`'s intro and "When to Create an RFC") — this is the yardstick for whether the RFC's scope and depth actually match what the process expects.
- "RFC Requirements" and the "RFC Quality Checklist" — the definitive list of what every RFC must contain.
- The template's exact section structure, section-by-section intent (the guidance text under each heading, e.g. what "Known Requirements" or "Alternatives Considered" is supposed to contain), and formatting conventions (e.g. ROAM risk table with Owner column, Questions-to-Answer table with Owner/Status/Notes, Alternatives table with a "Why It Was Not Chosen" column, Metadata table, Required Review Artifacts links, Supporting Materials, Review Notes).

Then read every file in the target RFC's folder end to end: `README.md`, `threat-model.md`, `readiness-review.md`, `protobufs.md` or other API contract files, `diagrams.md`, `references.md`, and anything under `assets/`. Cross-reference between files — inconsistencies between the RFC body and its own API contract or threat model are high-value findings.

### Check structural completeness against the template and README

This is a distinct pass from the architecture review, and it matters: do it explicitly, not just implicitly.

- Required files: is there a `threat-model.md`? A `readiness-review.md`? Do they exist and are they filled in, or are they missing entirely, or left as unedited template placeholders?
- Required metadata: is a sponsor named with appropriate technical authority (architect/VP)? Is there an explicit business case? Status/dates filled in?
- Section-by-section diff against `templates/rfc-template.md`: is the Metadata table present? Required Review Artifacts links? Known Requirements as its own section? Are Impacts and Risks, Questions to Answer, and Alternatives Considered using the template's table structures (ROAM status + owner; owner/status/notes; why-not-chosen), or has the author substituted a different, informally-structured format that drops fields the template considers necessary (e.g. an owner for each risk/question, a ROAM disposition, ranked ordering)? Is there a Supporting Materials section? A Review Notes section?
- Note every deviation you find, but weigh it appropriately: a renamed heading or reordered section is trivial; a dropped required file, a missing sponsor/business case, or risk/question tables missing an owner/accountability column are glaring and should be called out clearly, since they break the process's actual mechanics (nothing tracks who owns an open risk or question). Don't nitpick cosmetic differences as if they were the same severity as a missing threat model.

## Step 2: Build a complete mental model before critiquing

Before writing anything, be able to answer: What problem is this solving and for whom? What's the proposed architecture end to end (ingest → storage → processing → serving)? What are the data model / schema and API contracts? What tenancy, consistency, and failure-mode assumptions are baked in? What has the author already identified as risky (their risk table / open questions)? Don't re-list what the author already knows — build on it or challenge it.

## Step 3: Research every non-trivial technical claim — don't trust memory or the RFC's framing

For every named technology, product, protocol, or vendor the RFC leans on (databases, message brokers, cloud services, libraries, specific scaling/HA claims), use WebSearch/WebFetch to verify, not recall from training data:

- **Does the product actually do what the RFC claims?** (clustering model, HA/failover mechanics, consistency guarantees, sharding granularity — e.g. does "supports clustering" mean a single logical unit of data can be split across nodes, or only that many independent units can be distributed?)
- **Licensing.** Check the actual license (SSPL, BSL, AGPL, dual-license, etc.) against how the RFC intends to use it — hosted in-house to power a paying multi-tenant product is a specific trigger condition for several "source-available" licenses regardless of whether the software itself is redistributed. Quote the actual clause, don't paraphrase from memory. If ambiguous, say so explicitly and flag it for legal review rather than resolving it yourself.
- **"Alternatives Considered" sections.** Treat every rejection of an alternative as a claim needing evidence. Fact-check the stated reasons (e.g. "X doesn't support multi-tenancy" — is that still true, or true only of a free tier?). If the RFC gives no benchmark data, cost comparison, or POC results behind a foundational, hard-to-reverse decision, say so plainly — assertions without evidence on a decision this consequential are a finding.
- **Known real-world failure modes.** Search for migration write-ups, GitHub issues, or engineering blog posts from others who've run this technology at the scale implied by the RFC. Concrete "here's what broke for someone else at this scale" beats generic caution.

Run these research queries in parallel where independent. Prefer official docs for mechanism-of-action claims (sharding, concurrency model, replication) and independent sources (migration post-mortems, Hacker News threads, benchmark repos) for real-world caveats vendors don't advertise.

## Step 4: Critique the architecture and schema on their own merits

Look specifically for:

- **Internal inconsistencies.** Entities defined but never referenced by any relationship/edge. Relationships pointing at types that don't exist. Requirements stated in prose (e.g. "preserve provenance," "distinguish observed vs inferred") that aren't actually reflected as fields in the schema. A risk table whose "mitigation" column names something the design doesn't actually implement.
- **Missing coverage for the RFC's own stated primary use cases.** If the problem statement leads with a specific scenario (e.g. network/firewall correlation), check whether the schema/API can actually answer that scenario — ask for a worked example query/request and see if one is provided. If not, that's a gap.
- **Growth and lifecycle gaps.** What has a retention/TTL/cleanup story and what doesn't? Which entities could grow unboundedly (ephemeral cloud resources, high-cardinality identifiers, autoscaled compute) with no corresponding lifecycle policy?
- **Hub-node / hot-path / noisy-neighbor risk.** Anything that could become a high-fan-out node or hot partition/shard (shared IPs, common identities, popular services) and quietly corrupt query results or degrade shared infrastructure for others.
- **Trust boundaries.** Where does tenant/customer isolation actually get enforced, and is it asserted (a field is trusted at face value) or verified (cryptographically or structurally guaranteed)? Client-supplied scoping identifiers (tenant IDs, org IDs) in API requests that aren't derived from authenticated context are IDOR risk, not just a style nit.
- **Consistency and disaster-recovery story.** If there are multiple stores/projections, is there a defined consistency SLO? If disaster recovery relies on event-log replay, does the log's actual retention cover the claimed recovery scenario?
- **API/contract design.** Type fidelity (stringly-typed property bags lose information), ambiguous or unspecified semantics (search matching rules, traversal direction, pagination bounds), and whether safety controls (max depth/nodes/rate limits) exist at both ingest and query time, not just one.

## Step 5: Compile the missing-questions list

Separately from the critique, list concrete questions a good architect/systems designer would ask that the RFC doesn't answer — sizing/cost model with real numbers, legal review needs, capacity/tenant-count assumptions, compliance/PII/data-deletion story, operational ownership, and anything flagged as a "Question to Answer" in the RFC that actually should have gated the design decisions already made rather than being deferred.

## Step 6: Report

Default output is a structured chat response (not a new file) organized as:

1. **Bottom line** — 2-4 sentences: is the core design sound, and what's the single biggest issue.
2. **Process/structure deviations from the RFC template** — a short section, kept separate from the architecture critique so it doesn't get lost or dominate. Only glaring deviations (missing required files, missing sponsor/business case, missing accountability fields in tables); skip purely cosmetic differences.
3. **Technology/vendor choice** — verified claims vs. reality, licensing, alternatives-considered rigor.
4. **Schema/data model issues.** - This is espcially important if the RFC cover's a new data model or schema. Check for missing fields, relationships, or constraints that would make the model incomplete or inconsistent with the stated requirements.
5. **Pipeline/consistency/ops architecture issues.**
6. **API/contract issues.**
7. **Missing details / questions a good architect would ask** — concise punch list.
8. Brief acknowledgment of what's genuinely good, for balance.

The architecture/systems review (items 3-7) is still the primary point of this skill and should get the most depth — the structural check in item 2 is a fast, secondary pass, not the main event.

Use `file:line` markdown links back into the actual RFC files for every specific finding so the user can jump to it. Be direct about severity — don't hedge a real architectural constraint into a vague "consider whether..." Only write findings to a file in the repo (e.g. alongside the RFC) if the user explicitly asks to persist the review.

## Notes

- This skill is deliberately adversarial/skeptical by design — the user has asked for this posture explicitly. Don't soften findings to be agreeable.
- If the RFC's technology claims turn out to hold up under research, say so — the goal is accuracy, not manufacturing criticism.
- Re-verify facts per RFC; don't assume conclusions from a past review of a different RFC apply here even if it used the same technology, since versions and context change.

