# TC-AUTH-004 — Logout clears the session everywhere

| Field    | Value         |
|----------|---------------|
| ID       | TC-AUTH-004   |
| Feature  | Authentication |
| Priority | P1            |
| Type     | Functional / Security |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | auth, logout, session |

## Preconditions

- Logged-in session in **Tab A**.
- A second **Tab B** open to `/news`, also showing the logged-in
  state for the same user.

## Steps

1. In **Tab A**, click the **logout** link in the top-right of the
   masthead.
2. Verify Tab A returns to the anonymous home page (`/news` with a
   "login" link in the masthead).
3. **Reload Tab B.** Verify Tab B also reflects the logged-out state
   without manual interaction.
4. From Tab A's anonymous state, attempt to access a write-protected
   surface: e.g. click "submit" in the masthead.

## Expected

- Logout link returns HTTP 302 with `Set-Cookie: user=; Max-Age=0`
  (or equivalent invalidation).
- The `user` cookie is no longer present after logout.
- Tab B, on reload, no longer shows username or karma.
- Step 4 redirects to `/login` rather than allowing access.

## Postconditions / cleanup

- Browser is in clean anonymous state. Ready for another test cycle.

## Notes

- This case specifically guards against the "stale session in another
  tab" bug, which is a recurring class of issue in cookie-based auth.
  Server-side logout should invalidate the session for *all* tabs,
  not just the one that triggered logout — this is harder than it
  sounds when the client-side check is purely cookie-based.
