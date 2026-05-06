# TC-AUTH-002 — Login with invalid password (no account enumeration)

| Field    | Value         |
|----------|---------------|
| ID       | TC-AUTH-002   |
| Feature  | Authentication |
| Priority | P0            |
| Type     | Negative / Security |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | auth, login, security, account-enumeration |

## Preconditions

- A valid username `testacct1` (correct password known but **not used**
  in this case).
- A guaranteed-nonexistent username `__no_such_user_xxx_2026__`.
- Anonymous browser session.

## Steps

1. Navigate to `https://news.ycombinator.com/login`.
2. **Run A** — enter `testacct1` (real user) with a deliberately wrong
   password. Submit. Note the response copy and HTTP status.
3. Return to `/login`.
4. **Run B** — enter `__no_such_user_xxx_2026__` (no such account) with
   any password. Submit. Note the response copy and HTTP status.

## Expected

- Both runs return the same error response: same status code, same
  error message, same response time within reason (±200ms).
- The error message must not differ between the two cases — i.e. it
  must not say *"wrong password"* in Run A and *"no such user"* in
  Run B.
- No `user` cookie is set on either response.
- The page does not auto-redirect to a logged-in surface.

## Postconditions / cleanup

- Browser is still anonymous; no state to clean up.

## Notes

- This case guards against **username enumeration**, a low-impact but
  real security weakness where an attacker can probe the login form
  to discover which usernames exist. Best practice is for failed
  logins to return a generic error regardless of which field is
  wrong.
- For comparison, [restful-booker's `/auth`
  endpoint](https://github.com/santiagodalmas/restful-booker-tests/blob/main/findings/2026-05-06-bad-credentials-returns-200.md)
  has a related but different bug: it returns 200 OK on failed auth
  rather than 401. Different system, different defect, similar
  category.
