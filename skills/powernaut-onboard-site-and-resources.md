---
name: Onboard a site and its flexible resources
description: Authenticate to the Powernaut Partner API, register a connection point (site), and attach the flexible resources (battery, PV, EV, HVAC) that live behind it.
api: openapi/powernaut-partner-api-openapi-original.yml
operations: [GetToken, CreateSite, CreateResource, FindAllSites, GetSite]
---

# Onboard a site and its flexible resources

Use this to bring a new customer site and its distributed energy resources under management.

## Auth
1. Obtain a bearer token with `GetToken` (`POST /v1/auth/token`). Send HTTP Basic auth: base64 of `url-encoded(client_id):url-encoded(client_secret)` in the `Authorization: Basic` header.
2. The response returns `access_token`, `token_type: Bearer`, and `expires_in` (3600s). Send `Authorization: Bearer <access_token>` on every subsequent call. Refresh before expiry.

## Steps
1. Create the site (connection point) with `CreateSite` (`POST /v1/connect/sites`). Provide the grid identifier, location, and any supply points. Capture the returned site `id`.
2. Attach each flexible resource with `CreateResource` (`POST /v1/connect/resources`), setting `site_id` to the new site id, plus `type`, `capacity`, and (optionally) `parent_id`/`group_id`. To receive activation notifications, set `webhooks.accepted` to your callback URL.
3. Verify with `FindAllSites` (`GET /v1/connect/sites`, paginate via `page`/`page_size`) or `GetSite` (`GET /v1/connect/sites/{id}`).

## Rules
- Auth: bearer JWT only; do not reuse expired tokens (401 Unauthorized).
- Errors use a plain JSON envelope `{ message, error, status_code }`. 404 = site/resource not found; 409 = conflicting state.
- No idempotency key is supported — do not blindly retry POSTs; check with a GET first.
- See conventions/powernaut-conventions.yml and errors/powernaut-problem-types.yml.
