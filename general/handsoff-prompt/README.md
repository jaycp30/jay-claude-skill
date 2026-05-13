# handoff-prompt

A Claude Code skill for preserving session context across conversations. Writes a structured
`handoff.md` file when ending a session, and reads it to resume exactly where you left off.

---

## The problem it solves

Claude Code sessions don't persist. When you clear a session or hit context limits mid-project,
you lose everything -- what you were building, what failed, what the next step was. Without a
structured handoff, the next session starts cold and either repeats mistakes or asks you to
re-explain the whole situation.

This skill makes session continuity a two-command workflow.

---

## How to use it

### Ending a session

Tell Claude Code any of the following:

```
Wrap up this session
Create a handoff
Save our progress
End session
I'll continue later
```

Claude will write a `handoff.md` in your project root, then tell you to run `/clear`.

### Starting from a handoff

Open a fresh Claude Code session and paste:

```
Read handoff.md and pick up from exactly where it left off.
```

Claude reads the file, confirms what it loaded (goal, state, next step), and gets straight
to work without asking you to re-explain anything.

---

## What handoff.md captures

```markdown
# Goal
The single objective being worked toward

## Current State
Where the work stands right now -- what's working, broken, or partial

## Files in Flight
Active files being modified and what's changing in each

## Changed This Session
What was actually touched this session, with specifics

## Failed Attempts
Everything tried that didn't work, and specifically WHY -- this is the most
valuable section for a fresh session

## Next Step
The single concrete next action to take
```

---

## Why "Failed Attempts" matters most

It's the section that saves the most time. A new session without it will repeat the same
dead ends. The skill explicitly instructs Claude to document *why* something failed, not
just *what* was tried.

---

## Install

Download `handoff-prompt.skill` and install it via Settings > Skills in Claude Code.

Or copy `SKILL.md` into your skills directory manually as `handoff-prompt/SKILL.md`.

---

## Trigger phrases

The skill activates on:

- `/handoff`
- "wrap up this session"
- "create a handoff"
- "save our progress"
- "end session"
- "I'll continue later"
- "read handoff.md and continue"
- "pick up where we left off"
