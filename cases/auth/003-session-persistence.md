# TC-AUTH-003 — Session persists across page reloads and tab restarts

| Field    | Value         |
|----------|---------------|
| ID       | TC-AUTH-003   |
| Feature  | Authentication |
| Priority | P1            |
| Type     | Functional    |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | auth, session, cookie |

## Preconditions

- Logged-in session (TC-AUTH-001 has just passed).
- Browser is configured to persist cookies (default behavior; verify
  no privacy-mode flag).

## Steps

1. **Reload** the current page (`Cmd+R` / `Ctrl+R`). Confirm the
   masthead still shows username + karma.
2. **Open a new tab** to `https://news.ycombinator.com/`. Confirm the
   masthead shows username + karma without re-prompting for login.
3. **Close and reopen the browser** (full quit, not just the window).
   Open `https://news.ycombinator.com/`. Confirm the session is still
   active.
4. **Wait 24 hours** then revisit. Note: documented session lifetime
   varies — record what is observed.

## Expected

- Steps 1–3 keep the user logged in without re-authentication.
- The `user` cookie's `Expires`/`Max-Age` should reflect a long-lived
  session (HN documents "about a year"); record the actual value
  observed and flag any change between runs.
- No CSRF or session token is rotated mid-session in a way that breaks
  open tabs.

## Postconditions / cleanup

- Session remains active; no cleanup needed.

## Notes

- The 24-hour step is intentionally separate; it's slower than a
  per-release run can absorb, so in practice it's run on a quarterly
  cadence rather than per-release.
- A session that *silently* expires while the user is composing a
  long comment is a real and reported HN issue historically; if
  TC-AUTH-003 step 4 fails, escalate as P1.
