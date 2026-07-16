---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work. Save the work in the current directory in one file named `session-handoff-YYYYMMDD-HHMM.md`, with the date/time markers replaced by the actual current date and time.

Before writing, search the repo (open PRs/issues, an `rfcs/` directory, recent commits, existing PRDs or plans) for artifacts already produced during this work. Do not duplicate content already captured there — reference it by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.
