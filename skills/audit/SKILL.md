---
name: audit
description: Audit .claude/CLAUDE.md and .claude/LEARNINGS.md for structural rot, missing rule language (M1/M5/M7), missing privileged sections, stale entries, and broken cross-references. Dispatches the learnings-auditor agent.
when_to_use: |
  Use when the user says any of:
    "audit the learnings", "audit CLAUDE.md", "check the discipline",
    "validate the institutional memory", "lint the docs", "is LEARNINGS.md healthy",
    "are we still following the discipline", "review the .claude folder",
    "health check the docs".
  Also use proactively when the user has been editing .claude/CLAUDE.md or
  .claude/LEARNINGS.md by hand — after their changes, an audit catches drift.
allowed-tools: Agent, mcp__learnings__learnings_audit
disable-model-invocation: false
---

You are running the `/learn:audit` skill. Your job is to invoke the auditor agent and relay its findings.

## Steps

1. **Dispatch the auditor.** Invoke the `Agent` tool with subagent type `learnings-auditor`, no arguments. The agent reads `.claude/CLAUDE.md` and `.claude/LEARNINGS.md`, runs structural checks (M1/M5/M7 language present, required sections present, no H3 nesting, no stale dates, no broken `§N` cross-references), and may call `mcp__learnings__learnings_audit` for machine-checkable invariants.

2. **Wait for the agent's report.**

3. **Surface the report to the user verbatim.** Do not paraphrase or summarise — the auditor's wording is the deliverable. If the agent flagged auto-fixable issues and the user wants them applied, the user should re-run the relevant capture/init command or ask the auditor to apply fixes.

4. **Add a one-line meta-note** at the end indicating whether the audit found zero issues, warnings only, or errors that require action.

## Notes

This skill is read-only with respect to LEARNINGS.md and CLAUDE.md. It must not modify either file directly — that's the user's call after reading the report.
