---
name: Value a UK property with the Hometrack Valuation API
description: Check availability, exchange an API key for a short-lived token, and run a real-time AVM valuation on a single property.
api: openapi/hometrack-valuation-api-v1-openapi.yml
base_url: https://api.hometrack.com/valuation-api/v1
operations: [status, authentication, valuation]
generated: '2026-07-26'
method: generated
---

# Value a UK property with the Hometrack Valuation API

Hometrack's Valuation API is its real-time AVM: one request in, an automated
valuation with a confidence band out. It has its own credential model that is
older than the platform-wide OAuth flow — read the auth step carefully.

## Before you start

You need a Hometrack **API key** and an **account ID**, both issued under a
commercial agreement. There is no self-serve signup, no trial and no sandbox
(<https://www.hometrack.com/uk/#contactus>). Every call below runs against
production.

## Steps

1. **Check availability.** Call `status` (`GET /status`). HTTP 200 means the
   Valuation API is up; HTTP 503 means it is unavailable. The body is empty in
   both cases — read the status code, not the body. This is the only Hometrack
   operation that answers without a credential, so it is also the right
   pre-flight check before you burn a token.

2. **Exchange your API key for a token.** Call `authentication`
   (`POST /authentication/{apiKey}`). The response is
   `{"token": "<guid>", "links": [{"rel": "valuation", "href": "/v1/valuation"}]}`.
   **The token is valid for 5 minutes only.** Do not cache it beyond that; do
   not attempt to reuse it across processes.

3. **Run the valuation.** Call `valuation` (`POST /valuation/{accountId}`) with
   the token in an `Authorization` header in Hometrack's own format:

   ```
   Authorization: Token token="652da107-bb66-4886-9f73-d8d4a3243eb7"
   ```

   The spec states the looser variants `Token token=<guid>` and `Token <guid>`
   are also accepted. Send the property in the request body per the
   `Valuation request definition` schema.

4. **Read the result.** The response (`Valuation API Response Definition`)
   carries `propertyValuation` with `value`, `upperValue`, `lowerValue`,
   `confidenceBand` (`high` / `medium` / `low`) and `effectiveDate`. **Always
   surface the confidence band with the number** — a low-confidence AVM is a
   different product from a high-confidence one, and downstream Hometrack
   surfaces will refuse to produce a report when confidence falls below a
   configured minimum (see the 499 case in `errors/hometrack-problem-types.yml`).

## Rules

- **No idempotency.** There is no idempotency key on this API. A retried
  valuation is a second billable valuation. If step 3 times out, re-check
  before re-sending.
- **Rate limits are real but unpublished.** HTTP 429 means "Rate limit is
  exceeded. The response body will tell you when you can try again" — the retry
  interval is in the body (e.g. "Try again in 60 seconds"), not in a
  `Retry-After` header. Parse the body.
- **401 usually means an expired token**, not a bad key — the 5-minute window is
  short. Re-run step 2 rather than escalating.
- **403** means the account lacks the permission or licence; that is commercial,
  not technical.

## Related

- `authentication/hometrack-authentication.yml` — all three of Hometrack's credential layers
- `errors/hometrack-problem-types.yml` — full status catalogue
- `conventions/hometrack-conventions.yml` — rate limiting, error envelopes, retry posture
