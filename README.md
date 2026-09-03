# slimzalo-skill

Agent Skill để **Cursor**, **Codex**, **Claude Code** điều khiển [SlimZalo](https://app.slim.vn/slimzalo/php/gateway/app/) — gửi/đọc tin Zalo qua API với prompt tự nhiên tiếng Việt.

**SlimSoft** · info@slimsoft.vn

---

## Prompt ví dụ

```
Gửi tin từ tài khoản Zalo 0362466889 đến nhóm Tài liệu doanh nghiệp số: "File đã sẵn sàng."
```

Agent tự resolve SĐT → tài khoản, tên nhóm → group id, rồi gọi SlimZalo Agent API v1.

---

## Cài đặt

### Cursor (project hoặc global)

```powershell
# Trong project SlimZalo / zalomultiuser
git clone https://github.com/slimsoftvietnam/slimzalo-skill.git .cursor/skills/slimzalo-agent

# Hoặc global (mọi project)
git clone https://github.com/slimsoftvietnam/slimzalo-skill.git $env:USERPROFILE\.cursor\skills\slimzalo-agent
```

Rule Cursor (tuỳ chọn): copy nội dung từ `.cursor/rules/slimzalo-agent.mdc` trong repo zalomultiuser.

### Codex / Claude Code

```powershell
git clone https://github.com/slimsoftvietnam/slimzalo-skill.git $env:USERPROFILE\.codex\skills\slimzalo-agent
```

Hoặc symlink thư mục skill tương đương trên máy bạn.

### Biến môi trường (bắt buộc)

```bash
SLIMZALO_API_BASE=https://app.slim.vn/slimzalo/php/gateway/api/v1/index.php
SLIMZALO_API_KEY=zmu_xxxxxxxx
```

Tạo key tại SlimZalo → **Cài đặt → Khóa kết nối API**. Hướng dẫn đầy đủ: [setup.md](setup.md)

---

## Nội dung repo

| File | Mô tả |
|------|--------|
| [SKILL.md](SKILL.md) | Hướng dẫn cho agent (parse prompt, API, lỗi) |
| [setup.md](setup.md) | Thiết lập SlimZalo trên giao diện (user) |
| [AGENTS.md](AGENTS.md) | Prompt mẫu cho human |
| [reference.md](reference.md) | Bảng endpoint API |

---

## Yêu cầu

- SlimZalo + multizlogin đang chạy
- Tài khoản Zalo đã đăng nhập
- API key có scope `messages:send` (và `groups:read` nếu gửi theo tên nhóm)

---

## License

MIT — SlimSoft Vietnam
