# Hometrack

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
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
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

**Areas:** Real Estate · United Kingdom · PropTech · Valuation · AVM · Mortgage · Property Data · Climate Risk · Lending · Surveying

## Hometrack on APIs.io

Everything profiled for Hometrack is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/hometrack/](https://apis.io/providers/hometrack/)** — or go straight to one of the APIs or properties below.

### APIs (18)

- [Hometrack Authentication API](https://apis.io/apis/hometrack/hometrack-authentication-api/) — The Authentication API from Hometrack — 2 operation(s) for authentication.
- [Hometrack Brands API](https://apis.io/apis/hometrack/hometrack-brands-api/) — The Brands API from Hometrack — 1 operation(s) for brands.
- [Hometrack Broker API](https://apis.io/apis/hometrack/hometrack-broker-api/) — The Broker API from Hometrack — 10 operation(s) for broker.
- [Hometrack Epc Hometrack API](https://apis.io/apis/hometrack/hometrack-epc-hometrack-api/) — The Epc Hometrack API from Hometrack — 1 operation(s) for epc hometrack.
- [Hometrack Flood Twinn API](https://apis.io/apis/hometrack/hometrack-flood-twinn-api/) — The Flood Twinn API from Hometrack — 1 operation(s) for flood twinn.
- [Hometrack Ground Coastalerosion Twinn API](https://apis.io/apis/hometrack/hometrack-ground-coastalerosion-twinn-api/) — The Ground Coastalerosion Twinn API from Hometrack — 1 operation(s) for ground coastalerosion twinn.
- [Hometrack Ground Subsidence Twinn API](https://apis.io/apis/hometrack/hometrack-ground-subsidence-twinn-api/) — The Ground Subsidence Twinn API from Hometrack — 1 operation(s) for ground subsidence twinn.
- [Hometrack Ground Terrafirma API](https://apis.io/apis/hometrack/hometrack-ground-terrafirma-api/) — The Ground Terrafirma API from Hometrack — 1 operation(s) for ground terrafirma.
- [Hometrack Internal API](https://apis.io/apis/hometrack/hometrack-internal-api/) — The Internal API from Hometrack — 2 operation(s) for internal.
- [Hometrack Licences API](https://apis.io/apis/hometrack/hometrack-licences-api/) — The Licences API from Hometrack — 1 operation(s) for licences.
- [Hometrack Organisation API](https://apis.io/apis/hometrack/hometrack-organisation-api/) — The Organisation API from Hometrack — 16 operation(s) for organisation.
- [Hometrack Partners API](https://apis.io/apis/hometrack/hometrack-partners-api/) — The Partners API from Hometrack — 2 operation(s) for partners.
- [Hometrack Pvrplugin API](https://apis.io/apis/hometrack/hometrack-pvrplugin-api/) — The Pvrplugin API from Hometrack — 4 operation(s) for pvrplugin.
- [Hometrack Reporting API](https://apis.io/apis/hometrack/hometrack-reporting-api/) — The Reporting API from Hometrack — 7 operation(s) for reporting.
- [Hometrack Status API](https://apis.io/apis/hometrack/hometrack-status-api/) — The Status API from Hometrack — 1 operation(s) for status.
- [Hometrack Trial API](https://apis.io/apis/hometrack/hometrack-trial-api/) — The Trial API from Hometrack — 2 operation(s) for trial.
- [Hometrack Valuation API](https://apis.io/apis/hometrack/hometrack-valuation-api/) — The Valuation API from Hometrack — 1 operation(s) for valuation.
- [Hometrack Zoopla API](https://apis.io/apis/hometrack/hometrack-zoopla-api/) — The Zoopla API from Hometrack — 1 operation(s) for zoopla.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [hometrack-mcp.yml](https://apis.io/servers/hometrack/hometrack-mcp-yml/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Hometrack Agentic Access](https://apis.io/agentic-access/hometrack/hometrack-agentic-access/)

#### Collections (6)

Postman and Open Collection request collections.

- [Hometrack API Public](https://apis.io/collections/hometrack/open-hometrack-api-public/)
- [Broker Avm API](https://apis.io/collections/hometrack/open-hometrack-broker-avm-api/)
- [Climate API (v2)](https://apis.io/collections/hometrack/open-hometrack-climate-api-v2/)
- [Climate GraphQL](https://apis.io/collections/hometrack/open-hometrack-climate-graphql-api/)
- [(PRH) - Core External Client API v2.0](https://apis.io/collections/hometrack/open-hometrack-prh-core-external-client-api-v2/)
- [Valuation API](https://apis.io/collections/hometrack/open-hometrack-valuation-api-v1/)

#### Overlays (6)

OpenAPI overlays, including conversational phrasing for each operation.

- [Hometrack Api Public Overlay](https://apis.io/overlays/hometrack/hometrack-api-public-overlay/)
- [Hometrack Broker Avm Api Overlay](https://apis.io/overlays/hometrack/hometrack-broker-avm-api-overlay/)
- [Hometrack Climate Api V2 Overlay](https://apis.io/overlays/hometrack/hometrack-climate-api-v2-overlay/)
- [Hometrack Climate Graphql Api Overlay](https://apis.io/overlays/hometrack/hometrack-climate-graphql-api-overlay/)
- [Hometrack Prh Core External Client Api V2 Overlay](https://apis.io/overlays/hometrack/hometrack-prh-core-external-client-api-v2-overlay/)
- [Hometrack Valuation Api V1 Overlay](https://apis.io/overlays/hometrack/hometrack-valuation-api-v1-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Hometrack Authentication](https://apis.io/security/hometrack/hometrack-authentication/)
- [Hometrack Domain Security](https://apis.io/security/hometrack/hometrack-domain-security/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Hometrack Scopes](https://apis.io/scopes/hometrack/hometrack-scopes/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.hometrack.com/)
- [Documentation](https://developer.hometrack.com/apis)
- [API reference](https://developer.hometrack.com/apis)
- [Sign up](https://developer.hometrack.com/signup)
- [Changelog](https://developer.hometrack.com/api-changelog)
- [Blog](https://www.hometrack.com/blog/)
- [Compliance](https://www.hometrack.com/iso-27001/)
- [Terms of service](https://www.hometrack.com/terms/)
- [Privacy policy](https://www.hometrack.com/privacy/)
- [GitHub](https://github.com/Hometrack)

## API Evangelist rating

**Kin Score 45.3** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 68.8 |
| Contract quality | 46.6 |
| Contract governance | 18.2 |
| Operational transparency | 26.3 |
| Developer ergonomics | 49.4 |
| Access clarity | 42.1 |

**Agent readiness 35.5** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | derived (partial credit) |
| OpenAPI Examples | verified |
| Rate Limit Signal | — |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | — |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | — |
| Operation Distinctiveness | mostly |

**Regulatory alignment 39.2** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/hometrack/](https://apis.io/providers/hometrack/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Hometrack: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/hometrack/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
