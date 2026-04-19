---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [notifications, email, sendgrid, templates]
status: active
---

# Email Notifications

## Delivery Stack

```
Event (bug change, auth action)
    │
    ▼
bug-notification-event-service  (captures event)
    │
    ▼
bug-recipient-service           (determines recipients)
    │
    ▼
notification-dispatcher         (routes to delivery channel)
    │
    ▼
sendgrid-delivery               (sends via SendGrid API)
```

## Event Types

Bug-related events trigger notifications to configured recipients:
- `bug_created`
- `bug_assigned`
- `bug_status_changed`
- `bug_reopened`
- `bug_closed`
- `bug_comment`

Auth events always send to the acting user:
- `forgot-password`
- `verification` (email verify)
- `welcome`
- `password-changed`
- `password-reset-complete`
- `organization-invitation`

## HTML Templates (`src/lib/notifications/templates/`)

Each event type has a dedicated HTML template. All share a `base-layout.html` wrapper. Templates are rendered by `template-engine.ts` with variable substitution.

## Notification Settings

Admins can configure which recipient groups receive which event types via:
- `GET /api/admin/notifications/settings` — current settings
- `PATCH /api/admin/notifications/settings` — update settings

Settings stored via `notification-admin-service` → `notification-store`.

## Retry Mechanism

Failed deliveries can be retried by admins:
- `POST /api/admin/notifications/deliveries/[deliveryId]/retry`
- `POST /api/admin/notifications/events/[eventId]/retry`

## Delivery History

All delivery attempts are recorded. Admins can view:
- `GET /api/admin/notifications/deliveries` — filterable by event type, delivery status, recipient email, limit

## Notification Delivery Filters (`notification-center-filters.ts`)

| Filter | Type |
|---|---|
| `eventType` | string |
| `deliveryStatus` | string |
| `recipientEmail` | string |
| `limit` | integer (default 25) |

## Local Development

Leave `SENDGRID_*` env variables empty for basic local dev. Email delivery flows require valid SendGrid credentials. A test endpoint at `/api/test/sendgrid` can verify connectivity.