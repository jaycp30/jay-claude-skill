# Obsidian skills (installed with the vault)

The Obsidian vault tooling ships **four** Claude Code skills. On this machine they're
installed **loose** under `~/.claude/skills/<name>/SKILL.md` — not as an `obsidian-vault`
plugin package (there's none under `~/.claude/plugins/`). Claude auto-loads a skill when
your request matches its description; each is also invocable directly as `/<skill-name>`.

| Skill | `/command` | Handles | Fires when you mention… |
|-------|-----------|---------|-------------------------|
| [obsidian-markdown](#obsidian-markdown) | `/obsidian-markdown` | Writing `.md` notes in Obsidian syntax | wikilinks, callouts, frontmatter, tags, embeds |
| [obsidian-bases](#obsidian-bases) | `/obsidian-bases` | Building `.base` database views over notes | Bases, table/card views, filters, formulas |
| [json-canvas](#json-canvas) | `/json-canvas` | Building `.canvas` visual boards | Canvas, mind maps, flowcharts, nodes/edges |
| [obsidian-cli](#obsidian-cli) | `/obsidian-cli` | Driving a running Obsidian app from the terminal | read/create/search notes, tasks, plugin dev |

Each skill has a `references/` folder with the long-form tables. The sections below are the
short version.

**Two things worth knowing:**
- **`json-canvas` is Obsidian-*adjacent*, not Obsidian-only.** JSON Canvas is an
  [open spec](https://jsoncanvas.org/spec/1.0/) that happens to power Obsidian's Canvas
  feature, so it works on any `.canvas` file. Counting strictly "Obsidian-branded" skills
  it's **3**; counting the full vault-tooling set it's **4**.
- **`obsidian-cli` is the only one with a runtime dependency.** It needs Obsidian actually
  running and the `obsidian` CLI on your `PATH`. The other three are pure file-format skills
  that work on `.md` / `.base` / `.canvas` files with no app open — so "installed" ≠ "works
  out of the box" for the CLI specifically.

## How you actually use them

You mostly **don't invoke them by name.** Two paths:

1. **Just ask in plain language** while working in a vault. Say *"make a reading-list Base for
   my `#book` notes"* or *"turn these five bullets into a Canvas mind map"* and Claude
   auto-loads the matching skill from its description. This is the normal path.
2. **Type `/<skill-name>`** to force it — e.g. `/obsidian-bases` — when you want to be explicit
   or the auto-match didn't fire.

For your setup, run Claude from inside the vault so it has the context:
`cd ~/obsidian-vaults/work && claude` (see the [README](README.md) for referencing a vault
from another project). Each section below gives a **When to use it** trigger and a **full,
paste-able example** — not just fragments.

---

## obsidian-markdown

**What it is:** the authoring skill for Obsidian Flavored Markdown — the extras Obsidian
adds on top of normal Markdown. Standard Markdown (headings, bold, lists, tables) is assumed;
this skill only covers the Obsidian-specific bits.

**Cheatsheet:**

```markdown
[[Note Name]]                 internal link (Obsidian tracks renames)
[[Note#Heading|Label]]        link to a heading, custom display text
![[Note]]  ![[image.png|300]] embed a note / image at 300px wide
> [!warning] Title            callout (note, tip, warning, info, bug, todo…)
==highlight==   %%hidden%%     highlight / comment
#tag   #nested/tag            tags (also settable in frontmatter)
```

Frontmatter properties go at the top between `---` fences (`tags`, `aliases`, `cssclasses`).
Rule of thumb: `[[wikilinks]]` for notes inside the vault, `[text](url)` for external URLs.

**When to use it:** any time you're *writing or restructuring a note* and want it to link,
embed, tag, or highlight properly — e.g. capturing a meeting note in `work`, or turning raw
study notes in `learning` into a linked, tagged note. If the output is a `.md` file that lives
in a vault, this is the skill.

**Example — a full note that uses the Obsidian extras:**

```markdown
---
title: Kubernetes Networking
tags:
  - learning
  - k8s
aliases:
  - k8s networking
---

# Kubernetes Networking

Builds on [[Container Fundamentals]] and feeds into [[Service Mesh Basics]].

> [!tip] Mental model
> Every Pod gets its own IP — think "one VM per Pod," not "ports on a host."

![[k8s-network-diagram.png|500]]

Key idea: ==a Service is a stable front door for a set of ephemeral Pods.== #networking

%% TODO: add a note on NetworkPolicies once I've tested them %%
```

**Reference files:** `PROPERTIES.md`, `CALLOUTS.md`, `EMBEDS.md`.

---

## obsidian-bases

**What it is:** the skill for `.base` files — Obsidian's built-in database. A Base is a YAML
file that queries your notes and renders them as a table, cards, list, or map. Think "a saved
view over your vault," like a Notion database built from your existing frontmatter.

**Four parts of a Base:**
1. **filters** — which notes appear (by tag, folder, property, or date). Combine with
   `and` / `or` / `not`.
2. **formulas** — computed columns, e.g. `days_until_due: 'if(due, (date(due) - today()).days, "")'`.
3. **properties** — display-name / formatting overrides for columns.
4. **views** — one or more `table` / `cards` / `list` / `map` blocks, each with its own
   `order` (columns), optional `groupBy`, `limit`, and `summaries` (Sum, Average, Max…).

Embed a Base in a note with `![[MyBase.base]]` or a single view with `![[MyBase.base#View Name]]`.

**When to use it:** when you already have *many notes sharing frontmatter* and want a live
dashboard over them instead of maintaining a list by hand — a task tracker across all `#task`
notes, a reading list, a project index. If you'd otherwise copy-paste a table that goes stale,
use a Base: it re-queries every time you open it.

**Example — a complete task-tracker Base (`tasks.base`):**

```yaml
filters:
  and:
    - file.hasTag("task")
    - 'status != "done"'

formulas:
  days_until_due: 'if(due, (date(due) - today()).days, "")'

properties:
  formula.days_until_due:
    displayName: "Days left"

views:
  - type: table
    name: "Open tasks"
    order:
      - file.name
      - status
      - due
      - formula.days_until_due
    groupBy:
      property: status
      direction: ASC
```

Drop that in the vault, then surface it in any note with `![[tasks.base]]`. Every note tagged
`#task` with `status:` and `due:` frontmatter shows up automatically.

**Two traps the skill guards against:**
- Subtracting two dates gives a **Duration**, not a number — grab `.days` *before* rounding
  (`(now() - file.ctime).days.round(0)`), never round the Duration directly.
- Guard optional properties with `if(...)` so a missing field doesn't crash the formula.

**Reference file:** `FUNCTIONS_REFERENCE.md` (full Date/String/Number/List/File function list).

---

## json-canvas

**What it is:** the skill for `.canvas` files — the infinite spatial boards behind Obsidian's
Canvas (mind maps, flowcharts, research boards). It follows the open
[JSON Canvas 1.0 spec](https://jsoncanvas.org/spec/1.0/), so it isn't Obsidian-locked.

**What a canvas is:** a JSON file with two arrays — `nodes` (boxes on the board) and `edges`
(the arrows between them).

```json
{ "nodes": [], "edges": [] }
```

- **Node types:** `text` (Markdown box), `file` (embeds a vault file/image), `link` (external
  URL), `group` (a labelled container around other nodes). Every node needs a unique 16-char
  hex `id` plus `x`/`y`/`width`/`height`.
- **Edges** connect nodes by `fromNode` / `toNode` id, with optional `fromSide` / `toSide`
  anchors and a `label`.
- **Colors** are preset `"1"`–`"6"` (red…purple) or a hex string.

**When to use it:** when a note's ideas are *spatial rather than linear* — a mind map, an
architecture diagram, a "how do these notes connect" board, a project kanban. If you'd reach
for a whiteboard instead of an outline, use a Canvas. (For inline diagrams *inside* a note,
prefer a Mermaid code block via `obsidian-markdown` — reach for Canvas when you want a
free-floating board you drag around.)

**Example — a minimal two-node canvas with a labelled arrow (`ideas.canvas`):**

```json
{
  "nodes": [
    {
      "id": "6f0ad84f44ce9c17",
      "type": "text",
      "x": 0, "y": 0, "width": 300, "height": 120,
      "text": "# Problem\nNotes pile up, connections get lost.",
      "color": "1"
    },
    {
      "id": "a1b2c3d4e5f67890",
      "type": "text",
      "x": 450, "y": 0, "width": 300, "height": 120,
      "text": "# Fix\nLink notes + build MOCs.",
      "color": "4"
    }
  ],
  "edges": [
    {
      "id": "0123456789abcdef",
      "fromNode": "6f0ad84f44ce9c17", "fromSide": "right",
      "toNode": "a1b2c3d4e5f67890", "toSide": "left",
      "toEnd": "arrow",
      "label": "solved by"
    }
  ]
}
```

Open `ideas.canvas` in Obsidian and you get two boxes joined by a "solved by" arrow.

**Traps the skill guards against:** use `\n` (not literal `\\n`) for line breaks in text
nodes, keep every `id` unique, and make sure each edge's `fromNode`/`toNode` points at a node
that actually exists — dangling references break the file.

**Reference file:** `EXAMPLES.md` (mind maps, project boards, research canvases, flowcharts).

---

## obsidian-cli

**What it is:** the skill for the `obsidian` command-line tool, which talks to a **running**
Obsidian app (the app must be open). Use it to read, create, search, and edit notes without
leaving the terminal — and to reload/debug plugins and themes you're developing.

**Syntax:** parameters use `key=value` (quote values with spaces); flags are bare switches.

```bash
obsidian read file="My Note"
obsidian create name="New Note" content="# Hello" silent
obsidian append file="My Note" content="New line"
obsidian search query="search term" limit=10
obsidian daily:append content="- [ ] New task"
obsidian property:set name="status" value="done" file="My Note"
obsidian backlinks file="My Note"
```

- `file=` resolves like a wikilink (name only); `path=` is an exact vault-root path.
- No `file`/`path` → acts on the active note. `vault="Name"` (first) targets a specific vault.
- `silent` stops files from opening, `--copy` copies output to clipboard.
- `obsidian help` lists every command and is always current.

**When to use it:** for *scripted or hands-free vault operations while Obsidian is open* —
quick-capture into today's daily note without switching windows, bulk-updating a property,
piping search results into another command, or (its second job) reloading a plugin/theme
you're developing. Reach for it when you want the terminal to touch the vault; for editing a
`.md` file directly on disk, `obsidian-markdown` is enough and needs no running app.

**Example — capture a task into today's note, then confirm it landed:**

```bash
obsidian daily:append content="- [ ] Review the tasks.base dashboard"
obsidian daily:read
```

The first line appends a checkbox to today's daily note (creating it if needed); the second
prints the note back so you can see the task is there.

**Plugin dev loop:** `plugin:reload id=…` → `dev:errors` → `dev:screenshot` / `dev:dom` →
`dev:console level=error`. Also `eval code="…"` to run JS in the app context.
