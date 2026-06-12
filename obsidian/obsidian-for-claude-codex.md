# Obsidian for Claude Code / Codex — Setup & Upskilling Notes

> Personal learning notes. Goal: use Obsidian as a Markdown knowledge base that
> AI coding agents (Claude Code, Codex) can read and write directly.
> Author: jaycp30 · Started: 2026-06-12 · Platform: macOS (Apple Silicon/Intel)

---

## 0. Why Obsidian + Claude/Codex

- An Obsidian **vault is just a folder of plain `.md` files** on disk. No database, no lock-in.
- Because it's plain text on the filesystem, an AI agent pointed at that folder can **read, search, create, and edit your notes** the same way it edits code.
- Workflow that results: you keep your knowledge base in Obsidian for reading/linking/graphing, and let Claude Code do the heavy lifting — drafting notes, restructuring, summarizing, linking related ideas.
- It's Markdown, so it pushes to GitHub cleanly and renders in any editor.

**Mental model:** Obsidian = the human-friendly reader/editor. Claude Code = the agent that operates on the same files. The vault folder is the shared contract.

---

## 1. Installation (macOS)

### Step 1.1 — Check whether Obsidian is already installed
```bash
ls /Applications | grep -i obsidian
```
- **What it does:** lists everything in `/Applications` and filters for "obsidian" (case-insensitive).
- **If it prints `Obsidian.app`** → already installed, skip to Section 2.
- **If it prints nothing** → not installed, continue.

### Step 1.2 — Check whether Homebrew is available
```bash
which brew
```
- **What it does:** prints the path to `brew` if it's on your `PATH`, nothing if it isn't.
- Homebrew is the cleanest install path on macOS for a DevOps person — versioned, scriptable, easy to uninstall.

### Step 1.3a — Install via Homebrew (preferred)
```bash
brew install --cask obsidian
```
- **What it does:** downloads and installs the Obsidian desktop app as a "cask" (a GUI app, vs. a CLI formula).
- `--cask` is required because Obsidian is a graphical app, not a command-line tool.

### Step 1.3b — Install Homebrew first, if `which brew` returned nothing
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
- **What it does:** runs the official Homebrew installer script.
- `curl -fsSL` = fail on errors (`-f`), silent progress (`-s`), still show errors (`-S`), follow redirects (`-L`).
- After it finishes, follow the on-screen note about adding brew to your `PATH`, then re-run Step 1.3a.

> **Alternative (no Homebrew):** download the `.dmg` from <https://obsidian.md/download> and drag the app into Applications. Homebrew is recommended only because it keeps installs reproducible.

### Step 1.4 — Confirm the install
```bash
ls /Applications | grep -i obsidian
```
- Re-run the same check. You should now see `Obsidian.app`.

---

## 2. Create your first vault

A **vault** is the root folder of your notes. You can have many vaults; each is independent.

