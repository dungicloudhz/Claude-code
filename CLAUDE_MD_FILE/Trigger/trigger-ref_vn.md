# Tài Liệu Tham Khảo API Trigger.dev

Ví dụ code hoàn chỉnh cho bộ SDK Trigger.dev v4. Tất cả các task đều sử dụng thư viện `@trigger.dev/sdk`.

---

## Task Cơ Bản (Basic Task)

```ts
import { task } from "@trigger.dev/sdk";

export const processData = task({
  id: "process-data",
  retry: {
    maxAttempts: 3, // Thử lại tối đa 3 lần
    factor: 2, // Hệ số nhân thời gian thử lại
    minTimeoutInMs: 5000, // Chờ ít nhất 5 giây
    maxTimeoutInMs: 30_000,  // Chờ tối đa 30 giây
  },
  run: async (payload: { userId: string; data: any[] }) => {
    console.log(`Đang xử lý ${payload.data.length} mục cho người dùng có ID ${payload.userId}`);
    return { processed: payload.data.length }; // Kết quả trả về
  },
});
```

---

## Task Đặt Lịch (Scheduled Task - Cron)

```ts
import { schedules } from "@trigger.dev/sdk";

export const dailyReport = schedules.task({
  id: "daily-report",
  cron: "0 9 * * *", // 9 giờ sáng giờ UTC mỗi ngày

  run: async () => {
    console.log("Đang chạy báo cáo hàng ngày");
    // logic của task đặt ở đây
    return { status: "done" };
  },
});
```

Các biểu thức cron phổ biến:
- `"*/30 * * * *"` — mỗi 30 phút
- `"0 * * * *"` — mỗi giờ
- `"0 */8 * * *"` — mỗi 8 giờ
- `"0 9 * * *"` — 9 giờ sáng mỗi ngày
- `"0 8 * * 1"` — 8 giờ sáng mỗi Thứ Hai

---

## Task Lọc Lược Đồ Dữ Liệu (Schema Task - dùng Zod validation)

```ts
import { schemaTask } from "@trigger.dev/sdk";
import { z } from "zod";

export const validatedTask = schemaTask({
  id: "validated-task",
  schema: z.object({
    name: z.string(), // Yêu cầu tên là chuỗi
    videoId: z.string(),
    publishedAt: z.string(),
  }),
  run: async (payload) => {
    // payload đã được xác thực bằng Zod schema trước khi hàm run() được gọi và đã ép kiểu chuẩn Typescript
    return { message: `Đang xử lý ${payload.name}` };
  },
});
```

---

## Kích Hành Trigger Các Task Từ Phía Mã Backend (Backend Code)

```ts
import { tasks } from "@trigger.dev/sdk";
import type { processData } from "./trigger/tasks.js"; // Import đúng loại

// Kích hoạt một lần (Single trigger) — Cứ nã đạn và kệ nó (fire and forget)
const handle = await tasks.trigger<typeof processData>("process-data", {
  userId: "123",
  data: [{ id: 1 }, { id: 2 }],
});

// Kích hoạt nhiều lần (Batch trigger) — Hỗ trợ giới hạn 1,000 mục, 3MB mỗi payload
const batchHandle = await tasks.batchTrigger<typeof processData>("process-data", [
  { payload: { userId: "123", data: [{ id: 1 }] } },
  { payload: { userId: "456", data: [{ id: 2 }] } },
]);
```

---

## Kích Chuỗi (Triggering) Từ Một Task Bên Trong Xuyên Suốt Tới Task Khác

