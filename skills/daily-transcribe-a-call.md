---
name: Transcribe a Daily call
description: Start and stop real-time transcription in a room, then retrieve the transcript output.
api: openapi/daily-openapi-original.json
operations: [RoomTranscriptionStart, RoomTranscriptionStop, ListTranscript, GetTranscriptInfo, GetTranscriptLink]
---

# Transcribe a Daily call

## Auth
- `Authorization: Bearer DAILY_API_KEY`, base URL `https://api.daily.co/v1`.

## Steps
1. **Start transcription** — `RoomTranscriptionStart` (`POST /rooms/{room_name}/transcription/start`). Optionally choose language/region (EU region supported as of daily-js 0.91.0).
2. **Stop transcription** — `RoomTranscriptionStop` (`POST /rooms/{room_name}/transcription/stop`).
3. **Wait for `transcript.ready-to-download`** webhook (see `asyncapi/daily-webhooks.yml`).
4. **List / locate** — `ListTranscript` (`GET /transcript`) or `GetTranscriptInfo` (`GET /transcript/{transcriptId}`).
5. **Get the file** — `GetTranscriptLink` (`GET /transcript/{transcriptId}/access-link`) for a time-limited download URL.

## Conventions & errors
- Transcription storage must be enabled at the domain level for post-call transcripts to persist.
- Cursor-paginate `GET /transcript` with `limit`/`starting_after`.
- Errors follow the `{ error, info }` envelope; retry `429`/`5xx` with backoff.
