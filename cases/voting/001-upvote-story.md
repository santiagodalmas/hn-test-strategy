# TC-VOTE-001 — Upvote a story (logged in, sufficient karma)

| Field    | Value         |
|----------|---------------|
| ID       | TC-VOTE-001   |
| Feature  | Voting        |
| Priority | P0            |
| Type     | Smoke / Functional |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | voting, upvote, write-path |

## Preconditions

- Logged in as `testacct2` (the high-karma test account).
- A story on `/newest` posted by *another* user (TC-VOTE-002 covers
  the own-post case).

## Steps

1. On `/newest`, locate a target story (note its current `points`
   value if visible, and its `id`).
2. Click the upward triangle (`votearrow`) to the left of the story.
3. The triangle disappears (visual confirmation of vote).
4. Reload the page.

## Expected

- After step 3, the upward triangle is gone for that story for the
  current session.
- After reload, the triangle remains absent, confirming the vote
  persisted server-side.
- If `points` is visible on the listing (it isn't always for new
  stories), it should be ≥ the previously observed value (it may have
  also been upvoted/downvoted by others, so equality is not required).
- No error or interstitial page appears.

## Postconditions / cleanup

- **Revert the vote** by visiting the story's item page and clicking
  the *unvote* link. This is required by the data-hygiene rule in
  the strategy.

## Notes

- Vote actions are GET requests with a session token in the query
  string — they're idempotent on retry, but should be reverted to
  avoid skewing the public ranking.
- HN does not show a confirmation dialog or animation; *the absence
  of the arrow* is the only visual signal.
