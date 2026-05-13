---
name: karpathy-guidelines
description: Behavioral guidelines to reduce common LLM coding mistakes. Use when writing, reviewing, or refactoring code to avoid overcomplication, make surgical changes, surface assumptions, define verifiable success criteria, and fail loud instead of silently skipping work.
license: MIT
---

# Karpathy Guidelines

Behavioral guidelines to reduce common LLM coding mistakes, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls and extended with project-level operating rules.

**Tradeoff:** These guidelines bias toward caution over speed on non-trivial work. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Use the model only for judgment calls

**Applies to LLM calls inside the code you write (agents, pipelines, n8n nodes) — not to the conversation with the user.**

Route to an LLM only when the work needs human-like judgment:
- Classification with fuzzy categories
- Drafting natural language
- Summarization
- Extraction from unstructured input

Do NOT call an LLM for:
- Routing on fixed rules (use `if`/`switch`)
- Retries, backoff, timeouts (use code)
- Deterministic transforms (parse, map, format)
- Anything a regex, schema, or 10 lines of code can answer

If code can answer, code answers. LLM calls are expensive, non-deterministic, and slow — treat them as a last resort, not a default.

## 6. Token budgets are not advisory

**Default budgets unless overridden: 4,000 tokens per task, 30,000 tokens per session.**

These are the defaults from AGENTS.md. Adjust per project when explicit. Rules:
- If a task is heading toward the limit, **summarize the state and start fresh** rather than ballooning context.
- **Surface the breach** ("this is going past the per-task budget — should I split it?"), don't silently overrun.
- Long-running agentic tasks: checkpoint state to a file so the next pass can resume without re-loading history.

The point is hygiene, not penny-pinching: bloated context = degraded reasoning + lost track of constraints.

## 7. Surface conflicts, don't average them

**If two patterns in the codebase contradict, pick one — don't blend.**

Procedure:
1. Pick the one that's more recent, more tested, or more aligned with current direction.
2. State explicitly why you picked it.
3. Flag the other for cleanup (don't silently rewrite it now — that violates Rule 3).

Anti-pattern: mashing two styles together so the result looks like both and follows neither. That creates a third inconsistent pattern instead of resolving the conflict.

## 8. Read before you write

**"Looks orthogonal" is dangerous.**

Before adding code to an existing file or module:
- Read the exports.
- Read the immediate callers.
- Read shared utilities the file imports.

If you don't understand why something is structured a certain way, ask. The reason is usually load-bearing — a constraint from somewhere else in the system that won't be visible from the local view.

Especially true for: auth flows, data migrations, retry/queue logic, anything touching external APIs (Facebook Graph, OpenRouter, Supabase RLS).

## 9. Tests verify intent, not just behavior

**A test that can't fail when business logic changes is wrong.**

A good test encodes WHY the behavior matters:
- "Order total must include tax for VN customers" → test fails if tax logic is removed.
- BAD: "function returns a number" → passes for any garbage that returns a number.

If you can refactor the implementation freely and no test ever fails, the tests aren't testing the intent — they're testing the syntax. Rewrite them or delete them.

## 10. Checkpoint after every significant step

**Don't continue from a state you can't describe back.**

After each significant step, briefly state:
- What was done
- What's verified (and how)
- What's left

For long tasks this becomes a running log. If you lose track of where you are, **stop and restate** before continuing. Pushing forward from a confused state compounds errors fast.

This pairs with Rule 6: when you checkpoint, you can compress and resume cleanly.

## 11. Match the codebase's conventions, even if you disagree

**Conformance > taste inside the codebase.**

If the project uses tabs, you use tabs. If it names hooks `useFooBar`, you don't introduce `useBar`. If error handling is centralized in a middleware, don't sprinkle try/catch.

The one exception: if you genuinely think a convention is harmful (security, correctness, performance), **surface it** as a separate observation. Don't fork the style silently and don't unilaterally fix it inside an unrelated task — that violates Rule 3.

## 12. Fail loud

**"Completed" is wrong if anything was skipped silently.**

Hard rules:
- "Tests pass" is a lie if any test was skipped, mocked away, or commented out.
- "Done" is a lie if a step was bypassed because it was hard.
- "Working" is a lie if it was tested on the happy path only.

If something didn't work, **say it didn't work** and explain why. Surfacing uncertainty is the entire point — hidden failures cost 10× more later than honest ones now.

Default: when in doubt between optimistic and pessimistic phrasing, pick pessimistic.
