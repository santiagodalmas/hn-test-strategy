# TC-SUB-002 — Submit an Ask HN text-only story

| Field    | Value         |
|----------|---------------|
| ID       | TC-SUB-002    |
| Feature  | Submission    |
| Priority | P1            |
| Type     | Functional    |
| Coverage | Manual        |
| Cadence  | Per-release   |
| Tags     | submission, write-path, ask-hn |

## Preconditions

- Logged in as `testacct1`.
- TC-SUB-001 has not been run within the last submission rate-limit
  window (~30 minutes).

## Test data

- Title: `Ask HN: [TESTING] sample question to verify text-post flow`.
- Text: a short paragraph (~80 words) including a code block in
  `<code>` tags or markdown to verify rendering.

## Steps

1. Navigate to `/submit`.
2. **Leave the *url* field blank.** Enter the title (which begins
   with `Ask HN:` per HN convention).
3. Enter the body in the *text* field.
4. Click **submit**.

## Expected

- The story appears under `/ask` rather than `/`.
- The body text renders with HN's allowed markup (paragraph breaks,
  inline code, links). Disallowed tags should be stripped or escaped,
  not rendered.
- The story has no URL associated with it (`<span class="titleline">`
  contains the title only, with no outbound link).
- Comments are enabled by default.

## Postconditions / cleanup

- Author deletes the story within 1 hour.

## Notes

- The "Ask HN:" / "Show HN:" / "Tell HN:" prefixes are conventions
  HN uses for filtering — verify the story is correctly routed to
  `/ask`. Mis-routing is rare but worth a manual check after any
  template change.
