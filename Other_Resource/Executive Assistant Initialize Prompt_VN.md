# Khởi Tạo Trợ Lý Điều Hành / Bộ Não Thứ Hai (Executive Assistant / Second Brain)

Tôi muốn bạn thiết lập thư mục dự án này như một trợ lý điều hành cá nhân / bộ não thứ hai của tôi trong Claude Code. Bạn sẽ thực hiện việc này qua 3 giai đoạn. Hoàn thành trọn vẹn từng giai đoạn trước khi chuyển sang giai đoạn tiếp theo.

## Giai đoạn 1: Tạo cấu trúc thư mục

Trước tiên, hãy khởi tạo một kho lưu trữ `git` trong thư mục này nếu nó chưa tồn tại.

Sau đó, tạo cấu trúc mẫu như dưới đây. Chưa cần chèn nội dung vào các tệp – chỉ cần tạo một bộ khung với các tệp giữ chỗ (placeholder) ở những nơi được ghi chú.

```text
CLAUDE.md                           # Tệp bộ não chính (chúng ta sẽ điền vào Giai đoạn 3)
CLAUDE.local.md                     # Các thiết lập ghi đè cá nhân của tôi (git-ignored)
.gitignore                          # Bỏ qua các tệp .env, CLAUDE.local.md, settings.local.json
.claude/
  settings.json                     # Đối tượng JSON trống ở hiện tại: {}
  rules/                            # Chúng ta sẽ thêm các tệp quy tắc ở Giai đoạn 3
  skills/                           # Trống -- chúng ta sẽ xây dựng các kỹ năng dần theo thời gian
context/
  me.md                             # Về tôi (điền vào Giai đoạn 3)
  work.md                           # Chi tiết công việc/kinh doanh của tôi (điền vào Giai đoạn 3)
  team.md                           # Đội ngũ của tôi (điền vào Giai đoạn 3)
  current-priorities.md             # Những ưu tiên hiện tại (điền vào Giai đoạn 3)
  goals.md                          # Mục tiêu và cột mốc hàng quý (điền vào Giai đoạn 3)
templates/
  session-summary.md                # Mẫu để tổng kết phiên làm việc
references/
  sops/                             # Quy trình thực hành chuẩn (SOPs) (tạm để trống)
  examples/                         # Các ví dụ về đầu ra và hướng dẫn phong cách văn bản (tạm để trống)
projects/                           # Các dự án/luồng công việc đang hoạt động (tạm để trống)
decisions/
  log.md                            # Nhật ký quyết định (chỉ nối thêm - append-only, tạm để trống)
archives/                           # Tài liệu đã hoàn thành/lỗi thời (tạm để trống)
```

Đối với các thư mục trống, hãy tạo một tệp `.gitkeep` bên trong để `git` có thể theo dõi chúng.

Đối với tệp `templates/session-summary.md`, hãy sử dụng mẫu này:

```markdown
# Tổng Kết Phiên Làm Việc (Session Summary)

**Ngày:**
**Trọng tâm:**

## Những Công Việc Đã Hoàn Thành
- 

## Các Quyết Định Đã Đưa Ra
- 

## Các Vấn Đề Còn Tồn Đọng / Bước Tiếp Theo
- 

## Cập Nhật Bộ Nhớ
- Sở thích/Thói quen đã học được:
- Quyết định cần lưu nhật ký:
```

Đối với tệp `decisions/log.md`, hãy sử dụng mẫu khởi đầu này:

```markdown
# Nhật Ký Quyết Định (Decision Log)

Chỉ nối thêm (Append-only). Khi có một quyết định quan trọng được thông qua, hãy lưu nó tại đây.

Định dạng: [YYYY-MM-DD] QUYẾT ĐỊNH: ... | LÝ DO: ... | BỐI CẢNH: ...

---
```

Đối với tệp `.gitignore`:

```text
.env
CLAUDE.local.md
.claude/settings.local.json
node_modules/
```

Đối với tệp `CLAUDE.local.md`:

