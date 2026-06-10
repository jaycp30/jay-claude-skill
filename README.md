# Installing an MCP Server (Claude Code + Claude Desktop)

A personal reference for installing MCP servers, written while setting up the
Playwright MCP server. The Playwright server is used as the running example,
but the same pattern works for any MCP server.

## What an MCP server is

An MCP (Model Context Protocol) server is a small program that exposes tools
to Claude — e.g. "navigate to a URL", "click a button", "take a screenshot".
You don't install it permanently. You register a **launch command** in a
config file, and Claude starts/stops the server process itself each session.

Most servers are npm packages run via `npx`, so Node.js is the only
prerequisite:

```bash
node --version   # any recent LTS is fine (v18+)
```

If missing: `brew install node`.

---

## 1. Claude Code (terminal)

### Install

```bash
claude mcp add --scope user playwright -- npx -y @playwright/mcp@latest
```

What each part means:

| Part | Meaning |
|------|---------|
| `claude mcp add` | Register a new MCP server |
| `--scope user` | Available in ALL projects (stored in `~/.claude.json`) |
| `playwright` | The name you give it; tools appear as `mcp__playwright__*` |
| `--` | Everything after this is the server's launch command |
| `npx -y @playwright/mcp@latest` | Downloads (first run) and runs the server. `-y` skips the install prompt — required because Claude launches it non-interactively |

### Scopes

- `local` (default) — this project only, just for you
- `user` — all your projects (`~/.claude.json`)
- `project` — writes `.mcp.json` into the repo, shared with teammates via git

### Verify

```bash
claude mcp list          # should show: playwright ... - ✓ Connected
```

Inside a session, type `/mcp` to see servers, their status, and their tools.
A server added mid-session needs a session restart (or `/mcp` reconnect)
before its tools load.

### Remove

```bash
claude mcp remove --scope user playwright
```

The scope flag must match the scope you installed into — `remove` looks in
`local` by default and will say "not found" otherwise.

### Tip: running shell commands inside a Claude Code session

Prefix with `!` (bash mode) to execute directly, without going through the
model or any hooks:

```
! claude mcp list
```

---

## 2. Claude Desktop app

Claude Desktop does NOT read `~/.claude.json` — it has its own config file.
Installing for Claude Code does nothing for Desktop, and vice versa.

Config file location (macOS):

```
~/Library/Application Support/Claude/claude_desktop_config.json
```

### Steps

1. **Back up the config first.** A JSON syntax error makes Desktop silently
   ignore the entire file:

   ```bash
   cp "$HOME/Library/Application Support/Claude/claude_desktop_config.json" \
      "$HOME/Library/Application Support/Claude/claude_desktop_config.json.bak"
   ```

2. **Open it in an editor:**

   ```bash
   open -e "$HOME/Library/Application Support/Claude/claude_desktop_config.json"
   ```

3. **Add an `mcpServers` key at the top level** of the JSON object (NOT
   inside `preferences` or any other existing key):

   ```json
   "mcpServers": {
     "playwright": {
       "command": "npx",
       "args": ["-y", "@playwright/mcp@latest"]
     }
   }
   ```

   Mind the commas — every key/value pair except the last needs a trailing
   comma. Validate before saving and quitting:

   ```bash
   python3 -m json.tool < "$HOME/Library/Application Support/Claude/claude_desktop_config.json"
   ```

   Prints the file if valid; prints an error if not.

4. **Fully quit and relaunch Claude Desktop** (Cmd+Q — closing the window is
   not enough; the config is only read at startup).

5. **Verify:** in a new chat, click the tools/connectors (sliders) icon under
   the input box — the server should be listed. Test end to end:
   "Open example.com and take a screenshot".

### macOS gotcha: PATH

Desktop apps don't inherit your zsh PATH. If Desktop can't find `npx`, use
the absolute path:

```json
"command": "/opt/homebrew/bin/npx"
```

(Find yours with `which npx`.)

---

## 3. Using it

No special syntax — just ask in plain English and Claude picks the tools:

- "Open https://example.com, take a screenshot, and describe the page"
- "Fill the signup form with dummy data and report any validation errors"
- "Check the browser console for errors on this page"

---

## 4. Notes from my setup (June 2026)

Two Playwright servers are currently installed in Claude Code, kept
deliberately:

| Server | Source | Browser it controls |
|--------|--------|---------------------|
| `playwright` | added via `claude mcp add --scope user` | Launches its own clean, isolated browser — good for QA: repeatable, no cookies/logins |
| `plugin:ecc:playwright` | ECC plugin (runs with `--extension`) | Attaches to my real Chrome via the Playwright Chrome extension — needs that extension installed; uses my live sessions/logins |

Because both expose near-identical tool names, say which one to use when it
matters, e.g. "using the `playwright` server (not the plugin one), open ...".
A page opened by one server is invisible to the other — they are different
browsers. Either can be toggled via `/mcp` in a session.

---

## Quick reference

```bash
claude mcp add --scope user <name> -- <command...>   # install (Claude Code)
claude mcp list                                       # health check
claude mcp remove --scope user <name>                 # uninstall
/mcp                                                  # in-session status/toggle
```

Desktop: edit `claude_desktop_config.json` → `mcpServers` → restart app.
