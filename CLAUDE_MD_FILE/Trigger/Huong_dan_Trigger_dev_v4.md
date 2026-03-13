# Hướng dẫn Xây dựng Automation với Trigger.dev v4

Tài liệu này tổng hợp các nguyên tắc và kiến thức cốt lõi để xây dựng quy trình tự động hóa (automation) với **Trigger.dev** theo chuẩn hệ thống v4 mới nhất (từ phân tích tài liệu gốc của Claude).

## 1. Nguyên Tắc Cốt Lõi (Critical Rules)
- **Ngôn ngữ bắt buộc:** Chỉ sử dụng TypeScript. Không dùng script Python hay Shell. 
- **HTTP Request:** Sử dụng hàm `fetch` mặc định của Node API. Không khuyến khích cài đặt thêm thư viện như `axios` hay `node-fetch`.
- **Phiên bản SDK:** Tuyệt đối sử dụng `@trigger.dev/sdk` (v4). **KHÔNG ĐƯỢC DÙNG** cú pháp rẽ nhánh cũ của v2 (`client.defineJob`), nó sẽ làm hỏng toàn bộ hệ thống.
- **Bảo mật (Đặc biệt quan trọng):**
  - Mọi API keys, tokens, IDs (kể cả ID của Workspace/Channel từ third-party) phải để trong file `.env`.
  - Không bao giờ được hardcode các thông tin nhạy cảm trực tiếp vào code (việc in ra log kiểu `console.log(apiKey)` cũng là vi phạm).
  - Phải kiểm tra (validate) biến môi trường ở đầu task (`if (!apiKey) throw new Error(...)`).
  - Đảm bảo file `.env` đã được khai báo trong `.gitignore`.

## 2. Quy Trình 9 Bước Xây Dựng Automation
Mỗi khi tạo một kiến trúc automation mới, hãy tuân thủ trình tự sau:
1. **Understand (Tiếp nhận):** Lắng nghe và hiểu rõ ý tưởng tự động hóa của bạn. Chưa viết code vội.
2. **Research (Nghiên cứu):** Tìm hiểu các API/services liên quan (đọc docs, kiểm tra pricing, rate limits, tầng miễn phí và cách chứng thực auth).
3. **Clarify (Làm rõ):** Đặt câu hỏi để xác định rõ thông số:
   - *Nguồn (Source):* Lấy dữ liệu từ đâu?
   - *Đích (Output):* Nơi xuất dữ liệu ra (VD: gửi log về Slack, lưu Database)?
   - *Tần suất (Frequency):* Cron job định kỳ, lắng nghe Event sự kiện hay Trigger thủ công tay?
4. **Plan (Lập kế hoạch):** Mô tả kế hoạch chi tiết bằng ngôn ngữ con người để xác nhận đồng thuận trước khi build.
5. **Build (Viết code):** Viết file TypeScript trong thư mục `src/trigger/{automation-name}/`.
6. **Environment Setup (Biến môi trường):** Thêm biến chứa secret vào `.env` (chạy local) và nhập lên Dashboard của Trigger.dev (Staging & Production).
7. **Test Locally (Thử nghiệm local):** Chởi chạy dev server bằng lệnh `npx trigger.dev@latest dev` và trigger một payload mẫu để xem hoạt động.
8. **Deploy (Triển khai):** Thực hiện khi code chạy ổn ở môi trường local hoặc được chủ dự án thông qua lệnh push.
9. **Verify (Xác minh):** Kiểm tra log hệ thống production trên Dashboard Trigger để xem workflow có chạy đúng kết quả mong đợi end-to-end không.

---

## 3. Cấu trúc thư mục (Project Structure)
Mọi file script của trigger sẽ nằm trong thư mục `src/trigger/`. Có 2 cách đặt cấu trúc thường gặp:

1. **Automation Đơn giản:** Gom tất cả vào 1 file duy nhất. 
   - VD: `src/trigger/hello-world.ts`
2. **Automation Phức tạp (Orchestrator + Processor):** 
   Chia làm 2 file riêng biệt:
   - `check-task.ts`: Chạy định kỳ, rất nhẹ chỉ để đi fetch kiểm tra xem "có data nào mới không".
   - `process-task.ts`: Nhận lệnh từ `check-task` (kèm *idempotencyKey* để chống trùng lặp dữ liệu) và thực hiện các logic xử lý nặng tốn tài nguyên (VD: gọi LLM model qua API, biên tập ảnh...).

---

## 4. Các Code Snippets Quan Trọng Thường Dùng

### 4.1. Task Xử Lý Cơ Bản (Basic Task)
```typescript
import { task } from "@trigger.dev/sdk";

export const processData = task({
  id: "process-data",
  retry: {
    maxAttempts: 3, // Tự động chạy lại 3 lần nếu có lỗi trung gian
    factor: 2, 
    minTimeoutInMs: 5000,
    maxTimeoutInMs: 30_000,
  },
  run: async (payload: { userId: string; data: any[] }) => {
    // Mọi logic của bạn ở đây...
    console.log(`Đang chạy dữ liệu cho user ${payload.userId}`);
    return { processed: true };
  },
});
```

### 4.2. Task Đặt Lịch Định Kỳ (Cron Task)
```typescript
import { schedules } from "@trigger.dev/sdk";

export const dailyReport = schedules.task({
  id: "daily-report",
  cron: "0 9 * * *", // 9 giờ sáng mỗi ngày

  run: async () => {
    // Sẽ tự động thi hành vào 9h sáng hàng ngày
    // Trực tiếp query dữ liệu tại đây...
    return { status: "done" };
  },
});
```
*Các cú pháp cron hay dùng:*
- `"*/30 * * * *"`: Mỗi 30 phút.
- `"0 * * * *"`: Mỗi giờ tròn.

### 4.3. Kích Hoạt Task Định Tuyến Tương Tác Giữa Các Task
Khi task A gọi task B. *Lưu ý: trong project TypeScript, khi import đường dẫn đuôi file trong backend thì phải nhớ để `.js` thay vì `.ts`*.
```typescript
import { task } from "@trigger.dev/sdk";
import { childTask } from "./child-task.js"; // Bắt buộc đuôi .js

export const parentTask = task({
  id: "parent-task",
  run: async (payload) => {
    // 1. Gọi ẩn danh và KHÔNG đợi kết quả (Fire and forget)
    await childTask.trigger({ data: "value" });

    // 2. Gọi và CHỜ KẾT QUẢ thi hành xong (Trigger & Wait)
    const result = await childTask.triggerAndWait({ data: "value" });
    if (result.ok) {
        console.log("Dữ liệu đầu ra:", result.output); 
    } else {
        console.error("Task con đã thất bại:", result.error);
    }
  },
});
```

### 4.4. Tính Năng Chờ Không Tốn Phí (Waits)
Với Trigger.dev, bạn có thể gọi wait mà không bị tính phí server cho gian đoạn chờ (hệ thống sẽ lưu snapshot và khôi phục khi tới giờ). Chỉ áp dụng cho chờ **trên 5 giây**.
```typescript
import { task, wait } from "@trigger.dev/sdk";

export const taskWithWaits = task({
  id: "task-with-waits",
  run: async (payload) => {
    // Tạm dừng chạy hàm trong 1 khoảng thời gian
    await wait.for({ minutes: 5 }); // Chờ 5 phút
    await wait.for({ hours: 1 }); // Chờ 1 giờ

    // Tạm dừng cho đến ngày giờ cụ thể
    await wait.until({ date: new Date("2026-06-01") });

    return { status: "completed" };
  },
});
```