```markdown
# Cấu Hình Ghi Đè Cục Bộ (Local Overrides)

Các tùy chọn cá nhân và thiết lập cục bộ không chia sẻ thông qua git.
Hãy thêm vào đây bất kỳ cấu hình chuyên biệt nào thuộc máy của bạn.
```

## Giai đoạn 2: Phỏng vấn Onboarding

Trước khi điền vào các tệp ngữ cảnh, các quy tắc và `CLAUDE.md`, hãy phỏng vấn tôi. **Hãy hỏi từng phần một. Đừng tung ra tất cả các câu hỏi cùng một lúc** -- hãy hỏi một phần, đợi tôi trả lời đầy đủ, rồi mới chuyển sang phần tiếp theo.

### Phần 1: Giới Thiệu Về Bạn (About You)
- Tên của bạn là gì?
- Vai trò/Chức danh của bạn là gì? (vd: CEO, freelancer, nhà sáng tạo nội dung, lập trình viên)
- Múi giờ của bạn là gì?
- Chỉ trong một câu, công việc của bạn là gì?
- Ưu tiên #1 của bạn là gì -- điều mà mọi thứ khác đều phải xoay quanh hoặc hỗ trợ cho nó?

### Phần 2: Công Việc / Kinh Doanh của bạn (Your Business / Work)
- Công ty hoặc doanh nghiệp của bạn tên là gì? (hoặc mô tả công việc của bạn nếu bạn không phải là chủ doanh nghiệp)
- Các sản phẩm, dịch vụ hoặc nguồn doanh thu của bạn là gì? (Liệt kê từng mục thành một dòng)
- Ước tính mỗi mục mang lại khoảng bao nhiêu doanh thu? (tùy chọn, nhưng giúp tôi ưu tiên công việc tốt hơn)
- Bạn sử dụng các công cụ nào thường ngày? (ClickUp, Notion, Slack, Google Workspace, v.v.)
- Bạn đã có bất kỳ máy chủ MCP nào được kết nối với Claude Code chưa? (Nếu bạn không biết điều này nghĩa là gì, chỉ cần trả lời "chưa")

### Phần 3: Đội Ngũ Của Bạn (Your Team)
- Bạn có đội ngũ nào không? Nếu có, gồm bao nhiêu người?
- Ai là 2-3 nhân sự chủ chốt mà tôi nên biết? (Tên, vai trò, khi nào cần gắn thẻ/liên hệ với họ)
- Đội ngũ của bạn thường giao tiếp ở đâu? (ClickUp, Slack, v.v.)
- Khó khăn lớn nhất của bạn trong việc quản lý đội ngũ là gì?

### Phần 4: Các Ưu Tiên, Mục Tiêu & Dự Án (Priorities, Goals & Projects)
- 3-5 điều bạn đang tập trung cao độ nhất ngay lúc này là gì?
- Có bất kỳ hạn chót (deadlines) hoặc các dự án đặc biệt nhạy cảm về thời gian nào tôi nên biết không?
- Bạn có theo dõi các mục tiêu hoặc cột mốc hàng quý không? (Nếu có, hãy liệt kê chúng ra. Nếu bạn không có mục tiêu chính thức, không sao cả -- các ưu tiên hàng đầu của bạn ở trên sẽ được dùng để thay thế.)
- Bạn đang quản lý các dự án hay luồng công việc nào hiện tại? (Đây là các dự án/sáng kiến cụ thể, không phải những trách nhiệm lặp đi lặp lại. Vd: "ra mắt khóa học mới", "tuyển dụng trợ lý ảo (VA)", "thiết kế lại trang web")

### Phần 5: Phong Cách Giao Tiếp (Communication Preferences)
- Bạn thích thông tin được trình bày như thế nào? (gạch đầu dòng, các đoạn văn chi tiết, v.v.)
- Những điều làm bạn khó chịu khi đọc/viết là gì (pet peeves)? (vd: "không bao giờ dùng biểu tượng cảm xúc (emoji)", "không dùng dấu chấm than thừa thãi", "tránh viết quá 3 câu một đoạn")
- Tone giọng (văn phong) yêu cầu cho liên lạc nội bộ là gì? (thoải mái, chuyên nghiệp, v.v.)
- Tone giọng (văn phong) thế nào dành cho các nội dung công bố ra bên ngoài?

