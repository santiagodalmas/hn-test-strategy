# TC-AUTH-001 — Login with valid credentials

| Field    | Value         |
|----------|---------------|
| ID       | TC-AUTH-001   |
| Feature  | Authentication |
| Priority | P0            |
| Type     | Smoke / Functional |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | auth, login, smoke |

## Preconditions

- Test account with **known username and password** (`testacct1`).
- Browser session is anonymous (no existing HN cookie).
- Network connection is good; HN is responding (verify via `/ping` or `/`).

## Steps

1. Navigate to `https://news.ycombinator.com/login`.
2. In the *login* table (top of page), enter the username in the
   `acct` field.
3. Enter the password in the `pw` field.
4. Click the **login** submit button.

## Expected

- HTTP 302 redirect to `/news` (or wherever `goto` parameter pointed).
- A `user` cookie is set (`Set-Cookie: user=...; Path=/`).
- On the redirected page, the top-right of the masthead shows the
  account's username and karma score, replacing the "login" link.
- No error text appears anywhere on the page.

## Postconditions / cleanup

- Logged-in session remains active for follow-up cases. If this is the
  last case in a manual run, sign out via TC-AUTH-004.

## Notes

- This is the gating case for any other write-path test (submit,
  vote, comment). If TC-AUTH-001 fails, all dependent manual cases
  are skipped and a finding is opened immediately.
- The login form has known accessibility issues — see
  [`a11y-audit/findings/2026-05-06-form-label.md`](https://github.com/santiagodalmas/a11y-audit/blob/main/findings/2026-05-06-form-label.md).
  That is a separate, known issue; this case is not the place to
  re-flag it.
