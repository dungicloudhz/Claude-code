# Google Antigravity
## Kiến trúc & Developer Guide
- **Google Antigravity**: là agentic IDE thế hệ mới ra mắt tháng 11/2025 - fork từ VS Code nhưng được tái thiết kế hoàn toàn theo triết lý **agent-first**. Không còn AI là plugin phụ trợ - agent là thực thể trung tâm tự lên kế hoạch, thực thi code, kiểm thử trên browser và tạo Artifacts để bạn verify. Download: `antigravity.google/download`· CLI: `agy`

# ĐỊNH VỊ
- **Điểm mạnh cốt lõi**: **Async multi-agent** là diffenentiator lớn nhất. Thay vì chời AI xong rồi mới làm tiếp, bạn dispatch 5 agent cho 5 bugs đồng thời. Developer chuyển từ *người viết code sang* **kiến trúc sư quản lý agents**.

# KIẾN TRÚC TỔNG THỂ
**AntiGravity được tổ chức thành 5 component core phối hợp với nhau - tất cả hướng đến một mục tiêu: developer làm việc ở task level, không phỉa line level.**
- **L5** - **Customization Layer**: Rules, Workflows, Skills, GEMINI.md - developer định nghĩa behavior của agent `GEMINI.md` `.agent/rules/` `.agent/workflows/` `skills/`
- **L4** - **Agent Manager**: Mission Control - dispatch tasks, monitor agents chạy song song, review & approve Artifacts `Async tasks` `Multi-agent` `Artifacts`
- **L3** - **Editor View**: AI-powered IDE quen thuộc thuộc kế thừa VS Code - tab completion, inline commands, direct file editing
- **L2** - **Browser Subagent**: Specialized model điều khiển Chrome thực - click, scroll, type, chụp screenshot, đọc DOM, tạo recordings `Chrome Extension` `DOM capture` `Walkthrough recording`
- **L1** - **Tool Layer**: Terminal, filesystem, MCP services (Github, DB, Jira...) - agent gọi để thực thi task `Terminal` `File I/O` `MCP`

# LUỒNG THỰC THI MỘT TASK
![](./flow_antigravity.png)

# Agent Manager - Mission Control
**Đây là điểm khác biệt lớn nhất của AntiGravity. Thay vì chatbox tuyến tính phải chờ, Agent Manager là dashboard quản lý nhiều agents song song bất đồng bộ.**

- **Dispatch không cần chờ**: Gửi task mới ngay khi agent khác đang chạy. Mỗi task có agent instance riêng xử lý độc lập - 5 bugs xử lý đồng thời.
- **Real-time status**: Xem progress, artifacts (hiện vật) đã tạo, pending approvals (đang chờ phê duyệt), và reasoning (suy luận) của từng của từng agent - tất cả trên cùng dashboard.
- **Artifacts=bằng chứng**: Agent không chỉ nói "đã fix" - nó tạo Task List, Implementation Plan, Code Diff, Walkthrough recording để vạn verify trước khi approve.
- **Workspaces & Playground**: `Workspace`: project folder với file control. `Playground`: scratch area không có workspace, prototype nhanh

## Cách dùng Agent Manager hiệu quả
- Dùng **@filename** và **@foldername** để inject file context vào task ngay trong input.
- **Planning mode** cho complex tasks (tổ hợp) - agent tạo Task List + Implementation Plan artifact (Tạo tác kế hoạch thực hiện) trước khi viết bất kỳ dòng code nào.
- **Fast mode** cho task đơn giản như rename, format, thêm log line - không tạo plan, execute ngay
- **Review artifact kỹ Implementation Plan** - đây là lúc dễ phát hiện sai hướng nhất, trước khi agent dùng nhiều compute.
- Comment trực tiếp vào artifact để agent điều chỉnh - không cần bắt đầu lại từ đầu.

# Editor View
**Editor View là môi trường coding quen thuộc - kế thừa VS Code nhưng AI được tích hợp sâu hơn bất kỳ plugin nào.**

