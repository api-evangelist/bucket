---
name: Target a flag to a company or user
description: Read and update per-company and per-user feature-flag targeting in a Reflag (Bucket) environment.
api: openapi/bucket-management-openapi-original.json
operations: [getFlagTargeting, getCompanyFlags, updateCompanyFlags, getUserFlags, updateUserFlags]
auth: Authorization Bearer <management-api-key>
base_url: https://app.reflag.com/api
---

# Target a flag to a company or user

Enable or disable a flag for a specific company or user in one environment using
the Reflag Management API. All requests send `Authorization: Bearer <api-key>` and
`Content-Type: application/json`.

## Steps

1. **Inspect current targeting.** Call `getFlagTargeting`
   (`GET /apps/{appId}/flags/{flagKey}/targeting/{envId}`) to see the flag's rules
   for the target environment.
2. **Read entity state.** For a company call `getCompanyFlags`
   (`GET /apps/{appId}/envs/{envId}/companies/{companyId}/flags`); for a user call
   `getUserFlags` (`GET /apps/{appId}/envs/{envId}/users/{userId}/flags`).
3. **Apply the override.** For a company call `updateCompanyFlags`
   (`PATCH /apps/{appId}/envs/{envId}/companies/{companyId}/flags`); for a user call
   `updateUserFlags` (`PATCH /apps/{appId}/envs/{envId}/users/{userId}/flags`). Send
   an `updates[]` array of `{ flagKey, value }` and a human `changeDescription`.
4. **Confirm.** Re-read with the matching `get*Flags` operation to verify the value.

## Rules

- `envId`, `companyId`/`userId`, and `flagKey` must all be valid or the API returns
  `not_found` (404). See `errors/bucket-error-codes.yml`.
- Always include a `changeDescription` so the change is auditable in Reflag's
  targeting timeline.
- Targeting is environment-scoped; the same API key works across environments, so
  pass the correct `envId` explicitly (dev vs prod).
- See `data-model/bucket-data-model.yml` for the App > Environment > Flag > EntityFlag
  relationships.
