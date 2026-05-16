---
name: capture-failure
description: Record a failed approach in LEARNINGS.md so it isn't re-attempted later. Appends a (tried, failed because, mitigation) entry to the "Things that did NOT work and why" section.
when_to_use: |
  Use proactively when the user OR the assistant says any of:
    "that didn't work", "this approach failed", "we hit a dead end", "let's pivot",
    "abandoning this", "going to try something different", "stepping back",
    "different approach needed", "let's note this didn't work",
    "capture this as a failure", "this is a gotcha", "save this for posterity".
  Also use proactively when the assistant has just observed a failure pattern
  worth documenting — a misleading error message, a tool that silently no-ops,
  a config setting that breaks on macOS but not Linux. Before pivoting to a new
  approach, capture the failure.
argument-hint: "["one-line summary of what was tried"]"
allowed-tools: Read, mcp__learnings__learnings_capture_failure
disable-model-invocation: false
---

You are running the `/learn:capture-failure` skill. Your job is to append a well-formed failure entry to `.claude/LEARNINGS.md`.

## Steps

1. **Gather the three required fields.**
   - `tried`: a one-line description of the approach that failed. If the user supplied a quoted summary as an argument, use it verbatim. Otherwise infer it from recent conversation context (the last few turns: what command was run, what code was written, what plan was attempted).
   - `failed_because`: a one-line cause. Extract from recent context — error output, observed behaviour, why the model concluded it wouldn't work.
   - `mitigation`: a one-line "what to do instead" or "what we're trying next". Extract from the current plan or the user's next direction.

2. **Disambiguate if needed.** If any of the three fields cannot be confidently inferred from context, ask the user ONE consolidated clarifying question covering the missing fields. Do not write until you have all three.

3. **Read existing LEARNINGS.md** (optional but recommended) to make sure you aren't duplicating an entry that's already there. If a near-duplicate exists, mention it to the user and confirm they still want to add the new line.

4. **Call the MCP tool.** Invoke `mcp__learnings__learnings_capture_failure` with `{tried, failed_because, mitigation}`. The tool handles the formatting and appends to LEARNINGS.md §"Things that did NOT work and why".

5. **Confirm.** Report back:
   - The exact line that was added (as returned by the tool)
   - The section it landed in
   - The line number or anchor if the tool returns one

## Style

Keep each of the three fields to one line, present tense, concrete. Bad: "we had some issues with the auth flow". Good: "tried OAuth PKCE with a public client". The point of the entry is fast future-recognition, not narrative.
