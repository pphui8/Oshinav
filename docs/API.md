# API

The Oshinav backend exposes a versioned REST API. All endpoints are rooted at
`/api`; authentication endpoints are grouped under `/api/auth`.

This is the MVP API contract, based on the project requirements and
architecture. The backend implementation may evolve without changing the
meaning of these resources.

## Conventions

```text
https://<host>/api
```

- JSON is used for requests and responses unless noted otherwise.
- Resource IDs are UUID strings.
- Timestamps use RFC 3339, for example `2026-09-21T09:00:00+09:00`.
- Date-only values use `YYYY-MM-DD` and must remain date-only.
- Single resources are returned as `{ "data": {...} }`.
- Collections are returned as `{ "data": [...], "meta": {...} }`.
- A successful delete returns `204 No Content`.

Protected endpoints require:

```http
Authorization: Bearer <access-token>
```

## Authentication

Authentication endpoints live under `/api/auth`.

| Method | Endpoint | Auth | Purpose |
| --- | --- | --- | --- |
| `POST` | `/auth/register` | No | Create an account and return tokens. |
| `POST` | `/auth/login` | No | Authenticate a user and return tokens. |
| `POST` | `/auth/refresh` | No | Rotate a refresh token and issue a new access token. |
| `POST` | `/auth/logout` | Yes | Revoke the current refresh session. |
| `GET` | `/auth/me` | Yes | Return the authenticated user. |

Registration and login accept `{ "email": "fan@example.com", "password":
"a-secure-password" }`. Refresh accepts `{ "refresh_token": "<token>" }`.
Successful registration and login return `200 OK`, with this
shape:

```json
{
  "data": {
    "user": {
      "id": "8c1b7d7e-3d95-4b0e-9f1b-4a8ce4e8c6d4",
      "email": "fan@example.com",
      "display_name": null,
      "created_at": "2026-09-21T09:00:00+09:00"
    },
    "access_token": "<access-token>",
    "refresh_token": "<refresh-token>",
    "expires_in": 900
  }
}
```

Access tokens are short-lived. Refresh tokens should be stored securely by the
client. Redis may store refresh or revocation state; PostgreSQL remains the
source of truth for users and permissions.

## Activities and personal tracking