```ts
export const parentTask = task({
  id: "parent-task",
  run: async (payload) => {

    // Kích Hoạt Không Đợi Kết Quả  (Fire and forget) 
    await childTask.trigger({ data: "value" });

    // Kích Hoạt Sóng Lệnh Bật Về Chờ  (Trigger and wait) — Nó tự xuất hàm obj Trả Result không nên dùng rỗng thay cho Output
    const result = await childTask.triggerAndWait({ data: "value" });
    if (result.ok) {
      console.log("Output Thành Phẩm :", result.output); // Đây là file log giá trị từ bên childTask.run()
    } else {
      console.error("Cảnh Cáo Failed:", result.error);
    }

    // Nếu Mở Unwrap  — nếu fail không trả result lỗi ra catch ngoài mà sập tại dòng này 
    const output = await childTask.triggerAndWait({ data: "value" }).unwrap();

    // Dùng Khi  Cho Hàm Mở Đợi Batch Trigger Mới Trả Data  — Dùng lệnh Chờ
    const results = await childTask.batchTriggerAndWait([
      { payload: { data: "item1" } },
      { payload: { data: "item2" } },
    ]);
    for (const run of results) {
      if (run.ok) console.log("Success Thành Công :", run.output);
    }
  },
});

export const childTask = task({
  id: "child-task",
  run: async (payload: { data: string }) => {
    return { processed: payload.data }; // Tính Kết Quả Task child
  },
});
```

> **Tuyệt đối Không bao giờ** bọc  `triggerAndWait`, `batchTriggerAndWait`, hoặc các hàm `wait.*` ở bên trong lệnh JavaScript `Promise.all` —
> Vì nó sẽ chạy thành kiểu lệnh không hỗ trợ làm đơ hoặc xảy ra ngoài mong muốn cấu trúc Server .

---

## Khóa Trùng Lặp Idempotency Keys (Cách chống trùng lặp báo chạy 2 phát trên 1 phiên dữ liệu )

```ts
await processVideo.trigger(
  { videoId: "abc123", title: "My Video" },
  {
    idempotencyKey: `video-abc123`, // nếu vẫn là key gồng giống nhâu = nó không báo chạy 
  }
);
```

Dùng loại  idempotency keys cho lúc bất kỳ thời gian rủi ro trùng lặp do 2 lệnh chạy gần giờ cùng trúng 1 data items làm kích  — ví dụ, khi có
1 cái báo lịch trình định lượng dò  feed , vô tình do mạng hay cái gì đó đẩy cho trúng 2 khung giờ liền tù tì .

---

## Tính Chất Chờ Đợi Không Giữ Biến Máy Tính(Waits Không Mất Phí Server)

```ts
import { task, wait } from "@trigger.dev/sdk";

export const taskWithWaits = task({
  id: "task-with-waits",
  run: async (payload) => {

    // Chờ Chút Khoảng Phút/Giây Đợi Thời Lượng Giao Tính (Wait for a duration)
    await wait.for({ seconds: 30 }); // 30 sec
    await wait.for({ minutes: 5 });  // 5 p 
    await wait.for({ hours: 1 });    // 1 g

    // Đợi Cho Dùng Đúng Qua Tới Ngày Lịch Chỉ Xét
    await wait.until({ date: new Date("2026-06-01") });

    // Đợi Lấy Sự Duyệt Thông Qua Human Duyệt  (ví đụ có ngừời click cho cho qua bước kế Token Mở )
    await wait.forToken({
      token: "approval-token",
      timeoutInSeconds: 3600,
    });

    return { status: "completed" }; // Task chờ song
  },
});
```

>  Với Thời Giờ chờ Lệnh Mà Chờ Trên `hơn 5 giây` thì được nền tảng báo điểm check tự lưu State — Lúc chờ sẽ không tốn máy chủ compute Server do thời điểm này đang Ngủ Wait

---

## Kích Cọp Bị Nghẽn Do Dồn Gãy Từ Debounced (Debounced Triggering)

Khi hệ bị gồng lúc ấn nhả chuột hay có payload bắn dữ liệu bão tới thì cần gom thành 1 phát thi hành thôi : 

```ts
// Nếu Khi Khóa Dùng Lệnh Hệ Mở Gọi Gộp Số Data Rác Này (Leading mode) thì mặc Cấu hình Định Hệ Nó:
// Lên cái Dòng Truy Cập Chạy FIRST Lần Đầu Tiên Và Khoảng Các Khung Bão Tiếp Theo Nghép Nó Sẽ Kéo Khung Delay Dẹp Cần Quét  Mã Thư
await myTask.trigger(
  { userId: "123" },
  {
    debounce: {
      key: "user-123-update",
      delay: "5s", // Giao 5 Khung  Giây Chờ 
    },
  }
);

// Trái Nghĩa Kể Lên: Mở Lên Gọi Khung Chặn Xóa Hết Đi Giữ Chỉ LAST Payload Lúc Lệnh Thấy Khoảng Nghỉ Delay Hoàn Cuộc Hủy Nó 
await myTask.trigger(
  { data: "latest-value" },
  {
    debounce: {
      key: "trailing-example",
      delay: "10s",
      mode: "trailing", // Lệnh Cuối Khung 
    },
  }
);
```

