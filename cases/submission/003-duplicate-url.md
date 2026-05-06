# TC-SUB-003 — Submitting a duplicate URL surfaces the existing story

| Field    | Value         |
|----------|---------------|
| ID       | TC-SUB-003    |
| Feature  | Submission    |
| Priority | P2            |
| Type     | Functional / Edge case |
| Coverage | Manual        |
| Cadence  | Quarterly     |
| Tags     | submission, dedup, write-path |

## Preconditions

- Logged in as `testacct1`.
- A URL that **has been submitted to HN before** within the last ~12
  months (use any HN front-page story's URL — easy to find via
  `https://hn.algolia.com/?q=<domain>`).

## Test data

- URL: `https://example.com/some-known-article` (replace with a real
  recently-promoted story's URL).
- Title: any plausible title; HN should ignore it for duplicates.

## Steps

1. Navigate to `/submit`.
2. Paste the known-existing URL into the *url* field.
3. Enter a fresh title.
4. Click **submit**.

## Expected

- HN does **not** create a new story. Instead, it redirects to the
  existing story's `/item?id=…` page, or shows an interstitial linking
  to the existing story.
- No new entry appears on `/newest`.
- The existing story's vote count, age, and submitter are unchanged.

## Postconditions / cleanup

- No state to clean up — no new story was created.

## Notes

- Deduplication is reportedly URL-based (with some normalization for
  trailing slashes, common UTM params, and `www.`). If a non-obvious
  URL variation slips through, that's a finding worth filing.
- Run quarterly because this case is unlikely to regress without a
  significant submission-pipeline rewrite, but the cost of a regression
  here is meaningful (front-page noise).
