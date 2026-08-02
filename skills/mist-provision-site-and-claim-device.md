---
name: Provision a Mist site and claim a device
description: Create a site in a Mist organization and add (claim) a device into inventory so it can be assigned and managed.
api: openapi/mist-openapi-original.json
operations: [getSelf, createOrgSite, addOrgInventory, listSiteDevices, getSiteDevice]
---

# Provision a Mist site and claim a device

Use the Juniper Mist Cloud API to stand up a new site and bring hardware online.

## Auth
- Send `Authorization: Token {apitoken}` on every request (see `authentication/mist-authentication.yml`). Basic Auth is deprecated (Sept 2026).
- Pick the correct regional host — substitute `api` for `manage` in your portal hostname (e.g. `api.eu.mist.com`). Default: `https://api.mist.com`.

## Steps
1. **Confirm access** — `getSelf` (`GET /api/v1/self`) to read the admin's accessible orgs and privileges; capture the target `org_id`.
2. **Create the site** — `createOrgSite` (`POST /api/v1/orgs/{org_id}/sites`) with name, address/latlng, and timezone. Capture the returned `site_id`.
3. **Claim the hardware** — `addOrgInventory` (`POST /api/v1/orgs/{org_id}/inventory`) with the device claim code(s) or MAC(s). This adds devices to org inventory.
4. **Assign & verify** — `listSiteDevices` (`GET /api/v1/sites/{site_id}/devices`) then `getSiteDevice` (`GET /api/v1/sites/{site_id}/devices/{device_id}`) to confirm the device is present and reporting.

## Rules
- Respect the 5000 calls/hour quota; a 429 means back off until the next hourly boundary (`rate-limits/mist-rate-limits.yml`).
- Errors return `{ "detail": "..." }`; 404 means the `org_id`/`site_id` is wrong (`errors/mist-problem-types.yml`).
- Mist does not support idempotency keys — do not blind-retry a POST that may have succeeded; re-list inventory to check state.
