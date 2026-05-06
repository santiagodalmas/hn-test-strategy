# TC-BROWSE-003 — Top 10 stories on `/news` appear within API's top 50

| Field    | Value         |
|----------|---------------|
| ID       | TC-BROWSE-003 |
| Feature  | Browsing / API |
| Priority | P1            |
| Type     | Cross-layer / Contract |
| Coverage | **Automated** by [`hn-quality-monitor/src/crosscheck/ui-vs-api.js`](https://github.com/santiagodalmas/hn-quality-monitor/blob/main/src/crosscheck/ui-vs-api.js) |
| Cadence  | Daily (CI)    |
| Tags     | browsing, api, contract, cross-layer |

## Preconditions

- Anonymous session.
- Both surfaces respond:
  - `https://news.ycombinator.com/news` (UI)
  - `https://hacker-news.firebaseio.com/v0/topstories.json` (API)

## Steps

1. Fetch `/topstories.json`. Slice the first 50 ids — call this set
   `apiTop50`.
2. Load `/news` in a browser. Collect the first 10 `<tr class="athing">`
   rows by their `id` — call this list `uiTop10`.
3. For each id in `uiTop10`, assert it appears in `apiTop50`.

## Expected

- Every id in `uiTop10` is found in `apiTop50`.
- A single missing id is **not** treated as a hard defect — the UI's
  HTML is cached and the API list is dynamic, so small drift is
  expected. The contract is set-based with a generous window.
- Two or more missing ids in a single run is a finding worth
  investigating: it indicates a real divergence between the two
  surfaces.

## Postconditions / cleanup

- None.

## Notes

- The deliberately generous window (`UI_TOP_N=10`, `API_TOP_M=50`) was
  chosen after observing the natural drift between the cached HTML
  and the API. A stricter rank-equality check would produce false
  alarms; a wider window would dilute the test.
- Tunable thresholds are documented in the
  [crosscheck script](https://github.com/santiagodalmas/hn-quality-monitor/blob/main/src/crosscheck/ui-vs-api.js).
