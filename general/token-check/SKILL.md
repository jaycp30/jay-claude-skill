---
name: token-check
description: Use when the user asks /token-check, token-check, token usage, prompt tokens, context usage, or wants an estimate of how many tokens their recent prompt/conversation used.
---

Estimate token usage for the most recent exchange, including both:

- User input tokens: the prompt I sent, plus any pasted content, files, images, prior context, or tool output that likely had to be read to answer.
- Claude response/output tokens: the visible answer you produced, plus any substantial hidden reasoning/work implied by the task when it materially affects usage.

Be clear that this is an estimate, not exact billing telemetry. Give approximate ranges where useful, name the biggest token drivers, and suggest concrete ways to reduce token use next time.

If this is running in Claude Code, remind me that `/cost` is the source for actual current-session token/cost usage when available.

