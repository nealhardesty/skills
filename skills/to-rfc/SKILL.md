---
name: rfc-builder
description: Turn the current conversation into a complete RFC package — README.md, threat-model.md, readiness-review.md, and any warranted supplemental files, scaffolded into an rfcs/<name>/ folder. Synthesizes what's already been discussed, and asks targeted clarifying questions (sponsor, business case, owners, etc.) for anything required that's still missing, rather than guessing. Use when the user says things like "turn this into an RFC", "write up the RFC for this", "draft the RFC docs", or "build the RFC package".
disable-model-invocation: true
---

This skill produces a complete, process-conformant RFC package from the current conversation: the RFC document itself, its required threat model, its required readiness review, and any supplemental files the material actually warrants (diagrams.md, references.md, protobufs.md, poc-findings.md).

**This skill is self-contained.** All process knowledge, blank templates, and authoring guidance live in this skill's own directory (`templates/` and `reference/`, alongside this file) — it does not depend on network access or a live checkout of your organization's RFC repository to know what an RFC looks like. If a live checkout happens to be available, step 1 below makes best-effort, non-blocking use of it; if not, proceed entirely from the bundled files.

**Unlike a silent-synthesis skill: ask when something required is missing.** Don't blind-interview the user about everything — synthesize aggressively from what's already been said first. But for the specific, recurring gaps that this process treats as hard requirements (sponsor, business case, owners on risk/question rows), ask rather than fabricate. See `reference/conventions.md` → "Asking questions" for exactly what to ask and how to batch it.

## Process

### Step 0 — Determine where the package goes

Figure out the target folder before writing anything:

- If the current working directory (or a nearby parent) looks like a checkout of your organization's RFC repository (has a `templates/` dir with the same three template names, and/or an `rfcs/` dir), scaffold into `rfcs/<slug>/` there.
- Otherwise, ask the user where they want the package written. Don't assume.
- Derive `<slug>`: a short, lowercase, hyphenated name for the RFC's subject (e.g. `graph-correlation-service`, `notification-batching-service`), from the conversation topic. If it's ambiguous, or a folder with that name already exists with different content, confirm the name with the user rather than guessing.

### Step 1 — Load the bundled reference material

Read, from this skill's own directory (not from a remote repo):

- `templates/rfc-template.md`, `templates/threat-model-template.md`, `templates/readiness-review-template.md` — the blank templates to copy and fill in.
- `reference/conventions.md` — always read this one; it covers diagrams, supplemental-file conventions, cross-document consistency, the sponsor rule, evidence-grounding, and the question-asking policy.
- `reference/rfc-guide.md`, `reference/threat-model-guide.md`, `reference/readiness-review-guide.md` — read each as you get to drafting that specific document, not all at once.

Optional, best-effort, never blocking: if `gh` is available and your organization's RFC repository is reachable, you may spot-check whether the bundled templates have diverged from the live repo's `templates/` directory, and mention it to the user if so. Proceed with the bundled versions regardless — don't block on network access.

### Step 2 — Extract what the conversation already establishes

Before asking anything, go through the conversation and pull out whatever it already gives you: the problem, goals, architecture/design decisions already made (and their stated reasoning), constraints mentioned, risks or concerns already raised, alternatives already discussed and why they were rejected, any POC/spike results or measured data, any diagrams already produced, and anything said about rollout, cost, or ownership. Write these down as already-decided facts with their existing reasoning — do not re-derive or re-litigate a decision the conversation already made.

### Step 3 — Identify gaps, then ask one batched round of targeted questions

Compare what Step 2 gave you against the required fields called out in `reference/conventions.md` → "Asking questions" (sponsor, business case, rollback plan, accepted-deviation candidates, row owners, output location if unclear). Ask about real gaps only — don't ask about anything Step 2 already established, and don't ask one question at a time when several can be batched into a single message. If the user explicitly says to leave something as `TBD` or to proceed without an answer, honor that literally rather than substituting something plausible-sounding.

### Step 4 — Draft the three required documents

Using the loaded templates and per-document guides:

1. `README.md` from `templates/rfc-template.md` + `reference/rfc-guide.md`.
2. `threat-model.md` from `templates/threat-model-template.md` + `reference/threat-model-guide.md`. Do not skip this or leave it templated — it's a hard requirement for every RFC regardless of how "security-flavored" the topic feels.
3. `readiness-review.md` from `templates/readiness-review-template.md` + `reference/readiness-review-guide.md`. Let its Decision field be honestly nuanced (`Not Ready`, `Ready with Follow-ups`, etc.) — a freshly-drafted RFC being anything other than fully `Ready` everywhere is the normal, expected case, not a failure.

Then, only if the material genuinely warrants it (per `reference/conventions.md`'s supplemental-file table), add `diagrams.md`, `references.md`, `protobufs.md`, and/or `poc-findings.md`. Don't create an empty one just to appear thorough.

### Step 5 — Enforce cross-document consistency

Before presenting the result, check: does every mitigation named in the README's ROAM table read identically in the threat model's STRIDE table (and readiness review, where mirrored)? Does every diagram label match the final decisions, not a superseded draft of them? Is every risk/question/blocker row's owner a real name/role rather than blank or a rubber-stamped default? Is there any leftover template placeholder text (`YYYY-MM-DD`, blank required cells, unedited instructional prose) anywhere? Fix anything that fails this check before moving on.

### Step 6 — Write the files and report back

Write all files to the target folder from Step 0. Then tell the user, concisely:

- What was written (file list and location).
- What was synthesized directly from the conversation vs. what you asked about and got an answer for.
- What's still `TBD`/`Open`/`Blocked` and needs a human decision before this can go to PR (most commonly: a real sponsor).
- Suggested next step: open a PR against your organization's RFC repository (folder under `rfcs/`, `README.md`/`threat-model.md`/`readiness-review.md` named exactly as required), and consider running `/rfc-review` on the new folder for an adversarial pass before that PR goes up.
