---
name: Run a Fortify on Demand static (SAST) scan and retrieve findings
description: Start a static scan on a release, wait for completion, and pull the resulting vulnerabilities from Fortify on Demand.
api: openapi/fortify-software-fod-openapi.json
operations:
  - StaticScansV3_StartScan
  - ReleasesV3_GetReleaseScanByScanId
  - VulnerabilitiesV3_GetVulnerabilities
---

# Run a Fortify on Demand static (SAST) scan and retrieve findings

Use the Fortify on Demand (FoD) v3 REST API to run a static analysis scan and read the results.

## Authentication
1. POST to `https://api.ams.fortify.com/oauth/token` with the client-credentials grant using a tenant API key (client_id/client_secret) or a Personal Access Token. Request the scopes `start-scans` and `view-issues`.
2. Send the returned token as `Authorization: Bearer <access_token>` on every call.

## Steps
1. **Start the scan** — call `StaticScansV3_StartScan`
   (`POST /api/v3/releases/{releaseId}/static-scans/start-scan`) with the release id and the packaged payload. Capture the returned `scanId`. Requires scope `start-scans`.
2. **Poll for completion** — call `ReleasesV3_GetReleaseScanByScanId`
   (`GET /api/v3/releases/{releaseId}/scans/{scanId}`) until the scan `status` reaches a completed state. Respect HTTP 429 by backing off.
3. **Read findings** — call `VulnerabilitiesV3_GetVulnerabilities`
   (`GET /api/v3/releases/{releaseId}/vulnerabilities`) with `offset`/`limit` paging and `filters` to page through the results. Requires scope `view-issues`.

## Rules
- Pagination is `offset`/`limit`; the response wraps `items` + `totalCount`.
- Errors return `{ "errors": [ { "errorCode", "message" } ] }` — see errors/fortify-software-problem-types.yml.
- A 403 means the token is missing the required scope; a 429 means back off and retry.
