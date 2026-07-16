# skills

A collection of generic [Claude Code](https://claude.com/claude-code) agent skills for working through the lifecycle of a feature or design: stress-testing a plan, writing it up as a PRD or RFC, critically reviewing an RFC, and handing off a session to a fresh agent.

## Installation

### Via Claude Code's plugin marketplace

This repo is also a [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) — each skill below is published as its own plugin, so you can install only the ones you want.

Add the marketplace once:

```sh
claude plugin marketplace add nealhardesty/skills
```

Then install whichever skills you want (plugin names match the `Skill` column below):

```sh
claude plugin install grill@nealhardesty-skills
claude plugin install to-prd@nealhardesty-skills
claude plugin install to-rfc@nealhardesty-skills
claude plugin install rfc-review@nealhardesty-skills
claude plugin install session-handoff@nealhardesty-skills
claude plugin install humanize@nealhardesty-skills
```

To update later: `claude plugin marketplace update nealhardesty-skills`. To remove a skill: `claude plugin uninstall <name>`.

### Via `skills` (npx)

Alternatively, install with [`skills`](https://www.npmjs.com/package/skills):

```sh
npx skills@latest add nealhardesty/skills
```

You can also install a single skill by pointing at its subfolder, e.g.:

```sh
npx skills@latest add nealhardesty/skills/skills/grill
```

Run `npx skills@latest --help` for other options (updating, listing what's installed, etc.).

## Skills

| Skill | Trigger | What it does |
|---|---|---|
| [`grill`](skills/grill) | "grill me on this plan", stress-testing a design before building | Interviews you relentlessly, one question at a time, about every branch of a plan or design, offering a recommended answer for each and exploring the codebase or industry standards where relevant, until you reach shared understanding. |
| [`to-prd`](skills/to-prd) | "turn this into a PRD" | Synthesizes the current conversation (no interview) into a PRD — problem statement, solution, extensive user stories, implementation/testing decisions, out of scope — and publishes it to the project's issue tracker with a `ready-for-agent` label. |
| [`to-rfc`](skills/to-rfc) (`rfc-builder`) | "turn this into an RFC", "draft the RFC docs" | Turns a conversation into a complete, process-conformant RFC package (`README.md`, `threat-model.md`, `readiness-review.md`, plus warranted supplemental files like diagrams/references/protobufs) scaffolded into `rfcs/<slug>/`. Synthesizes aggressively from what's already been discussed and only asks about genuine hard-requirement gaps (sponsor, business case, row owners). Fully self-contained — bundles its own templates and guides. |
| [`rfc-review`](skills/rfc-review) | "review this RFC", "what's wrong with this RFC" | Deep, adversarial review of an RFC: checks structural completeness against the template, verifies every non-trivial technology/vendor claim against outside research (not training-data recall), and critiques the architecture, schema, and API for inconsistencies, missing lifecycle/DR stories, and trust-boundary gaps. |
| [`session-handoff`](skills/session-handoff) (`handoff`) | "hand this off", "write a handoff doc" | Compacts the current conversation into a dated handoff document for another agent to pick up — references existing artifacts (PRDs, plans, issues) instead of duplicating them, and redacts sensitive information. |
| [`humanize`](skills/humanize) | "humanize this doc", "de-AI-ify this", "clean this up" | Rewrites a document to strip AI-generated tells (filler vocabulary, summary sandwiches, uniform sentence rhythm), compresses redundant prose, and fixes common errors (grammar, inconsistent terminology, broken Markdown, dangling references) — while preserving every fact and any mandated structure or compliance terminology (RFC 2119, ISO clauses, legal boilerplate). |

## Contributing

Adding or changing a skill? See [AGENTS.md](AGENTS.md) — every skill's identity is duplicated across `SKILL.md`, `plugin.json`, `marketplace.json`, and this README, and all of them must be updated in the same change.

## License

See [LICENSE](LICENSE).
