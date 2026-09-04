# SlimZalo Agent API — Reference

Base path suffix: `/api/v1/index.php`

Production: `https://app.slim.vn/slimzalo/php/gateway/api/v1/index.php`  
Local: `http://localhost/zalomultiuser/php/gateway/api/v1/index.php`

## Endpoints

| Method | Path | Scope | Description |
|--------|------|-------|-------------|
| GET | `/health` | — | Engine + multizlogin status |
| GET | `/openapi` | — | Machine-readable catalog |
| GET | `/accounts` | `accounts:read` | Logged-in Zalo accounts |
| GET/POST | `/friends` | `friends:read` | Friend list (`account_id` required) |
| GET/POST | `/groups` | `groups:read` | Group list (`account_id`, optional `limit`) |
| GET | `/threads` | `messages:read` | Conversations from inbox |
| GET | `/messages` | `messages:read` | Inbox messages or `source=group_history` |
| POST | `/messages` | `messages:send` | Send text; `http(s)` URL in `msg` → Zalo link card (thumbnail). Optional `attachments` = image files |
| POST | `/users/find` | `users:find` | Find UID by phone |
| GET/POST | `/users/info` | `users:read` | User profile |
| POST | `/friends/request` | `friends:request` | Send friend request |

## Scopes (presets in UI)

| Preset | Scopes |
|--------|--------|
| `full` | All scopes |
| `read` | read-only (no send) |
| `send` | `messages:read` + `messages:send` |

## POST /messages fields

| Field | Required | Notes |
|-------|----------|-------|
| `account_id` | Yes* | *Optional if API key is bound to one account |
| `thread_id` | Yes | User UID or group ID |
| `type` | No | `user` (default) or `group` |
| `msg` | Yes* | *Or `attachments` array of image URLs. If `msg` contains an `http(s)://` URL, SlimZalo sends a Zalo link card (thumbnail) via parseLink+sendLink; falls back to plain text if parse fails |
| `attachments` | No | `["https://..."]` |
| `request_id` | No | Idempotency hint; auto-generated if omitted |

## Inbound (receive messages into inbox)

Agents read from inbox; receiving requires:

1. Webhook configured (SlimZalo → Cài đặt → Webhook)
2. Listen filters (SlimZalo → Cài đặt → Lọc tin)
3. multizlogin pushing events to inbound URL

Not controlled via Agent API send endpoints.

## SlimZalo product

- Console: https://app.slim.vn/slimzalo/php/gateway/app/
- API docs UI: …/docs.php
