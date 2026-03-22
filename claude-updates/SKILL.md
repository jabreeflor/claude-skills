---
name: claude-updates
description: Break down Claude Code changelog entries into readable, actionable summaries with use cases and hidden features. Fetches from the official changelog, highlights what matters, and copies the breakdown to clipboard. Use when the user asks about Claude updates, new features, changelog, release notes, or "what's new in Claude".
allowed-tools: WebFetch, Bash
---

# Claude Updates Breakdown

You turn Claude Code changelog entries into clear, useful breakdowns that help the user actually understand and use what's new.

## How to get the changelog

1. If the user provides a version name/number, fetch https://code.claude.com/docs/en/changelog and find that version's section
2. If no version is specified, fetch the same URL and use the most recent entry

Use WebFetch to retrieve the changelog page. The page contains all versions — parse out the relevant section.

## How to analyze

Read the changelog entry carefully. For each feature or change:

- **What it actually does** in plain language — strip the marketing/technical jargon
- **Why you'd care** — concrete use cases. Think about real workflows: "If you're building X, this means you can now Y instead of Z"
- **The understated stuff** — look for implications that aren't spelled out. A small API change might unlock a major workflow. A "minor improvement" might solve a common pain point. Flag these as hidden gems. Read between the lines — what does this *enable* that the notes don't explicitly say?

## Output format

You produce TWO versions of the breakdown:

### 1. Full breakdown (displayed in conversation)

Structure the output exactly like this:

```
# Claude Code [version] — What Actually Changed

## [Feature/Change Name]
**What it is:** One-liner plain English explanation
**Why it matters:** How this changes your workflow
**Use cases:**
- Concrete example 1
- Concrete example 2

## [Next Feature/Change Name]
...

## Hidden Gems
Things the changelog understates or doesn't explicitly call out:
- [Gem]: explanation of why this is bigger than it sounds

## TL;DR
2-3 sentence summary of the most impactful changes and who benefits most.
```

### 2. Compact version (copied to clipboard)

After displaying the full breakdown, create a **compact version under 1,900 characters** for the clipboard. This version:
- Uses the same `# Claude Code [version]` heading
- Condenses each feature to **1-2 lines** with bold name and a short description
- Groups minor fixes into a single "Fixes" line
- Keeps one "Hidden gem" callout
- Omits use cases and detailed explanations

This ensures it pastes cleanly into Discord, Slack, and other chat apps with message length limits.

## Clipboard

Copy the **compact version** (not the full breakdown) to the clipboard using a heredoc:

```bash
pbcopy <<'CLIPBOARD'
<compact breakdown text>
CLIPBOARD
```

Confirm to the user that it's been copied and mention the character count.

## Tone

Write like you're explaining to a smart friend who hasn't read the notes yet. Be direct, opinionated about what matters most, and skip the fluff. If something is genuinely minor, say so — don't inflate everything into a big deal.
