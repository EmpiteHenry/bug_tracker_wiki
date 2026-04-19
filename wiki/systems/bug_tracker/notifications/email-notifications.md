---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [notifications, email, sendgrid, templates]
status: active
---

# Email Notification System

## Architecture

```
BugNotificationEventService
    → BugRecipientService (who gets the email)
    → NotificationDispatcher
        → SendGridDelivery (actual send)
```

All delivery attempts recorded in `notification-store.ts`.

## Event Types

Defined in `bug-notification-event-service.ts`. Known template files:

| Template | Trigger |
|---|---|
| `bug-created.html` | New bug submitted |
| `bug-assigned.html` | Bug assigned to user |
| `bug-status-changed.html` | Status update |
| `bug-closed.html` | Bug closed |
| `bug-reopened.html` | Bug reopened |
| `bug-comment.html` | New comment |
| `forgot-password.html` | Password reset request |
| `verification.html` | Email verification |
| `welcome.html` | Signup complete |
| `organization-invitation.html` | Org invitation sent |
| `password-changed.html` | Password changed confirmation |
| `password-reset-complete.html` | Reset complete |

Templates extend `base-layout.html`. Rendered by `template-engine.ts`.

## Template Registry

`email-template-registry.ts` maps event type strings to template files. `email-template-registry.test.ts` validates all templates compile.

## Delivery & Retry

`notification-admin-service.ts` exposes admin operations:
- `listNotificationDeliveryHistory()` — filtered delivery log
- `retryNotificationDeliveryForAdmin(deliveryId)` — re-attempt a failed delivery
- `retryNotificationEvent(eventId)` — re-dispatch an event
- `listNotificationSettingsSummary()` / `updateNotificationSettings()` — per event-type + recipient-group toggles

Admin endpoints:
- `GET/PATCH /api/admin/notifications/settings`
- `GET /api/admin/notifications/deliveries`
- `POST /api/admin/notifications/deliveries/[id]/retry`
- `POST /api/admin/notifications/events/[id]/retry`

## Notification Filter State (`notification-center-filters.ts`)

Fields: `eventType`, `deliveryStatus`, `recipientEmail`, `limit` (default 25).

## SendGrid Config

`SENDGRID_API_KEY` env var. Leave empty for local dev (email disabled). Set to enable delivery.