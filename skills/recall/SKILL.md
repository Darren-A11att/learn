---
name: recall
description: Search the project's institutional memory (LEARNINGS.md) for prior knowledge on a topic. Returns matching section headings, first paragraphs, and line ranges so the model can cite them rather than re-deriving.
when_to_use: |
  **Use proactively before responding to any non-trivial technical question** to check whether the topic is already documented. The plugin's M5 rule ("cite, don't re-derive") depends on this skill firing on its own.
  
  Specifically use when the user asks any of:
    "what do we know about X", "have we hit this before", "didn't we deal with Y",
    "search the learnings", "find prior knowledge on Z", "is there a section on W",
    "look this up in LEARNINGS", "check institutional memory", "any prior art".
  
  Also use proactively whenever a user prompt touches a topic that appears in
  .claude/CLAUDE.md's trigger-keyword list — even if the user didn't explicitly
  ask to search. Better to look and find nothing than to silently re-derive
  something the project already knows.
argument-hint: "<topic or keyword>"
allowed-tools: mcp__learnings__learnings_relevant_sections, mcp__learnings__learnings_read
disable-model-invocation: false
---

You are running the `/learn:recall` skill. Your job is to find LEARNINGS.md sections relevant to a topic and surface them to the user with a recommendation.

## Steps

1. **Take the topic argument.** It may be a phrase, a keyword, or a symptom description. If empty, ask the user for one.

2. **Query the matcher.** Call `mcp__learnings__learnings_relevant_sections` with `{query: <topic>, max_results: 5}`. The tool returns matches ranked by relevance, each with: heading, first paragraph, line range, and a relevance score.

3. **Handle zero results.** If the matcher returns nothing, say so plainly and suggest the user check whether the project's trigger-keyword list in CLAUDE.md covers this topic. Do not fabricate matches.

4. **Display the matches.** For each match, render:
   - The H2 heading
   - The first paragraph verbatim
   - The line range (e.g. `.claude/LEARNINGS.md:42-58`)
   - The relevance score if available

5. **Recommend the most relevant section** with a one-sentence justification explaining why it's the best match for the user's topic. If the top two are close in relevance, mention both.

6. **Offer to read more.** End with a one-line offer: "Reply with the section name and I'll read it in full" (which would call `mcp__learnings__learnings_read` to fetch the full section body).

## Notes

This skill is read-only. Never append to or modify LEARNINGS.md from here.
