# KIẾN TRÚC DỰ ÁN
## Cấu trúc dự án tối ưu cho Claude
- Hướng dẫn thiết kế cấu trúc thư mục, định nghĩa **Rule, Skills, Tools, Agents, Worflow** và **Memory** - để Claude đọc hiểu codebase và hỗ trợ lập trình hiệu quả nhất.

## TẠI SAO CẦN CẤU TRÚC ĐẶC BIỆT?
- **Claude không "nhớ" codebase của bạn tự động**. Mỗi lần mở conversation mới là một tờ giấy trắng. Cấu trúc tốt = **giúp Claude hiểu ngữ cảnh nhanh**, code đúng conversation, không lặp lỗi cũ, và làm việc như một team member thực sự.

# KHÁI NIỆM
## Các khái niệm cốt lõi
- **Hiểu rõ 7 building block này là nền tẳng để xây dựng workflow AI hiệu quả cho dự án của bạn.**
    - **Rules** - **`QUY TẮC & RÀNG BUỘC`**: `Những điều Claude PHẢI làm hoặc KHÔNG được làm. Coding convenstions, security rules, naming partterns của dự án`.
    - **Skills** - **`NĂNG LỰC CỤ THỂ`**: `Tập hợp kiến thức và kỹ năng cho domain cụ thể. Ví dụ: skill viết JPA query, skill tạo React component theo design system`.
    - **Tools** - **`CÔNG CỤ THỰC THI`**: `Functions Claude có thể gọi để tương tác với hệ thống: đọc file, query DB, gọi API, chạy terminal commands`.
    - **Agents** - **`TÁC NHÂN TỰ ĐỘNG`**: `Claude instance chuyên trách một domain: Backend Agent, Frontend Agent, QA Agent, DevOps Agent. Mỗi agent có context và tools riêng`.
    - **Workflow** - **`QUY TRÌNH TỰ ĐỘNG HÓA`**: `Chuỗi bước tự động được định nghĩa: từ nhận yêu cầu → phân tích → implement(thực hiện) → test → review → deploy`.
    - **Memory** - **`BỘ NHỚ NGỮ CẢNH`**: `Nơi lưu trữ kiến thức quan trọng: decisions (quyết định) đã được đưa ra, bugs đã gặp, patterns đang dùng, tech debt (nợ công nghệ) cần xử lý`.
    - **Context** - **`NGỮ CẢNH DỰ ÁN`**: `CLAUDE.md - file markdown chứa mọi thuông tin Claude cần biết về dự án ngay khi mở conversation mới`.

## Mối quan hệ giữa các khái niệm
![](./relationship_ai.png)

# KIẾN TRÚC
## KIẾN TRÚC HẠ TẦNG
- Một dự án AI-optimized được tổ chức theo 5 tầng rõ ràng, từ hạ tầng đến giao tiếp với Claude.
    - **L5** - **AI Interaction Layer** *(Lớp tương tác AI)*: CLAUDE.md, .currsorrules, workflows, memory files - nơi Claude đọc context `CLAUDE.md` `.cursorrules` `/ai/`
    - **L4** - **Application Layer**: Frontend (React/Next.js) + Backend (Spring Boot) - business logic và UI `frontend/` `backend/` `API contracts`
    - **L3** - **Domain Layer**: Entities, aggregates, domain events, value objects - pure bussiness logic `domain/` `entities/` `events/`
    - **L2** - **Infrastructure Layer** *(Lớp cơ sở hạ tầng)*: Database, cache, message queue, external APIs - adapters và implementation `persistence/` `messaging/` `integration/`
    - **L1** - **DevOps Layer**: Docker, K8s manifests, CI/CD pipelines, IaC (Terraform) - 