- **Tab Completions**: Autocomplete inline context-aware từ toàn bộ workspace, hiểu codebase của bạn khoogn chỉ file đang mở.
- **Inline Commands**: Select code → `Cmd+K` để ra lệnh trực tiếp. Agent sửa tại chỗ, không cần sang chat.
- **Bidirectional**: Bạn edit file, agent thấy ngay. Agent tạo file mới, bạn thấy ngay Explorer. Real-time hai chiều.

**EDITOR VS AGENT MANAGER**
- **Agent Manager**: task phức tạp, multi-file, cần plan, cần verify qua browser. **Editor View**: đang hands-on code, cần gợi ý inline nhanh, hoặc chỉnh trực tiếp file agent vừa tạo.

# Browser Subagent
**Tính năng độc đáo nhất của Antigravity - agent thực sự điều khiển Chrome để test và verify, không phải giả lập**.
- **Full browser control**: Click, fill forms, scroll, navigate - agent dùng Chrome thật qua Chrome Extension. Không phải headless, không phải mô phỏng.
- **Đọc và hiểu trang**: DOM capture, screenshot, markdown parsing - agent "đọc" được trang web, hiểu content và layout thực tế.
- **Automated E2E testing**: Agent tự deploy app, mở browser, test từng flow, chụp screenshot - tất cả tự động. Bạn review Walkthrough artifact sau.
- **Walkthrough recording**: Agent tạo recording của toàn bộ test session - Walkthrough artifact giúp bạn verify kết quả trực quan như xem video demo.

**JAVASCRIPT EXECUTION POLICY**
- Browser subagent có thể chạy JS trong trang. **Request Review** (recommend) - hỏi trước khi chạy JS. **Always Proceed** - tự động nhưng có risk Prompt Injection từ malicious webpage content. **Disabled** - an toàn nhất nhưng hạn chế automation.

# Artifacts - Hệ thống "Bằng chứng"
Artifacts giải quyết **Trust Gap** - thay vì tin lời agent nói "đã fix", bạn có bằng chứng cụ thể để verify. Đây là UX innovation quan trọng nhất của Antigravity.

|Artifact type|Khi nào xuất hiện|nội dung|Bạn cần làm gì|
|-|-|-|-|
|Task List|Ngay khi nhận task (Planning mode)|Danh sách subtasks có structre và dependency|Review, comment để điều chỉnh direction|
|Implementation Plan `quan trọng`|Sub TaskList, trước khi code|Technical details: files cần tạo/sửa, approach, tradeoffs|**Review kỹ nhất đây** - dễ phát hiện sai hướng|
|Code Diff|Sau khi agent viết/sửa code|Diff của tất cả files thay đổi|Review changes, approve hoặc request chỉnh sửa|
|Screenshot|Sau khi browser test|Ảnh chụp UI trước/sau, kết quả test visually|Verify UI troogn đúng như mong đợi|
|Walkthough `video-like`|Sau E2E testing|Recording của agent test flow trên browser thật|Xem như video demo - verify full flow|
|Architecture Diagram|Khi planning complex system|Sơ đồ kiến trúc, data flow, component diagram|Review và comment ngay trên diagram|

**PRO TIP**
- Đừng skip artifact reivew để **code nhanh hơn**. Thời gian review Implementation Plan là thời gian tiết kiệm được từ việc undo sau hướng sau đó. Reigorous interrogation ở Artifact stage = **chất lượng ouput cao hơn**.

# Rules - System Instructions luôn chạy ngầm
Rule là **hướng dẫn bất biến** luôn ative - agent đọc và tuân theo mọi lúc, không cần bạn nhắc lại. Khác với workflows (cần trigger), Rules hoạt động như một syste prompt tự động.

- **Global Rules**: áp dụng cho mọi project. Triết lý codeing các nhân hoặc tổ chức. Vị trí: `~/.gemini/GEMINI.md`
- **Workspace Rule**: Chỉ cho project hiện tại, override Global Rules. Ví dụ `.agent/rules/` trong project root

