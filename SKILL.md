---
name: slimzalo-agent
description: >-
  Operate SlimZalo via Agent API v1 with natural-language prompts (phone number,
  group name, message text). Guide users through SlimZalo UI setup (accounts,
  API keys, group whitelist). Use for SlimZalo, Zalo messaging, gửi tin Zalo,
  hộp thư Zalo, zmu_ keys, or SlimZalo configuration.
---

# SlimZalo Agent API

SlimZalo REST **Agent API v1** — agents send/read Zalo without the web UI.

**User setup on SlimZalo (accounts, API keys, group whitelist):** [setup.md](setup.md)  
**Human prompts & examples:** [AGENTS.md](AGENTS.md)

---

## Natural-language prompts (primary mode)

Users speak in plain Vietnamese. **Do not** ask for `ownId`, `thread_id`, or API URLs unless resolution fails.

### Example user request

> Gửi tin nhắn từ tài khoản Zalo có số điện thoại 0362466889 đến nhóm Tài liệu doanh nghiệp số: "File đã sẵn sàng."

### Parse intent

| User says | Resolve to |
|-----------|------------|
| SĐT `0362466889`, `+84362466889`, `84362466889` | `account_id` via `/accounts` (normalize digits, match `phoneNumber`) |
| "tài khoản Zalo 09xx…" / "từ Zalo …" | Same — phone or pick only account if key is per-account |
| "nhóm X", "vào nhóm X", "group X" | `thread_id` + `type: "group"` via `/groups` name match |
| "gửi cho Minh", "nhắn bạn …" | `type: "user"` via `/friends` or `/users/find` |
| Text in `"..."` or after `:` | `msg` |
| No message body | **Ask once:** "Nội dung tin nhắn là gì?" — do not guess |

### Phone normalization

Strip spaces/dashes. Compare variants:

- `0362466889`
- `84362466889`
- `+84362466889`

Match if suffix aligns (last 9–10 digits) against `phoneNumber` from `/accounts`.

### Group name matching

1. `GET /groups?account_id=…&limit=80` (increase if needed)
2. Exact name match first, then case-insensitive contains
3. Multiple matches → list options, ask user to pick
4. No match → show closest names from list

### Execution flow

```
1. If SLIMZALO_API_* missing → guide user using setup.md (steps 1–4)
2. GET /health
3. Resolve account (phone or per-account key)
4. Resolve recipient (group name / friend / phone)
5. If msg missing → ask user
6. POST /messages
7. Confirm: account phone, recipient name, message preview, request_id
```

### Minimal POST body (after resolution)

```json
{
  "account_id": "OWN_ID",
  "thread_id": "GROUP_OR_USER_ID",
  "type": "group",
  "msg": "Nội dung"
}
```

Per-account API key (`zmu_…`): omit `account_id` if key is bound to one Zalo account.

**Link thumbnail:** nếu `msg` chứa URL `http(s)://`, SlimZalo gửi **link card** (ảnh preview như Zalo PC). Để URL trong `msg` — **không** đưa URL bài viết vào `attachments` (field đó chỉ gửi file ảnh). Parse lỗi → vẫn gửi tin chữ.

---

## Environment

| Variable | Production example |
|----------|-------------------|
| `SLIMZALO_API_BASE` | `https://app.slim.vn/slimzalo/php/gateway/api/v1/index.php` |
| `SLIMZALO_API_KEY` | `zmu_…` |

Local: `http://localhost/zalomultiuser/php/gateway/api/v1/index.php`

Auth: `Authorization: Bearer $SLIMZALO_API_KEY` — never in query string, never log.

---

## Prerequisites (agent checks silently)

1. multizlogin running (`/health` → `multizlogin.ok`)
2. Zalo account logged in
3. API key with `messages:send` (and `groups:read` for group by name)
4. Target group whitelisted OR `allow_any_group` — see [setup.md](setup.md) step 4

If setup incomplete, **walk the user through setup.md** in plain Vietnamese with SlimZalo menu paths.

---

## API quick reference

| Action | Call |
|--------|------|
| Health | `GET /health` |
| Accounts | `GET /accounts` |
| Groups | `GET /groups?account_id=&limit=80` |
| Friends | `GET /friends?account_id=` |
| Find by phone | `POST /users/find` `{"account_id","phone"}` |
| Send | `POST /messages` |
| Read inbox | `GET /messages?account_id=&limit=20` |
| OpenAPI | `GET /openapi` |

Full table: [reference.md](reference.md)

---

## Errors → user guidance

| code | Tell user |
|------|-----------|
| `UNAUTHORIZED` | Tạo key tại Cài đặt → Khóa API, set env |
| `GROUP_NOT_ALLOWED` | Cài đặt → Lọc tin → bật nhóm; hoặc Gửi tin → cho phép mọi nhóm |
| `RATE_LIMITED` | Đợi `retry_after` giây, không gửi liên tục |
| `UPSTREAM_ERROR` | Chạy `start.bat`, kiểm tra tài khoản Zalo online |

Always report `request_id` on failure.

---

## Security

- HTTPS in production; minimal scopes on keys
- No bulk send on rate limit; no secrets in Zalo message body
