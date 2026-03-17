Dưới đây là hướng dẫn chi tiết để sử dụng **Claude Code** - công cụ AI coding của Anthropic chạy trực tiếp trong terminal.
# 1. Yêu cầu & cài đặt
- **Yêu cầu**:
    - Node.js phiên bản 18 trở lên (hoặc dùng bản cài đặt native)
    - Tài khoản Claude có trả phí (Pro, Max, Teams, hoặc Enterprise) - Hoặc Claude Console với billing đã kích hoạt
- **Cài đặt:**: Chạy lệnh sau trong terminal tùy theo hệ điều hành của ban. Lưu ý: Cài đặt qua `npm install - g @anthropic-ai/claude-code` vẫn hoạt động nhưng đã bị deprecated (yêu cầu) - nên dùng bản cài đặt native thay thế. Nên trước đây đã cài qua npm, có thể chuyển sang bằng lệnh `claude install`.
```bash
# Cài đặt (native - khuyến nghị)
claude install

# Hoặc qua npm (cũ)
npm install -g @anthropic-ai/claude-code
```

- **Đăng nhập lần đầu**: Sau khi cài đặt, điều hướng vào thư mục dự án và chạy `claude`. Bạn sẽ được hỏi muốn dùng subcription hay API billing. Sau đó nhận link đăng nhập tới verification code để nhập vào terminal. Một workspace "Claude Code" sẽ tự động được tạo để theo dõi usage và chi phí.

# 2. Khởi động & Các lệnh cơ bản
Có 2 chế độ chính: **Interactive mode** - chạy `claude` để bắt đầu REPL; **One-shot mode** - dùng `claude -p "câu hỏi"` cho các lệnh nhanh.
Ví dụ lệnh nhanh:
```bash
claude -p "Đây là loại project gì?"
claude -p "Liệt kê các file trong thư mục này"
claude -c # Tiếp tục conversation (cuộc hội thoại) trước
claude -r # Resume session gần nhất
```

# 3. Cách giao tiếp hiệu quả
Claude Code rất giỏi suy luận ý định, nhưng sẽ hiệu quả hơn khi bạn giải thích mục tiêu và ràng buộc của mình. Thay vì chỉ nói `tạo web scraper`, hãy nói: `Tôi muốn scrape giá sản phẩm từ một trang e-commerce để theo dõi biến động giá. Tôi nghĩ dùng Python với request và BeatifulSoup. Lưu trữ dữ liệu bằng CSV cho đơn giản. Scraper chạy hàng ngày và xử lý được timeout.`

- **Các tác vụ phổ biến:**
    - `Implement user authentication using JWT` → Claude lên kế hoạch và viết code
    - `Users bị lỗi 401 khi nhập sai mật khẩu 2 lần` → Claude tìm và sửa bug
    - `Refactor module này dùng async/await thay cho callbacks` → Tái cấu trúc code
# 4. Slash Commands quan trọng
|Lệnh|Tác dụng|
|--|--|
|`/init`|Tạo file CLAUDE.md cho project|
|`/clear`|Xóa lịch sử chat (giải phóng token)|
|`/install-github-app`|Tích hợp để Claude tự review PR|

Dùng `/clear` thường xuyên - mỗi khi bắt đầu task mới, hãy clear chat. Bạn không cần lịch sử chiếm token, và cũng không cần Claude chạy thêm các lệnh tóm tắt conversation cũ.

# 5. File CLAUDE.md - Cực kỳ quan trọng
Tạo file `CLAUDE.md` trong thư mục gốc của project để Claude hiểu codebase của bạn. Claude Code tự động đọc file này khi khởi tạo.

```markdown
# CLAUDE.md

## Project Overview
Mô tả ngắn về project và các công nghệ chính.

## Development Guidelines
- Coding standards và conventions
- Cấu trúc thư mục ưa thích
- Cách viết test

## Important Commands
- Build: `npm run build`
- Test: `npm test`
- Dev server: `npm run dev`
```
Một CLAUDE.md được viết tốt sẽ mang lại lợi ích cho mọi session. Mỗi phút bạn bỏ ra để sửa nó sẽ giúp bạn tiết kiệm thời gian nhắc lại sau này. Commit file này vào repo để cả team đều được hưởng lợi.

# 6. Plan Mode - Dùng chon task phức tạp
Nếu một task liên quan đến hơn 2-3 file hoặc có quyết định kiên trúc, hãy bắt đầu bằng Plan Mode. Việc lên kế hoạch trước giúp tiết kiệm hàng giờ qua lại sau này. Nhảy thẳng vào implementation với prompt mơ hồ khiến Claude phải tự đưa ra giả định - những giả định đó tích lỹ và bạn có thể thấy mình đang đi sai hướng 3 file sau.

# 7. Quản lý quyền (Permission System)
- Claude Code luôn xin phép trước khi chỉnh sửa file. Bạn có thể phê duyệt từng thay đổi hoặc bặt chế đội `Accept all` cho cả session.
- Permission modes không chỉ là tính năng bảo mật - chúng là công cụ workflow cho phép bạn kiểm soát mức độ tự chủ của Claude.

# 8. Mẹo nâng cao
- **Message queuing**: Bạn có thể gõ chiêu prompt và Claude sẽ xử lý chúng theo thứ tự. Claude đủ thông minh để biết khi nào nên chờ phản hồi từ bạn trước khi chạy prompt tiếp theo.
- **Tích hợp IDE**: Cài extension Claude Code cho VS Code, Cursor. Bạn có thể chạy nhiều instance song song trong các pane khác nhau của IDE, miễn là chúng làm việc trên các phần khác nhau của codebase.

# 9. Gói subcription
Claude Code đi kèm với subscription. Gói **Pro ($20/tháng)** là đủ cho hầu hết developers cá nhân. Nếu dùng Claude Code cả ngày cho dự án nặng, hãy cần nhắc gói **Max 5x**. Không có phí overrage - bạn được giới hạn có định mỗi chu kì và sẽ được cảnh bảo khi gần hết.

Tóm lại, Claude Code mạnh nhất khi bạn: (1) có CLAUDE.md rõ ràng, (2) giao tiếp như với động nghiệp - giải thích ngữ cảnh và mục tiêu, (3) dùng Plan Mode cho task lớn, và (4) `/clear` thường xuyên để tiết kiệm token. Bạn muốn hiểu sau hơi về tính năng nào không?