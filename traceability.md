# Traceability — cases ↔ automation

This matrix maps every test case in [`cases/`](./cases/) to the
automation suite that runs it (or marks it `manual` / `not yet
covered`). When a case becomes automated, this file is updated in the
same change as the test code.

## Coverage summary

| Status | Count | % |
|---|---|---|
| Automated (CI) | 3 | 21% |
| Manual (per-release) | 9 | 64% |
| Manual (quarterly) | 2 | 14% |
| Not yet covered | 0 | 0% |
| **Total** | **14** | 100% |

Functional read-path is fully automated. Write-path (submission,
voting, comments) is manual by design — see
[strategy §6](./strategy.md#6-prioritization-framework). The
accessibility surface is automated separately by
[`a11y-audit`](https://github.com/santiagodalmas/a11y-audit) and is
not represented in this case set (it tests a different dimension and
is best tracked via the rule-by-rule reports there).

## Matrix

| ID | Title | P | Coverage | Repo / Job |
|---|---|---|---|---|
| TC-AUTH-001 | Login with valid credentials | P0 | Manual | — |
| TC-AUTH-002 | Login with invalid password (no enumeration) | P0 | Manual | — |
| TC-AUTH-003 | Session persists across reloads / tabs | P1 | Manual | — |
| TC-AUTH-004 | Logout clears the session everywhere | P1 | Manual | — |
| TC-SUB-001 | Submit a URL story (happy path) | P0 | Manual | — |
| TC-SUB-002 | Submit an Ask HN text-only story | P1 | Manual | — |
| TC-SUB-003 | Submitting a duplicate URL surfaces the existing story | P2 | Manual (quarterly) | — |
| TC-VOTE-001 | Upvote a story | P0 | Manual | — |
| TC-VOTE-002 | Cannot vote on own post | P1 | Manual | — |
| TC-VOTE-003 | Flagging a story records the action | P2 | Manual | — |
| TC-BROWSE-001 | First 100 articles on `/newest` are sorted newest→oldest | P0 | **Automated** | [`hn-quality-monitor` / UI sort validator](https://github.com/santiagodalmas/hn-quality-monitor) |
| TC-BROWSE-002 | "More" link advances 30 articles per click without duplication | P0 | **Automated (indirect)** | [`hn-quality-monitor` / UI sort validator](https://github.com/santiagodalmas/hn-quality-monitor) |
| TC-BROWSE-003 | Top 10 stories on `/news` appear within API's top 50 | P1 | **Automated** | [`hn-quality-monitor` / cross-check](https://github.com/santiagodalmas/hn-quality-monitor) |
| TC-COMMENT-001 | Post a top-level comment on a story | P1 | Manual | — |
| TC-COMMENT-002 | Deeply nested reply chain renders in correct order | P2 | Manual (quarterly) | — |

## Gaps

The strategy section [§5](./strategy.md#5-test-types--coverage)
explicitly calls out three coverage gaps not represented in this case
set:

- **Cross-browser smoke** — write a Playwright test that runs the
  read-path through Chromium, Firefox, and WebKit.
- **Performance perception** — page-load timing SLOs.
- **Security baselines** — CSP, HSTS, cookie flags, CSRF on write
  endpoints.

These are deliberately deferred — none are P0 for HN's risk profile,
and adding them now would dilute the focus of the existing suites.
