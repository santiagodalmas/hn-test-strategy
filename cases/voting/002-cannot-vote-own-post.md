# TC-VOTE-002 — User cannot vote on their own post

| Field    | Value         |
|----------|---------------|
| ID       | TC-VOTE-002   |
| Feature  | Voting        |
| Priority | P1            |
| Type     | Negative / Functional |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | voting, self-vote, anti-abuse |

## Preconditions

- Logged in as the user who submitted the story under test
  (`testacct1` immediately after TC-SUB-001).

## Steps

1. Navigate to `/newest` and locate `testacct1`'s recently submitted
   story.
2. Observe the area to the left of the story title where the upward
   triangle would normally appear.
3. (Optional) Try forcing a vote via the URL by manually constructing
   the vote endpoint — `https://news.ycombinator.com/vote?id=<id>&how=up&auth=<token>` — using the auth token from a real upvote elsewhere
   on the page.

## Expected

- **Step 2:** No upward triangle is rendered next to the user's own
  story. The position is empty or contains a non-clickable spacer.
- **Step 3:** The forced vote URL returns the user to a page (or
  refresh) where their story still has no upvote indication and no
  point count change attributable to them.

## Postconditions / cleanup

- No state to revert (no vote should have been recorded).

## Notes

- This guards against a known anti-abuse vector: self-promotion via
  upvoting one's own posts. Server-side enforcement is critical — UI
  hiding alone is not sufficient if the underlying endpoint accepts
  the call.
- The "force via URL" step in (3) is intentionally adversarial. If
  it succeeds, that is a P0 finding regardless of where TC-VOTE-002
  was meant to sit on the priority list.
