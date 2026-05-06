# Test Strategy — Hacker News

A practical test strategy for [Hacker News](https://news.ycombinator.com/),
written as if I joined a small QA team responsible for it. It describes
what to test, what to skip, in what order, with what tooling, and how
findings are reported. The companion [`cases/`](./cases/) directory
holds concrete, executable-or-manual test cases derived from this
strategy.

This document is also the connective tissue for three of my other
repos:

- [`hn-quality-monitor`](https://github.com/santiagodalmas/hn-quality-monitor)
  — automated UI + API + cross-layer checks, daily.
- [`a11y-audit`](https://github.com/santiagodalmas/a11y-audit)
  — accessibility audits against WCAG 2.1 AA, daily.
- [`restful-booker-tests`](https://github.com/santiagodalmas/restful-booker-tests)
  — bug-hunt patterns I'd reuse here for negative-path testing.

The [traceability matrix](./traceability.md) shows which test cases
are automated by which suite.

## 1. Product summary

Hacker News is a community-curated news aggregator: read-mostly for
anonymous visitors, write-enabled for logged-in users (submit,
comment, vote, flag). Its surface is small (~10 page templates) but
high-traffic and load-bearing for the YC ecosystem. There is no
native mobile app — the mobile surface *is* the responsive web.

Key properties:
- Public Firebase API mirrors a subset of UI data.
- No GraphQL, no client-side framework — server-rendered HTML, minimal
  JS. Cheap to test.
- User actions (vote, comment, submit) are gated by **karma
  thresholds**, not by paid tiers — this is a meaningful test
  variable.
- Unmoderated submissions go to `/newest`; promoted stories to `/`.
  Promotion is algorithmic — a black box for testing purposes.

## 2. Goals

In order of priority:

1. **No silent regressions.** Functional behavior on the read path
   (browse, paginate, view item, follow comment threads) must stay
   correct day-to-day.
2. **Contract stability between UI and API.** Both surfaces are
   public; consumers exist for both. Drift between them is a defect.
3. **Accessibility floor.** A site this widely-used should meet
   WCAG 2.1 AA. Track regressions; document the current gap.
4. **Auth & write-path correctness on a representative sample.**
   Submissions, votes, comments — happy paths plus a small set of
   high-impact negative paths.
5. **Observability of upstream weather.** When HN returns
   rate-limit responses or upstream Cloudflare interferes, our test
   results must distinguish "system regressed" from "upstream noise."

Explicitly *not* a goal: full coverage. HN's rate limits and
write-side moderation make exhaustive automated testing impractical
and impolite — the strategy aims for high-signal, low-burden.

## 3. Scope

### In scope

- All anonymous read paths (`/`, `/newest`, `/ask`, `/show`, `/jobs`,
  `/item?id=…`, `/user?id=…`, `/from?site=…`).
- Public Firebase API at `hacker-news.firebaseio.com/v0`.
- Login flow, session persistence, logout.
- Voting, flagging, commenting (a representative sample, manually).
- Submission of URL and text posts (rate-limited; manual smoke only).
- Search via Algolia at `hn.algolia.com` (it's the de-facto search).
- Accessibility (WCAG 2.1 A + AA).

### Out of scope

- HN's promotion / ranking algorithm (a black box; YC owns it).
- Anti-spam and shadow-ban behavior (proprietary).
- Email flows (password reset, etc.) — could be added if credentials
  are available.
- Performance under load: HN handles its own scale; load-testing it
  from outside is impolite. *Performance perception* (response time
  SLO on common pages) is in scope.
- Native mobile apps — none exist.

## 4. Risk register

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | UI sort order drifts on `/newest` (newest→oldest invariant breaks) | Low | High (front-page consumer trust) | Daily UI sort validator |
| R2 | Firebase API schema changes silently | Low | High (third-party consumers break) | Daily API contract checks |
| R3 | UI and API disagree on top stories | Medium | Medium | Daily cross-check |
| R4 | New WCAG violations introduced in a CSS change | Medium | Medium | Daily a11y audit with regression-baseline thresholds |
| R5 | Login or session breaks for a subset of browsers | Low | High | Manual cross-browser smoke before/after CSS or HTML changes; not yet automated |
| R6 | Rate-limit or Cloudflare blocks our test runs from cloud IPs | High | Low (test infra, not product) | Polite delays, broad rate-limit detection, retries with backoff |
| R7 | Submission deduplication misclassifies a unique URL as duplicate | Low | Medium | Manual case in `cases/submission/` |
| R8 | Comment thread renders in wrong order under deep nesting | Low | Medium | Manual case in `cases/comments/` |

## 5. Test types & coverage

| Test type | Surface | Coverage today | Owner |
|---|---|---|---|
| Sort-order regression | UI `/newest` | Automated (daily) | `hn-quality-monitor` |
| API contract / schema | Firebase API | Automated (daily) | `hn-quality-monitor` |
| UI/API agreement | both | Automated (daily) | `hn-quality-monitor` |
| Accessibility (WCAG 2.1 AA) | UI x6 pages | Automated (daily) | `a11y-audit` |
| Auth happy/unhappy paths | UI login | Manual case set | this repo |
| Submission write-path | UI submit | Manual case set | this repo |
| Voting / karma gates | UI item / story | Manual case set | this repo |
| Comment thread rendering | UI item | Manual case set | this repo |
| Cross-browser smoke | UI | Not automated | gap |
| Performance perception | UI | Not automated | gap |
| Security (CSRF, XSS, headers) | UI/API | Not automated | gap |

The "gap" rows are documented intentionally — calling out what's *not*
covered is part of the strategy.

## 6. Prioritization framework

Test cases are tagged with one of:

- **P0** — broken P0 = "site is unusable for this user." Run on every
  release; automate when feasible.
- **P1** — important happy/unhappy path on a high-traffic surface.
  Automate when feasible; otherwise manually verified per release.
- **P2** — edge cases, rare states, polish bugs. Manual; explored
  during exploratory testing windows.
- **P3** — known-low-likelihood, documented for completeness.

Decision rule for "should this be automated?":

```
automate if:
  - case is P0 or P1
  - and the system surface is stable (selectors / endpoints)
  - and the case can run politely (no rate-limit hammering)
  - and the value of catching the regression > maintenance cost

otherwise:
  - manual on release, or "documented but not run"
```

Several HN cases (submission, voting) fail the third condition — they
require a logged-in account with sufficient karma and would consume
quota. They stay manual on purpose.

## 7. Environments & data

HN has a single public production environment. There is no staging.
Implications:

- All automated tests run against production. They must be
  **read-only by default**, **rate-polite**, and **idempotent**.
- Anything that mutates state (submission, vote, comment) is in the
  manual case set, run by a single test account, with explicit cleanup
  steps where applicable.
- Test data: a single dedicated test account with deliberately low
  karma, plus a second account with high karma for karma-gated
  features. Never a personal account.

Data hygiene rule: any submission made for testing must include the
prefix `[TESTING]` in the title and be deleted within 1 hour. Any vote
on someone else's content must be reverted within the same session.

## 8. Tooling decisions

| Need | Tool | Rationale |
|---|---|---|
| Browser automation | Playwright | Modern, multi-browser, has both browser and `request` contexts. |
| API testing (one-off) | Playwright `request` | No extra dependency. |
| API testing (collection-based) | Postman + Newman | Re-importable; collection runs in CI; HTML reports. |
| Accessibility | axe-core via `@axe-core/playwright` | De facto standard; clear severity model. |
| CI | GitHub Actions | Free for public repos; cron + artifact uploads cover all needs. |
| Reporting | Markdown in `findings/` | Reviewable in GitHub UI; no external dashboard. |

I deliberately avoid: Selenium (no advantage over Playwright for this
scope), Cypress (single-origin restriction is awkward for HN's
multi-host content), full BDD/Cucumber (overhead unjustified for a
solo QA effort on a stable surface).

## 9. Reporting & escalation

For each automated suite:

1. CI runs daily. Result is a pass/fail badge.
2. On failure, the suite uploads its artifact (markdown report or
   Newman HTML) for triage.
3. If the failure looks like a real regression (not upstream noise),
   a `findings/YYYY-MM-DD-short-title.md` is written following the
   structured QA-report template (severity, repro, expected vs
   actual, impact, proposed fix).

For manual cases:

1. Cases live in [`cases/`](./cases/) and are run on a documented
   cadence (see each case's "Cadence" field).
2. Results are recorded in the case file's "Run history" section.
3. Defects discovered during a manual run move to the relevant repo's
   `findings/`.

## 10. Coverage & traceability

The [traceability matrix](./traceability.md) maps every test case in
[`cases/`](./cases/) to one of:

- `automated by → <repo>` — covered by an existing CI job.
- `manual` — run by a human on the documented cadence.
- `not yet covered` — known gap.

The matrix is the source of truth for "what gets tested." When a new
case is written or a case becomes automated, the matrix is updated in
the same change.
