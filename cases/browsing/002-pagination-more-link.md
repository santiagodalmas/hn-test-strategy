# TC-BROWSE-002 — "More" link advances 30 articles per click without duplication

| Field    | Value         |
|----------|---------------|
| ID       | TC-BROWSE-002 |
| Feature  | Browsing      |
| Priority | P0            |
| Type     | Functional / Regression |
| Coverage | **Automated** indirectly by [`hn-quality-monitor`](https://github.com/santiagodalmas/hn-quality-monitor) (the sort validator depends on this behavior) |
| Cadence  | Daily (CI)    |
| Tags     | browsing, pagination, regression |

## Preconditions

- Anonymous browser session.
- HN is responding (no "Sorry, we're not able to serve your requests..." page).

## Steps

1. Navigate to `/newest`.
2. Record the set of `id` values from `<tr class="athing">` on the
   first page (expect 30 unique ids).
3. Click the **More** link.
4. Record the set of `id`s on the second page.
5. Repeat for two more pages.

## Expected

- Each page contains exactly **30** `<tr class="athing">` rows.
- The four pages combined yield exactly **120 distinct ids** — no
  overlap between pages, no missing ids on a page boundary.
- The "More" link's `href` updates between pages (it includes a
  `next=` parameter pointing at the boundary id).

## Postconditions / cleanup

- None.

## Notes

- The `hn-quality-monitor` UI sort validator depends on this
  invariant; a deduplication failure here would surface as
  `pagination unhealthy` retries or, if subtle enough, a sort
  validation failure with overlap.
- In practice, this case does fail occasionally from cloud IPs,
  but the failure mode is *empty page* rather than *duplicate*.
  See [finding 2026-05-05](https://github.com/santiagodalmas/hn-quality-monitor/blob/main/findings/2026-05-05-cloud-ip-empty-pagination.md).
