# karpathy-guidelines

A Claude **Skill** that encodes behavioral guidelines to reduce common LLM coding mistakes — derived from [Andrej Karpathy's observations on LLM coding pitfalls](https://x.com/karpathy/status/2015883857489522876) and extended with project-level operating rules from `AGENTS.md`.

Drop it into your Claude environment (Claude Code, Cowork, claude.ai with skills enabled) and Claude will automatically apply these rules when writing, reviewing, or refactoring code.

## What problem does this solve?

LLMs writing code tend to fail in predictable ways:

- Overcomplicating instead of asking
- Refactoring code that wasn't asked to be touched
- Silently skipping hard steps and claiming "done"
- Blending two conflicting patterns into a worse third one
- Writing tests that pass for any garbage that compiles
- Burning context until reasoning degrades

This skill is 12 short rules that push back against each of those failure modes.

## The 12 rules (summary)

| # | Rule | One-liner |
|---|------|-----------|
| 1 | Think Before Coding | State assumptions. Ask when uncertain. |
| 2 | Simplicity First | Minimum code that solves it. Nothing speculative. |
| 3 | Surgical Changes | Touch only what you must. Don't "improve" adjacent code. |
| 4 | Goal-Driven Execution | Define success criteria. Loop until verified. |
| 5 | Model only for judgment calls | If code can answer, code answers. |
| 6 | Token budgets are not advisory | Surface breaches. Don't silently overrun. |
| 7 | Surface conflicts, don't average them | Pick one pattern, flag the other. |
| 8 | Read before you write | Read exports, callers, shared utils first. |
| 9 | Tests verify intent, not just behavior | A test that can't fail when logic changes is wrong. |
| 10 | Checkpoint after every significant step | Don't continue from a state you can't describe back. |
| 11 | Match the codebase's conventions | Conformance > taste. |
| 12 | Fail loud | "Completed" is wrong if anything was skipped silently. |

Full text lives in [`SKILL.md`](./SKILL.md).

## Install

### Claude Code / Cowork

Clone into the user skills folder:

```bash
git clone https://github.com/<your-user>/karpathy-guidelines.git \
  ~/.claude/skills/karpathy-guidelines
```

(Or whatever your skills directory is — e.g. `/mnt/skills/user/karpathy-guidelines/` in containerized environments. The folder name must match the skill name, and the file inside must be named exactly `SKILL.md`.)

### claude.ai

Upload `SKILL.md` through the skills UI when it's available on your plan.

## When it triggers

Claude loads this skill automatically when the task involves:

- Writing new code
- Reviewing or refactoring existing code
- Debugging
- Anything where silently skipping work or overcomplicating would be a real risk

You don't need to invoke it manually.

## Customization

A few defaults you may want to tune for your project:

- **Rule 6 token budgets** — set to `4,000/task` and `30,000/session`. Raise these for long vibe-coding sessions; lower them for tight automation work. Edit the numbers directly in `SKILL.md`.
- **Rule 5 scope** — written for LLM-as-a-component in production code (n8n agents, OpenClaw bots, etc.), not for the assistant talking to you. If you want the assistant itself bound by it too, reword the opening line.
- **Rule 11 conventions** — language is generic. If your codebase has hard rules (e.g. "no `any` in TS", "all errors via `Result<T,E>`"), append them here so Claude has the actual constraints in context.

## Credits

- Original 4 rules: condensed from [Andrej Karpathy's Twitter/X thread](https://x.com/karpathy/status/2015883857489522876) on LLM coding failure modes.
- Rules 5–12: extended from internal `AGENTS.md` operating template.

## License

MIT
