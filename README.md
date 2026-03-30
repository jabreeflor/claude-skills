# Claude Code Skills

Custom skills for [Claude Code](https://claude.com/claude-code) — browser automation, terminal recording, autonomous build loops, web content extraction, and Discord control.

---

## Installation

### Option 1 — npx (recommended)

Install everything at once:

```bash
npx skills add jabreeflor/claude-skills
```

Install a specific skill:

```bash
npx skills add jabreeflor/claude-skills --skill nova-loop
npx skills add jabreeflor/claude-skills --skill record-browser
npx skills add jabreeflor/claude-skills --skill discord
npx skills add jabreeflor/claude-skills --skill agent-reach
```

Install globally (available across all projects):

```bash
npx skills add jabreeflor/claude-skills -g
```

### Option 2 — Clone & symlink

```bash
git clone https://github.com/jabreeflor/claude-skills.git ~/claude-skills

# Install everything at once
for skill in ~/claude-skills/browser-recording/*/ ~/claude-skills/terminal-recording/*/ ~/claude-skills/nova-loop-suite/*/ ~/claude-skills/web-tools/*/; do
  ln -sf "$skill" ~/.claude/skills/
done
ln -sf ~/claude-skills/discord ~/.claude/skills/discord
```

> Claude Code requires each skill to be a **flat top-level directory** under `~/.claude/skills/`. When symlinking, link the inner skill folders — not the group folders.

---

## Skills

### 🎥 Browser Recording

Record browser interactions as MP4 using Chrome DevTools Protocol.

| Command | Description |
|---------|-------------|
| `/record-browser [url] [--output path] [--actions "..."]` | Start a screencast and save as MP4 |
| `/stop-recording` | Stop the active screencast and finalize the video |

**Requirements:**
- Chrome running with `--remote-debugging-port=9222`
- Chrome DevTools MCP server with `--experimentalScreencast`
- `ffmpeg` (re-encodes VP9 → H.264 for universal playback)

---

### 🖥️ Terminal Recording

Record terminal sessions as asciicast, script logs, or animated GIFs.

| Command | Description |
|---------|-------------|
| `/record-terminal [--output path] [--format asciicast\|script\|gif] [--commands "..."] [--title "..."]` | Start recording a terminal session |
| `/stop-terminal-recording` | Stop the active recording and finalize output |

**Formats:**

| Format | Notes |
|--------|-------|
| `asciicast` *(default)* | Replayable via `asciinema play` or asciinema.org |
| `script` | BSD/GNU `script` capture — works on any system |
| `gif` | Converts asciicast → animated GIF for easy sharing |

**Requirements:**
- `asciinema` (for asciicast/gif)
- `agg` (for gif conversion only)
- `script` — pre-installed on macOS and Linux

---

### 🔁 Nova Loop — Autonomous Feature Builder

Fully autonomous build → verify → fix → publish → review cycle.

| Command | Description |
|---------|-------------|
| `/nova-loop <spec.md> [--cycles N] [--retries N]` | Run the full loop on a feature spec |
| `/nova-help` | Explain Nova Loop and list all commands |
| `/cancel-nova` | Stop an active loop and clean up worktrees |
| `/commit-push-pr` | Stage, commit, push, and open/update a PR in one step |

**How it works:**

1. Creates an isolated git worktree
2. Reads the feature spec and studies the codebase
3. Builds with TDD (tests first, then implementation)
4. Verifies (tests + lint + type-check) and auto-fixes failures
5. Opens a PR via `/commit-push-pr`
6. Self-reviews the diff — loops back to step 3 if issues are found

---

### 🌐 Agent-Reach — Web Content Extraction

Fetch and extract clean, readable content from any public URL using [Agent-Reach](https://github.com/Panniantong/Agent-Reach). Strips ads, navigation, and HTML clutter for LLM-ready output.

| Command | Description |
|---------|-------------|
| `/agent-reach <url> [--raw] [--save <path>]` | Extract clean text from a URL, stripped of ads and navigation |

**Use cases:** research agents, documentation fetching, article summarization, fact-checking with live web content.

**Requirements:**
- Python 3 installed
- Agent-Reach is auto-installed on first use to `~/.local/share/agent-reach`

---

### 💬 Discord Automation

Control Discord Desktop via Chrome DevTools Protocol.

| Command | Description |
|---------|-------------|
| `/discord <action> [args...]` | Messaging, voice, navigation, search, status, and more |

**Supported actions:** `send`, `reply`, `open-dm`, `join-voice`, `leave-voice`, `mute`, `unmute`, `search`, `set-status`, `open-server`, `open-channel`

**Requirements:**
- Discord Desktop running with `--remote-debugging-port=9225`

---

## Repo Structure

```
claude-skills/
├── browser-recording/
│   ├── record-browser/           — Start screencast + automate interactions
│   └── stop-recording/           — Stop screencast + finalize MP4
├── terminal-recording/
│   ├── record-terminal/          — Record terminal sessions
│   └── stop-terminal-recording/  — Stop recording + finalize output
├── nova-loop-suite/
│   ├── nova-loop/                — Autonomous build→verify→fix→publish→review
│   ├── nova-help/                — Explain Nova Loop and list commands
│   ├── cancel-nova/              — Stop active loop + clean up worktrees
│   └── commit-push-pr/           — Stage, commit, push, and create/update PR
├── web-tools/
│   └── agent-reach/              — Extract clean text from any public URL
└── discord/                      — Control Discord Desktop via CDP
```

---

## License

MIT