### Phần 6: Bạn Muốn Nhờ Giúp Đỡ Việc Gì Ngay Lúc Này? (What Do You Want Help With?)
- Những công việc lặp đi lặp lại nào đang ngốn nhiều thời gian nhất của bạn? (Liệt kê bao nhiêu tùy thích)
- Công việc thiết yếu đầu tiên bạn muốn giao lại cho trợ lý giải quyết là gì?
- Bạn có bất kỳ quy trình làm việc (workflows) cụ thể nào muốn tự động hóa hoặc đưa vào làm mẫu không?

## Giai đoạn 3: Phác thảo & Xây dựng các tệp

Dựa trên câu trả lời của tôi, hãy điền thông tin vào tất cả các tệp:

### Các Tệp Ngữ Cảnh (Context files)
- **context/me.md** -- Hồ sơ cá nhân của tôi (theo đáp án Phần 1).
- **context/work.md** -- Chi tiết thông tin kinh doanh/công việc của tôi (theo Phần 2).
- **context/team.md** -- Cấu trúc tổ chức đội ngũ (theo Phần 3; nếu tôi làm một mình (solo), thì chỉ cần ghi chú điều đó và có thể bỏ qua nội dung tệp này).
- **context/current-priorities.md** -- Các nhiệm vụ/ưu tiên hiện tại từ Phần 4, đính kèm ngày tháng hôm nay.
- **context/goals.md** -- Mục tiêu & Cột mốc hàng quý từ Phần 4. (Nếu không có bộ tiêu chuẩn đô lường mục tiêu chuẩn xác nào, có thể sử dụng các ưu tiên hiện tại như mục tiêu không chính thức. Đính kèm chú thích về Quý diễn ra (Ví dụ: "Q2 2026"). Viết một ghi chú trên cùng tệp: "Cập nhật tệp này vào giai đoạn bắt đầu mỗi Quý.")

### Dự Án (Projects)
Nếu người dùng liệt kê các dự án đang hoạt động tại Phần 4, hãy tạo một thư mục riêng cho từng dự án bên trong thư mục `projects/`. Mỗi thư mục dự án sẽ bao gồm một tệp `README.md` ngắn gọn chứa:
- Mô tả tóm lược một dòng về dự án
- Trạng thái hiện tại (Đang tiến hành, Lên kế hoạch, Tạm hoãn)
- Các ngày hạn chót hoặc cột mốc đã được nhắc đến

Nếu người dùng chưa nhắc tới bất kỳ đặc thù nào, thì hãy để trống thư mục `projects/`.

### Các Tệp Quy Tắc (Rules files) tại `.claude/rules/`
Tạo các tệp quy tắc liên quan tới tùy chọn phong cách giao tiếp của tôi (Phần 5). Các loại mẫu đề xuất:
- **communication-style.md** -- Văn phong viết, định dạng yêu thích, những thứ cần tránh.
- Bất kỳ các quy định đặc thù về ngành nghề/miền chuyên môn phát sinh xuất hiện từ các đáp án (ví dụ nếu tôi có quy chế đặt tên file cụ thể hay tiêu chuẩn nội dung nhất định).

Giữ cho mỗi tệp quy tắc (Rule file) tập trung vào một CHỦ ĐỀ DUY NHẤT. Đừng thiết lập vượt quá phạm vi 3 - 4 tệp ban đầu.

### CLAUDE.md (Bộ Não Trung Tâm / The main brain)
Đây là tệp trọng yếu nhất của toàn dự án. Cần đảm bảo dung lượng file DƯỚI 150 Dòng. Thông tin bao hàm:

1. **Danh Tính (Identity)** -- Khai báo vai trò trên 1 dòng (Ví dụ: "Bạn là trợ lý điều hành của [TÊN]").
2. **Ưu Tiên Số 1 (Top Priority)** -- Điều quan trọng cốt lõi số 1 mà mọi thứ phải xoay quanh nó.
3. **Nhúng / Gọi Tệp Ngữ Cảnh (Context imports)** -- Dùng các lệnh dẫn chuẩn như `@context/me.md`, `@context/team.md`, `@context/goals.md` gọi tắt (import) ngay trong hệ thống thay vì phải viết nội dung của chúng thành lời.
4. **Tích hợp Công Cụ (Tool integrations)** -- Hệ thống đang kết nối thêm với App nào (ClickUp,...)
5. **Danh mục Kỹ năng (Skills directory)** -- Chỉ tới `.claude/skills/` và trình bày ngắn gọn cách kỹ năng hoạt động.
6. **Nhật Ký Quyết Định (Decision Log)** -- Trỏ tới `decisions/log.md` kèm 1 điều kiện theo đuổi văn phong nối tiếp (Append-only).
7. **Bộ Nhớ (Memory)** -- Bổ sung thêm một phân mục nhỏ giải thích cơ chế của bộ nhớ:
   - "Claude Code tự duy trì một hệ thống trí nhớ bền vững xuyên suốt các cuộc hội thoại. Khi làm việc, hệ thống tự động ghi lại thói quen, khuynh hướng hành vi cá nhân hay sở thích mà không đòi hỏi thao tác thiết lập cài đặt phụ phí nào."
   - "Nếu bạn muốn Trợ lý nhớ 1 tính chất chuyên sâu nào vào hệ thống, chỉ cần nhắc 'hãy nhớ rằng tôi luôn muốn điều X' và nó sẽ tự lưu."
   - "Bộ nhớ + Tệp ngữ cảnh + Nhật Ký Quyết Định = trợ lý sẽ ngày một thông minh hơn theo thời gian mà không cần bạn phải rập khuôn giải thích lại từ đầu."
8. **Duy Trì Ngữ Cảnh Tương Lai (Keeping context current)** -- Một phần để bảo trì cập nhật ngắn hạn như:
   - Thay đổi các yếu tố tại `context/current-priorities.md` mỗi khi cần dời mục tiêu sang một phần mới.
   - Nhắc làm mới nội dung vào lúc chuyển Quý tại `context/goals.md`.
   - Lưu trữ các quyết định hệ trọng tại file `decisions/log.md`.
   - Thêm tài liệu tham khảo khi phát sinh nhu cầu.
   - Phát triển nhiều Skills khi phát hiện bạn đang đề xuất lặp đi lặp lại cùng một nội dung.
9. **Dự án (Projects)** -- Trỏ hệ thống thư mục tới `projects/` để thông báo chỗ lưu trữ của các công việc đang chạy.
10. **Mẫu (Templates)** -- Điểm qua về `templates/`.
11. **Tài Liệu Tham Khảo (References)** -- Điểm qua về `references/`.
12. **Quy Tắc Lưu Trữ (Archives rule)** -- Ngưng xóa. Hãy nén dồn chúng sang thư mục lưu trữ thay thế.

Tuyệt đối KHÔNG viết thêm về tùy chọn "Các Quy Chế Giao Tiếp" dồn hết nội dung đó vào CLAUDE.md -- chỉ tiêu đó sẽ nằm ở `.claude/rules/communication-style.md`.

KHÔNG lặp lại các chi tiết từ file context — dùng `@` để tải chúng qua cấu trúc mã.

TỪ CHỐI cung cấp bản mô tả "Giao Thức Mở Đầu Phiên / Session Start Protocol" yêu cầu đọc nguyên rổ file — hệ thống `@` đã thay phần việc đó rất nhịp nhàng.