### Step 2.1 — Decide where it lives
For a GitHub-backed learning vault, keep it somewhere you control and can `git init`:
```bash
mkdir -p ~/obsidian-vaults/learning
```
- **What it does:** creates the folder (`-p` makes parent dirs as needed and won't error if it already exists).

### Step 2.2 — Open it as a vault
1. Launch Obsidian (Spotlight → "Obsidian", or `open -a Obsidian` in the terminal).
2. On the start screen choose **"Open folder as vault"**.
3. Select `~/obsidian-vaults/learning`.

Obsidian creates a hidden `.obsidian/` folder inside it for config (themes, plugins, hotkeys). Everything else is your notes.

### Step 2.3 — See the structure from the terminal
```bash
ls -la ~/obsidian-vaults/learning
```
- **What it does:** shows all files including the hidden `.obsidian/` config dir (`-a` = show hidden, `-l` = long/detailed).

---

## 3. Core concepts (the 20% that gives 80%)

| Concept | What it is | How to do it |
|---|---|---|
| **Note** | One `.md` file | `Cmd+N` for a new note |
| **Internal link** | A link between notes | Type `[[` then the note name |
| **Tag** | Inline label for grouping | Write `#devops` anywhere in a note |
| **Folder** | Plain directory | Right-click sidebar → New folder |
| **Backlinks** | "What links here" panel | Auto-shown at bottom of each note |
| **Graph view** | Visual map of links | `Cmd+G` |
| **Command palette** | Run any action | `Cmd+P` |
| **Quick switcher** | Jump to any note fast | `Cmd+O` |

**Key idea — linking over filing:** Don't obsess over folders. Capture notes and connect them with `[[links]]`. The graph and backlinks reveal structure you didn't plan. This is the "Zettelkasten" philosophy Obsidian is built around.

### Markdown you'll use daily
```markdown
# H1 title
## H2 section
- bullet
1. numbered
**bold**  *italic*  `inline code`
> blockquote
[[Another Note]]        <- internal link
[external](https://x)   <- web link
#tag
- [ ] todo item
- [x] done item
```
Fenced code blocks (triple backticks) with a language hint give syntax highlighting:
````markdown
```bash
echo "hello"
```
````

---

## 4. Connecting Obsidian to Claude Code / Codex

This is the payoff. The agent operates on the vault folder.

### Step 4.1 — Point the agent at the vault
Open the vault folder as the working directory for your agent:
```bash
cd ~/obsidian-vaults/learning
claude
```
- **What it does:** starts Claude Code with the vault as its project root, so it can read/write your notes.
- For Codex, point it at the same folder the same way.

### Step 4.2 — Things you can now ask the agent to do
- "Create a note `aws-lambda-cheatsheet.md` summarizing my Lambda gotchas."
- "Read all notes tagged `#bedrock` and create an index note linking them."
- "Find notes that mention IAM and add `[[IAM]]` links between them."
- "Rewrite `messy-meeting-notes.md` into a clean structured note."

### Step 4.3 — Keep agent and human in sync
- After the agent edits files, Obsidian picks up changes **live** — no refresh needed.
- If you edit in Obsidian while the agent reads, that's fine too; it's just files.
- **Watch out:** don't have the agent rewrite a note while you're typing in it — last write wins. Coordinate one at a time.

### Step 4.4 — Optional: a vault `CLAUDE.md` for house rules
Create a `CLAUDE.md` at the vault root telling the agent your conventions:
```markdown
# Vault conventions
- New notes go in the matching topic folder; create one if missing.
- Use kebab-case filenames: aws-lambda-cheatsheet.md
- Every note starts with an H1 title and a one-line summary.
- Link related notes with [[wikilinks]], don't duplicate content.
- Use #tags for cross-cutting topics (#aws, #devops, #claude-code).
```
- The agent reads this automatically and follows it — like a style guide for your notes.

---

## 5. Back up / publish to GitHub

Since the vault is plain files, version it like any repo.

### Step 5.1 — Initialize git in the vault
```bash
cd ~/obsidian-vaults/learning
git init
```

### Step 5.2 — Ignore Obsidian's local-only cruft
Create a `.gitignore` with:
```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```
- **Why:** `workspace.json` stores which panes you had open — noise in git. Keep the rest of `.obsidian/` so themes/plugins travel with the repo.

### Step 5.3 — First commit and push
```bash
git add .
git commit -m "docs: initial Obsidian learning vault"
gh repo create obsidian-learning --private --source=. --push
```
- **`gh repo create`:** makes the GitHub repo and pushes in one step (uses the `gh` CLI). `--private` keeps it private; `--source=.` uses the current folder; `--push` pushes immediately.
- Per your account rules, this is a personal project → use the **`jaycp30`** GitHub account.

> **Note:** Obsidian also has an official **Sync** service (paid) and a community **Git plugin** that auto-commits. For a DevOps person, plain `git` + `gh` is the most transparent and is what I'd start with.

---

## 6. Upskilling path (suggested order)

1. **Week 1 — Capture habit.** Make a daily note. Dump anything you learn. Don't organize yet.
2. **Week 1 — Linking.** Start connecting notes with `[[ ]]`. Open the graph (`Cmd+G`) and watch it grow.
3. **Week 2 — Templates.** Learn the core **Templates** plugin for repeatable note shapes (meeting note, cheatsheet, troubleshooting log).
4. **Week 2 — Agent loop.** Point Claude Code at the vault and have it summarize/restructure a messy note. Feel the workflow.
5. **Week 3 — Tags + MOCs.** Build "Maps of Content" — index notes that link out to a topic's notes (e.g. `AWS.md` linking all AWS notes).
6. **Week 3 — Backup.** Get the GitHub flow solid (Section 5).
7. **Ongoing — Plugins.** Add only when you feel a real need (see Section 7).

---

## 7. Plugins worth knowing (don't install all at once)

**Core (built-in, just enable):**
- **Templates** — insert boilerplate into new notes.
- **Daily notes** — one note per day, great for a work log.
- **Backlinks / Outgoing links** — see connections.

**Community (Settings → Community plugins → Browse):**
- **Dataview** — query your notes like a database (e.g. "list all notes tagged #todo"). Powerful but learn it later.
- **Templater** — supercharged templates with scripting.
- **Git** — auto-commit/sync the vault on a schedule.

> Rule of thumb: every plugin is maintenance surface. Add one, use it for a week, then decide.

---

## 8. Daily-driver cheat sheet (keyboard)

| Action | Shortcut |
|---|---|
| New note | `Cmd+N` |
| Quick switch to a note | `Cmd+O` |
| Command palette | `Cmd+P` |
| Graph view | `Cmd+G` |
| Search all notes | `Cmd+Shift+F` |
| Toggle edit/preview | `Cmd+E` |
| Insert internal link | type `[[` |

---

## 9. Quick troubleshooting

- **"Obsidian won't open — unidentified developer":** right-click the app → Open → confirm. Or `xattr -dr com.apple.quarantine /Applications/Obsidian.app`.
- **Changes not showing:** Obsidian is file-based; if a note looks stale, click another note and back. Confirm you opened the right folder as the vault.
- **Lost the vault:** it's just the folder you picked. `ls ~/obsidian-vaults/` to find it.

---

## 10. Summary

1. Install with `brew install --cask obsidian`.
2. Make a folder, open it as a vault.
3. Learn 7 things: note, `[[link]]`, `#tag`, search, graph, command palette, quick switcher.
4. Point Claude Code / Codex at the vault folder — now an agent can manage your notes.
5. `git init` + `gh repo create` to back it up on GitHub (`jaycp30` account).
6. Build the capture habit first; add plugins only when you feel the need.
