---
name: handoff-prompt
description: >
  Generates a structured handoff file to preserve session context across Claude Code sessions.
  Use this skill whenever the user wants to end a Claude Code session and continue later without
  losing context, says things like "wrap up this session", "save our progress", "create a handoff",
  "end session", "I'll continue later", or asks how to pick up where they left off in a new session.
  Also trigger when the user wants to resume work from a previous session and mentions a handoff file,
  or asks Claude to "read handoff and continue". This skill is essential for any multi-session
  development workflow in Claude Code.
---

# Handoff Prompt Skill

This skill handles two directions of a session handoff workflow in Claude Code:

1. **Ending a session**: Writing a timestamped `handoff_YYYYMMDDHHMMSS.md` that captures all context before clearing
2. **Starting a session**: Reading the most recent handoff file and resuming exactly where the last session left off

---

## When the user wants to END a session

Generate a handoff file in the project root. The filename must include a timestamp suffix using the current date and time at the moment of file creation, in the format `handoff_YYYYMMDDHHMMSS.md` — for example, `handoff_20260518143022.md`. Get the current timestamp from the system (e.g. via `date +%Y%m%d%H%M%S` in bash, or equivalent) — never hardcode or guess it.

The file must capture:

### File structure

```markdown
# Goal
[The single clear objective being worked toward — not a summary, the actual goal]

## Current State
[Where the work stands right now — what's working, what's broken, what's partial]

## Files in Flight
[List of files actively being modified, with a one-line note on what's being changed in each]

## Changed This Session
[What was actually touched/modified during this session — be specific, not vague]

## Failed Attempts
[Everything tried that didn't work, and specifically WHY it failed — this is critical, don't skip it]

## Next Step
[The single, concrete next action to take — not a list, not a plan, just the immediate next thing]
```

### Writing guidelines

- **Goal**: One sentence. What are we actually building or fixing?
- **Current State**: Be honest. If it's broken, say so and describe the symptom.
- **Files in Flight**: Paths relative to project root. One line per file.
- **Changed This Session**: Concrete, not vague. "Added auth middleware to routes/api.js" not "worked on auth".
- **Failed Attempts**: This section saves the most time. Be specific. "Tried X, failed because Y" — not just "tried X".
- **Next Step**: One thing. The very next thing to do. If there's uncertainty, state what needs to be figured out first.

### After writing the file

Tell the user the exact filename that was created, then say:

```
handoff_YYYYMMDDHHMMSS.md is ready. To continue in a fresh session:

1. Run /clear to end this session
2. In the new session, paste: Read handoff_YYYYMMDDHHMMSS.md and pick up from exactly where it left off.
```

(Replace YYYYMMDDHHMMSS with the actual timestamp in both places.)

---

## When the user wants to START from a handoff

If the user says something like "read handoff and continue" or starts a session referencing a handoff file:

1. If the user specifies a filename, read that file. If they don't, look for all `handoff_*.md` files in the project root and read the most recent one (highest timestamp).
2. Confirm you've loaded it by briefly stating: the filename, the goal, current state, and the next step.
3. Proceed immediately with the next step — do not ask for confirmation unless something is ambiguous or the next step is destructive.

Example confirmation format:
```
Picked up from handoff_20260518143022.md.
Goal: [goal]
State: [current state in one line]
Resuming: [next step]
```

Then get to work.

---

## Important notes

- The "Failed Attempts" section is the most valuable part of this file. A new session that skips it will repeat the same mistakes. Make sure it's thorough.
- If there are no failed attempts in the session, write "None this session" — don't omit the section.
- The file should be written to the project root, not a subdirectory.
- Keep the file human-readable. Another developer (or a future Claude session) should be able to skim it in under 60 seconds.
- The timestamp suffix means multiple handoff files can coexist safely. When resuming without a specific filename, always pick the most recent one.
