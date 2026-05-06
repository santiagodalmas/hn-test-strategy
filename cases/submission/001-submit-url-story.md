# TC-SUB-001 — Submit a URL story (happy path)

| Field    | Value         |
|----------|---------------|
| ID       | TC-SUB-001    |
| Feature  | Submission    |
| Priority | P0            |
| Type     | Smoke / Functional |
| Coverage | Manual        |
| Cadence  | Per-release (write-path; rate-limited) |
| Tags     | submission, write-path, rate-limited |

## Preconditions

- Logged in as `testacct1`.
- A unique URL that has never been submitted to HN (see *Notes*).
- Title prefixed with `[TESTING]` per the data-hygiene rule in the
  strategy.

## Test data

- URL: a personal blog post or a fresh GitHub Pages URL controlled
  by the tester. Verifying *uniqueness* is the responsibility of
  TC-SUB-003, not this case.
- Title: `[TESTING] HN submission flow check 2026-05-06`.

## Steps

1. From `/news`, click **submit** in the top masthead.
2. On `/submit`, enter the URL in the *url* field and the title in
   the *title* field. Leave *text* empty.
3. Click the **submit** button.

## Expected

- HTTP 302 to `/newest` (or the new item page, depending on flow).
- The submitted story appears at the top of `/newest`.
- The story's `<span class="age">` `title` attribute encodes a
  timestamp within the last 60 seconds.
- The story is attributed to `testacct1`.
- No "duplicate" interstitial or "rate-limited" page is shown.

## Postconditions / cleanup

- The story remains live for ≤1 hour, then is deleted by the tester
  via the story's *delete* link (newly submitted stories can be
  deleted by their author within a window).
- Vote counts on the story, if any, are not interfered with.

## Notes

- HN rate-limits submissions per-user. A failed run should not retry
  immediately; wait at least 30 minutes between submission attempts.
- Deletion window for the submitter is roughly 2 hours; document any
  observed change.
- This case is intentionally manual. Automating a write-path test
  against a third-party production system would consume submission
  quota and pollute the public feed — a poor cost/benefit trade.
