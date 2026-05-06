# TC-VOTE-003 — Flagging a story records the action

| Field    | Value         |
|----------|---------------|
| ID       | TC-VOTE-003   |
| Feature  | Voting        |
| Priority | P2            |
| Type     | Functional    |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | voting, flag, moderation |

## Preconditions

- Logged in as `testacct2` with sufficient karma to flag (HN documents
  a karma threshold, typically 30+).
- A story on `/newest` posted by another user that you can plausibly
  flag (use a deliberately self-submitted test story, then flag it
  from the high-karma account).

## Steps

1. On the target story's item page (`/item?id=<id>`), find the
   `flag` link in the row of action links under the title.
2. Click `flag`.

## Expected

- The page either reloads with the `flag` link replaced by `unflag`,
  or a confirmation interstitial is shown ("flagged.") that links
  back to the story.
- The flag is persisted: reloading the page still shows the
  flagged-state UI.
- The story's vote count is unchanged by the flag itself (a flag is
  not a downvote).

## Postconditions / cleanup

- **Unflag the story** by clicking `unflag` to revert. The data
  hygiene rule applies — flags from a test account should not affect
  public moderation signals.

## Notes

- HN's flag system is opaque: flags above a threshold contribute to
  removing a story from `/newest`, but the UI does not reveal flag
  counts. This case verifies the *user-facing affordance*, not the
  moderation outcome.
- If the flag link is missing entirely, that's likely a karma
  threshold issue rather than a defect — verify account karma
  before filing.
