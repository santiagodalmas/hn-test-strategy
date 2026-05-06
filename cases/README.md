# Test cases

Concrete test cases derived from [`../strategy.md`](../strategy.md).
Each case is a self-contained markdown file in the format described in
[`_template.md`](./_template.md).

## Index

### Authentication

| ID | Priority | Title | Coverage |
|---|---|---|---|
| [TC-AUTH-001](./auth/001-login-happy-path.md) | P0 | Login with valid credentials | Manual |
| [TC-AUTH-002](./auth/002-login-invalid-password.md) | P0 | Login with invalid password (no account enumeration) | Manual |
| [TC-AUTH-003](./auth/003-session-persistence.md) | P1 | Session persists across page reloads and tab restarts | Manual |
| [TC-AUTH-004](./auth/004-logout.md) | P1 | Logout clears the session everywhere | Manual |

### Submission

| ID | Priority | Title | Coverage |
|---|---|---|---|
| [TC-SUB-001](./submission/001-submit-url-story.md) | P0 | Submit a URL story (happy path) | Manual |
| [TC-SUB-002](./submission/002-submit-text-story.md) | P1 | Submit an Ask HN text-only story | Manual |
| [TC-SUB-003](./submission/003-duplicate-url.md) | P2 | Submitting a duplicate URL surfaces the existing story | Manual |

### Voting

| ID | Priority | Title | Coverage |
|---|---|---|---|
| [TC-VOTE-001](./voting/001-upvote-story.md) | P0 | Upvote a story (logged in, sufficient karma) | Manual |
| [TC-VOTE-002](./voting/002-cannot-vote-own-post.md) | P1 | User cannot vote on their own post | Manual |
| [TC-VOTE-003](./voting/003-flag-story.md) | P2 | Flagging a story records the action without UI confirmation | Manual |

### Browsing

| ID | Priority | Title | Coverage |
|---|---|---|---|
| [TC-BROWSE-001](./browsing/001-newest-sort-order.md) | P0 | First 100 articles on `/newest` are sorted newest→oldest | **Automated** by `hn-quality-monitor` |
| [TC-BROWSE-002](./browsing/002-pagination-more-link.md) | P0 | "More" link advances 30 articles per click without duplication | **Automated** by `hn-quality-monitor` |
| [TC-BROWSE-003](./browsing/003-ui-api-agreement.md) | P1 | Top 10 stories on `/news` appear within API's top 50 | **Automated** by `hn-quality-monitor` |

### Comments

| ID | Priority | Title | Coverage |
|---|---|---|---|
| [TC-COMMENT-001](./comments/001-post-top-level-comment.md) | P1 | Post a top-level comment on a story | Manual |
| [TC-COMMENT-002](./comments/002-deep-thread-rendering.md) | P2 | Deeply nested reply chain renders in correct order | Manual |

The cross-cutting view of which suite automates which case is in
[`../traceability.md`](../traceability.md).