---

## Cấu Hình Trở Chạy Phục Hồi Nếu Đang Die Thì Thử Chạy  (Retry Configuration)

```ts
export const resilientTask = task({
  id: "resilient-task",
  retry: {
    maxAttempts: 10,   // Nó Cú Đá Tận 10 Lần Mới Thôi Không Check Try Thêm Báo Die 
    factor: 1.8,       // Thời Khoảng Chờ Sẽ Xuyên Hóa Bằng Cấp Nhân Sau  Mỗi Vòng Try Backoff Multiplier Nhân
    minTimeoutInMs: 500, // Thấp Cuối Nghỉ Đợi
    maxTimeoutInMs: 30_000, 
    randomize: false,  // Chơi Chuẩn Không Lỗi Giờ Khỉ Lỗi Random Khác
  },
  run: async (payload) => {
    // Trong Này Thấy Khóa Catch Khi Dựng Code Văng Sập API Nó Sẽ Tự Cút Try Nó Thử Mà Bạn Chả Cần Phải Đụng Tay 
    return { done: true };
  },
});
```

---

## Mô Hình Kiến Trúc Chuẩn Nhạc Trưởng Kèm Xử L‎ý Vi Phân Tính (Orchestrator + Processor Pattern)

Đây Là Lệnh Code Quy Tắc Chân Ai Cơ Bản Lúc Nào Cần Sử Khi Bật Cron Báo Gõ List Lọc  New Khác Data Từ User

```ts
// check-task.ts —  Task Rất Nhẹ Dùng File Chỉ Cron Rình Lệnh Khi Khuyên Đi Vòng Tuần Chạy Rất Là Nhẹ 
import { schedules } from "@trigger.dev/sdk";
import { processItem } from "./process-item.js"; // note: .js cần gọi khi mở code  extension bắt buộc 

export const checkTask = schedules.task({
  id: "check-task",
  cron: "0 */8 * * *", //  Cách Vào Sau  khoảng 8 tiếng rinh feed  1 lần 

  run: async () => {
    const items = await fetchNewItems(); // Khai Nguồn Data List Mở Poll Lộ Cho 
    for (const item of items) {
      await processItem.trigger(
        { id: item.id, data: item }, // Nổ Súng Process Mọi Bấm Quệt Đã Dò Item  Bóng Từng Kẻ  Trên Cho Gắn Data 
        { idempotencyKey: `item-${item.id}` } // Có Mã Check Ngăn Chặn Giết Lệnh Trùng Item  Trừ Chết System Bắn Nhầm 
      );
    }
    return { dispatched: items.length }; // Xem Dấu Vết  Item Lệnh 
  },
});

// process-item.ts — Khung Lính Đánh Công File Riêng Chứa Nhiệm Chém Đồ Nặng  Tác Cho Từng Data 
import { task } from "@trigger.dev/sdk";

export const processItem = task({
  id: "process-item",
  run: async (payload: { id: string; data: any }) => {
    // Các lệnh tốn RAM Gặp Tốn Core như Gõ Đặt Hàm Ghi Data , Hỏi Lời LLm Gọi Chat , Gọi Dấu API , Ném Output
    return { processed: payload.id }; // Chào Qua Code Mở Khóa Đích Process 
  },
});
```

---

## TUYỆT ĐỐI CẤM SỬ DỤNG  (v2 — Vi Lệnh Đánh Sập Code Lên System V2 breaks everything)

```ts
// ❌ Cấm Mã Lên File Của V2 Cú Pháp Khủng  — KÔNG SỬ DỤNG
client.defineJob({
  id: "job-id",
  run: async (payload, io) => { /* ... */ },
});
```

Chỉ Có Thể Gõ Luôn Từ Đầu Chuẩn API Lệnh `task()`, hay `schedules.task()`, hay `schemaTask()` Báo Khởi Đầu Lệnh `@trigger.dev/sdk`.
