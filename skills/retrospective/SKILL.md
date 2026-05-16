---
name: retrospective
description: Run a retrospective across all project artifacts (.claude/, patches/, root docs) and write a perspective doc recommending which mechanics should be promoted to LEARNINGS.md. Does NOT modify LEARNINGS.md directly.
when_to_use: |
  Use when the user says any of:
    "retro", "retrospective", "look back at this project", "what did we learn",
    "summarise the patterns", "synthesise the takeaways", "wrap-up",
    "end-of-project review", "extract what worked from this session".
  Also use proactively when a milestone is reached (a release tag, a major
  phase complete, the end of a long working session) and the user hasn't
  explicitly asked but the right move is to harvest patterns before moving on.
allowed-tools: Read, Bash, Grep, Glob, Write, mcp__learnings__thoughts_create, mcp__learnings__learnings_read
disable-model-invocation: false
---

You are running the `/learn:retrospective` skill. Your job is to harvest patterns and decisions from a project's artifacts and recommend which belong in LEARNINGS.md.

## Steps

1. **Enumerate inputs.** Use `Glob` and `Bash` to list:
   - All files under `.claude/` (CLAUDE.md, LEARNINGS.md, thoughts/, plans/, etc.)
   - All files under `patches/` if that directory exists
   - Root-level docs: `README.md`, `CHANGELOG.md`, `NOTES.md`, `TODO.md`, and any `*.md` at the repo root
   - Optionally, recent git log entries (`git log --oneline -50`) to spot themes

2. **Read each file.** Use `Read` for markdown files. For large files, read in chunks. Skim for: decisions made, things that worked, things that didn't, recurring patterns, recipes, and any "lesson" or "gotcha" language.

3. **Cross-reference with existing LEARNINGS.md.** Call `mcp__learnings__learnings_read` to load the current LEARNINGS.md sections. Anything already documented there should NOT be re-recommended — the goal is to find what's missing.

4. **Identify recurring patterns.** Group your findings into themes:
   - **Mechanics that worked** — process patterns, command sequences, workflows that showed up more than once
   - **Mechanics that failed** — dead ends, false starts, costly mistakes
   - **Decisions worth recording** — choices made with explicit rationale that future work will need to know
   - **Recipes** — concrete reproducible step-sequences

5. **Write the perspective file.** Use `mcp__learnings__thoughts_create` with `{kind: "perspective", slug: "retrospective"}` to allocate the next numbered slot (`.claude/thoughts/0N-retrospective.md`). The file should contain:
   - A short intro naming the time period and artifacts reviewed
   - One section per theme from step 4, with bullet points
   - A **"Recommended for LEARNINGS.md"** section at the end listing concrete proposed entries, each in the same shape as the existing LEARNINGS.md sections (heading + 1–3 paragraphs)
   - For each recommendation, cite the source artifact (file path + line range) that motivated it

6. **Report.** Print the file path of the retrospective and a one-sentence summary of how many recommendations it contains. Explicitly tell the user: *"I have NOT modified LEARNINGS.md. Review the recommendations and run `/learn:capture-win` or `/learn:capture-failure` for each one you want to promote."*

## Constraints

- Never write to `.claude/LEARNINGS.md` from this skill. The retrospective is advisory only.
- If `.claude/thoughts/` doesn't exist, create it first (`mkdir -p`).
- If the project has no artifacts beyond the bare scaffold, say so — don't manufacture findings.
