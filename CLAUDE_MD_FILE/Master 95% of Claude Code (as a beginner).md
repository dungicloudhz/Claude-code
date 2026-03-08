# Làm chủ 95% Claude Code (Dành cho người mới bắt đầu)

Hướng dẫn này tóm tắt các khái niệm cốt lõi, framework WAT, và các bước để xây dựng, kiểm thử và triển khai các hệ thống tự động hóa AI sử dụng Claude Code.
**Tác giả:** Nate Herk

## 1. Giao diện & Thiết lập
Claude Code cho phép bạn xây dựng các dự án mã nguồn phức tạp và các hệ thống tự động hóa ngay bên trong môi trường phát triển máy tính cá nhân (IDE) của bạn.
- **Môi trường:** Hướng dẫn này sử dụng Visual Studio Code (VS Code).
- **Tiện ích mở rộng (Extension):** Tìm kiếm và cài đặt tiện ích "Claude Code" trong cửa hàng ứng dụng của VS Code.
- **Yêu cầu truy cập:**
  - Yêu cầu phải có gói cước Anthropic trả phí (Claude Pro hoặc Team).
  - Bạn phải đăng nhập bằng tài khoản Anthropic của mình bên trong tiện ích mở rộng.
- **Bố cục:**
  - **Bên trái (Explorer):** Đây là nơi chứa cấu trúc tệp của bạn (Workflows, Tools, Prompts).
  - **Bên phải (Agent):** Giao diện chat nơi bạn tương tác với Claude Code để lập kế hoạch và thực thi các tác vụ.
- **Bỏ qua quyền (Bypass Permissions):** Để tự động hóa nhanh hơn, bạn có thể bật "Bypass Permissions" trong cài đặt tiện ích. Điều này cho phép agent chỉnh sửa tệp mà không cần hỏi sự chấp thuận của bạn ở mỗi bước.

## 2. Framework WAT
Để xây dựng các hệ thống tự động hóa AI đáng tin cậy, Nate sử dụng một cấu trúc 3 lớp gọi là Framework WAT. Framework này tách biệt phần lập luận suy diễn (AI) khỏi phần thực thi tất định (Code).

**Lớp 1: Workflows (Luồng công việc - `/workflows`)**
- **Định dạng:** Các tệp `.md` (Markdown).
- **Mục đích:** Đây là các Quy trình thao tác chuẩn (SOP). Chúng định nghĩa mục tiêu, đầu vào yêu cầu, các chuỗi công cụ cần dùng, và cách xử lý các trường hợp ngoại lệ bằng tiếng Anh (ngôn ngữ tự nhiên).
- **Sự so sánh:** Lớp này giống như một "người quản lý" nói cho nhân viên biết chính xác các bước cần thực hiện.

**Lớp 2: Agent (Trợ lý - `claude.md`)**
- **Định dạng:** Tệp `.md` (Markdown) chứa system prompt (lời nhắc hệ thống).
- **Mục đích:** Đây là tập hợp các hướng dẫn cốt lõi dành cho Claude Code. Nó chỉ cho agent cách điều hướng thư mục, công cụ nào cần sử dụng, và cách tuân theo framework WAT.
- **Tự phục hồi (Self-Healing):** Agent được hướng dẫn cách tự đọc các lỗi, tinh chỉnh lại công cụ (refactor), và cập nhật lại workflows nếu có một bước nào đó thất bại.

**Lớp 3: Tools (Công cụ - `/tools`)**
- **Định dạng:** Các tệp `.py` (Python).
- **Mục đích:** Là những đoạn mã tham gia thực thi trực tiếp các hành động (ví dụ: quét dữ liệu trang web, gửi email, truy vấn cơ sở dữ liệu).
- **Bảo mật:** Các thông tin nhạy cảm và API key không bao giờ được lưu trữ trong các tệp này. Chúng được bảo mật trong một tệp `.env` để ngăn chặn rủi ro rò rỉ.

