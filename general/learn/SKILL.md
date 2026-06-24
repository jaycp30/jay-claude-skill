---
name: learn
description: On-demand teaching and quiz mode. Invoke with /learn to make sure I actually understand a piece of work, not just that it shipped. Use at the end of a session to review what we built, or point it at a target ("/learn the auth flow", "/learn what we changed today"). Also trigger when I ask to be taught, quizzed, walked through, or to have something explained so I can explain it myself afterward. The goal is retention and being able to defend the decisions later.
---

# /learn

A focused teaching pass. Unlike ambient teaching, this runs because I asked for it, so go thorough: actually verify understanding, do not just narrate.

Source: adapted from a prompt shared by Thariq (x.com/trq212) and Suzanne at Anthropic.

## Pick the target

- If I named something after `/learn` (a file, a feature, a concept, "what we changed today"), focus there.
- If I gave nothing, default to the work completed in the current session.
- If it is genuinely unclear what I want reviewed, ask one short question, then proceed.

## How to run it

Work through it in stages, not one dump at the end. Before moving to the next stage, confirm I have actually mastered the current one. Cover both the high level (motivation, why it matters) and the low level (business logic, edge cases).

Start by finding out where I already am. Ask me to restate my own understanding first, then fill the gaps from there. Do not lecture into a vacuum. Let me ask questions, and support eli5, eli14, and elii (explain like I'm an intern) when I ask for them.

## What I should walk away understanding

Keep a running checklist (an `understanding.md` in the repo for anything substantial) covering:

1. **The problem**, what it was, why it existed, and the different approaches that were on the table.
2. **The solution**, why it was resolved this way, the design decisions, the tradeoffs, and the edge cases.
3. **The broader context**, why this matters and what the changes will impact downstream.

Drill into the why, and keep drilling into deeper whys. Make sure I get the what and the how too. Understanding the problem clearly matters most, so do not let it stay vague.

## Quiz, do not assume

Verify instead of taking "got it" at face value. Mix open-ended and multiple-choice questions, grounded in the specific work, not generic trivia. For multiple choice:

- Vary which position the correct answer is in. Do not let it always be the same option.
- Do not reveal the answer until I have committed to mine.
- Pull in real artifacts when it helps: show the actual code, or have me step through it with the debugger.

## When to stop

Do not call this done until I have demonstrated, not just claimed, that I understand everything on the checklist. If something is shaky, stay on it. Close by noting anything still worth reviewing later.
