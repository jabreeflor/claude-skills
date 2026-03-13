---
name: skill-eval
description: "Evaluate and benchmark Claude Code skills by running test cases with and without the skill active, scoring outputs, and iterating on improvements. Use when you want to measure whether a skill is actually improving results, validate skill quality before publishing, or A/B test skill versions."
user-invocable: true
args: "<skill-name> [--cases <n>] [--iterate]"
---

# Skill Evaluator

Evaluate skill quality by running test cases both with and without the skill, scoring outputs, and reporting a delta.

## Workflow

### 1. Generate Test Cases

Propose 3–5 representative prompts that the skill is designed to handle. Cover:
- Core use case (the obvious one)
- Edge case (unusual input or format)
- Failure mode (something the skill should handle gracefully)

Example for a meeting-summary skill:
- "Summarize this Meet transcript" (core)
- "What were the action items from this call?" (variant)
- "Who was responsible for the API migration?" (specific extraction)

### 2. Run Without Skill

Execute each test case with the skill disabled (or with an explicit instruction to ignore it). Record raw output.

### 3. Run With Skill

Execute each test case with the skill active. Record output.

### 4. Score Each Pair

For each test case, score both outputs on the criteria most relevant to the skill's purpose. Example scoring rubric:

| Criterion | Points |
|---|---|
| Correct structure / sections present | 2 |
| Responsible parties identified | 2 |
| Actionable next steps listed | 2 |
| Consistent format across runs | 2 |
| No hallucination | 2 |

Total: 10 pts per run. Report delta (with skill − without skill).

### 5. Report Results

```
Skill: meeting-summary
Test cases: 4

| # | Prompt                          | Without | With | Delta |
|---|----------------------------------|---------|------|-------|
| 1 | Summarize this transcript        | 5/10    | 9/10 | +4    |
| 2 | What are the action items?       | 6/10    | 8/10 | +2    |
| 3 | Who owns the API migration?      | 3/10    | 9/10 | +6    |
| 4 | Key decisions from today's call  | 5/10    | 7/10 | +2    |

Average delta: +3.5 / 10
Verdict: PASS — skill meaningfully improves outputs
```

Verdict thresholds:
- **PASS**: avg delta ≥ +2
- **MARGINAL**: avg delta +1 to +1.9
- **FAIL**: avg delta < +1 (skill adds no measurable value)

### 6. Iterate (if `--iterate` flag set)

After reporting results, identify the lowest-scoring test case and suggest a specific SKILL.md improvement to address it. Apply the change and re-run that case only. Repeat until PASS or user stops.

## Usage Examples

```bash
# Evaluate a skill with auto-generated test cases
/skill-eval meeting-summary

# Run 6 test cases
/skill-eval discord --cases 6

# Evaluate and auto-iterate until PASS
/skill-eval nova-loop --iterate
```

## Notes

- Be honest in scoring — the point is signal, not flattery
- If the skill has no measurable effect, say so clearly
- Test cases should reflect real usage, not cherry-picked easy wins
- When iterating, change one thing at a time to isolate what helps
