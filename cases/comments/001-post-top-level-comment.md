# TC-COMMENT-001 — Post a top-level comment on a story

| Field    | Value         |
|----------|---------------|
| ID       | TC-COMMENT-001 |
| Feature  | Comments      |
| Priority | P1            |
| Type     | Functional / Smoke |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | comments, write-path, rate-limited |

## Preconditions

- Logged in as `testacct1`.
- A story on `/newest` posted by another user. Avoid commenting on
  hot front-page stories (they attract real readers and a test
  comment is intrusive).

## Test data

- Comment body: `[TESTING] verifying comment-post flow — please
  ignore.` Including the `[TESTING]` marker per the data-hygiene rule.

## Steps

1. From `/newest`, click the story's title to open `/item?id=<id>`.
2. In the comment box at the top of the page, type the test body.
3. Click **add comment**.

## Expected

- HTTP 302 to the story's item page with the new comment present.
- The new comment is attributed to `testacct1` and has an `age`
  within the last 60 seconds.
- The comment's text is rendered with HN's allowed inline markup
  (paragraphs, italics, links via auto-detection).
- A *delete* link is present on the new comment (within the deletion
  window).

## Postconditions / cleanup

- **Delete the comment** within 1 hour using the *delete* link on
  the comment itself. Public stories are real conversations and
  test data must not pollute them.

## Notes

- HN rate-limits comments per-user; account for this between case runs.
- If the deletion window has expired (occasionally HN tightens this),
  the test comment becomes permanent — file a finding documenting
  the observed deletion window vs. expected.
