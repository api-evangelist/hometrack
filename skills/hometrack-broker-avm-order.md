---
name: Order a broker AVM valuation and collect the result
description: Create a Hometrack Broker AVM valuation order (optionally with climate data), poll it to completion, and retrieve the valuation, its inputs and the raw audit projection.
api: openapi/hometrack-broker-avm-api-openapi.yml
base_url: https://api.hometrack.com/valuation/v2
operations: [ValuePropertyBrokerV2-Spec, ValuePropertyBroker-Spec, GetValuationOrderByIdV2-Spec, GetValuationStatusByIdV2-Spec, FindValuationByIdV2-Spec, GetValuationInputByIdV2-Spec, GetRawValuationByIdV2-Spec]
generated: '2026-07-26'
method: generated
---

# Order a broker AVM valuation and collect the result

The Broker AVM API is the mortgage-origination path: a broker submits a
property **and its lending context**, Hometrack returns an automated valuation.
It is an order-then-poll API, not a synchronous one.

## Before you start

You need an OAuth 2.0 client-credentials token
(`authentication/hometrack-authentication.yml`): POST to
`https://hometrack-prod.eu.auth0.com/oauth/token` with `client_id`,
`client_secret`, `audience=https://api.hometrack.com`,
`grant_type=client_credentials`. The token lasts 24 hours; present it as
`Authorization: Bearer <token>`.

Every operation exists in a v1 and a v2 form served side by side. **Use v2** —
`ValuePropertyBrokerV2-Spec` "creates the valuation order with climate data for
the property", which is the version that carries Hometrack's climate risk into
the valuation.

## Steps

1. **Create the order.** Call `ValuePropertyBrokerV2-Spec`
   (`POST /broker/v2/order`). The body (`apiRequestBroker`) has two parts:

   - `order`: `accountId`, `orderReference` (your own reference — use it, see
     Rules), `userId`, `valuationType`.
   - `valuations[]`: for each property, `address` (`postcode`, `address`,
     `uprn`), `effectiveDate`, a `loan` block (`repaymentType`, `loanAmount`,
     `outstandingLoanAmount`, `additionalLoanAmount`, `remainingTerm`,
     `minimumRentalIncome`, `existingMortgage`, `instructionType`) and a
     `property` block (`bedrooms`, `receptions`, `propertyType`,
     `propertyStyle`, `constructionType`, `constructionPeriod`, `yearBuilt`,
     `floorArea`, `parking`, `exLocalAuthority`, `visibleFromRoad`).

   The response (`valuationOrderResponse`) returns `orderId`, your
   `orderReference`, and the `valuations` with their `valuationId`s.

2. **Poll the order status.** Call `GetValuationStatusByIdV2-Spec`
   (`GET /broker/v2/order/{orderId}/status`). The response
   (`valuationStatusResponse`) carries `valuationId`, `reference`, `status` and
   `valuationType`. Note that `status` is an undocumented integer enum (0–3) —
   treat any non-terminal value as "keep polling" and confirm the mapping with
   Hometrack before encoding business logic on it.

3. **Fetch the valuation.** Call `FindValuationByIdV2-Spec`
   (`GET /broker/v2/valuation/{valuationId}`). **HTTP 202 means the valuation is
   still being produced** — back off and re-poll. HTTP 200 returns
   `apiValuationResponseBroker`: `order`, `valuationId`, `reference` and `avm`
   (`avmValuationBroker`) with `valuationDate`, `effectiveDate`, `result`,
   `capital` (`cavmCapital`), `rental` (`cavmRental`), `property`
   (`propertyAttributesUsedBroker` — the attributes the model actually used, not
   the ones you asserted), `additionalAttributes` and `address`.

4. **Reconcile inputs when the answer surprises you.** Call
   `GetValuationInputByIdV2-Spec`
   (`GET /broker/v2/valuation/{valuationId}/input`) to see the inputs supplied
   at inception, and compare against `propertyAttributesUsedBroker` in step 3.
   Divergence between what you asserted and what the model used is the usual
   explanation for an unexpected value.

5. **Audit trail (optional).** `GetRawValuationByIdV2-Spec`
   (`GET /internal/v2/valuation/{valuationId}/raw`) returns "the raw valuation
   response with all data, audit traces etc". It sits on an `/internal/` path —
   published in the contract, but treat it as a diagnostic surface, not a
   production dependency.

## Rules

- **Ordering is billable and NOT idempotent.** Hometrack publishes no
  idempotency key. Step 1 must not be blindly retried. Carry a stable
  `order.orderReference` of your own and check for an existing order under it
  before re-sending.
- **Do not mix v1 and v2 identifiers.** An order created on `/broker/v2/order`
  should be read back through the v2 operations.
- **No webhooks.** There is no callback when a valuation completes; polling is
  the only mechanism Hometrack offers.
- **401** = expired/missing bearer token. **403** = "Operation forbidden", a
  licensing or permission issue. **400** = "Incorrect input data submitted";
  validate against the schema, do not retry unchanged. **404** = valuation
  order not found.

## Related

- `data-model/hometrack-data-model.yml` — the order → valuation → AVM graph
- `conventions/hometrack-conventions.yml` — 202-and-poll, no idempotency, no events
- `errors/hometrack-problem-types.yml`
