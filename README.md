# Hometrack (hometrack)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Hometrack is a United Kingdom property data, valuation and risk-decisioning company, founded in 1999 and now part of Houseful — the Silver Lake-owned group that also owns Zoopla, PrimeLocation, Alto and Jupix. It launched its automated valuation model in 2002 and says it now runs more than 50 million valuations a year, that 18 of the top 20 UK mortgage lenders use its AVM in their origination processes, and that it was the first AVM accredited by Moody's, Standard & Poor's and Fitch. It is a founding member of the European AVM Alliance. In the UK value chain it does not sit on the listings side at all: with no MLS in this market, residential listings are controlled by Rightmove and Zoopla and reach them through agency CRM software, while Hometrack sits on the lending and risk side — valuation, comparables, climate and property risk data, surveyor allocation and case management for mortgage lenders, surveyors, brokers, housing associations and investors. Its API posture is unusually revealing and must be stated in two halves. The developer surface is real and genuinely public: an Azure API Management developer portal at developer.hometrack.com is served anonymously, its data plane answers unauthenticated requests, and six APIs — a Valuation API, a Broker AVM API, a Property Risk Hub core client API, a Climate API, a Climate GraphQL API and an internal-facing public API — are listed there with full operation and schema metadata, from which six OpenAPI 3.0.1 documents were harvested. The access gate, however, is commercial: the portal states plainly that "to interact with any of our APIs you will need to have a valid API Key for that respective product. If you do not yet have an API Key, please contact us", and the gateway at api.hometrack.com answers anonymous calls with HTTP 401 "Unauthorized. Access token is missing or invalid." Authentication is OAuth 2.0 client credentials through Auth0 (hometrack-prod.eu.auth0.com) against the audience https://api.hometrack.com, with documented scopes read:valuations and write:valuations. So: contracts are readable by anyone, data is reachable by nobody without a Hometrack commercial agreement. There is no RESO Web API or Data Dictionary certification and no OData $metadata anywhere in Hometrack's stack — RESO is a North American NAR/MLS construct and the UK has no MLS to certify against. Notably, Hometrack's Climate API keys every property off the UPRN, the Unique Property Reference Number issued by GeoPlace and distributed by Ordnance Survey: the UK does have a universal property identifier, it just comes from government rather than from a real-estate standards body. Hometrack itself publishes no open data — the open UK property layer is HM Land Registry Price Paid and Ordnance Survey, not Hometrack.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/hometrack/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/hometrack/refs/heads/main/apis.yml)

## Tags

- Real Estate
- United Kingdom
- PropTech
- Valuation
- AVM
- Mortgage
- Property Data
- Climate Risk
- Lending
- Surveying

## Timestamps

- **Created:** 2026-07-26
- **Modified:** 2026-07-26

## APIs

### Hometrack Valuation API

Described in Hometrack's own Azure API Management catalogue as the "Generally available version of the Valuation API". Three operations: POST /authentication/{apiKey} exchanges an API key for a temporary token valid for five minutes, GET /status reports whether the Valuation API is currently available, and POST /valuation/{accountId} runs a valuation on a given property. The valuation endpoint uses token-based authorization with an Authorization header of the form 'Token token="<guid>"' (Hometrack documents that it also accepts looser variants without the quotes or the token= prefix). GET /status is the one operation in Hometrack's entire published surface that answered an anonymous probe with HTTP 200 — it returned a zero-byte body, confirming the gateway is live.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com/valuation-api/v1`

#### Tags

- Valuation
- AVM
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-valuation-api-v1-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)
- [Authentication](authentication/hometrack-auth0-openid-configuration.json)

### Hometrack Broker AVM API

Hometrack's own catalogue description is "This API provides access to the Broker AVM service." Twelve operations across two revisions (v1 and v2 paths served side by side): POST /broker/order creates a valuation order for a property, POST /broker/v2/order "creates the valuation order with climate data for the property", and GET operations retrieve the order, the order status, the valuation, the valuation input supplied at inception, and — on /internal/... paths — the raw valuation response "with all data, audit traces etc". The harvested request schema for a broker order is detailed and real: an order block with accountId, orderReference, userId and valuationType, and a valuations array carrying address (postcode, address, uprn), effectiveDate, a loan block (repaymentType, loanAmount, outstandingLoanAmount, additionalLoanAmount, remainingTerm, minimumRentalIncome, existingMortgage, instructionType) and a property block (bedrooms, receptions, propertyType, propertyStyle, constructionType, constructionPeriod, yearBuilt, floorArea, parking, exLocalAuthority, visibleFromRoad). Anonymous GET on this path returns HTTP 401.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com/valuation/v2`

#### Tags

