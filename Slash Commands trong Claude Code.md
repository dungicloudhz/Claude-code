Đây là hướng dẫn chi tiết toàn bộ Slash Commands trong Claude Code:
# Danh sách đầy đủ Built-in Slash Commands
Claude Code có hai loại slash (/) commands: **build-in commands** đi kèm với Claude Code, và **custom commands** được lưu trong thư vục `.claude/commands/`.

# 1. Quản lý Context & Session
- `/clear` Xóa toàn bộ lích sử hội thoại, bắt đầu phiên mới hoàn toàn. Dùng `/claer` trước khi bắt đầu một task khacs trong cùng project. Lưu ý quan trọng: `/clear` xóa toàn bộ context - dùng `/compact` nếu bạn muốn giữ lại bản toám tắt.
    ```bash
    /clear
    ```
- `/compact [instructions]` Nén hội thoại hiện tại thành bản tóm tắt có cấu trúc, sau đó thay thế lịch sử cũ. Bạn giữ được thông tin quan trọng khi giảm kích thước context xuống 50-70%. Đây là lệnh chiến lược nhất cho các session dài.
    ```bash
    /compact
    /compact focus on refactoring decisions ← chỉ định Claude ưu tiên gì khi tóm tắt
    ```
- `/context` Hiển thị trực quan mức sử dụng context hiện tại - giúp bạn biết khi nào cần `/compact`.
- `/cost` Xem thống kê số token đã dùng và ước tính chi phí trong session.

# 2. Cài đặt và cấu hình
- `/config` Mở giao diện cài đặt - thay đổi các tùy chọn của Claude Code.
- `/model` Chuyển đổi giữa các model: Sonnet (mặc định cho hầu hết task), Kaiku (nhanh và tiết kiệm token), Opus (phức tạp, nhiều bước).
    ```bash
    /model claude-opus-20250514
    /model claude-haiku-4-5
    ```
- `/memory` Chỉnh sửa CLAUDE.md - file bộ nhớ dài hạn của project.
- `/login` `/logout` Chuyển đổi tài khoản Anthropic hoặc đăng xuất.

# 3. Tích hợp & Mở rộng
- `/init` Tạo file `CLAUDE.md` cho project - Claude sẽ tự phân tích codebase và điền thông tin ban đầu.
    ```bash
    /init
    ```
- `install-github-app` Sau khi chạy lệnh này, Claude sẽ tự động review các PR của bạn. Rất hữu ích vì Claude thường phát hiện lỗi logic và vấn đề bảo mật mà con người hay bỏ qua. Claude sẽn thêm file `claude-code-review` với prompt review có sẵn mà bạn có để điều chỉnh.
- `/mcp` Quản lý kết nối MCP server - tích hợp thêm công cụ bên ngoài như database, API, filesystem.
- `hook` Cấu hình hooks - shell commands tự động chạy tại các thời điểm trong vòng đời của Claude Code, như: format code sau khi chỉnh sửa file, hoặc chạy type check sau mỗi lần edit.
- `/ide` Quản lý tích hợp IDE (VS Code, Cursor v.v.)
# 4. Tiện ích khác
- `/help` Hiển thị tất cả slash command có sẵn, bao gồm cả custom commands từ `.claude/commands/` và các commands từ MCP server đã kết nối.
- `/doctor` Kiểm tra sức khỏe cài đặt - hữu ích khi gặp lỗi không rõ nguyên nhân.
- `/bug` Báo cáo lỗi trực tiếp với Anthropic.
- `/export [filename]` Xuất hội thoại ra file hoặc clipboard.
- `/add-dir` Thêm thưu mục làm việc bổ sung - hữu ích cho monorepo.
- `/agent` Quản lý các custom AI subagent.
- `/exit` Thoát khỏi REPL

# Custom Slash Commands - Tự tạo lệnh riêng
Slash commands thực ra là các prompt được lưu bên trong file markdown. Để Claude nhận diện được, file phải được lưu trong `~/.claude/commands` (dùng toàn tục) hoặc trong `.claude/commands/` của project (chỉ dùng trong project đó).

Ví dụ tạo lệnh `/review`:
```bash
mkdir -p .claude/commands
```
```markdown
<!-- .claude/commands/review.md -->
---
allowed-tools: Read, Grep, Glob, Bash(git diff:*)
description: Comprehensive PR review
---

## Changed Files
!`git diff --name-only HEAD~1`

## Detailed Changes  
!`git diff HEAD~1`

## Review Checklist
1. Code quality và readability
2. Security vulnerabilities
3. Performance implications
4. Test coverage
```

Gọi bằng: `/review`
**Truyền tham số**: Dùng `$ARGUMENTS` để bắt tất cả nội dung sau tên lệnh. Ví dụ: `/fix-issue 123 high` sẽ truyền `123 high` vào `$ARGUMENT`.
```markdown
<!-- .claude/commands/fix.md -->
Fix issue #$ARGUMENTS với mức ưu tiên cao. Phân tích nguyên nhân trước khi sửa.
```
# Tóm tắt nhanh để nhớ

| Tình huống | Dùng lệnh |
|---|---|
| Bắt đầu task mới | `/clear` |
| Session dài, hết token | `/compact` |
| Đổi sang model nhanh hơn | `/model claude-haiku-4-5` |
| Khởi tạo project mới | `/init` |
| Xem token đã dùng | `/cost` |
| Tích hợp GitHub | `/install-github-app` |
| Claude bị lỗi kỳ lạ | `/doctor` |
| Xem tất cả lệnh | `/help` |

Bạn muốn tìm hiểu thêm về phần nào — ví dụ cách tạo custom commands hay cấu hình hooks?