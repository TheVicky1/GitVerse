# GitVerse Threat Model: Token Handling

This document describes how the GitVerse web app handles the GitHub OAuth token used for private-repository checks, and which mitigations are visible in the code. It makes no claim that cannot be checked in the repository. Where something depends on the hosting platform or on behavior outside this code, it says so. For the project's policy statements and how to report a vulnerability, see [SECURITY.md](../../SECURITY.md).

Scope: the Go handlers in `web/` (`oauth.go`, `handlers.go`, `headers.go`), `internal/counters`, and `site/callback.js`. The CLI (`cmd/gitverse`) uses your existing `gh` login on your machine and is not covered here.

## Architecture in brief

- The repository's only direct Go dependency is `go-gh`, and the handlers use no database or session store; the only state between sign-in steps is a signed cookie in the browser.
- Public checks (`/result`) use a server-side bot token (`GH_BOT_TOKEN`), not the visitor's credentials.
- Private checks use the visitor's own OAuth token, for one request only (`/api/private`).
- Secrets come from environment variables (`GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `GH_BOT_TOKEN`, `STATE_SECRET`). `FromEnv` rejects a `STATE_SECRET` shorter than 32 bytes and, when variables are missing, reports their names only.

## Token lifecycle

1. **Start.** `GET /auth/login` creates a random nonce and a random PKCE code verifier (`NewState`, using `crypto/rand`), signs them into a cookie, and redirects to GitHub's authorize URL with `state=<nonce>`, `code_challenge` (S256) and `scope=repo`.
2. **Callback.** GitHub redirects the browser to `/auth/callback`, a static page. `callback.js` removes `code` and `state` from the address bar (`history.replaceState`) and POSTs them to `/api/private`.
3. **Verify.** `/api/private` first clears the state cookie (it is single use), checks the content type and caps the body at 4 KiB, then verifies the cookie's HMAC-SHA256 signature, that the nonce matches the `state` GitHub echoed back, and that the state is not older than 10 minutes (`StateMaxAge`).
4. **Exchange.** The server exchanges the code for an access token at GitHub's token endpoint, sending the client secret and the PKCE verifier. A refresh token, if returned, is ignored (`Exchange`).
5. **Use.** The token is used to read the signed-in user's login (`GET /user`) and to build the report through `counters.NewWithToken`. One 50 second deadline covers the exchange, the user lookup and the report.
6. **Revoke.** Right after a successful exchange, the handler defers a revoke: `DELETE /applications/{client_id}/token` with the token in the body, authenticated with the client ID and secret. It runs on a fresh context with its own 8 second budget, so it runs even if the request context has timed out or the client left. The variable holding the token is then set to an empty string.
7. **Respond.** The response is the rendered report fragment with `Cache-Control: no-store`, `X-Robots-Tag: noindex` and `Referrer-Policy: no-referrer`.

The token is never written to a response, to a cookie, or to a log line by the handler code: log statements print error types or fixed messages (for example `private: %T`), and the comments in `handlers.go` state that panic values and errors are not logged.

Limits of this guarantee, from the code:

- If the revoke call fails (network error or any status other than 204), the failure is logged and the token is **not** retried; it stays valid until GitHub expires or the user revokes it.
- Revoking deletes the token, but the app's grant stays, so the next sign-in skips the consent screen (comment on `Revoke`).
- The requested scope is `repo`, which is broad. Consider that when deciding whether to sign in.
- Memory is not wiped: setting the variable to `""` drops the reference but Go gives no guarantee about when memory is cleared. The code does not claim otherwise.
- What the hosting platform logs or retains about requests is outside this repository.

## Threats and mitigations

| Threat | Mitigation in code | Not covered here |
| :--- | :--- | :--- |
| **Login CSRF / forged callback** | State cookie is HMAC-signed with `STATE_SECRET`, compared in constant time with the returned nonce, expires after 10 minutes and is cleared on use. The cookie is `HttpOnly`, `SameSite=Lax`, and `Secure` except on localhost. | Compromise of `STATE_SECRET` |
| **Authorization code interception** | PKCE with S256: the verifier lives only in the signed cookie and is sent on exchange. `callback.js` strips the code from the URL; `/auth/callback` is served with `Referrer-Policy: no-referrer` and `Cache-Control: no-store`. | |
| **Token or secret sent to another host via redirect** | The GitHub HTTP client refuses to follow redirects (`CheckRedirect` returns `http.ErrUseLastResponse`) and has a 20 second timeout. | |
| **Token persisting after use** | Deferred revoke (above); `no-store` on the response; no code path stores the token. | Revoke failures (logged only) |
| **Token written to logs** | `counters.NewWithToken` sets `LogIgnoreEnv: true` so `GH_DEBUG` cannot enable request logging; handlers log types and fixed messages only. | Platform-level request logging |
| **Process crash skipping revoke** | A panic inside report building is recovered and reported as a type name, so in-flight requests' deferred revokes still run (comment in `build`). | |
| **XSS** | Content-Security-Policy on every response: `default-src 'self'`, `script-src 'self' https://www.googletagmanager.com` (no `'unsafe-inline'`), `style-src 'self'`, `object-src 'none'`, `base-uri 'self'`, `frame-ancestors 'none'`. Also `X-Content-Type-Options: nosniff`. Result HTML comes from Go's `html/template` (per the comment in `callback.js`). | `callback.js` inserts the server HTML with `innerHTML`, so safety relies on the template escaping |
| **Oversized or wrong-type requests** | `/api/private` accepts only POST with `application/x-www-form-urlencoded` and a 4 KiB body limit. | |
| **Clickjacking** | `frame-ancestors 'none'` in the CSP. | |
| **Third-party requests** | CSP `connect-src` and `img-src` list the allowed hosts, including Google Analytics and Tag Manager. | |

The same header list is applied to static files through `vercel.json` (generated by `cmd/gendocs` from `web.SecurityHeaders()`) and by `cmd/devserver` locally.

## Not claimed

The code does not set a `Strict-Transport-Security` header, and TLS termination is done by the hosting platform, not by this code, so this document makes no statement about HSTS or TLS versions. It also makes no claim about how long a private check takes: the only bound in the code is the 50 second report deadline.

## Reporting a vulnerability

Do not open a public issue. Follow the private reporting options in [SECURITY.md](../../SECURITY.md).
