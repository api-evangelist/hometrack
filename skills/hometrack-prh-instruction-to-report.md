---
name: Instruct a property-risk case and collect its report
description: Create a Hometrack Property Risk Hub instruction, track the case through its status machine, attach and verify documents, and retrieve the latest valuation report.
api: openapi/hometrack-prh-core-external-client-api-v2-openapi.yml
base_url: https://api.hometrack.com/prh
operations: [post-organisation-orgid-instruction, put-organisation-orgid-instruction, get-organisation-orgid-case, get-organisation-orgid-case-caseid, get-organisation-orgid-case-caseid-status, get-organisation-orgid-case-caseid-status-latest, post-organisation-orgid-case-caseid-status, get-organisation-orgid-case-caseid-documents, post-organisation-orgid-case-caseid-documents, get-organisation-orgid-case-caseid-documents-documentreference, get-organisation-orgid-case-caseid-documents-documentreference-verify, get-organisation-orgid-case-caseid-report-latest, get-organisation-orgid-case-caseid-report-revision, get-organisation-orgid-property-repository, get-organisation-orgid-property-postalcode-postalcode]
generated: '2026-07-26'
method: generated
---

# Instruct a property-risk case and collect its report

The Property Risk Hub (PRH — internally "PRISM") is Hometrack's case-management
platform for mortgage lenders: you instruct a property-risk process, it becomes
a case, the case runs through a 25-state machine and ends with a valuation
report. Every path is scoped to `/organisation/{orgId}`.

## Before you start

You need an OAuth 2.0 client-credentials bearer token and your `orgId`. PRH's
401/403 descriptions are explicitly OAuth-worded ("Bad or Expired Token", "Bad
OAuth Request or Forbidden"), so this API expects the Auth0 flow, not a legacy
exchange token.

## Steps

1. **Instruct.** Call `post-organisation-orgid-instruction`
   (`POST /organisation/{orgId}/instruction`). The body is the published
   "PRH - External Client API - Create Instruction - JSON schema": `Brand`,
   `ProcessCode`, `IsPortfolio`, `InstructionReference`, `AllowDuplicate`,
   `UpdateCaseOnly`, `AdditionalReferences`, `InstructionComment`,
   `InstructionDate`, `ExcludeFromBilling`, `Properties`, `Contacts`,
   `CustomAttributes`.

   **`AllowDuplicate` is your duplicate control** — leave it false and carry a
   stable `InstructionReference`. This API has no idempotency key, and an
   instruction dispatches real work (potentially a surveyor).

2. **Reinstruct / reprocess if needed.** Call
   `put-organisation-orgid-instruction`
   (`PUT /organisation/{orgId}/instruction`) with the same shape plus `CaseId`
   and `ProcessDataItems`. Use `UpdateCaseOnly` when you want to amend case data
   without re-running the process.

3. **Find the case.** Call `get-organisation-orgid-case`
   (`GET /organisation/{orgId}/case`) filtered by `instructionRef` (complete or
   partial) or `lenderRef`. This is the one paged collection in Hometrack's
   estate: request `page` (zero-indexed) and `pageSize`, read back `TotalCount`,
   `PageCount`, `Page`, `PageSize`; `pageResults=false` turns paging off with a
   5,000-result cap. Other useful filters: `caseStatus`, `hasReport`,
   `reportAcceptable`, `lastProviderStatus`, `lastProviderUpdatedFrom/To`,
   `reportUpdatedFrom/To`, and `orderBy` (a SQL ORDER BY string).

   Each row gives you `CaseId`, `InstructionReference`, `CaseReference`,
   `CaseStatus`, `riskClassification`, `fullAddress` and `reportRevision`.

4. **Track the case.** Call `get-organisation-orgid-case-caseid` for the full
   case, and `get-organisation-orgid-case-caseid-status-latest` for the most
   recent data-provider status. `CaseStatus` moves through the published enum —
   `Instructed`, `PropertyVerified`, `ValuationRetrieved`,
   `PropertyRisksRetrieved`, `PropertyRiskAssessed`, `PropertyRiskComplete`,
   `ValuationRequested`, `ValuationComplete`, `WaitingForManualReferral`,
   `PhysicalValuationRequested`, `PhysicalValuationCompleted`,
   `WaitingForUnderwriterAssessment`, `OnHold`, `Cancelled`,
   `WaitingForAllocation`, and others. Poll; there are no webhooks.

5. **Post a status (data providers only).** If you are the surveyor or supplier
   side, call `post-organisation-orgid-case-caseid-status` with
   `InstructionReference`, `StatusCode`, `StatusDate`, `StatusMessage`,
   `StatusReason`, `Notes`, `Contacts`, `StatusDataItems`.

6. **Documents.** List with
   `get-organisation-orgid-case-caseid-documents`, attach with
   `post-organisation-orgid-case-caseid-documents` (returns **202** — the upload
   is accepted, not yet processed), fetch one with
   `get-organisation-orgid-case-caseid-documents-documentreference`, and confirm
   integrity with
   `get-organisation-orgid-case-caseid-documents-documentreference-verify`
   (also 202/200).

7. **Collect the report.** Call
   `get-organisation-orgid-case-caseid-report-latest` for the current revision,
   or `get-organisation-orgid-case-caseid-report-revision` for a specific one.
   `Case.reportRevision` tells you which revision is current — if it is `null`,
   no report exists yet.

8. **Search the property repository.** `get-organisation-orgid-property-repository`
   filters by `externalReference`, `instructionReference` or `uprn`;
   `get-organisation-orgid-property-postalcode-postalcode` searches by
   `postalCode` (+ optional `addressLine1`) with paging.

## Rules

- **425 Too Early is normal here.** "The requested resource is not available
  yet, but will be soon" — back off and re-poll rather than treating it as an
  error.
- **429** = "Request limit exceeded. Try again later." No `Retry-After` header
  is declared; back off exponentially.
- Error bodies are `{"message": "...", "reason": "..."}` — `message` is
  required, `reason` carries "Further error information".
- PRH declares the same thirteen statuses on all nineteen operations
  (400, 401, 403, 404, 405, 406, 415, 425, 429, 500, 502, 504). Handle 502/504
  as transient.
- **No idempotency key.** Instructions are billable and dispatch real work. Use
  `InstructionReference` + `AllowDuplicate=false` as your safety net, and
  `ExcludeFromBilling` only when Hometrack has agreed it.

## Related

- `data-model/hometrack-data-model.yml` — instruction → case → process → document → report
- `conventions/hometrack-conventions.yml` — the only paged API in the estate
- `errors/hometrack-problem-types.yml`
