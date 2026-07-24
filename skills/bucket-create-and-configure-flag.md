---
name: Create and configure a feature flag
description: Create a new feature flag in a Reflag (Bucket) app and update its configuration using the Management API.
api: openapi/bucket-management-openapi-original.json
operations: [listApps, listFlags, createFlag, updateFlag]
auth: Authorization Bearer <management-api-key> (app-scoped, works across environments)
base_url: https://app.reflag.com/api
---

# Create and configure a feature flag

Use the Reflag Management API to create a flag and adjust its configuration. All
requests send `Authorization: Bearer <api-key>` and `Content-Type: application/json`.

## Steps

1. **Find the app.** Call `listApps` (`GET /apps`) to get the `appId` for the app
   you want to work in. Pass `orgId` as a query parameter if you manage multiple orgs.
2. **Check existing flags.** Call `listFlags` (`GET /apps/{appId}/flags`) so you do
   not duplicate an existing flag key. Flag keys must match the provider's key format.
3. **Create the flag.** Call `createFlag` (`POST /apps/{appId}/flags`) with the flag
   key and definition in the JSON body.
4. **Update it later.** Call `updateFlag` (`PATCH /apps/{appId}/flags/{flagId}`) to
   change the flag's configuration; send only the fields you are changing.

## Rules

- Errors come back as `{ "error": { "code", "message" }, "issues": { field: [msg] } }`
  (application/json, not RFC 9457). Handle `invalid_request` (400), `unauthenticated`
  (401), `unauthorized`/`not_allowed` (403), `not_found` (404). See `errors/bucket-error-codes.yml`.
- No idempotency key is documented — do not blindly retry `POST`/`PATCH` on timeout;
  re-check with `listFlags` first. See `conventions/bucket-conventions.yml`.
- The API key is app-scoped and spans all environments; targeting is per-environment.
- Prefer the Reflag CLI (`npx reflag flags create`) in CI/CD; it wraps these operations
  and can regenerate TypeScript types (`npx reflag flags types`).
