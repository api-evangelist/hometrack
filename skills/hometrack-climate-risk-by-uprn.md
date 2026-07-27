---
name: Assemble a property climate-risk profile by UPRN
description: Pull Hometrack's five climate and energy datasets — EPC, flood, ground, subsidence and coastal erosion — for a single UK property addressed by its UPRN.
api: openapi/hometrack-climate-api-v2-openapi.yml
base_url: https://api.hometrack.com/climate
operations: [Epc, Flood, Ground, SubsidenceScoreData, CoastalErosionData]
generated: '2026-07-26'
method: generated
---

# Assemble a property climate-risk profile by UPRN

Hometrack's Climate API returns five independent risk datasets for one
property. It is the cleanest API in Hometrack's estate: five GETs, one
identifier, explicit tracing headers.

## Before you start

**You need the UPRN.** The Unique Property Reference Number is the UK's
government-issued national property identifier (issued by GeoPlace, distributed
by Ordnance Survey). Every Climate path is `/<dataset>/{uprn}`; a postcode will
not address this API. If you only have an address, resolve the UPRN first —
Hometrack's PRH property repository search
(`get-organisation-orgid-property-repository`) accepts a `uprn` filter but does
not resolve one from an address, so this usually comes from your own address
data.

Authenticate with an OAuth 2.0 client-credentials bearer token (see
`authentication/hometrack-authentication.yml`).

## Steps

Call the five operations for the same `{uprn}`. They are independent — run them
concurrently.

1. `Epc` — `GET /epc-hometrack/{uprn}` — EPC (energy performance) data.
   Response `EnergyCertificateResponseModelV2` / `EnergyRating`. Supplier:
   Hometrack.
2. `Flood` — `GET /flood-twinn/{uprn}` — flood data.
   Response `FloodScoreResponseModelV2`. Supplier: Twinn.
3. `Ground` — `GET /ground-terrafirma/{uprn}` — ground data.
   Response `GroundScoreResponseModelV2`. Supplier: Terrafirma.
4. `SubsidenceScoreData` — `GET /ground-subsidence-twinn/{uprn}` — subsidence
   score data. Response `SubsidenceScoreResponseModelV2` with current and future
   models. Supplier: Twinn.
5. `CoastalErosionData` — `GET /ground-coastalerosion-twinn/{uprn}` — coastal
   erosion data. Response `CoastalErosionScoreResponseModelV2` with current,
   future and management-plan models. Supplier: Twinn.

Four of the five accept a `dataVersion` parameter. Pin it when you need
reproducible risk scoring across a portfolio run; omit it to take the current
version.

## Always send the tracing headers

Every Climate operation accepts three headers, and this is the only Hometrack
API that offers them:

- `X-Client-ID` — your caller identifier
- `X-Client-Reference` — your own reference for this request
- `X-Correlation-ID` — a correlation id you generate, so one property lookup can
  be traced across all five calls and across Hometrack's suppliers

Send all three on all five calls with the same `X-Correlation-ID`. When you
raise a support case about a discrepancy, this is the only evidence you will
have.

## Rules

- **A 404 means no data for that UPRN**, not a bad request — each operation
  returns its own message ("EPC data not found", "Flood data not found",
  "Ground data not found", "SubsidenceScoreData data not found",
  "CoastalErosionData data not found"). Treat it as data absence and carry on
  with a partial profile; do not retry.
- **400** = "Request was not valid" — check the UPRN format.
- **Suppliers matter.** Three of the five datasets are Twinn's and one is
  Terrafirma's. If you are reporting risk to a lender or a borrower, attribute
  the source; the path segment names it.
- The Climate GraphQL API (`/climate/graphql`) fronts the same backend but its
  schema is auth-gated and could not be captured — do not assume field parity
  with these five REST operations.

## Related

- `conventions/hometrack-conventions.yml` — correlation headers, error envelopes
- `data-model/hometrack-data-model.yml` — the UPRN-keyed climate entities
- `conformance/hometrack-conformance.yml` — why UPRN, and why not RESO
