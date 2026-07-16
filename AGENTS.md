# Repository guide for agents

This repo is a collection of Claude Code agent skills, published two ways: as an [npx `skills`](https://www.npmjs.com/package/skills) source, and as a [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces). Each skill lives in `skills/<name>/` with a root `SKILL.md`.

## Registry sync is mandatory for every skill change

A skill's identity is duplicated across several files by design (npx and the plugin marketplace read different manifests). Any time you **add, rename, remove, or meaningfully change** a skill, update all of these in the *same* change — a skill that exists in one registry but not another is a bug:

1. **`skills/<name>/SKILL.md`** — the skill itself. `name` and `description` in the frontmatter are the source of truth; keep them consistent with everything below.
2. **`skills/<name>/.claude-plugin/plugin.json`** — `name` (must equal the folder name and the SKILL.md `name`), `description`, and `version`. Bump `version` on a meaningful change.
3. **`.claude-plugin/marketplace.json`** — the `plugins` array needs an entry with matching `name`, `source: "./skills/<name>"`, and `description`.
4. **`README.md`** — both places: the `claude plugin install <name>@nealhardesty-skills` line in the install section, and a row in the Skills table.

Descriptions should read consistently across these files (they can be worded for their context, but must not contradict each other). After any change, run:

```sh
claude plugin validate .
```

**`claude plugin validate` does NOT check `SKILL.md` frontmatter YAML.** The `npx skills` tool parses it strictly and *silently skips* any skill whose frontmatter won't parse — so a skill can pass plugin validation yet be invisible to `npx skills add`. Keep the frontmatter valid YAML: in particular, an unquoted `description` value must not contain a colon-space (`: `), which the parser reads as a nested mapping. Use an em-dash or reword instead. To verify a skill parses, confirm it appears in:

```sh
npx skills@latest add nealhardesty/skills --list
```

Removing a skill means deleting its folder **and** its entries in `marketplace.json` and both README locations. Renaming means doing both at once, keeping the folder name, `plugin.json` name, `marketplace.json` name, and SKILL.md `name` identical.

## Skill authoring conventions

- Write instructions **to the agent** in the imperative ("Read the file," "Scan for…") — not as a persona or system prompt ("You are a…"). Match the existing skills.
- Set `disable-model-invocation: true` for skills that should only run on explicit `/<name>` invocation (e.g. ones that write files or publish). Leave it unset for skills safe to auto-trigger from natural phrasing.
- Keep `SKILL.md` tight — no filler. A bloated skill undercuts its own advice, especially the `humanize` skill.
- Folder names, `name` fields, and the marketplace entry are all kebab-case and must match.