Ví dụ: `~/.gemini/GEMINI.md`
```markdown
# GEMINI.md — Global Rules (áp dụng mọi project)

## Language
- Luôn dùng TypeScript thay vì JavaScript
- Python: type hints bắt buộc (Python 3.10+)
- Không bao giờ hardcode API keys hoặc secrets

## Code Quality
- Mọi public method phải có JSDoc/docstring
- Error handling: không silent catch — luôn log hoặc rethrow
- Tên biến: descriptive, không viết tắt trừ khi rất phổ biến

## Testing
- Viết test cùng lúc với code, không phải sau
- Test file đặt cạnh source: foo.ts → foo.test.ts

## Security
- Không generate hardcoded credentials dù là example
- SQL: luôn parameterized queries, không string concat
```

Ví dụ: `.agent/rules/backend.md`
```markdown
# Backend Rules — Payment Service

## Stack (bắt buộc dùng)
- Runtime: Node.js 22 + TypeScript strict mode
- Framework: Fastify 5 (không dùng Express)
- DB: PostgreSQL với Prisma ORM
- Validation: Zod cho tất cả external input

## Architecture
# Layered architecture — không mix concerns:
- routes/       ← HTTP layer (thin, chỉ parse/validate)
- services/     ← Business logic
- repositories/ ← Database access
- domain/       ← Types, interfaces, value objects

## API Design
- Response format: { data, error, meta: { requestId, timestamp } }
- Versioning: /api/v1/ prefix
- Error: AppError class với code + statusCode + message

## Không được làm
- Không dùng any type
- Không trả về stack trace ra client trong production
- Không query không có WHERE clause và LIMIT
```

**RULE VS WORKFLOWS**
- **Rules** = passive, luôn active ngầm, giống system prompt. **Workflows** = active, chỉ chạy khi bạn trigger bằng `/slash` command. Rules là *luật*, Workflow là *quy trình có thể gọi lại*.

# Workflows - Quy trình tigger-on-demand (trigger theo yêu cầu)
Workflow là **saved prompts** được trigger bằng `/slash` command. Định nghĩa một lần, dùng mãi. Chain workflows lồng nhau để tạo pipeline phức tạp.

- **Global Workflows**: Dùng được mọi project. Vị trí: `~/.gemini/antigravity/global_workflows/`
- **Workspace Workflows**: Chỉ trong project hiện tại. Vị trí `.agent/workflows` trong project root

Ví dụ: `.agent/workflows/code-review.md`
```markdown
# Workflow: review
# Trigger: /review
# Mô tả: Code review toàn diện cho staged changes

1. Chạy `git diff --staged` để lấy danh sách thay đổi
2. Đọc từng file thay đổi
3. Phân tích theo thứ tự ưu tiên:
   - 🔴 Security issues (injection, auth bypass, data leak)
   - 🔴 Correctness bugs (logic errors, null deref, off-by-one)
   - 🟡 Performance issues (N+1 queries, missing index, O(n²))
   - 🟡 Style violations (theo .agent/rules/)
   - 🟢 Suggestions (refactoring, naming, docs)
4. Tạo Review Report artifact với format:
   ## Summary
   [X critical · Y warnings · Z suggestions]
   ## Issues
   [file:line | severity | giải thích | code fix]
5. Nếu có critical issue: propose code fix ngay
```

Ví dụ: `.agent/workflows/ship-feature.md` (Chain workflow)
```markdown
# Workflow: ship-feature
# Trigger: /ship-feature
# Mô tả: Pipeline hoàn chỉnh từ code đến PR

1. Chạy workflow /generate-tests
   → Tạo unit tests cho code vừa viết
2. Chạy: `npm test` hoặc `npx vitest run`
   → Phải PASS trước khi tiếp tục
3. Chạy workflow /review
   → Code review, không có critical issue mới proceed
4. Chạy: `git add -A && git commit -m "[auto] ..."` 
5. Tạo GitHub PR qua MCP với:
   - Title: mô tả ngắn gọn feature
   - Body: link to Task List artifact + summary of changes
   - Reviewers: lấy từ CODEOWNERS nếu có
```

# Skills - Progressive Disclosure Knowledge
Skills giải quyết "tool bloat" - thay vì load hết context vào agent (chậm, tốn token, confuse), Skills chỉ được load **khi request khớp với skills description**.

