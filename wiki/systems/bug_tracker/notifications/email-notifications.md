---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [notifications, email, sendgrid, templates]
status: active
---

# Email Notifications

## Architecture

```
Bug event → BugNotificationEventService
              → BugRecipientService (determines who to notify)
                → NotificationDispatcher
                  → SendGridDelivery (sends email)
                  → NotificationStore (persists delivery record)
```

Key source files:

| File | Role |
|---|---|
| `bug-notification-event-service.ts` | Creates notification events on bug lifecycle changes |
| `bug-recipient-service.ts` | Resolves recipients for each event type |
| `notification-dispatcher.ts` | Dispatches events to delivery providers |
| `sendgrid-delivery.ts` | Calls SendGrid HTTP API |
| `notification-store.ts` | Persists notification and delivery rows |
| `email-template-registry.ts` | Maps event types to template files |
| `template-engine.ts` | Simple HTML template renderer (variable substitution) |
| `notification-admin-service.ts` | Admin queries and retry operations |

## Event Types

Bug lifecycle events that trigger notifications (inferred from template files):

- `bug-created` — new bug submitted
- `bug-assigned` — assignee changed
- `bug-status-changed` — status transition
- `bug-closed` — bug moved to Closed
- `bug-reopened` — bug moved back from Closed
- `bug-comment` — new comment added

Auth events:
- `verification` — email verification
- `forgot-password`
- `password-changed`
- `password-reset-complete`
- `organization-invitation`
- `welcome`

## Templates

HTML templates live in `src/lib/notifications/templates/`. All templates extend `base-layout.html` which provides the common email shell.

## Configuration

- SendGrid API key: set via environment variable (see `.env.example`)
- Notification settings per event type and recipient group are configurable by admins at `GET/PATCH /api/admin/notifications/settings`

## Retry

- Admins can retry a failed delivery: `POST /api/admin/notifications/deliveries/[deliveryId]/retry`
- Admins can retry an entire notification event: `POST /api/admin/notifications/events/[eventId]/retry`
- Delivery history is viewable at `GET /api/admin/notifications/deliveries`