### Danh mục Kỹ Năng (Skills directory)
Nghiêm cấm điền vào và để trống thư mục `.claude/skills/`. Đừng tạo ra một danh mục kỹ năng riêng biệt dồn ép. Chỉ cần xác định CLAUDE.md đã nhắc định cụm cấu trúc là đủ:
- Mỗi kỹ năng đi theo thư mục cấu hình: `.claude/skills/skill-name/SKILL.md`
- Kỹ năng sẽ được thành hình dần dần nhờ kết hợp lưu lượng làm việc hữu hình.
- Viết kèm một lời đề nghị trong Phần 6 (những điều họ muốn nhờ giúp đỡ) coi như dạng Khát Vọng Thúc Đẩy / Backlog cần được định lượng bằng hình thức trong CLAUDE.md hoặc là ra ở 1 khu vực file tách rời.

## Bước Thực Hiện Cuối (Final Step)

Sau khi hoàn tất quá trình cài đặt mọi thứ:
1. Hiện ra trước mắt tôi sơ đồ cấu trúc dạng Cây (Tree View) đối với File / Thư mục bạn được phép triển khai.
2. Trình bày nội dung một dòng tóm lược những thành phẩm đang nằm trong đó với mọi tệp chạy.
3. Xuất hiện mục định lượng dự án chờ List Backlog (Bảng liệt kê Kỹ năng cần xây) được tạo dựng theo như Phần 6 người dùng đưa ra. -- Qua thời gian các "Luồng quy trình này" đã và sẽ phát triển thành Skill cho bạn xử lý.
4. Trình bày mẫu tổng quát bảng thông số tóm tắt để việc bảo trì duy trì dễ dàng:

**Duy Trì Sự Nhanh Nhạy Cho Trợ Lý Của Bạn (Keeping Your Assistant Sharp)**
- **Hàng Tuần (Weekly):** Không yêu cầu hành động. Tự AI Auto-Memory sẽ gánh vác phần đó.
- **Hàng Tháng (Monthly):** Dạo qua `context/current-priorities.md`. Nếu mục tiêu có sự di dời thì update nội hàm tại nơi đó.
- **Hàng Quý (Quarterly):** Bổ sung, cập nhật `context/goals.md` bằng các biến số Milestone và Target lớn.
- **Khi Phát Sinh (As needed):** Triển khai quyết định lên Logging từ file `decisions/log.md`. Bổ sung tham khảo tài liệu thư viện mới. Kiến tạo kỹ năng.
- **Pro Tip:** Nếu cần hệ thống nắm bắt thông tin chuyên sâu vĩnh viễn (Permanent). Hãy lưu lại dòng nội dung truyền tải với AI rành rọt: "Ghi nhớ là anh có thói quen thiên vị ưu tiên dùng X". Trí thông minh gốc sẽ tự lưu truyền câu thoại theo từng phiên sau này.

5. Triển khai nhánh Git Commit phiên bản thứ nhất cho tổng thể bộ tệp tin bên trên.
6. Xin một yêu cầu về việc muốn triển khai/tạo đà bổ sung một kỹ năng bất định nào ngay không?

## Quy Tắc Bắt Buộc Dành Cho Bạn (Claude)

- **TUYỆT ĐỐI KHÔNG TỰ TẠO RA BẤT KỲ KỸ NĂNG NÀO ở thời điểm này**. Hãy để trống mục Thư mục Kỹ Năng của bạn sau khi hoàn thành quy trình ban đầu.
- Giữ vững cho CLAUDE.md nằm yên DƯỚI Ranh Giới 150 DÒNG. Vượt quá thì chỉ biểu thị việc bạn đang quá tham vọng.
- Vận dụng biểu đồ nhập hệ thống `@` (vd: `@context/me.md`) vào trong nội tại CLAUDE.md song song với việc cắt gọn lại nội dung văn phong.
- Một file quy tắc đại diện cho một CHỦ ĐỀ DUY NHẤT. Giới hạn khoảng 3-4 file Rule để dẫn độ bộ máy.
- LUÔN LUÔN HỎI TỪNG PHẦN. Xong 1 phần hỏi/Hãy chờ đáp trả từ phía tài khoản của tôi xong MỚI qua mục kế theo sau câu đó.
- Nếu như tiếp nhận thấy câu "Bỏ Qua / Skip" / "Bổ sung sau đó đi" tới từ tôi, Hãy khởi tạo thư mục trống để dành cho bước tới tiếp sau.
