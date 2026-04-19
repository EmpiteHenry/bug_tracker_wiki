---
type: system
owner: engineering
last_updated: 2026-04-20
source_count: 10
tags: [notifications, email, sendgrid, templates]
status: active
---

# Notification System

## Architecture

```
Bug / Auth event
    → BugNotificationEventService   (decides whether to fire)
    → BugRecipientService           (resolves recipient list)
    → NotificationDispatcher        (routes to delivery channel)
    → SendGridDelivery              (delivers via SendGrid API)
    → NotificationStore             (persists delivery record)
```

## Event Types

Events that trigger notifications (inferred from email templates):

| Template | Trigger |
|---|---|
| `bug-created.html` | New bug filed |
| `bug-assigned.html` | Bug assigned to user |
| `bug-status-changed.html` | Bug status updated |
| `bug-closed.html` | Bug closed |
| `bug-reopened.html` | Bug reopened |
| `bug-comment.html` | Comment added |
| `organization-invitation.html` | User invited to org |
| `verification.html` | Email verification required |
| `welcome.html` | Signup complete |
| `forgot-password.html` | Password reset requested |
| `password-changed.html` | Password changed |
| `password-reset-complete.html` | Password reset completed |

## Template Engine

`template-engine.ts` compiles HTML templates from `src/lib/notifications/templates/` using a base layout (`base-layout.html`) that all transactional emails extend.

`email-template-registry.ts` maps event types to templates and required variables.

## Settings

Notification settings (which recipient groups receive which events) are configurable per-organisation via:
- `GET /api/admin/notifications/settings`
- `PATCH /api/admin/notifications/settings`

Service: `notification-admin-service.ts`.

## Delivery History & Retry

- `GET /api/admin/notifications/deliveries` — paginated delivery log
- `POST /api/admin/notifications/deliveries/:id/retry` — retry a failed delivery
- `POST /api/admin/notifications/events/:id/retry` — replay a notification event

## Local Development

Leave SendGrid environment variables empty for local development. Email delivery is skipped; notification records are still written to the database.