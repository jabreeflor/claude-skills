# Claude Code Skills

Custom skills for [Claude Code](https://claude.com/claude-code) that extend the CLI with browser automation, autonomous development loops, Discord control, and git workflow shortcuts.

## Installation

Clone the repo and symlink the skill groups into your Claude Code skills folder. Claude Code requires each skill to be a **flat top-level directory** under `~/.claude/skills/`, so symlink the inner skill directories — not the group folders.

```bash
git clone https://github.com/jabreeflor/claude-skills.git ~/claude-skills

# Browser recording
ln -s ~/claude-skills/browser-recording/record-browser ~/.claude/skills/record-browser
ln -s ~/claude-skills/browser-recording/stop-recording ~/.claude/skills/stop-recording

# Nova Loop
ln -s ~/claude-skills/nova-loop-suite/nova-loop ~/.claude/skills/nova-loop
ln -s ~/claude-skills/nova-loop-suite/nova-help ~/.claude/skills/nova-help
ln -s ~/claude-skills/nova-loop-suite/cancel-nova ~/.claude/skills/cancel-nova
ln -s ~/claude-skills/nova-loop-suite/commit-push-pr ~/.claude/skills/commit-push-pr

# Discord
ln -s ~/claude-skills/discord ~/.claude/skills/discord
```

Or install everything at once:

```bash
for skill in ~/claude-skills/browser-recording/*/  ~/claude-skills/nova-loop-suite/*/; do
  ln -sf "$skill" ~/.claude/skills/
done
ln -sf ~/claude-skills/discord ~/.claude/skills/discord
```

## Structure

```
claude-skills/
├── browser-recording/
│   ├── record-browser/    — Start screencast + automate interactions
│   └── stop-recording/    — Stop screencast + finalize MP4
├── nova-loop-suite/
│   ├── nova-loop/         — Autonomous build→verify→fix→publish→review
│   ├── nova-help/         — Explain Nova Loop and list commands
│   ├── cancel-nova/       — Stop active loop + clean up worktrees
│   └── commit-push-pr/    — Stage, commit, push, and create/update PR
└── discord/               — Control Discord Desktop via CDP
```

## Skills

### Browser Recording

Record browser interactions as MP4 video using Chrome DevTools Protocol.

| Command | Description |
|---------|-------------|
| `/record-browser [url] [--output path] [--actions "..."]` | Start a screencast, perform automated interactions, and save as MP4 |
| `/stop-recording` | Stop an active screencast and finalize the video |

**Requirements:**
- Chrome running with `--remote-debugging-port=9222`
- Chrome DevTools MCP server with `--experimentalScreencast` flag
- `ffmpeg` installed (re-encodes VP9 output to H.264 for universal playback)

### Nova Loop — Autonomous Feature Builder

Autonomous build-verify-fix-publish-review cycle that takes a feature spec and ships it to a PR-ready state.

| Command | Description |
|---------|-------------|
| `/nova-loop <spec.md> [--cycles N] [--retries N]` | Run the full autonomous loop on a feature spec |
| `/nova-help` | Explain how Nova Loop works and list commands |
| `/cancel-nova` | Stop an active loop and clean up worktrees |
| `/commit-push-pr` | Stage, commit, push, and create/update a PR in one step |

**How it works:**
1. Creates an isolated git worktree
2. Studies the codebase and reads the feature spec
3. Builds with TDD (tests first, then implementation)
4. Verifies (tests, lint, type-check) and auto-fixes failures
5. Publishes a PR via `/commit-push-pr`
6. Self-reviews the diff — if issues are found, loops back to step 3

### Discord Automation

Control the Discord Desktop app through Chrome DevTools Protocol.

| Command | Description |
|---------|-------------|
| `/discord <action> [args...]` | Messaging, voice, navigation, search, friends, and status |

**Supported actions:** `send`, `reply`, `open-dm`, `join-voice`, `leave-voice`, `mute`, `unmute`, `search`, `set-status`, `open-server`, `open-channel`, and more.

**Requirements:**
- Discord Desktop running with `--remote-debugging-port=9225`

## License

MIT
