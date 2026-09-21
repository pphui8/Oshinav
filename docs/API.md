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

## Activities

An activity is the primary object a user tracks. `activity_type` is one of
`event`, `ticket`, `lottery`, `preorder`, `merchandise_release`, `pickup`, or
`shipment`. User status is independent of activity type and may be one of
`interested`, `applied`, `booked`, `paid`, `ordered`, `won`, `lost`,
`picked_up`, `attended`, or `cancelled`.

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/activities` | List the user's tracked activities. |
| `POST` | `/activities` | Create an activity manually. |
| `GET` | `/activities/{activity_id}` | Get an activity and its milestones. |
| `PATCH` | `/activities/{activity_id}` | Update editable activity fields. |
| `DELETE` | `/activities/{activity_id}` | Remove the user's tracked activity. |
| `PUT` | `/activities/{activity_id}/status` | Set the user's current status. |

`GET /activities` supports `status`, `activity_type`, `from`, `to`, `page`, and
`per_page`. The default sort is the next upcoming milestone, then
`created_at`.

### Create activity

`POST /activities` accepts manual or already-confirmed extracted data:

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

The server returns `201 Created`. A timed milestone must include a timezone.
Date-only milestones use `date` instead of `at`; these fields are mutually
exclusive.

`PUT /activities/{activity_id}/status` accepts
`{ "status": "booked" }` and returns the updated activity. Status changes
should be retained in status history even when history is not included in the
default response.

## Import and extraction

Import accepts a URL, X post content, screenshot, or manual source and creates
an asynchronous extraction job. Expensive external-source and LLM work must
not block the request.

| Method | Endpoint | Auth | Purpose |
| --- | --- | --- | --- |
| `POST` | `/imports` | Yes | Submit a source for extraction. |
| `GET` | `/imports/{import_id}` | Yes | Get extraction status and result. |
| `POST` | `/imports/{import_id}/confirm` | Yes | Confirm data and create an activity. |

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
uncertainty for extracted fields. Confirmation returns `201 Created` with the
new activity.

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

Discovery polling, matching, and duplicate suppression run in background jobs.

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

- Authentication, ownership, and visibility are checked server-side.
- Passwords are never returned by the API and must be stored with a suitable
  password-hashing algorithm.
- Import, discovery, and notification work is queued and may finish after the
  originating request.
- Retryable mutations should support an `Idempotency-Key` header, especially
  imports and activity creation.
