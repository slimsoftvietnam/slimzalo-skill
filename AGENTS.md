# SlimZalo — Hướng dẫn cho Agent & Human

Coding agent (Cursor, Codex, Claude Code) điều khiển SlimZalo qua API. User có thể **prompt tự nhiên** — không cần biết `ownId` hay `thread_id`.

> Chi tiết agent: [SKILL.md](SKILL.md)  
> Thiết lập SlimZalo trên giao diện: [setup.md](setup.md)

---

## Prompt đơn giản (copy & dùng)

```
Gửi tin từ tài khoản Zalo 0362466889 đến nhóm Tài liệu doanh nghiệp số: "File đã upload xong."
```

| Tình huống | Prompt |
|------------|--------|
| Gửi nhóm | `Zalo 0362466889 → nhóm Team Dev: "Deploy 14h"` |
| Gửi cá nhân | `Gửi từ Zalo 0362466889 cho SĐT 0987654321: "Đã nhận đơn"` |
| Gửi link (thumbnail) | `Zalo 0362466889 → nhóm Team Dev: "https://ai.slim.vn/blog/claude-fable-5-1-giam-chi-phi-ai"` |
| Đọc tin mới | `Đọc 20 tin mới SlimZalo, tóm tắt` |
| Kiểm tra | `Check SlimZalo và liệt kê nhóm của 0362466889` |

---

## Thiết lập (một lần)

Xem [setup.md](setup.md) — tóm tắt:

1. Chạy `start.bat` (multizlogin)
2. Đăng nhập Zalo trong Console
3. Tạo API key (`zmu_...`)
4. Whitelist nhóm hoặc bật gửi mọi nhóm
5. Set `SLIMZALO_API_BASE` + `SLIMZALO_API_KEY`

---

## Liên hệ

**SlimSoft** — info@slimsoft.vn  
Sản phẩm: [SlimZalo](https://app.slim.vn/slimzalo/php/gateway/app/)
