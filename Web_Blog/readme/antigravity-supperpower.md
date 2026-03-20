# obra/superpowers x Antigravity
**Superpowers** là một agentic skills framework & phương pháp phát triển phần mềm hoàn chỉnh - bộ **skills có thể tái sử dụng** giúp AI agent của bạn có kỷ luật: brainstorm (động não) trước khi code, lập kế hoạch trước khi thực thi, TDD, tự review. Tích hợp vào AntiGravity qua thư mục `.agent/`.

**TRẠNG THÁI HỖ TRỢ ANTIGRAVITY**
Repo gốc **obra/superpowers** chua có official AntiGravity - đang trong PR #682. Hiện dùng qua **port community** (copy .agent/folder) hoặc bootstrap thủ công. Xem hướng dẫn chi tiết trong trang này.

# Tại sao cần Superpowers?
AI agent không có kỷ luật - nó sẽ nhảy thẳng vào viết code mà không suy nghĩ. Superpowers đặt ra guardrails bắt buộc.

**Không có Superpower**
```markdown
Bạn: "Build me a CLI tool"
AI: Viết code ngay lập tức
Bạn: Nhận ra giữa chừng không đúng ý
Kết quả: Undo hàng giờ công việc
```
**Có Superpowers**
```markdown
Bạn: "Build me a CLI tool"
AI: Hỏi requirements → Propose design → Bạn approve
AI: Lập kế hoạch chi tiết → Bạn approve
AI: Implement từng task + TDD
```

- **Brainstorm trước khi code** - agent hỏi clarifying questions, propose 2-3 approaches, present design cho bạn approve từng phần.
- **Plan trước khi Implement** - mỗi task 2-5 phút, có file paths cụ thể, complete code, verification steps
- **TDD bắt buộc** - viết failing test TRƯỚC, xem fail, viết minimal code, xem pass, commit. Không skip được
- **Subagent review** - mỗi task có 2-stage review: spec compliance và code quality, fresh subagent mỗi lần
- **Skills tự động trigger** - agent không cần được nhắc. Thấy "let's build X" → brainstorming tự kích hoạt
- **YAGNI + DRY** - không build thứ chưa cần, không lặp code. Enforced by skills

# Cách Superpowers hoạt động
