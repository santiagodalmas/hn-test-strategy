# Hacker News — Test Strategy & Cases

[![link-check](https://github.com/santiagodalmas/hn-test-strategy/actions/workflows/links.yml/badge.svg)](https://github.com/santiagodalmas/hn-test-strategy/actions/workflows/links.yml)

A practical test strategy and case library for
[Hacker News](https://news.ycombinator.com/), written as a real QA
deliverable: scope, risks, prioritization, environments, tooling, and
14 concrete test cases — three of them already automated by the
companion suites in this portfolio.

> Documentation-only repo. The other repos in this set hold the actual
> automation; this one explains *how* and *why* it is structured the
> way it is.

## What's here

| File | Purpose |
|---|---|
| [`strategy.md`](./strategy.md) | Top-level test strategy: goals, scope, risks, prioritization, environments, tooling, reporting. |
| [`cases/`](./cases/) | 14 test cases organized by feature area. Each is a self-contained markdown file using the format in [`cases/_template.md`](./cases/_template.md). |
| [`traceability.md`](./traceability.md) | Coverage matrix: every case → automation suite (or "manual"). |

## Cases at a glance

```
cases/
├── auth/        4 cases  — login, session, logout
├── submission/  3 cases  — URL, text, dedup
├── voting/      3 cases  — upvote, self-vote guard, flag
├── browsing/    3 cases  — sort order, pagination, UI/API agreement
└── comments/    2 cases  — post, deep-thread rendering
```

Three of them (the `browsing/` set) are automated — see the
[traceability matrix](./traceability.md). The rest are manual by
design: they're write-path actions on a third-party production
system, where automating would consume submission/comment quota and
pollute the public feed for negligible signal gain. The strategy
makes that decision explicit in
[§6 — Prioritization framework](./strategy.md#6-prioritization-framework).

## How this fits the rest of the portfolio

This repo is the connective tissue for three automation suites that
all target Hacker News:

- [`hn-quality-monitor`](https://github.com/santiagodalmas/hn-quality-monitor)
  automates `TC-BROWSE-001` / `002` / `003`.
- [`a11y-audit`](https://github.com/santiagodalmas/a11y-audit) covers
  the accessibility dimension, which this strategy lists as
  in-scope but tracks via its own rule-by-rule reports.
- [`restful-booker-tests`](https://github.com/santiagodalmas/restful-booker-tests)
  uses similar negative-path patterns, applied to a different target
  for explicit bug-hunt practice.

Reading order if you're evaluating this portfolio:

1. **This repo** — start with [`strategy.md`](./strategy.md) for the
   thinking.
2. **`hn-quality-monitor`** — see the automation that implements the
   browsing cases.
3. **`a11y-audit`** — see the accessibility dimension treated
   separately.
4. **`restful-booker-tests`** — see the bug-hunt approach applied
   somewhere bug-finding is the explicit goal.

## Why a docs-only repo

For QA work, *what you choose not to test* and *why* are usually as
revealing as the tests themselves. A docs-only repo lets the strategy
breathe without competing with executable code for attention. Anyone
reviewing this portfolio can read `strategy.md` and `traceability.md`
in five minutes and understand:

- the testing footprint across all three other repos,
- the gaps that exist *intentionally*,
- the framework for deciding what becomes automation vs. what stays
  manual.

## CI

The only CI on this repo is a Markdown link checker — it ensures all
internal cross-references and external citations stay valid. Runs on
every push and weekly via cron.
