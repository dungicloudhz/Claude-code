# CLAUDE.md - Quy tắc Website Frontend

# Luôn Làm Đầu Tiên
- **Gọi kỹ năng `frontend-design`** trước khi viết bất kỳ mã frontend nào, trong mọi phiên làm việc, không ngoại lệ.

# Hình Ảnh Tham Khảo
- Nếu có hình ảnh thao khảo: phải khớp chính xác về bố cục, khoảng cách, kiểu chữ và màu sắc. Sử dụng nội dung thay thế tạm thời (hình ảnh qua `https://placehold.co/`, văn bản chung chung). Không cải thiện hay thêm thắt vào thiết kế.
- Nếu không có hình ảnh tham khảo: thiết kế từ đầu với độ hoàn thiện cao (xem các quý tắc bảo vệ bên dưới).
- Chụp ảnh màn hình kết quả của bạn, so sánh với bản tham khảo, sửa các điểm không khơp, chụp lại ảnh màn hình. Thực hiện ít nhất 2 vòng so sánh. Chỉ dừng lại khi không còn khác biệt nào có thể nhìn thấy hoặc khi người dùng yêu cầu dừng.

# Máy Chủ Cục Bộ (Local Server)
- **Luôn phục vụ (serve) trên localhost** - không bao giờ chụp ảnh màn hình từ URL `file:///`.
- Khởi động máy chủ phát triển (dev server): `node serve.mjs`(phục vụ thư mục gốc của dự án `http://localhost:3000`)
- `server.mjs` nằm ở thư mục gốc của dự án. Khởi động nó trong nền trước khi chụp bất kỳ ảnh màn hình nào.
- Nếu server đang chạy rồi, không khởi động thêm một phiên bản (instance) thứ 2.

# Quy Trình Chụp Ảnh Màn Hình
- Puppeteer được cài đặt `C:/Users/nateh/AppData/Local/Temp/puppeteer-test/`. Bộ nhớ cache chủa Chrome tại `C:/Users/nateh/.cache/puppeteer/`.
- **Luôn chụp ảnh màn hình từ localhost**: `node screenshot.mjs http://localhost:3000`. Ảnh màn hình được lưu tự động vào `./temporary screenshots/screenshot-N.png` (số tự động tăng, không bao giờ ghi đè).
- Tùy chọn hậu tố nhãn. `node screenshot.mjs http://localhost:3000 label` → lưu thành `screenshot-N-label.png`
- `screenshot.mjs` nằm ở thư mục gốc của dự án. Sử dụng nó như nguyên bản.
- Sau khi chụp, đọc file PNG `temporary screenshots/` bằng công cụ Read - Claude có thể nhì và phân tích ảnh trực tiếp.
- Khi so sánh, hãy cụ thể: "tiêu đề 32px nhưng bản tham khảo là ~24px", "khoảng cách thẻ là 16px nhưng đáng lẽ phải 24px".
- Kiểm tra: spacing/padding (khoảng cách), front size/weight/line-height (kích thước, đậm, chiều cao dòng), colors (mã hex chính xác), alignment (căn lề), border-radius (bo bo góc), shadows (đổ bóng), image sizing (kích thước ảnh).

# Mặc Định Đầu Ra
- Một file `index.html` duy nhất, tất cả style viết inline, trừ khi người dùng yêu cầu khá.
- Tailwind CSS qua CDN: `<script src="https://cdn.tailwindcss.com"></script>`.
- Hình ảnh tạm thời (Placeholder): `https://placehold.co/WIDTHxHEIGHT`.
- Responsive ưu tiên thiết bị di động (Mobile-first).

# Tài Nguyên Thương Hiệu
- Luôn kiểm tra thư mục `brand_assets/` trước khi thiết kế. Nó có thể chứa logo, hướng dẫn màu sắc, tài liệu hướng dẫn phong cách (style guide), hoặc hình ảnh.
- Nếu có tài nguyên ở đó, hãy sử dụng chúng. Không dùng placeholder khi có sẵn tài nguyên thật.
- Nếu có logo, hãy sử dụng nó. Nếu có bảng màu được định nghĩa, hãy dùng chính xác các giá trị đó — không tự bịa ra màu thương hiệu.

# Các nguyên tắc thiết kế chung
- **Màu sắc**: Không bao giờ dùng bảng màu mặc định của Tailwind (indigo-500, blue-600, v.v.). Chọn một màu thương hiệu tùy chỉnh và phát triển từ nó.
- **Đổ bóng**: Không bao giờ dùng `shadow-md` phẳng. Dùng đổ bóng xếp lớp, nhuốm màu (color-tinted) với độ mờ (opacity) thấp.
- **Kiểu chữ**: Không bao giờ dùng cùng một font cho cả tiêu đề và văn bản (body). Kết hợp một font display/serif với một font sans sạch sẽ. Áp dụng tracking hẹp (`-0.03em`) cho các tiêu đề lớn, line-height lớn (`1.7`) cho văn bản.
- **Gradient**: Xếp lớp nhiều gradient dạng tỏa tròn (radial). Thêm nhiễu/kết cấu (grain/texture) qua bộ lọc noise SVG để tạo chiều sâu.
- **Hiệu ứng động**: Chỉ animate thuộc tính `transform` và `opacity`. Không bao giờ dùng transition-all. Sử dụng easing theo phong cách lò xo (spring-style).
- **Trạng thái tương tác**: Mọi phần tử có thể click đều cần có trạng thái hover, focus-visible, và active. Không có ngoại lệ.
- **Hình ảnh**: Thêm một lớp phủ gradient (`bg-gradient-to-t from-black/60`) và một lớp xử lý màu sắc bằng `mix-blend-multiply`.
- **Khoảng cách**: Sử dụng các biến (tokens) khoảng cách có chủ đích, nhất quán — không dùng các mức ngẫu nhiên của Tailwind.
- **Chiều sâu**: Các bề mặt nên có hệ thống phân lớp (base → elevated → floating), không nằm tất cả trên cùng một mặt phẳng z.

# Các Quy Tắc Cứng
- Không thêm các section, tính năng, hoặc nội dung không có trong văn barn tham khảo.
- Không "cải thiện" một thiết kế tham khảo - phải khớp với nó.
- Không dừng lại sau chỉ một lần chụp ảnh màn hình.
- Không sử dụng `transaction-all`
- Không sử dụng màu blue/indigo mặc định của Tailwind làm màu chính yếu