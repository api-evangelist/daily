---
name: Create a Daily room and join token
description: Spin up a Daily video/audio room and mint a scoped meeting token so a participant can join.
api: openapi/daily-openapi-original.json
operations: [CreateRoom, CreateMeetingToken, GetRoomConfig, DeleteRoom]
---

# Create a Daily room and a join token

Use this to programmatically create a call and let a user join it.

## Auth
- Send `Authorization: Bearer DAILY_API_KEY` on every request (key from dashboard.daily.co, scoped to one domain).
- Base URL: `https://api.daily.co/v1`. HTTPS only. Never expose the key client-side.

## Steps
1. **Create the room** — `CreateRoom` (`POST /rooms`). Optionally set `name`, `privacy` (`public`/`private`), and `properties` (e.g. `exp` expiry, `enable_recording`). Response returns the room `name` and `url`.
2. **Mint a meeting token** — `CreateMeetingToken` (`POST /meeting-tokens`) with `properties.room_name` set to the room from step 1. Add `is_owner`, `user_name`, and an `exp` as needed. For private rooms the token is required to join.
3. **Join** — hand the room `url` plus the token to the client (daily-js / Prebuilt / native SDK).
4. **(Optional) Verify** — `GetRoomConfig` (`GET /rooms/{room_name}`) to confirm room settings.
5. **Clean up** — `DeleteRoom` (`DELETE /rooms/{room_name}`) when the call is done (this endpoint is rate-limited to ~2 req/sec).

## Conventions & errors
- No idempotency-key support: do not blind-retry `CreateRoom`; check for an existing room by name first.
- On `429` (`error: "rate-limit-error"`) back off exponentially.
- Errors are `{ "error": "...", "info": "..." }`; branch on the stable `error` string (e.g. `authentication-error`, `invalid-request-error`). See `errors/daily-error-codes.yml`.