- AVM
- Valuation
- Mortgage
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-broker-avm-api-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)

### Hometrack PRH Core External Client API v2.0

Catalogued by Hometrack as "(PRH) - Core External Client API v2.0 — Our PRH 'Core' API offering to all customers who want it." PRH is Hometrack's Property Risk Hub, the case-management and surveyor-instruction platform sold to mortgage lenders. Nineteen operations under /organisation/{orgId}: create an instruction (POST /instruction) and reinstruct or reprocess an existing one (PUT /instruction); list and retrieve cases; retrieve a case filtered to a specific case process and that process's status; list, attach, retrieve and verify case documents; list and retrieve valuation reports attached to a case, including a /report/latest shortcut; read and post data-provider status updates on a case; and search the property valuation repository by postal code or repository id. The harvested schema document is the richest in Hometrack's catalogue at 257 component schemas, including a full "PRH - External Client API - Create Instruction - JSON schema" with a worked example.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com/prh`

#### Tags

- Property Risk
- Case Management
- Surveying
- Mortgage
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-prh-core-external-client-api-v2-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)

### Hometrack Climate API (v2)

Five GET operations returning climate and energy risk data for a single property, keyed on UPRN — the Unique Property Reference Number, the UK's government-issued national property identifier: /epc-hometrack/{uprn} returns EPC (energy performance) data, /flood-twinn/{uprn} returns flood data, /ground-terrafirma/{uprn} returns ground data, /ground-subsidence-twinn/{uprn} returns subsidence score data and /ground-coastalerosion-twinn/{uprn} returns coastal erosion data. Four of the five take a dataVersion parameter, and the supplier is visible in the path segments — Twinn for flood, subsidence and coastal erosion, Terrafirma for ground. Every operation accepts X-Client-ID, X-Client-Reference and X-Correlation-ID tracking headers. The harvested schema document carries 35 component schemas covering the response models (FloodScoreResponseModelV2, SubsidenceScoreResponseModelV2, CoastalErosionScoreResponseModelV2, GroundScoreResponseModelV2, EnergyCertificateResponseModelV2 and their sub-models). The APIM contract records the backend as web-uks-prod-data-api.azurewebsites.net/v2.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com/climate`

#### Tags

- Climate Risk
- Property Data
- EPC
- Flood
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-climate-api-v2-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)

### Hometrack Climate GraphQL API

A GraphQL API registered in Hometrack's API Management catalogue with type "graphql" and path /climate/graphql, fronting the same climate data backend (web-uks-prod-data-api.azurewebsites.net/graphql). It is a real, listed API but its contract is not anonymously retrievable: the operations collection and the schema collection both returned empty arrays to an unauthenticated caller, so no GraphQL SDL could be harvested, and the OpenAPI export for it is a stub with servers and security schemes but no paths. Recorded here because absence of the schema is itself the finding.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com/climate/graphql`

#### Tags

- GraphQL
- Climate Risk
- Property Data
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-climate-graphql-api-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)

### Hometrack API Public

Catalogued by Hometrack as "Hometrack API for public consumption" and mounted at the gateway root. Twenty operations that are mostly account, licensing, branding and report-generation plumbing rather than a property data product: POST /api/authentication/{apiKey} exchanges an API key for a plain-text GUID token; /api/reporting/PropertyValuation requests and then downloads a Property Valuation Report; /api/brands/{token}/{targetAccountApiKey} reads and writes the co-branding configuration used on those reports; /api/licences/{token}/{product} returns the product licences belonging to the authenticated account; /api/partners and /api/zoopla/partners create and read partner-to-account entries under "partner.create" and "partner.modify" permissions; /api/pvrplugin endpoints serve the Property Valuation Report plugin; and /api/trial endpoints issue trial licences. The name is misleading — nothing here is callable without a Hometrack-issued API key, and the presence of a scheduled-task endpoint (GET /api/reporting/moveToBlobStorage) and two "For test purpose" endpoints suggests an internal API that has been published to the catalogue as-is.

- **Human URL:** [https://developer.hometrack.com/apis](https://developer.hometrack.com/apis)
- **Base URL:** `https://api.hometrack.com`

#### Tags

- Reporting
- Accounts
- Licensing
- Real Estate
- United Kingdom

#### Properties

- [OpenAPI](openapi/hometrack-api-public-openapi.yml)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)

## Common Properties

