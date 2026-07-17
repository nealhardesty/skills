---
name: reverse-spec-gen
description: Reverse-engineers an existing codebase into SPEC.md (technical architecture) and USERGUIDE.md (how to install, configure, and use it), grounded entirely in what the code and config actually show. Use when asked to document, write a spec for, or create a user guide for a system that has little or no existing documentation.
disable-model-invocation: true
---

# Reverse Spec Generator

Produce two documents from an existing, undocumented (or poorly documented) codebase:

- **`SPEC.md`** — technical architecture, for engineers who need to work in the system.
- **`USERGUIDE.md`** — how to install, configure, run, and use the system, for operators and end users.

**Every claim in both documents must trace back to something actually observed in the repository.** This is a reverse-engineering task, not a writing task — the failure mode is a plausible-sounding, generic document that doesn't match the real system. Where the codebase doesn't settle a question, write "Not determined from the codebase" rather than a guess. Don't infer a framework's version, a service name, or an architectural pattern from convention or training-data priors if the repo itself doesn't show it.

## Process

### Step 0 — Scope the target

Check for existing documentation in the project, read it thoroughly if found.

Check whether the repository is a monorepo or otherwise contains multiple independently deployable services/apps. If so, ask the user which one to document (or confirm they want the whole system covered as one umbrella) — guessing wrong here means redoing everything downstream.


Check whether `SPEC.md` and/or `USERGUIDE.md` already exist at the intended output location. If either does, confirm with the user before overwriting rather than clobbering existing docs silently.

For a large repository, don't try to read everything into context — delegate directory-by-directory or subsystem-by-subsystem exploration to an Explore-type agent and synthesize from its findings.

### Step 1 — Survey platform & environment

Find and *open* (not just list) configuration files: `package.json`, `pom.xml`, `requirements.txt`/`pyproject.toml`, `go.mod`, `docker-compose.yml`, `Dockerfile`, `.nvmrc`/`.tool-versions`, lockfiles. Pull actual version strings from them — don't state a version you didn't read.

### Step 2 — Map the architecture

Scan the root directory structure and identify the architectural pattern in use (monolith, microservices, MVC, hexagonal, event-driven, etc.) from what's actually there — module boundaries, service folders, message brokers — not from the pattern's name alone. Locate main entry points (`index.js`, `main.go`, `App.java`, etc.). Ignore vendor/build/generated directories (`node_modules`, `dist`, `vendor`, `.next`, target).

### Step 3 — Analyze data & integrations

Locate database schemas, ORM configs (Prisma, Hibernate, SQLAlchemy, etc.), or migration files. Identify third-party integrations, message queues, or external services from API client code and environment variable templates (`.env.example`, `.env.sample`).

### Step 4 — Trace the user-facing surface

This step feeds the User Guide specifically: find how someone actually installs, configures, runs, and uses the system. Look at `package.json` scripts, `Makefile` targets, Dockerfiles/compose files, CLI entry points, HTTP route definitions, and top-level UI pages/screens. If a README or other docs already exist, treat them as a lead to verify against the code, not a source to copy — existing docs are often stale.

### Step 5 — Draft `SPEC.md`

Audience: staff engineers and technical PMs. Precise terminology, objective tone. Use Mermaid.js diagram for the architecture.

1. **System Overview** — 2-3 paragraphs: what the system does and its primary technical stack.
2. **Platform & Dependencies** — runtime/language versions; core frameworks with versions; key infrastructure dependencies (e.g. Redis, PostgreSQL, Kafka), as a table.
3. **System Architecture** — the structural pattern in prose, plus a Mermaid.js diagram of component interactions.
4. **Core Directory Structure** — a brief tree map of the directories that matter and what they contain.
5. **Data Storage & Models** — primary database technologies and the core domain entities (e.g. User, Order, Payment).
6. **External Integrations** — third-party services the system depends on (e.g. Stripe, AWS S3, SendGrid).
7. **Build, Test & CI** — test frameworks in use, how tests are run, and any CI pipeline config found.
8. **Open Questions & Assumptions** — anything you could not determine from the codebase, and anything stated as an inference rather than a confirmed fact. Do not omit this section — a spec with no open questions from a reverse-engineering pass is a sign something was guessed instead of verified.

### Step 6 — Draft `USERGUIDE.md`

Audience: someone who needs to install, run, and use the system, but hasn't read the source. Plain, task-oriented language — favor imperative steps and concrete commands over architectural explanation (that belongs in `SPEC.md`).

1. **What This Does** — one short paragraph, plain language, no jargon.
2. **Prerequisites** — runtime versions, accounts, or external services needed before setup, as found in Step 1/3.
3. **Installation & Setup** — concrete steps, derived from and verified against actual scripts/commands (not copied from a possibly-stale README).
4. **Configuration** — environment variables as a table: name, purpose, required/optional, default if any.
5. **Running It** — the actual commands to start the system, ports, and entry URLs.
6. **Core Workflows** — the main things a user does with the system, derived from routes/CLI commands/UI screens found in Step 4.
7. **Troubleshooting** — include only if the codebase gives real evidence for it (explicit error handling, existing docs you've verified). Omit the section entirely rather than invent generic troubleshooting advice.

## Output

Write `SPEC.md` and `USERGUIDE.md` to the location confirmed in Step 0 (repo root by default).
