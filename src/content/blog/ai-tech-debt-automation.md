---
title: 'Using AI to Close your Engineering Gaps — Tech Debt'
description: "Tech debt doesn't get fixed because the process never makes space for it. AI changes the economics of the problem — here's how to close the gap and automate the upkeep so it never comes back."
pubDate: 'May 18 2026'
---

_This post addresses some of the potential issues and risks your team may find during their AI adoption journey. Read more about them in [The Hidden Engineering Risks in AI Adoption](/blog/hidden-eng-risk-ai-adoption-turn-advantage/#development-practices---clean-up-the-codebase-before-ai-learns-from-it)_

## The Gap that Never Gets Fixed

I've watched teams carry debt for years, not because they didn't care, but because the process never made space for it. There's always a feature to ship, a bug to fix, a deadline to hit.

[Tech debt builds up and makes teams slower](https://www.alixpartners.com/insights/102jlar/can-ai-solve-the-rising-costs-of-technical-debt/) as it makes it harder to introduce new features. The longer it sits, the more it compounds and the harder it is to justify paying it down when velocity is already under pressure.

AI changes the economics of this problem by introducing easier, more automated ways to address tech debt even before it builds up.

## Close the Gap

Point your AI agent at your codebase to surface the worst offenders — duplicate logic, dead code, high-churn modules, anti-patterns that keep getting copied. You'll have a prioritised heatmap of where the rot lives before the end of the week. A prompt like this gives you a structured starting point (raw material for the team to triage):

```text wrap
Analyse this module for technical debt. Flag: duplicate logic, functions over 50 lines, missing error handling, magic numbers, commented-out code, and unresolved TODOs.
For each issue, rate severity (high/medium/low) and estimate remediation effort (small/medium/large). Return structured JSON.
```

Use AI to generate refactoring PRs for the clearly-scoped fixes: renaming, extracting functions, removing dead code, modernising old patterns. Review them small and often — not as a project, as a background habit. A well-constrained remediation prompt matters here:

```text wrap
The following function has been flagged as tech debt: [code]. The issue is [description].
Refactor it to align with [preferred pattern], without changing behaviour.
All existing tests must pass. Output only the changed file.
```

Vague prompts produce vague PRs. The more precisely you describe what good looks like, the less time you spend reviewing AI's interpretation of it.

For bigger refactors, scope tightly and use the harness pattern: structured input, test validation, retry on failure. **Don't point an agent at the whole monorepo and hope for the best**.

## Keep it Closed

The model teams _should_ adopt: a background loop that runs without anyone manually scheduling it. A scheduled CI job (eg a GitHub Action running weekly) scans the codebase using your debt-identification prompts and logs findings to a living register, prioritised by impact. From that register, a second job picks up tasks that are clearly scoped and well-understood, writes the fix, and opens a draft PR for review.

![AI Tech Debt Automated Loop](/ai-tech-debt-loop.excalidraw.png)

The team's job:

1. Maintain the prompts that define what good looks like for their specific codebase
2. Ensure tech debt items have been classified and prioritised
3. Review and approve the PRs that come in

**As you improve the accuracy of your prompts you'll be able to get rid of step 2 entirely.**

## What You Need in Place First

Before you hand the wheel to an AI agent, the foundations need to be solid. A poorly constrained agent on a poorly instrumented codebase propagates debt instead of reducing it.

**Test coverage, static analysis, linters, and formatters** are the guardrails that keep the agent working within safe boundaries. Without good test coverage in particular, the agent has no way to verify its changes are correct. These tools should be enforced as hard CI gates before any automation touches the codebase.

**A clear `AGENTS.md`.** The agent needs to understand what "good" looks like for your specific codebase. Document:

- Package and module structure
- Integration patterns
- Code structure conventions
- Tech stack constraints
- What counts as tech debt in your context
- What's explicitly out of scope
- PR conventions
- ADRs

## The Bigger Win: What This Does to Your Team

When PRs start arriving from a machine, the team is forced to do the thinking they've been avoiding. You cannot leave "tech debt" as a vague, uncomfortable concept when an AI is asking you to define it precisely enough to act on it — and then reviewing the output of those definitions every week.

Teams that go through this process end up with three things they didn't have before: a shared, written definition of what good looks like for their codebase; a habit of triaging and prioritising debt like a first-class engineering concern; and sharper instincts for reviewing AI-generated changes, which makes them better reviewers overall.

The final outcome (besides a cleaner codebase) is a team that's had the quality conversations they'd been deferring, backed by a system that makes sure the standards hold without anyone manually scheduling a cleanup sprint.

> AI can boost your output, but the real win is quality: a codebase that grows stronger and easier to maintain to humans and agents with every cycle.