## 3. Lập kế hoạch & Xây dựng tự động hóa
Trước khi viết bất kỳ dòng code nào, bạn phải chuyển sang **Chế độ Kế hoạch (Plan Mode)** trong giao diện Claude Code.
1. **Trút bỏ ý tưởng (The Brain Dump):** Mô tả mục tiêu của bạn một cách rõ ràng (ví dụ: "Cào dữ liệu các kênh YouTube trong ngách AI và tạo ra một slide trình bày có nhận diện thương hiệu").
2. **Hỏi đáp lặp lại (Iterative Questioning):** Trong Chế độ Kế hoạch, agent sẽ đặt các câu hỏi làm rõ lại với bạn về yêu cầu tần suất, các điểm dữ liệu, và yêu cầu đầu ra.
3. **Danh sách công việc (To-Do List):** Sau khi kế hoạch được chấp thuận, agent sẽ tạo một danh sách check-list và thực thi nó từng bước một (tiến hành tạo thư mục, viết mã Python, và thiết lập luồng công việc).

## 4. Siêu năng lực: MCPs & Skills
Claude Code có thể được mở rộng bằng các khả năng bên ngoài để xử lý các tác vụ mà bản thân nó không thể làm một cách bẩm sinh.
- **Máy chủ MCP (Model Context Protocol):** Hãy coi đây giống như một "Cửa hàng Ứng dụng" cho AI. Nó cung cấp một cổng giao tiếp toàn cầu để kết nối tới các dịch vụ như Gmail, Google Calendar, hay Slack mà không cần phải viết các mã tích hợp API riêng lẻ cho từng nhiệm vụ nhỏ.
- **Kỹ năng (Skills):** Đây là những bộ hướng dẫn có thể tái sử dụng hoặc các lời nhắc tùy chỉnh (ví dụ: một kỹ năng "Thiết kế Canvas") mà Claude chỉ tải lên khi cần dùng.
  - **Local Skills:** Được cài đặt để dùng cho một dự án cụ thể.
  - **Global Skills:** Được cài đặt trên toàn bộ hệ thống Claude Code của bạn để thiết lập sẵn cho bất kỳ dự án nào.

## 5. Kiểm thử & Tối ưu hóa
Việc tự động hóa mới chỉ xong một nửa chặng đường sau khi xây dựng. Bạn luôn phải kiểm thử và kiểm tra lại logic.
- **Chạy thử lần đầu (Initial Run):** Thực thi workflow trong môi trường thử nghiệm để nhận diện xem có thiếu các thư viện phụ thuộc hay lỗi API không.
- **Giải quyết lỗi (Error Resolution):** Nếu một công cụ thất bại, bạn có thể sao chép lỗi từ cửa sổ terminal và dán nó trở lại vào giao diện chat của Claude Code. Agent sẽ đọc và phân tích log, sửa tập lệnh Python lỗi, và tự động chạy lại.
- **Nhận diện thương hiệu & Tài sản (Branding & Assets):** Bạn có thể kéo thả các file tài nguyên (như logo hoặc hình ảnh) vào thư mục dự án và yêu cầu agent tự động kết hợp chúng vào các sản phẩm cuối cùng như (PDFs, Slide decks, v.v.).

## 6. Triển khai vào Sản phẩm Thực tế (Modal)
Sau khi tính năng tự động hóa hoạt động tốt ở môi trường cá nhân (local), bạn cần đóng gói và đưa nó lên đám mây để tự động chạy theo chu kỳ (Cron) hoặc có một điều kiện kích hoạt nền (Webhook).
- **Nền tảng lưu trữ:** Hướng dẫn này sử dụng **Modal**, một hệ sinh thái cơ sở hạ tầng serverless dành cho Python.
- **Lợi ích:** Bạn chỉ cần trả tiền cho số giây mà mã máy thực sự chạy. Hệ thống này sẽ lo liệu phần "máy chủ thiết lập trên đám mây" đó thay cho bạn.
- **Các bước triển khai:**
  1. Cài đặt Modal client qua Claude Code.
  2. Ra lệnh cho agent: "Đẩy workflow này lên Modal để chạy vào lúc 6 giờ sáng mỗi thứ Hai."
  3. Đánh giá bảo mật: Luôn yêu cầu agent thực hiện việc đánh giá bảo mật trước khi triển khai để đảm bảo không bị rò rỉ các lỗi bảo mật hoặc API keys nào.
- **Giám sát (Monitoring):** Sử dụng trang báo cáo của Modal để kiểm tra log và theo dõi lịch sử chạy ứng dụng. Nếu ứng dụng trên mây bị ngắt hoặc lỗi, hãy copy dòng log của Modal và đưa về Claude Code để nó xử lý việc khắc phục sự cố.

***

*Bạn muốn kết nối với những người khác đang xây dựng và kiếm tiền từ việc tự động hóa AI?*
Trở thành Thành viên AIS Plus.
