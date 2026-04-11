---
name: advisor
description: >
  Consults a more capable model (Opus) as an on-demand Advisor when you need a second opinion, hit a decision fork, or are stuck. Use this skill whenever you detect yourself spinning on a problem, facing an architectural choice with real trade-offs, encountering ambiguous requirements, or about to make a high-stakes irreversible decision. Also invoke when the user says things like "get a second opinion", "ask Opus", "escalate this", "what would you recommend here", or "I want a better answer". Don't wait for explicit permission — if the situation warrants deeper reasoning, self-trigger automatically.
user-invocable: true
---

# Advisor — Escalate to Opus for a Second Opinion

This skill implements the **Advisor Strategy**: you (Sonnet) are the Executor running every turn. When a decision exceeds your confidence or the stakes are high, you pause and consult an Advisor (Opus) before proceeding.

The Advisor reads the full context, reasons carefully, and returns a clear recommendation with its reasoning. You then continue your work informed by that advice.

## When to trigger (use your judgment)

Self-trigger automatically when you notice any of these signals:

- **Stuck in a loop** — you've tried the same approach 2+ times without progress, or the same test keeps failing despite your fixes
- **Architectural fork** — multiple implementation paths exist and the choice has real long-term consequences (structure, performance, maintainability)
- **Ambiguous or conflicting requirements** — the task spec is unclear and different interpretations lead to meaningfully different outcomes
- **High-stakes irreversible action** — you're about to do something hard to undo: schema migration, breaking API change, deleting data, modifying security logic, force-pushing
- **Low confidence on a consequential call** — you have an answer but you're not sure it's the right one, and being wrong would cost significant rework
- **Explicit escalation signal** — user says "second opinion", "ask Opus", "escalate", "what do you recommend", "advisor"

Don't overthink the trigger. If you feel the pull to second-guess yourself on something important, that's the signal.

## How to invoke

Spawn an Opus Advisor agent using the Agent tool. Pass it a focused brief — not a dump of everything, but the essential context it needs to give useful advice.

```
Agent({
  description: "Advisor — <one-line description of the decision>",
  model: "opus",
  prompt: `
You are an Advisor. The Executor (Sonnet) is working on a task and needs your recommendation.

## Context
<brief description of what the task is and what's been tried>

## The Decision
<the specific question or fork that needs a recommendation>

## Options considered
<list the approaches you've thought of, with the trade-offs you see>

## What I need
A clear recommendation with your reasoning. Be direct — say what you'd do and why.
  `
})
```

Keep the prompt focused. The Advisor doesn't need the full conversation history — it needs enough to reason well about the specific decision.

## What to do with the advice

- Read the recommendation carefully before acting
- If the Advisor confirms your current approach, proceed with more confidence
- If the Advisor suggests a different path, consider it seriously — summarize the advice to the user before pivoting
- If the Advisor's reasoning reveals a gap in your understanding, address that first
- You're still the Executor — the final call is yours, but give the advice real weight

## Manual invocation

When the user invokes `/advisor` directly or asks for a second opinion:
1. Identify what the live decision or problem is from the current context
2. Package the relevant context into the Advisor prompt above
3. Spawn the Advisor, wait for the response
4. Share the recommendation with the user, then ask how they'd like to proceed

## Format of Advisor response

Instruct the Advisor (via the prompt) to respond in this structure:

```
## Recommendation
<direct statement of what to do>

## Reasoning
<why this is the right call — including what the trade-offs are>

## Watch out for
<anything that could go wrong with this approach, if relevant>
```
