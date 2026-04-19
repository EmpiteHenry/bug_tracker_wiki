---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 14
tags: [notifications, email, sendgrid, templates]
status: active
---

# Notification System

The notification subsystem lives in `src/lib/notifications/`. It handles all transactional email delivery via SendGrid and the admin interface for reviewing delivery history and settings.

## Architecture

```
Bug event / Auth event
       ↓
BugNotificationEventService   ← decides which notifications to send
       ↓
NotificationDispatcher        ← resolves recipients and settings
       ↓
SendGridDelivery              ← sends via SendGrid API
       ↓
NotificationStore             ← persists event + delivery records
```

## Event Types

Bug-related notification events:

| Event | Template |
|---|---|
| `bug_created` | `bug-created.html` |
| `bug_assigned` | `bug-assigned.html` |
| `bug_status_changed` | `bug-status-changed.html` |
| `bug_closed` | `bug-closed.html` |
| `bug_reopened` | `bug-reopened.html` |
| `bug_comment` | `bug-comment.html` |

Auth-related:

| Event | Template |
|---|---|
| Signup welcome | `welcome.html` |
| Email verification | `verification.html` |
| Forgot password | `forgot-password.html` |
| Password changed | `password-changed.html` |
| Password reset complete | `password-reset-complete.html` |
| Organization invitation | `organization-invitation.html` |

## Template Engine (`template-engine.ts`)

Templates are HTML files in `src/lib/notifications/templates/`. They extend `base-layout.html`. Variables are interpolated by the template engine — no external templating library.

`email-template-registry.ts` maps event types to template files.

## Recipient Resolution (`bug-recipient-service.ts`)

`BugRecipientService` determines who receives a notification for a given bug event: the reporter, assignee, project members, and any watchers, subject to per-recipient notification preferences.

## Notification Settings

Admins control which event types are enabled for which recipient groups via `GET/PATCH /api/admin/notifications/settings`. Settings are stored in the database.

## Delivery History

Every send attempt is recorded as a delivery record with status (`pending | sent | failed`). Admins can view delivery history and retry failed deliveries or events via:

- `POST /api/admin/notifications/deliveries/[id]/retry`
- `POST /api/admin/notifications/events/[id]/retry`

Filters on the delivery list: eventType, deliveryStatus, recipientEmail, limit.

## SendGrid Configuration

SendGrid API key and sender address are set via environment variables. Leave them empty for local development unless testing email flows. See `.env.example`.

## Notification Flow Test

`src/lib/notifications/bug-notification-flow.test.ts` is an integration test for the full notification pipeline.