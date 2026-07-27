---
name: Triage Fortify on Demand vulnerabilities for a release
description: List a release's vulnerabilities, pull details and remediation recommendations to triage application security findings.
api: openapi/fortify-software-fod-openapi.json
operations:
  - VulnerabilitiesV3_GetVulnerabilities
  - VulnerabilitiesV3_GetVulnerabilityDetails
  - VulnerabilitiesV3_GetVulnerabilityRecommendations
---

# Triage Fortify on Demand vulnerabilities for a release

Read and triage the security findings on a Fortify on Demand release.

## Authentication
Obtain a bearer token via client-credentials at `https://api.ams.fortify.com/oauth/token` with scope `view-issues` (add `manage-issues` if you will audit/suppress). Send `Authorization: Bearer <access_token>`.

## Steps
1. **List findings** — `VulnerabilitiesV3_GetVulnerabilities`
   (`GET /api/v3/releases/{releaseId}/vulnerabilities`), paging with `offset`/`limit` and narrowing with `filters` (e.g. by severity or category).
2. **Get details** — for each finding of interest, `VulnerabilitiesV3_GetVulnerabilityDetails`
   (`GET /api/v3/releases/{releaseId}/vulnerabilities/{vulnId}/details`).
3. **Get remediation guidance** — `VulnerabilitiesV3_GetVulnerabilityRecommendations`
   (`GET /api/v3/releases/{releaseId}/vulnerabilities/{vulnId}/recommendations`).

## Rules
- Page every list with `offset`/`limit`; read `items` + `totalCount`.
- Error envelope is `{ "errors": [ { "errorCode", "message" } ] }`.
- 403 = insufficient scope; 429 = rate limited, back off.
