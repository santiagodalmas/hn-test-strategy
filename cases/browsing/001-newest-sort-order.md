# TC-BROWSE-001 — First 100 articles on `/newest` are sorted newest→oldest

| Field    | Value         |
|----------|---------------|
| ID       | TC-BROWSE-001 |
| Feature  | Browsing      |
| Priority | P0            |
| Type     | Regression    |
| Coverage | **Automated** by [`hn-quality-monitor`](https://github.com/santiagodalmas/hn-quality-monitor) (UI sort validator, runs daily) |
| Cadence  | Daily (CI)    |
| Tags     | browsing, sort, newest, regression |

## Preconditions

- Anonymous browser session (no login required).
- HN responding (verify `/ping` or load `/`).

## Steps

1. Navigate to `https://news.ycombinator.com/newest`.
2. Collect every `<tr class="athing">` row's `id` and the `title`
   attribute of its sibling `<span class="age">`.
3. Click the **More** link at the bottom of the list. Wait for the
   next page to load.
4. Repeat steps 2–3 until at least 100 articles have been collected.
5. Take exactly the first 100 articles (in the order they appeared
   across pagination).
6. For each adjacent pair `(article[i], article[i+1])`, parse the
   timestamps from the `title` attribute and verify
   `timestamp[i] >= timestamp[i+1]`.

## Expected

- Exactly 100 articles are collected (HN's "More" link advances 30 at
  a time, so 4 page loads suffice).
- Adjacent timestamps are non-increasing across the full set.
- Equal timestamps are allowed: two posts in the same minute is
  normal.

## Postconditions / cleanup

- None. Read-only path.

## Notes

- This case is the canonical example used by the
  [`hn-quality-monitor/src/ui/sort-validator.js`](https://github.com/santiagodalmas/hn-quality-monitor/blob/main/src/ui/sort-validator.js)
  script. The script also handles HN's rate-limit behavior and
  Cloudflare interference (see
  [finding](https://github.com/santiagodalmas/hn-quality-monitor/blob/main/findings/2026-05-05-cloud-ip-empty-pagination.md)).
- The timestamp source of truth is `<span class="age">`'s `title`
  attribute, which carries `YYYY-MM-DDTHH:MM:SS <unix>`. The unix
  value is preferred over the ISO string for timezone safety.
