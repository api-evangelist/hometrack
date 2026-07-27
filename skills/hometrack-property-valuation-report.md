---
name: Request and download a Property Valuation Report
description: Exchange an API key for a token, check the account's licences, request a Property Valuation Report, and download the generated PDF or XML.
api: openapi/hometrack-api-public-openapi.yml
base_url: https://api.hometrack.com
operations: [AuthenticationApi_Post, LicencesApi_Licences, ReportingApi_RequestPropertyValuationReport, ReportingApi_RetrievePropertyValuationReport, BrandsApi_Get]
generated: '2026-07-26'
method: generated
---

# Request and download a Property Valuation Report

The Property Valuation Report (PVR) is Hometrack's consumer-facing valuation
document. It is produced asynchronously by the Hometrack API Public and
downloaded by transaction reference.

## Before you start

This API is called "public" but nothing in it is callable without a
Hometrack-issued API key. It uses the **legacy path-token** model, not the
platform OAuth flow: you exchange your API key for a GUID and then pass that
GUID as a **URL path segment** on nearly every operation. Treat that token as a
secret in a hostile place — it will appear in proxy and CDN logs. Keep its
lifetime short and never log full request URLs.

## Steps

1. **Exchange the API key.** Call `AuthenticationApi_Post`
   (`POST /api/authentication/{apiKey}`). It returns a plain-text GUID
   (`Token`). Every `{token}` below is this value.

2. **Check the licence first.** Call `LicencesApi_Licences`
   (`GET /api/licences/{token}/{product}`) — it "returns all the product
   licences (valid, expired, blocked, etc…) which belong to the authenticated
   account". Do this before requesting a report: a lapsed licence or an
   exhausted report allowance surfaces at request time as **HTTP 402 Payment
   Required** ("the license has lapsed or the report volume has been used up"),
   which is a commercial failure you would rather catch early.

3. **(Optional) Read the branding.** Call `BrandsApi_Get`
   (`GET /api/brands/{token}/{targetAccountApiKey}`) to see the co-branding the
   report will render with. "If a brand for the specific target account is not
   found, the default Hometrack brand configuration is returned."

4. **Request the report.** Call `ReportingApi_RequestPropertyValuationReport`
   (`POST /api/reporting/PropertyValuation`) with a
   `PropertyValuationReportRequest`. A successful request returns **201** and a
   `transactionReference` (`CreatedReportModel`). **Supply a unique transaction
   ID** — a duplicate returns **409** ("non-unique transaction ID is provided"),
   which is the closest thing this API has to duplicate-request protection. Use
   that deliberately: a stable, caller-generated transaction ID turns 409 into
   your idempotency check.

5. **Download the report.** Call
   `ReportingApi_RetrievePropertyValuationReport`
   (`GET /api/reporting/PropertyValuation/{token}/{transactionReference}`).
   **HTTP 202 means the report is still being generated** — back off and
   re-poll. HTTP 200 returns the file as `application/pdf` or
   `application/xml`.

## Rules

- **402 Payment Required** — licence lapsed or report volume exhausted.
  Commercial, not technical; stop and escalate.
- **499 Client Closed Request** — Hometrack reuses this non-standard code for a
  business outcome: "production failed when the confidence level for the subject
  property was lower than the minimum level specified in configuration", or a
  generic production failure. A 499 for confidence is **not** retryable — the
  AVM could not value the property to your configured threshold. Surface that to
  the user rather than looping.
- **410 Gone** — the transaction record exists but the PDF cannot be found.
- **503** — "this or a dependent service is unavailable" / "unable to
  authenticate at this time"; transient, retry with backoff.
- Do not call `ReportingApi_MoveToBlobStorage`, `ReportingApi_TestGet`,
  `ReportingApi_TestPost` or `ReportingApi_ProcessPropertyValuation` — these are
  internal plumbing (a scheduled task and two endpoints Hometrack itself labels
  "For test purpose") that were published to the catalogue as-is.

## Related

- `errors/hometrack-problem-types.yml` — the 402/409/410/499 semantics in full
- `components/hometrack-components.yml` — the PVR plugin, which orders the same report from a partner website
- `authentication/hometrack-authentication.yml` — why this API's token model differs
