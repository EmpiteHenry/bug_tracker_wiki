---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 8
tags: [notifications, email, sendgrid, templates]
status: active
---

# Notification System

## Architecture

```
Bug Event
    │
    ▼
bug-notification-event-service.ts   — creates a notification event record
    │
    ▼
bug-recipient-service.ts            — resolves who should receive this event
    │
    ▼
notification-dispatcher.ts          — dispatches to delivery channels
    │
    ▼
sendgrid-delivery.ts                — sends via SendGrid API
    │
    ▼
notification-store.ts               — records delivery attempt + status
```

## Event Types

Bug lifecycle events that trigger notifications:

| Event | Template |
|---|---|
| Bug created | `bug-created.html` |
| Bug assigned | `bug-assigned.html` |
| Bug status changed | `bug-status-changed.html` |
| Bug closed | `bug-closed.html` |
| Bug reopened | `bug-reopened.html` |
| Comment added | `bug-comment.html` |

Auth events:

| Event | Template |
|---|---|
| Signup | `welcome.html` |
| Email verification | `verification.html` |
| Forgot password | `forgot-password.html` |
| Password reset complete | `password-reset-complete.html` |
| Password changed | `password-changed.html` |
| Org invitation | `organization-invitation.html` |

## Template Engine

`template-engine.ts` renders HTML templates in `src/lib/notifications/templates/`. All templates extend `base-layout.html`.

`email-template-registry.ts` maps event types to their template files and subject lines.

## Admin Controls

Admins can:
- View delivery history: `GET /api/admin/notifications/deliveries`
- Retry a failed delivery: `POST /api/admin/notifications/deliveries/[id]/retry`
- View/update notification settings (which events send to which recipient groups): `GET|PATCH /api/admin/notifications/settings`
- Retry a failed event: `POST /api/admin/notifications/events/[id]/retry`

## Configuration

SendGrid API key is set via environment variable. Leave empty for local dev unless testing email flows.
