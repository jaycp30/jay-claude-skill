---
name: token-check
description: Use when the user asks /token-check, token-check, token usage, prompt tokens, context usage, or wants an estimate of how many tokens their recent prompt/conversation used.
---

When invoked, estimate the token usage of the user's most recent prompt and the recent exchange.

Be clear that this is an estimate, not exact billing telemetry. Explain the main contributors to token usage, such as long pasted text, files, images, prior context, tool outputs, or detailed instructions.

If the user is using Claude Code, remind them that `/cost` is the source for actual current-session usage when available.

Output:
- Estimated prompt size: short / medium / large, with approximate token range if possible
- Main token drivers
- Suggestions to reduce token use next time
