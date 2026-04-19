---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [notifications, email, sendgrid, templates]
status: active
---

# Email Notifications

## Overview

All transactional email is delivered via **SendGrid**. The notification pipeline handles event routing, recipient resolution, template rendering, and delivery tracking.

## Pipeline

```
Bug/Auth Event
  └── bug-notification-event-service.ts   — emit notification event
       └── notification-dispatcher.ts      — route to recipient groups
            └── bug-recipient-service.ts   — resolve recipient list
                 └── template-engine.ts    — render HTML template
                      └── sendgrid-delivery.ts — deliver via SendGrid API
                           └── notification-store.ts — record delivery
```

## Notification Events

Events triggered by bug lifecycle:

| Event | Template |
|---|---|
| Bug created | `bug-created.html` |
| Bug assigned | `bug-assigned.html` |
| Bug status changed | `bug-status-changed.html` |
| Bug comment added | `bug-comment.html` |
| Bug closed | `bug-closed.html` |
| Bug reopened | `bug-reopened.html` |

Auth notification templates:

| Event | Template |
|---|---|
| Signup welcome | `welcome.html` |
| Email verification | `verification.html` |
| Forgot password | `forgot-password.html` |
| Password reset complete | `password-reset-complete.html` |
| Password changed | `password-changed.html` |
| Organization invitation | `organization-invitation.html` |

All templates extend `base-layout.html` via the template engine.

## Template Engine

`template-engine.ts` renders HTML templates with variable substitution. Templates live in `src/lib/notifications/templates/`.

`email-template-registry.ts` maps event types to template files.

## Admin Features

Via `notification-admin-service.ts`:

- `listNotificationDeliveryHistory()` — paginated delivery log
- `retryNotificationDeliveryForAdmin()` — retry a failed delivery
- `retryNotificationEvent()` — replay a notification event
- `listNotificationSettingsSummary()` / `updateNotificationSettings()` — enable/disable notification types per recipient group

## Configuration

SendGrid API key is set via environment variables. Leave empty for local development when not testing email flows.

Test endpoint available at `/api/test/sendgrid` (development only).