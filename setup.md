# Thiết lập SlimZalo cho Agent (hướng dẫn user)

Agent đọc phần này khi user chưa setup, gặp lỗi auth/policy, hoặc hỏi "cấu hình SlimZalo thế nào".

## URLs (production)

| Trang | URL |
|-------|-----|
| Console | https://app.slim.vn/slimzalo/php/gateway/app/ |
| Tài khoản Zalo | …/accounts.php |
| Cài đặt Webhook | …/settings.php?tab=webhook |
| Cài đặt Lọc tin | …/settings.php?tab=filter |
| Cài đặt Gửi tin (chống chặn) | …/settings.php?tab=send |
| Khóa API | …/settings.php?tab=api-keys |
| Tài liệu API | …/docs.php |

Local thay bằng: `http://localhost/zalomultiuser/php/gateway/app/`

---

## Bước 1 — Chạy dịch vụ kết nối Zalo

**Trên server / máy dev:**

```powershell
cd D:\Xampp\htdocs\zalomultiuser
start.bat
```

Hoặc `cd multizlogin && npm start` — service lắng nghe `:3000`.

**Kiểm tra:** Agent gọi `GET {SLIMZALO_API_BASE}/health` → `multizlogin.ok` phải `true`.

Nếu `false`: chạy lại `start.bat`, kiểm tra `multizlogin/src/config/.env` và `GATEWAY_SECRET` khớp `php/gateway/config.php`.

---

## Bước 2 — Đăng nhập tài khoản Zalo

1. Mở SlimZalo Console → menu **Tài khoản Zalo**
2. **Thêm tài khoản** → quét QR bằng app Zalo trên điện thoại
3. Đợi trạng thái **Đang hoạt động**

Ghi nhớ **số điện thoại** (vd. `0362466889`) — user sẽ dùng trong prompt tự nhiên.

---

## Bước 3 — Tạo Khóa kết nối API (bắt buộc cho agent)

1. **Cài đặt** → tab **Khóa kết nối API**
2. **Tạo khóa mới**
3. Chọn **tài khoản Zalo** (vd. SĐT `0362466889`)
4. Preset:
   - **send** — chỉ gửi + đọc tin (đủ cho agent gửi tin)
   - **full** — đầy đủ (gửi, đọc, bạn bè, nhóm, tìm user)
5. **Lưu key** `zmu_...` ngay — chỉ hiện một lần

**Gợi ý:** Key gắn 1 tài khoản → user prompt ngắn hơn (không cần nói SĐT mỗi lần).

### Cấu hình env cho agent

```bash
SLIMZALO_API_BASE=https://app.slim.vn/slimzalo/php/gateway/api/v1/index.php
SLIMZALO_API_KEY=zmu_xxxxxxxx
```

Cursor: Settings → Secrets, hoặc file `.env` local (không commit).

---

## Bước 4 — Cho phép gửi tin vào nhóm (quan trọng)

Mặc định SlimZalo **chặn gửi** vào nhóm chưa whitelist.

### Cách A — Whitelist từng nhóm (khuyến nghị)

1. **Cài đặt** → tab **Lọc tin**
2. Chọn **tài khoản Zalo** (SĐT cần gửi)
3. **Thêm nhóm** → **Chọn nhóm** → tìm tên (vd. *Tài liệu doanh nghiệp số*) → chọn → **Bật**
4. **Lưu bộ lọc**

### Cách B — Cho phép mọi nhóm

1. **Cài đặt** → tab **Gửi tin**
2. Bật **Cho phép gửi vào mọi nhóm** (`allow_any_group`)
3. **Lưu**

Nếu agent gặp `GROUP_NOT_ALLOWED` → hướng dẫn user làm Bước 4.

---

## Bước 5 — Webhook + lọc tin đến (chỉ khi cần **đọc** tin)

Gửi tin **không** cần bước này. Chỉ cần khi agent **đọc hộp thư** / trả lời tin đến.

1. **Cài đặt → Webhook** — SlimZalo tự gắn URL mặc định khi thêm tài khoản
2. **Cài đặt → Lọc tin** — bật nhận tin cá nhân / nhóm cần theo dõi
3. Tin mới sẽ vào **Hộp thư** → agent đọc qua `GET /messages`

---

## Bước 6 — Kiểm tra end-to-end

```bash
# 1. Health
curl -s "$SLIMZALO_API_BASE/health"

# 2. Danh sách tài khoản
curl -s -H "Authorization: Bearer $SLIMZALO_API_KEY" "$SLIMZALO_API_BASE/accounts"

# 3. Danh sách nhóm (thay OWN_ID)
curl -s -H "Authorization: Bearer $SLIMZALO_API_KEY" \
  "$SLIMZALO_API_BASE/groups?account_id=OWN_ID&limit=80"
```

Hoặc nhắn agent:

> Check SlimZalo health và liệt kê nhóm của Zalo 0362466889

---

## Checklist nhanh (agent tóm tắt cho user)

```
[ ] start.bat / multizlogin chạy
[ ] Tài khoản Zalo đã login (SĐT đúng)
[ ] API key zmu_... + SLIMZALO_API_* trong env
[ ] Nhóm đích đã whitelist HOẶC allow_any_group
[ ] /health OK
```

---

## Lỗi thường gặp → hướng dẫn user

| Lỗi | User cần làm |
|-----|----------------|
| `UNAUTHORIZED` | Tạo/lưu lại API key, set `SLIMZALO_API_KEY` |
| `UPSTREAM_ERROR` / health fail | Chạy `start.bat`, kiểm tra tài khoản online |
| `GROUP_NOT_ALLOWED` | Cài đặt → Lọc tin → bật nhóm, hoặc Gửi tin → allow mọi nhóm |
| `RATE_LIMITED` | Đợi, giảm tần suất; xem Cài đặt → Gửi tin |
| Không tìm thấy nhóm | Kiểm tra tài khoản đúng SĐT; tên nhóm chính xác; tài khoản có trong nhóm |
