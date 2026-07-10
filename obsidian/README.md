# Obsidian + AI coding agents

Notes and how-to for using Obsidian as a Markdown knowledge base that AI coding agents
(Claude Code, Codex, opencode) can read and reference.

## Contents
- [obsidian-for-claude-codex.md](obsidian-for-claude-codex.md) — full setup + upskilling guide (install → vault → GitHub).
- [obsidian-skills.md](obsidian-skills.md) — the 4 Claude skills installed with the vault (markdown, bases, canvas, CLI).
- [obsidian-glossary.md](obsidian-glossary.md) — Obsidian / PKM acronyms and terms.
- [vault-CLAUDE.md](vault-CLAUDE.md) — example house-rules file for a vault.
- [welcome.md](welcome.md) — example vault home note.

---

## How to point Claude Code / Codex / opencode at an Obsidian vault

An Obsidian vault is just a folder of `.md` files, so any agent can use it once it has
**(1) filesystem access to the path** and **(2) an instruction to consult the vault.**
Tools differ only in which instruction file they auto-read.

### Instruction-file map

| Tool | Auto-reads | Where |
|------|------------|-------|
| Claude Code | `CLAUDE.md` | cwd + parent dirs, plus `~/.claude/CLAUDE.md` (global) |
| Codex | `AGENTS.md` | cwd + parents, plus `~/.codex/AGENTS.md` (global) |
| opencode | `AGENTS.md` (also `CLAUDE.md`) | cwd + parents, plus `~/.config/opencode/AGENTS.md` |

Tip: keep one file and symlink the other so they never drift:
```bash
cd /path/to/vault
ln -s CLAUDE.md AGENTS.md   # Codex/opencode now read the same rules as Claude
```

### Pattern A — work *inside* the vault (simplest)
Launch the agent from the vault folder; it becomes the project root and auto-reads the
instruction file there:
```bash
cd ~/obsidian-vaults/<vault>
claude        # or: codex        # or: opencode
```

### Pattern B — reference the vault *from a code project*

**Claude Code** — grant access, then instruct:
```bash
claude --add-dir ~/obsidian-vaults/<vault>      # one-off
```
or persist it in `~/.claude/settings.json`:
```json
{
  "permissions": {
    "additionalDirectories": ["/Users/you/obsidian-vaults/<vault>"]
  }
}
```
Then add to the project's `CLAUDE.md` (or global `~/.claude/CLAUDE.md`):
> For my conventions and past solutions, consult `~/obsidian-vaults/<vault>` —
> start at `index.md` and the `moc-*.md` notes; read specific notes on demand.

**Codex** — add the pointer to the global `~/.codex/AGENTS.md`:
```markdown
## Knowledge base
Consult ~/obsidian-vaults/<vault> (start at index.md / moc-*.md) for conventions and past work.
```
Codex is sandboxed; to read a path outside the workspace, run with appropriate access
(`codex --sandbox workspace-write` with the dir added) or approve the read when prompted.

**opencode** — in `opencode.json` (project or `~/.config/opencode/`), load entry-point notes:
```json
{ "instructions": ["~/obsidian-vaults/<vault>/index.md", "~/obsidian-vaults/<vault>/moc-*.md"] }
```

### The one rule that matters for all three
**Don't make the agent ingest the whole vault.** Point it at `index.md` + `moc-*.md`
(Maps of Content) as entry points and let it open individual notes on demand. Globbing
`**/*.md` into context will blow the context window and cost you on every turn — the MOC
structure exists precisely to give the agent a small front door.

### Keeping private notes private
If a vault holds client/work or personal data, keep it in its **own local folder with no
git remote** (no `git init`, or a separate private repo). Never register a private vault
path in a settings file that gets committed to a public repo.
