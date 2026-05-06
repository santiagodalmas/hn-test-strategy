# TC-COMMENT-002 — Deeply nested reply chain renders in correct order

| Field    | Value         |
|----------|---------------|
| ID       | TC-COMMENT-002 |
| Feature  | Comments      |
| Priority | P2            |
| Type     | Functional / Edge case |
| Coverage | Manual        |
| Cadence  | Quarterly     |
| Tags     | comments, threading, indent, rendering |

## Preconditions

- Anonymous session is fine — this is a read-side rendering check.
- A known story with a comment thread at least **6 replies deep**.
  Find one via `https://hn.algolia.com/?type=comment` and follow up
  the parent chain to confirm depth.

## Steps

1. Open `/item?id=<id>` for the chosen story.
2. Scroll to the deepest sub-thread. Verify reply order from top to
   bottom (parent → reply → reply-to-reply, …).
3. Verify the indent of each comment is greater than its parent's
   indent. HN uses fixed-pixel `<img>` spacers (`<img src="s.gif">`)
   to indent — count spacer widths to confirm.
4. Click `[-]` on the root parent of the sub-thread. Verify the entire
   sub-thread collapses.
5. Click `[+]` to expand. Verify ordering and indent are preserved
   exactly as before the collapse.

## Expected

- Linear top-to-bottom order corresponds to depth-first traversal
  of the reply tree (each comment immediately followed by its first
  reply, etc.).
- Indent monotonically increases by parent → child; siblings share
  indent.
- Collapse/expand preserves order and indent.
- No comment "jumps" position after expand.

## Postconditions / cleanup

- None — read-only.

## Notes

- HN's threading model is one of the simplest and most-loved
  comment systems on the web. Defects here are rare but high-impact:
  if a deep thread reorders or mis-indents, multi-week conversations
  become incomprehensible.
- The use of `<img src="s.gif">` for indentation is itself an a11y
  finding (decorative images without alt) — see
  [`a11y-audit/findings/2026-05-06-image-alt.md`](https://github.com/santiagodalmas/a11y-audit/blob/main/findings/2026-05-06-image-alt.md).
  This case ignores that and focuses on rendering correctness.
