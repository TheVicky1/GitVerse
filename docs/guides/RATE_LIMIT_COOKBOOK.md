# GitHub API Rate Limit Cookbook

How GitHub's REST API rate limits work, how to read the headers, and what GitVerse actually does about them. Statements about GitVerse are taken from the code and name the file. Statements about GitHub's limits come from GitHub's REST API rate limit documentation; check it for current values and for GitHub Apps and Enterprise limits, which this page does not cover.

## GitHub's primary rate limits

As documented by GitHub for the REST API:

- Unauthenticated requests: 60 requests per hour, tied to the originating IP address.
- Authenticated requests: 5,000 requests per hour for a user.

## Response headers

Every REST API response carries these headers (per GitHub's docs):

| Header | Meaning |
| :--- | :--- |
| `X-RateLimit-Limit` | Maximum number of requests allowed in the current window |
| `X-RateLimit-Remaining` | Requests left in the current window |
| `X-RateLimit-Used` | Requests made in the current window |
| `X-RateLimit-Reset` | When the window resets, in UTC epoch seconds |
| `X-RateLimit-Resource` | Which rate limit bucket the request counted against (for example `core` or `search`) |

Secondary rate limits are separate. When one is hit, GitHub answers 403 or 429 and may include a `Retry-After` header (seconds to wait).

## Recipes

Check your remaining quota (this call does not count against the primary limit, per GitHub's docs):

```bash
curl -s -H "Authorization: Bearer $TOKEN" \
     -H "Accept: application/vnd.github+json" \
     https://api.github.com/rate_limit
```

Print the headers of any call:

```bash
curl -sI -H "Authorization: Bearer $TOKEN" https://api.github.com/user | grep -i '^x-ratelimit'
```

Convert the reset time to a readable date:

```bash
date -u -d @"$RESET_EPOCH"      # GNU date (Linux, Git Bash)
```

Back off on a 403 or 429, following GitHub's guidance (a sketch, not code GitVerse contains):

```bash
# 1. If Retry-After is present: sleep that many seconds.
# 2. Else if X-RateLimit-Remaining is 0: sleep until X-RateLimit-Reset.
# 3. Else: wait at least a minute, then double the wait on each further failure.
```

## What GitVerse does

### It reads most badge data from github.com pages, not the REST API

`internal/official/official.go` documents that the earned badges, tiers, unlock dates and milestone history are not in GitHub's API. They come from the public profile page and from `/users/<user>/achievements/<slug>/detail` on `github.com`. The client requests `https://github.com` paths, not `api.github.com`, so these calls do not go through the REST API headers above. GitVerse does not assume they are unlimited: any non-200 response (other than a 404 for a missing user) is returned as an error `HTTP <status>`.

To stay polite, `internal/report/report.go` fetches the badge detail pages with at most 4 requests in flight (the `sem` channel in `fetchDetails`).

### REST and GraphQL calls

`internal/counters/counters.go` makes the API calls: merged pull requests (search), repositories and stars (REST), and accepted answers, sponsorships and highlight flags (GraphQL).

- Fewer calls by design: merged PRs are counted once per report (`mergedPRs` uses a `sync.Once`),; with a token the merged-PR search lists 100 results per page, and the public fallback asks for one result (`per_page=1`) because it only needs the total.
- Without a token (public fallback): merged PR and star counts still work, GraphQL-only data is skipped (`Facts` returns nil). A 403 or 429 from the API becomes `ErrRateLimited`: "GitHub's API rate limit for anonymous use was hit; run `gh auth login`". In the report, that failure appears as a note on the affected badge ("could not count: ...") rather than failing the whole report.
- The CLI uses your existing `gh auth login` token (`counters.New`). The web handlers use a server-side bot token for public checks, or the signed-in user's token for a private check (`counters.NewWithToken`).

### Caching on the web

GitVerse has no in-memory cache of star or follower counts in Go code. Caching is done with HTTP response headers that Vercel's CDN honors:

| Route | `Cache-Control` | Source |
| :--- | :--- | :--- |
| `/result?u=<login>` (success) | `public, s-maxage=3600, stale-while-revalidate=86400` | `web/handlers.go` |
| `/result` (error page) | `public, s-maxage=60` | `web/handlers.go` |
| `/api/stars` | `public, s-maxage=60, stale-while-revalidate=300` | `web/handlers.go` |
| `/api/private` | `no-store` | `web/handlers.go` |

`/api/stars` makes one request for the repository's `stargazers_count` and, if GitHub does not answer with 200 or the body does not parse, returns a built-in fallback value instead of an error.

### Failing gracefully on the web

`Result` maps a failure to a message and status instead of crashing: a timeout (the report has a 50 second budget, `Timeout` in `FromEnv`) answers 504 and suggests the CLI; any other GitHub failure answers 502 with "GitHub did not answer. Please try again in a minute."

GitVerse does not retry and does not implement exponential backoff for failed GitHub calls in the code shown above.

## Practical advice

- Run the CLI after `gh auth login` for the higher authenticated quota.
- Do not poll in a loop. Check `X-RateLimit-Remaining` and `X-RateLimit-Reset` and wait for the reset.
- Honor `Retry-After` when GitHub sends it.
