---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 12
tags: [notifications, email, sendgrid]
status: active
---

# Notification System

The notification system sends transactional emails via SendGrid for bug lifecycle events and auth flows.

## Architecture

```
Bug/Auth Event
     │
BugNotificationEventService   ← records event
     │
NotificationDispatcher        ← fan-out to recipients
     │
BugRecipientService           ← determines who gets notified
     │
SendGridDelivery              ← sends email via SendGrid API
     │
NotificationStore             ← persists delivery record
```

## Event Types

Bug-related events that trigger notifications:

| Event | Template |
|---|---|
| Bug created | `bug-created.html` |
| Bug assigned | `bug-assigned.html` |
| Bug status changed | `bug-status-changed.html` |
| Bug comment added | `bug-comment.html` |
| Bug closed | `bug-closed.html` |
| Bug reopened | `bug-reopened.html` |

Auth-related emails:

| Purpose | Template |
|---|---|
| Email verification | `verification.html` |
| Welcome | `welcome.html` |
| Forgot password | `forgot-password.html` |
| Password reset complete | `password-reset-complete.html` |
| Password changed | `password-changed.html` |
| Organization invitation | `organization-invitation.html` |

All templates extend `base-layout.html`.

## Template Engine

`src/lib/notifications/template-engine.ts` — simple variable substitution engine for HTML email templates. Templates live in `src/lib/notifications/templates/`.

## Notification Settings

Per-organization settings control which event types are enabled for which recipient groups. Managed via:
- Admin UI: `/admin/notifications`
- API: `PATCH /api/admin/notifications/settings`

## Retry Mechanism

Failed deliveries and events can be retried via admin API:
- `POST /api/admin/notifications/deliveries/[deliveryId]/retry`
- `POST /api/admin/notifications/events/[eventId]/retry`

## Local Development

Leave `SENDGRID_API_KEY` empty to disable email delivery locally. The notification flow still runs; deliveries are recorded as skipped/failed in the database.