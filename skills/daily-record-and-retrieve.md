---
name: Record a Daily call and retrieve the file
description: Start and stop a cloud recording in a Daily room, then list it and get a download link.
api: openapi/daily-openapi-original.json
operations: [RoomRecordingsStart, RoomRecordingsStop, ListRecordings, GetRecordingInfo, GetRecordingLink]
---

# Record a Daily call and retrieve the file

## Auth
- `Authorization: Bearer DAILY_API_KEY`, base URL `https://api.daily.co/v1`.

## Steps
1. **Start recording** — `RoomRecordingsStart` (`POST /rooms/{room_name}/recordings/start`). Optionally pass a recording `type` (`cloud`, `cloud-audio-only`, `raw-tracks`) and layout options. This endpoint is heavily rate-limited (~1 req/sec).
2. **Stop recording** — `RoomRecordingsStop` (`POST /rooms/{room_name}/recordings/stop`) when the session ends.
3. **Wait for the `recording.ready-to-download` webhook** (see `asyncapi/daily-webhooks.yml`) — cloud processing is asynchronous.
4. **List / locate** — `ListRecordings` (`GET /recordings`, cursor-paginated with `limit`/`starting_after`) or `GetRecordingInfo` (`GET /recordings/{recording_id}`).
5. **Get a download link** — `GetRecordingLink` (`GET /recordings/{recording_id}/access-link`) returns a time-limited URL.

## Conventions & errors
- Prefer the `recording.ready-to-download` webhook over polling `ListRecordings`.
- `GET /recordings` is rate-limited (~2 req/sec) — page with cursors, don't hammer.
- Handle `429`/`5xx` with exponential backoff; branch on the `error` field in the JSON envelope.
