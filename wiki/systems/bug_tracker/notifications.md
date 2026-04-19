---
type: system
owner: engineering
last_updated: 2026-04-19
source_count: 10
tags: [notifications, email, sendgrid, templates]
status: active
---

# Notification System

The notification system delivers email notifications for bug lifecycle events and auth flows via SendGrid.

## Pipeline

```
Bug/auth event occurs
    → BugNotificationEventService records notification event
    → NotificationDispatcher picks recipients (BugRecipientService)
    → SendGridDelivery sends email
    → NotificationStore records delivery attempt
```

## Notification Events (Bug Lifecycle)

| Event | Template |
|---|---|
| Bug created | `bug-created.html` |
| Bug assigned | `bug-assigned.html` |
| Bug status changed | `bug-status-changed.html` |
| Bug closed | `bug-closed.html` |
| Bug reopened | `bug-reopened.html` |
| Bug comment added | `bug-comment.html` |

## Auth Emails

| Event | Template |
|---|---|
| Welcome / signup | `welcome.html` |
| Email verification | `verification.html` |
| Forgot password | `forgot-password.html` |
| Password reset complete | `password-reset-complete.html` |
| Password changed | `password-changed.html` |
| Organization invitation | `organization-invitation.html` |

## Template Engine

`template-engine.ts` renders HTML templates from `src/lib/notifications/templates/`. All templates extend `base-layout.html`. The registry in `email-template-registry.ts` maps event types to templates.

## Admin Controls

Notification settings (enable/disable per event type and recipient group) are managed at `PATCH /api/admin/notifications/settings`. Failed deliveries can be retried via the admin API.

## Configuration

SendGrid API key is set via environment variable. Leave blank for local dev unless testing email flows (see README).

## Service Files

| File | Purpose |
|---|---|
| `notification-dispatcher.ts` | Orchestrates event → delivery |
| `bug-notification-event-service.ts` | Records and queries notification events |
| `bug-recipient-service.ts` | Resolves who should receive a notification |
| `sendgrid-delivery.ts` | Calls SendGrid API |
| `notification-store.ts` | Persists delivery records |
| `notification-admin-service.ts` | Admin queries and retry logic |