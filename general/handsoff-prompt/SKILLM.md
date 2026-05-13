---
name: handoff-prompt
description: >
  Generates a structured handoff.md file to preserve session context across Claude Code sessions.
  Use this skill whenever the user wants to end a Claude Code session and continue later without
  losing context, says things like "wrap up this session", "save our progress", "create a handoff",
  "end session", "I'll continue later", or asks how to pick up where they left off in a new session.
  Also trigger when the user wants to resume work from a previous session and mentions a handoff.md
  file, or asks Claude to "read handoff.md and continue". This skill is essential for any multi-session
  development workflow in Claude Code.
---

# Handoff Prompt Skill

This skill handles two directions of a session handoff workflow in Claude Code:

1. **Ending a session**: Writing a `handoff.md` that captures all context before clearing
2. **Starting a session**: Reading `handoff.md` and resuming exactly where the last session left off

---

## When the user wants to END a session

Generate a `handoff.md` file in the project root. The file must capture:

### handoff.md Structure

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

Tell the user:

```
handoff.md is ready. To continue in a fresh session:

1. Run /clear to end this session
2. In the new session, paste: Read handoff.md and pick up from exactly where it left off.
```

---

## When the user wants to START from a handoff

If the user says something like "read handoff.md and continue" or starts a session referencing a handoff file:

1. Read `handoff.md` from the project root
2. Confirm you've loaded it by briefly stating: the goal, current state, and the next step
3. Proceed immediately with the next step — do not ask for confirmation unless something is ambiguous or the next step is destructive

Example confirmation format:
```
Picked up from handoff.md.
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
