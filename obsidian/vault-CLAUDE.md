# Vault conventions (read by Claude Code / Codex)

This folder is an Obsidian vault — a knowledge base of plain Markdown notes.
When you operate in this folder, follow these rules.

## Filenames
- Use kebab-case: `aws-lambda-cheatsheet.md`, not `AWS Lambda Cheatsheet.md`.
- One topic per note. Keep notes focused and short over long and sprawling.

## Note structure
- Every note starts with an H1 title (`# Title`) and a one-line summary under it.
- Use `##` sections. Prefer bullets and tables over walls of text.
- Fence code blocks with a language hint (```bash, ```python, etc.).

## Linking over duplicating
- Connect related notes with `[[wikilinks]]` instead of copy-pasting content.
- If a concept deserves its own note, create it and link to it.
- Use `#tags` for cross-cutting topics: `#aws`, `#devops`, `#claude-code`, `#bedrock`.

## Folders
- Organize by topic when a cluster grows. Don't pre-build empty folders.
- Capture first, organize later — a flat vault is fine early on.

## When editing
- Don't rewrite a note the user may be actively typing in. One writer at a time.
- Preserve existing `[[links]]` and `#tags` when restructuring a note.

## Out of scope
- This is a personal learning vault. No secrets, credentials, or company data here.