**NGUYÊN LÝ PROGRESSIVE DISCLOSURE**
Agent biết general programming từ Gemini 3. Skill cung cấp **specialized knowledge (kiến thức chuyên ngành) của team bạn**: conversations, business rules, domain expertise. Khi user hỏi "run database migration", agent scan available skills, tìm `db-migration` skill, load vào context và thực thi đúng theo team standard.

- **Global Skills**: Dùng mọi nơi. `~/.gemini/antigravity/skills/`
- **Workspace Skills**: Riêng cho project. `.agent/skills/{name}/SKILL.md`

**Cấu trúc một Skill**
```json
// .agent/skills/db-migration/
├── SKILL.md ← mô tả + instructions (agent đọc khi match)
├── examples.md ← ví dụ migration thực tế trong project
└── scripts/ ← helper scripts agent có thể gọi
 └── run-migration.sh
```
```markdown
---
name: database-migration
description: >  
  LOAD khi user nhắc đến: "migration", "migrate database",
  "schema change", "Prisma migrate", "alter table", "add column".
---

# Skill: Database Migration

## Project DB Stack
- ORM: Prisma với PostgreSQL 16
- Migration files: prisma/migrations/
- Staging DB: postgresql://staging.internal/appdb

## Quy trình bắt buộc
1. Cập nhật prisma/schema.prisma
2. Run: `npx prisma migrate dev --name {snake_case_name}`
3. Review generated SQL trong prisma/migrations/
4. Test staging: `DATABASE_URL=$STAGING npx prisma migrate deploy`
5. Chỉ production sau khi staging OK

## Naming convention
snake_case, mô tả rõ ràng:
- add_user_email_index ✅
- migration_1 ❌

## Không được làm
- Không edit migration file sau khi đã commit
- Không xóa migration files cũ
- Không dùng raw SQL, luôn qua Prisma client
```

# MCP Tools - Kết nối services bên ngoài
Antigravity hỗ trợ **Model Context Protocol (MCP)** - cho phép agent gọi tools kết nối với databases, Github, Jira, và bất kỳ service nào có MCP server.
- **Github MCP**: Đọc issues, tạo PR, xem diff, Agent tự tạo PR sau khi xong feature.
- **Database MCP**: Query DB trực tiếp để lấy schema, database thực tế. Agent không cần bạn paste.
- **Jira/Linear MCP**: Đọc tickets. "Fix bug từ PROJ-123" - agent đọc description tự động.

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}" }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres",
               "postgresql://localhost/mydb"]
    }
  }
}
```

# Cấu trúc thư mục chuẩn
Đây là cấu trúc **chính xác theo Antigravity spec** - dựa trên official docs và Google Cloude Solutions examples.
```markdown
├── .agent/ ← AntiGravity workspace config (official spec)
│ ├── rules/ ← Workspace rules (luôn active)
│ │ ├── coding-standards.md ← style, naming
│ │ ├── architecture.md ← layer rules, patterns
│ │ └── security.md ← security constraints
│ ├── workflows/ ← Saved prompts, /slash triggers
│ │ ├── generate-tests.md ← /generate-tests
│ │ ├── code-review.md ← /review
│ │ ├── idea-to-app.md ← /idea-to-app
│ │ └── ship-feature.md ← /ship-feature (chained)
│ ├── skills/ ← Progressive disclosure packages
│ │ ├── db-migration/
│ │ │ └── SKILL.md
│ │ └── api-design/
│ │ └── SKILL.md
│ └── mcp.json ← MCP server configs
├── src/ ← Source code
└── README.md
─── LOBAL (HOME directory) ───
~/.gemini/
├── GEMINI.md ← Global Rules (mọi project)
└── antigravity/
 ├── global_workflows/ ← Global Workflows
 ├── skills/ ← Global Skills
 └── browserAllowlist.txt ← Browser security domains
```

**ĐẶT TÊN QUAN TRỌNG**
Antigravity đọc `.agent/rules/` và `.agent/workflows/` - không phải `.cursorrules` hay `CLAUDE.md`. Global context `~/.gemini/GEMINI.md`.

# Task Sandbox - Thử ngay
Nhập task bất kỳ hoặc chọn scenario mẫu để xem response của Antigravity agent với typewriter animation.