An activity is a shared object that many users may track. `activity_type` is one of
`event`, `ticket`, `lottery`, `preorder`, `merchandise_release`, `pickup`, or
`shipment`. User status is independent of activity type and may be one of
`interested`, `applied`, `booked`, `paid`, `ordered`, `won`, `lost`,
`picked_up`, `attended`, or `cancelled`.

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/activities` | List the authenticated user's tracked activities, including personal state. |
| `POST` | `/activities` | Create a new shared activity or track a confirmed existing one. |
| `GET` | `/activities/{activity_id}` | Get a visible shared activity, milestones, sources, and the user's personal state when tracked. |
| `PATCH` | `/activities/{activity_id}` | Update shared activity fields when the caller has permission. |
| `POST` | `/activities/{activity_id}/track` | Create the user's tracking relationship; idempotent. |
| `DELETE` | `/activities/{activity_id}/track` | Stop tracking for the current user only. |
| `PUT` | `/activities/{activity_id}/status` | Set the current user's status. |

`GET /activities` supports `status`, `activity_type`, `from`, `to`, `page`, and
`per_page`. The default sort is the next upcoming milestone, then
`created_at`.

### Create activity

`POST /activities` accepts manual or already-confirmed extracted data. It must run shared-activity matching before creating a new record. A successful response identifies whether the activity was `created` or `reused`, and creates the current user's tracking relationship.

```json
{
  "title": "Example Live 2026",
  "activity_type": "event",
  "starts_on": "2026-11-14",
  "ends_on": "2026-11-14",
  "timezone": "Asia/Tokyo",
  "location": { "name": "Example Hall", "address": "Tokyo" },
  "milestones": [
    {
      "kind": "application_closes",
      "at": "2026-10-01T23:59:00+09:00",
      "timezone": "Asia/Tokyo"
    }
  ],
  "source": { "type": "url", "url": "https://example.com/event" },
  "initial_status": "interested",
  "related_subject_ids": []
}
```

The server returns `201 Created` when a new shared activity is created and
`200 OK` when an existing shared activity is reused. A timed milestone must
include a timezone. Date-only milestones use `date` instead of `at`; these
fields are mutually exclusive.

Stopping tracking through `DELETE /activities/{activity_id}/track` must not delete the shared activity, its milestones, or its sources. `PUT /activities/{activity_id}/status` accepts
`{ "status": "booked" }` and returns the updated activity. Status changes
should be retained in status history even when history is not included in the
default response.

`PATCH /activities/{activity_id}` changes shared information only when the
caller has the required permission. It must not accept personal status,
tracking, or notification fields.

## Import and extraction

Import accepts a URL, X post content, screenshot, or manual source and creates
an asynchronous extraction job. Expensive external-source and LLM work must
not block the request.

| Method | Endpoint | Auth | Purpose |
| --- | --- | --- | --- |
| `POST` | `/imports` | Yes | Submit a source for extraction. |
| `GET` | `/imports/{import_id}` | Yes | Get extraction status and result. |
| `POST` | `/imports/{import_id}/confirm` | Yes | Confirm data, match or create a shared activity, and track it for the caller. |

JSON input to `POST /imports` looks like:

```json
{ "source_type": "url", "url": "https://example.com/event" }
```

Screenshot imports use `multipart/form-data`. Submission returns `202 Accepted`:

```json
{ "data": { "id": "0e4a73a5-d7b7-4641-8d0d-0df06e34a4ea", "status": "queued" } }
```

Import status is `queued`, `processing`, `needs_confirmation`, `completed`, or
`failed`. A completed extraction preserves the original source and exposes
uncertainty for extracted fields. Confirmation must show likely existing
matches when confidence is uncertain. The request may include an explicit
`existing_activity_id` after user confirmation; otherwise the server matches
using stable identifiers, URLs, title, dates, venue, subjects, and type.
Confirmation returns `201 Created` when a shared activity is created and
`200 OK` when an existing activity is reused; both responses include the
user's tracking relationship and the result (`created` or `reused`).

## Subjects, follows, and discovery

Subjects are works, creators, franchises, shops, venues, brands, or topics.

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/subjects` | Search or list known subjects. |
| `POST` | `/subjects` | Create a subject when no match exists. |
| `GET` | `/subjects/{subject_id}` | Get subject details. |
| `GET` | `/follows` | List the user's followed subjects. |
| `POST` | `/subjects/{subject_id}/follow` | Follow a subject. |
| `DELETE` | `/subjects/{subject_id}/follow` | Unfollow a subject. |
| `GET` | `/discoveries` | List relevant discovered activities. |
| `POST` | `/discoveries/{discovery_id}/save` | Save a discovery as an activity. |
| `POST` | `/discoveries/{discovery_id}/dismiss` | Dismiss a discovery. |

`GET /subjects` supports `q` and `category`. Categories include `anime`,
`game`, `vtuber`, `creator`, `shop`, `brand`, `venue`, and `topic`. Following
is idempotent: repeating a follow does not create a duplicate.

Discovery polling, matching, shared-activity updates, and duplicate
notification suppression run in background jobs. Saving a discovery tracks
the matched shared activity for the current user; it does not create a second
activity record when the candidate already exists.

## Notifications

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/notifications` | List the user's notifications. |
| `GET` | `/notifications/unread-count` | Return the unread count. |
| `POST` | `/notifications/{notification_id}/read` | Mark one notification read. |
| `POST` | `/notifications/read-all` | Mark all notifications read. |
| `GET` | `/notification-settings` | Get notification preferences. |
| `PATCH` | `/notification-settings` | Update notification preferences. |

Notifications are generated from known activity milestones. Notification
delivery status is independent of the user's activity status.

## Errors

Errors use a stable machine-readable code:

```json
{
  "error": {
    "code": "validation_error",
    "message": "The request is invalid.",
    "fields": {
      "milestones[0].timezone": "Timezone is required when 'at' is set."
    },
    "request_id": "req_01J..."
  }
}
```

Common status codes are `400` malformed request, `401` missing or invalid
token, `403` forbidden, `404` not found or not visible, `409` conflict,
`422` validation failure, `429` rate limit, and `500` unexpected server error.

Collection metadata should include `page`, `per_page`, `total`, and `has_more`.
Clients should include `request_id` when reporting failures.

## Security and request behavior

- Authentication, shared-resource permissions, personal relationship ownership,
  and visibility are checked server-side.
- Shared activity deletion is not an MVP operation. Removing a user's tracking
  relationship uses the `/track` endpoint and never removes shared data.
- Passwords are never returned by the API and must be stored with a suitable
  password-hashing algorithm.
- Import, discovery, and notification work is queued and may finish after the
  originating request.
- Retryable mutations should support an `Idempotency-Key` header, especially
  imports and activity creation.