- [Website](https://www.hometrack.com/)
- [Developer Portal](https://developer.hometrack.com/)
- [Documentation](https://developer.hometrack.com/apis)
- [Authentication](https://developer.hometrack.com/api-authentication)
- [OpenID Connect Discovery](authentication/hometrack-auth0-openid-configuration.json)
- [Sign Up](https://developer.hometrack.com/signup)
- [Sign In](https://developer.hometrack.com/signin)
- [Contact Us](https://www.hometrack.com/contact-us/)
- [About](https://www.hometrack.com/about/)
- [Blog](https://www.hometrack.com/blog/)
- [Blog RSS](https://www.hometrack.com/feed/)
- [Press Releases](https://www.hometrack.com/press-releases/)
- [Newsroom](https://www.hometrack.com/newsroom/uk-house-price-index/)
- [Terms of Service](https://www.hometrack.com/terms/)
- [Privacy Policy](https://www.hometrack.com/privacy/)
- [Security](https://www.hometrack.com/iso-27001/)
- [GitHub Organization](https://github.com/Hometrack)
- [Linked In](https://uk.linkedin.com/company/hometrack)

## Access

- **Access gate:** partner-only. Hometrack's developer portal states verbatim: "To interact with any of our APIs you will need to have a valid API Key for that respective product. If you do not yet have an API Key, please contact us." There is no application form, no review workflow, no published terms and no self-service issuance — a commercial agreement with Hometrack is the only documented route to a credential. In practice that means being a mortgage lender, surveyor, broker, housing association or investor buying valuation, comparables or property risk data under contract. No MLS, board, association, IDX or VOW licence exists in this market.
- **What is genuinely open:** the contracts. `developer.hometrack.com` is a stock Azure API Management portal served anonymously, and its data plane answers unauthenticated requests — the API catalogue, every operation's request/response metadata with worked JSON examples, every component schema, and the OpenAPI export endpoint all returned HTTP 200 with no credentials. Six OpenAPI 3.0.1 documents covering 55 paths and 381 schemas were harvested this way.
- **What is closed:** the data. `GET https://api.hometrack.com/valuation/v2/broker/order/1` returns HTTP 401 `{ "statusCode": 401, "message": "Unauthorized. Access token is missing or invalid." }`. Exactly one operation in the whole surface answered anonymously — `GET https://api.hometrack.com/valuation-api/v1/status`, HTTP 200 with a zero-byte body, the documented availability check.
- **Auth model:** OAuth 2.0 client credentials via Auth0. POST to `https://hometrack-prod.eu.auth0.com/oauth/token` with `client_id`, `client_secret`, `audience: https://api.hometrack.com` and `grant_type: client_credentials`; the Bearer token is valid 24 hours. Documented scopes are `read:valuations` and `write:valuations`. The Auth0 discovery document is served anonymously and is saved verbatim at [authentication/hometrack-auth0-openid-configuration.json](authentication/hometrack-auth0-openid-configuration.json). Two APIs also implement an older pattern: `POST /authentication/{apiKey}` exchanges a Hometrack-issued API key for a short-lived token (five minutes on the Valuation API).
- **RESO posture:** no RESO reference found anywhere. No Web API or Data Dictionary certification, no OData `$metadata`, no RESO Universal Property Identifier. `https://www.reso.org/certificates/` was fetched anonymously (HTTP 200, 416,233 bytes) and contains zero occurrences of "hometrack" and zero of "united kingdom". The UK has no MLS to certify against, and Hometrack is on the lender side of the market rather than the listings side.
- **Property identifier:** Hometrack's Climate API keys every operation off the **UPRN** — the Unique Property Reference Number issued by local authorities, curated by GeoPlace and distributed via Ordnance Survey AddressBase. The UK does have a universal property identifier; it comes from government rather than from a real-estate standards body, which is the structural inverse of the RESO UPI story.
- **Open data:** none published by Hometrack. Its house price index and rental market reports are PDFs and web pages behind a newsletter signup, not APIs. Hometrack *consumes* the open UK layer — UPRN and EPC data — without republishing it. The open UK property layer is HM Land Registry (Price Paid and ownership data under the Open Government Licence) and Ordnance Survey.
- **Webhooks / events / SDKs / Postman:** none. No webhook or callback operation exists in any of the six APIs; the Broker AVM order workflow is asynchronous but served by polling. No AsyncAPI, no streaming. `github.com/Hometrack` exists but has zero public repositories. The authentication page shows a Postman screenshot but publishes no collection.
- **Versioning:** a real, published deprecation policy on the portal home — only the three newest versions are available at any time, plus an optional public beta/preview, with a worked example (v11b beta, v10 latest, v9 first deprecation warning, v8 last warning).
- **Trial / sandbox / pricing:** none. `sandbox.hometrack.com` does not resolve, no pricing is published, and the only "trial" endpoint in the catalogue itself requires an existing Hometrack API key.

See [review.yml](review.yml) for the full probe log, RESO posture, access gate, auth model, and harvest provenance.
