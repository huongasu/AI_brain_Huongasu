# WORK-Brain — Sổ Tay Vận Hành Agent

> File này là **nguồn chân lý duy nhất** cho bất kỳ AI Agent nào vận hành trên vault này.
> Đọc file này TRƯỚC KHI thực hiện bất kỳ thay đổi nào.
>
> **Bản tùy biến của Huong** — dựa trên `llm-wiki-template` (Karpathy LLM Wiki Pattern), điều chỉnh cho vault tri thức chuyên môn nhân sự (WORK-Brain), vận hành qua Antigravity.

---

## ⚠️ LỖI NỀN TẢNG ĐÃ BIẾT (Mẹo Tùy Chọn)

> [!tip] Vấn đề với `grep_search` trên các Vault lớn (như OneDrive)
> Ở một số môi trường có hàng ngàn file hoặc đồng bộ cloud, tool `grep_search` có thể bị timeout.
> Nếu gặp lỗi "context canceled", hãy chuyển sang dùng PowerShell `Select-String` hoặc FTS5.

**Workaround — Dùng PowerShell `Select-String`:**
```powershell
# Tìm kiếm text trong toàn bộ wiki (bao gồm sub-folder, không phân biệt hoa thường)
Get-ChildItem -Path ".\wiki" -Filter "*.md" -Recurse | Select-String -Pattern "từ_khóa" -Encoding utf8

# Tìm trong raw/
Get-ChildItem -Path ".\raw" -Filter "*.md" -Recurse | Select-String -Pattern "từ_khóa" -Encoding utf8
```

**Chiến lược tìm kiếm ưu tiên:**
1. **Ưu tiên 1 (MCP):** Gọi tool `search_knowledge(query="từ_khóa")` — nhanh nhất, chính xác nhất qua FTS5.
2. **Ưu tiên 2 (MCP):** Đọc resource `brain://wiki/index` — lấy Master catalog.
3. **Ưu tiên 3 (MCP):** Đọc resource `brain://wiki/{article}` — lấy bài cụ thể.
4. **Fallback 1 (Khi không có MCP):** `view_file` trên `_index.md` → scan aliases/summary → `view_file` bài cụ thể.
5. **Fallback 2 (Khi không có MCP):** `run_command` với PowerShell `Select-String` (như trên).

## MCP Server Integration

WORK-Brain cung cấp MCP Server (`second-brain`) cho các AI Agent kết nối trực tiếp.

### Cấu hình
Xem `integrations/mcp/config-sample.json` và `integrations/mcp/README.md`.

### Tools khả dụng
| Tool | Mô tả | Read/Write |
|------|--------|-----------|
| `search_knowledge` | FTS5 full-text search (sessions, wiki) | Read |
| `rebuild_index` | Rebuild FTS5 index | Write |
| `check_health` | Wiki audit + health score | Read |
| `find_wiki_orphans` | Tìm bài mồ côi, tùy chọn auto-fix | Read/Write |
| `mark_duplicate_raws`| Đánh dấu raw trùng lặp | Read/Write |
| `ingest_codebase` | Trích xuất AST repo | Write |

### Resources
| URI | Mô tả |
|-----|--------|
| `brain://wiki/index` | Master Index |
| `brain://wiki/{article}` | Bài wiki cụ thể theo tên |

Khi Agent có hỗ trợ MCP, **luôn ưu tiên** dùng tools/resources trên thay vì gọi subprocess.

---

## Mục Đích

Vault này là **AI-managed knowledge base** theo phương pháp Karpathy LLM Wiki Pattern, tùy biến cho **tri thức chuyên môn nhân sự** của Huong (dự án Khung Năng Lực tại Vietcombank, cùng kinh nghiệm nhân sự tích lũy qua các nơi từng làm). LLM viết và duy trì toàn bộ nội dung wiki. Huong nạp tài liệu, đặt câu hỏi, và duyệt kết quả — hiếm khi tự sửa trực tiếp.

**Bộ chủ đề khởi điểm:** Khung năng lực, Tuyển dụng, C&B/KPI, Đánh giá hiệu suất, Đào tạo & phát triển, Văn hóa & gắn kết, Quản trị nhân sự SME.

**Nguồn kiến thức nạp vào:** tài liệu dự án KNL, quy chế các nơi từng làm (⚠️ xem *Quy Tắc Ẩn Danh* bên dưới), ghi chép kinh nghiệm rời rạc, khóa học/sách chuyên môn.

## Cấu Trúc Thư Mục

```
WORK-Brain/
├── AGENTS.md             ← FILE NÀY — vault schema
├── .agents/workflows/     ← 9 workflow (xem mục "Tổng Quan Workflow" bên dưới)
├── integrations/mcp/      ← Tích hợp MCP Server
├── scripts/                ← brain.py, brain_mcp.py, build_search_index.py, ...
│
├── raw/                  ← Nguồn gốc. KHÔNG BAO GIỜ sửa, chỉ THÊM.
│   ├── articles/         ← Bài viết web (clip hoặc paste)
│   ├── papers/           ← Paper học thuật, tài liệu chuyên môn
│   ├── repos/            ← Ghi chú repo GitHub, tóm tắt README
│   ├── videos/           ← Transcript video, ghi chú
│   ├── tweets/           ← Thread X/Twitter
│   └── misc/             ← Ảnh, CSV, dataset, quy chế nội bộ, khác
│
├── wiki/                 ← Kiến thức đã biên dịch. AI duy trì.
│   ├── _index.md          ← Master index (PHẢI luôn cập nhật)
│   ├── _glossary.md       ← Thuật ngữ và định nghĩa
│   ├── overview.md        ← Tóm tắt tổng quan cho cross-project access
│   ├── concepts/          ← Bài khái niệm (lõi — VD: "Đánh giá 360 độ")
│   ├── frameworks/        ← ⭐ Framework/mô hình nhân sự (VD: "Mô hình ASK", "9-Box Grid")
│   ├── casestudy/         ← ⭐ Case study thực tế đã ẩn danh (VD: "Triển khai KPI tại DN quy mô 200 người")
│   ├── lessons/           ← ⭐ Bài học kinh nghiệm rút ra (VD: "Sai lầm khi thiết kế thang lương")
│   └── comparisons/       ← So sánh A vs B (VD: "OKR vs KPI")
│
├── outputs/               ← Nội dung tạo ra
│   ├── reports/           ← Báo cáo tổng hợp AutoResearch
│   ├── slides/
│   ├── charts/
│   └── summaries/
│
└── sessions/               ← Session logs (AI Memory)
    ├── current-context.md  ← Ngữ cảnh hiện tại (STABLE FACTS + ACTIVE STATE)
    ├── .hot-buffer.md       ← Bộ đệm quyết định giữa phiên
    └── session-summary-*.md← Archive từng phiên
```

> [!info] ⭐ Tùy biến của Huong
> `tools/` và `people/` của template gốc **đã được thay** bằng `frameworks/`, `casestudy/`, `lessons/` — phù hợp hơn với tri thức nhân sự (framework thay vì công cụ phần mềm; case study/bài học thay vì hồ sơ nhân vật).

## Quy Ước File

### Đặt tên
- **kebab-case**: `mo-hinh-ask-competency.md`
- **Không dấu cách**: Dùng gạch nối
- **Mô tả rõ**: Tên file phải truyền tải nội dung ngay khi nhìn

### Frontmatter (YAML) — Bắt buộc cho MỌI file

```yaml
---
title: "Tiêu đề dễ đọc"
sources: ["[[raw/source-1.md]]", "[[raw/source-2.md]]"]
date_added: 2026-07-08
tags: [concept, hr, competency]
aliases: [tên viết tắt, tên tiếng Anh]
status: draft | reviewed | canonical | needs-review | stub
related:
  - "[[bai-lien-quan-1]]"
  - "[[bai-lien-quan-2]]"
summary: "Tóm tắt 1 dòng cho _index.md"
---
```

## 🔤 Ngôn Ngữ (Tùy biến của Huong)

- **Nội dung wiki viết bằng tiếng Việt**; thuật ngữ chuyên môn nhân sự và kỹ thuật **giữ nguyên tiếng Anh** khi đã phổ biến hơn bản dịch — ví dụ: KPI, JD, PMS, OKR, gap analysis, succession planning, talent pool, 9-Box Grid.
- Không ép dịch thuật ngữ nếu bản dịch tiếng Việt tối nghĩa hơn hoặc ít dùng trong thực tế ngành nhân sự VN.
- Alias trong frontmatter nên bao gồm **cả hai dạng** (Việt + Anh) để tìm kiếm dễ dàng: `aliases: [Khung Năng Lực, Competency Framework, KNL]`.
- Trong hội thoại làm việc (chat với Agent), Huong có thể trộn Việt-Anh tự nhiên — Agent không cần chuẩn hóa lại câu hỏi, chỉ chuẩn hóa khi VIẾT vào wiki.

### Giọng Văn — Bách Khoa Toàn Thư
- Viết giọng văn trung lập, dẫn chứng cụ thể. Không phải blog, không phải notes.
- Tránh: "thú vị là", "đáng chú ý", "rất quan trọng", "groundbreaking", "legendary"
- Tránh editorial voice: "interestingly", "importantly", "it should be noted"
- Cấu trúc bài theo chủ đề (thematic), không theo dòng thời gian (chronological)
- Cảm xúc/nhận định truyền qua direct quotes từ raw source
- Ưu tiên quotes đắt giá, tránh quote tràn lan
- 1 ý = 1 câu. Câu ngắn. Viết đoạn văn, hạn chế bullet-point trừ khi liệt kê.
- Attribution thay vì assertion: "Tài liệu KNL mô tả nó là..." thay vì "Nó rất..."

## Duy Trì Index

File `wiki/_index.md` là **master catalog**. Quy tắc:
1. PHẢI liệt kê mọi bài trong `wiki/` kèm tóm tắt 1 dòng
2. Nhóm theo thư mục con (concepts, frameworks, casestudy, lessons, comparisons)
3. Cập nhật `_index.md` MỖI LẦN thêm/xóa bài wiki
4. Bao gồm số lượng bài và timestamp cập nhật cuối

## Quy Tắc Biên Dịch

Khi biên dịch từ `raw/` sang `wiki/`:
1. Đọc trọn vẹn nguồn raw
2. Kiểm tra `wiki/_absorb_log.json` để xem raw nào đã compile (dựa theo hash SHA256)
3. Xác định concepts, frameworks, case study, bài học được nhắc đến
4. Với mỗi thực thể:
   - Kiểm tra bài wiki đã tồn tại chưa → CẬP NHẬT
   - Nếu chưa → TẠO bài mới
5. **Đọc lại toàn bộ bài wiki TRƯỚC KHI cập nhật — không thương lượng**
6. Sau khi đọc lại, tự hỏi: "Entry mới bổ sung chiều sâu gì mà bài chưa có?"
   - Nếu câu trả lời là "không gì mới" → KHÔNG sửa bài
7. **Contradiction Check** — Trước khi ghi đè bất kỳ thông tin nào, kiểm tra claim mới có mâu thuẫn với claim cũ không. Nếu mâu thuẫn → KHÔNG ghi đè, thêm callout `[!warning] Mâu Thuẫn Chưa Giải Quyết` và tag `needs-review`.
8. Khi cập nhật, **integrate** nội dung mới vào mạch viết hiện có — không chỉ append bullet point ở cuối
9. Thêm `[[backlinks]]` đến bài liên quan hiện có
10. Cập nhật `_index.md`
11. Cập nhật `wiki/_absorb_log.json` — ghi nhận raw đã compile
12. Không bao giờ xóa nội dung khỏi bài hiện có — chỉ refine và integrate

## 🔒 Quy Tắc Ẩn Danh (Tùy biến của Huong)

Áp dụng **bắt buộc** khi nguồn raw là quy chế, chính sách, hoặc tài liệu nội bộ từ **các nơi Huong đã từng làm việc** (không áp dụng cho dự án hiện tại tại Vietcombank/UMA, vốn thuộc phạm vi công việc chính thức của Huong):

1. **Trước khi ghi vào `raw/`:** Khi nạp loại nguồn này, gắn tag `tags: [internal-policy, anonymized-source]` trong frontmatter.
2. **Trước khi compile vào `wiki/`:** Loại bỏ hoặc thay thế các định danh sau bằng placeholder trung lập:
   - Tên công ty/tổ chức cụ thể → thay bằng `[Doanh nghiệp X]`, `[Công ty ngành Y quy mô Z nhân sự]`
   - Tên người, chức danh gắn với cá nhân cụ thể → thay bằng vai trò chung (`Trưởng phòng Nhân sự`, `Giám đốc Vận hành`)
   - Số liệu định danh (mã số nhân viên, số hợp đồng, địa chỉ cụ thể) → loại bỏ hoàn toàn
   - Số liệu tài chính/lương thưởng gắn với công ty cụ thể → có thể giữ dạng khoảng (range) hoặc tỷ lệ, không giữ số tuyệt đối kèm tên công ty
3. **Được giữ nguyên:** Logic, cấu trúc, phương pháp luận, bài học kinh nghiệm — đây mới là giá trị tri thức cần lưu, không phải danh tính nguồn.
4. Bài case study loại này khi tạo trong `wiki/casestudy/` phải có dòng đầu: `> [!info] Nguồn đã được ẩn danh hóa theo quy tắc bảo mật.`
5. Nếu không chắc một chi tiết có cần ẩn danh hay không → mặc định ẩn danh (an toàn hơn). Hỏi Huong nếu nghi ngờ ảnh hưởng đến giá trị bài học.

## Chuẩn Chất Lượng

- Mỗi bài wiki phải **tự đủ nghĩa** (hiểu được khi đọc riêng lẻ)
- Tối thiểu 200 từ cho bài concept
- Mỗi bài phải link đến ≥2 bài wiki khác
- Tránh trùng lặp nội dung — link thay vì lặp lại
- Dùng tiếng Việt cho nội dung, tiếng Anh cho thuật ngữ kỹ thuật/chuyên ngành

### Giới Hạn Kích Thước Bài
- **Giới hạn:** 15–120 dòng nội dung (không tính frontmatter)
  - Dưới 15 dòng → tag `status: stub`, ưu tiên bổ sung khi có raw mới
  - Trên 120 dòng → xem xét tách sub-topic thành bài riêng
- **Anti-Cramming:** Nếu sub-topic xuất hiện ≥3 đoạn trong 1 bài → tách thành bài con
- **Anti-Thinning:** Không tạo bài nếu không viết được ≥3 câu có ý nghĩa. Mỗi lần touch bài → phải làm nó giàu hơn

## 🏷️ Entity-Type Templates (Đã tùy biến)

Mỗi loại bài wiki có cấu trúc riêng. Dùng đúng template theo entity type.

**Concept** (`wiki/concepts/`):
- `## Định Nghĩa` — 1 đoạn văn rõ ràng
- `## [Sections thematic]` — tùy chủ đề (Cơ Chế, Cách Tính, Ví Dụ...)
- `## Liên Hệ / Ứng Dụng` — context thực tế trong công việc nhân sự
- `## Nguồn Tham Khảo`

**Framework** (`wiki/frameworks/`) ⭐ *thay thế Tool*:
- `## Tổng Quan` — framework là gì, ai/tổ chức nào phát triển, mục đích
- `## Cấu Trúc / Thành Phần` — các thành phần cấu thành framework
- `## Ứng Dụng Trong [Context]` — cách áp dụng thực tế, ví dụ minh họa
- `## Ưu Điểm / Hạn Chế` — đánh giá trung lập
- `## Nguồn Tham Khảo`

**Case Study** (`wiki/casestudy/`) ⭐ *thay thế Person*:
- `## Bối Cảnh` — tình huống, quy mô đơn vị (đã ẩn danh nếu cần), thời điểm
- `## Vấn Đề / Mục Tiêu` — thách thức cần giải quyết
- `## Giải Pháp Triển Khai` — cách tiếp cận, các bước thực hiện
- `## Kết Quả / Bài Học` — kết quả đạt được, điều rút ra
- `## Nguồn Tham Khảo`

**Lessons** (`wiki/lessons/`) ⭐ *mới*:
- `## Bối Cảnh Rút Ra` — tình huống dẫn đến bài học
- `## Bài Học Cụ Thể` — điều rút ra, nêu rõ ràng, cụ thể
- `## Áp Dụng / Khuyến Nghị` — cách vận dụng cho tình huống tương lai
- `## Nguồn Tham Khảo`

**Comparison** (`wiki/comparisons/`):
- `## Bối Cảnh` — tại sao so sánh
- `## Bảng So Sánh` — bảng tiêu chí rõ ràng
- `## Phân Tích` — đánh giá từng chiều
- `## Kết Luận`

**Report/Summary** (`outputs/`):
- `## Context` — bối cảnh yêu cầu
- `## Phân Tích` — nội dung chính
- `## Kết Luận / Hành Động`
- `## Nguồn`

> [!warning] Cần đồng bộ thủ công
> Ba workflow `compile.md`, `breakdown.md`, `cleanup.md` trong `.agents/workflows/` vẫn còn tham chiếu cứng đến `wiki/tools/` và `wiki/people/` từ template gốc. AGENTS.md này (nguồn chân lý) đã đổi sang `frameworks/casestudy/lessons/` — nếu Agent gặp mâu thuẫn giữa AGENTS.md và nội dung 3 file workflow đó, **luôn ưu tiên AGENTS.md**. Khuyến nghị: nhờ Claude patch lại 3 file này trong lần làm việc tiếp theo để đồng bộ hoàn toàn.

## Classify-Before-Extract

Trước khi compile raw/ thành wiki/, **phân loại nguồn theo type** để áp dụng extraction strategy phù hợp:

| Source Type | Đặc điểm | Extraction Strategy |
|-------------|----------|-------------------|
| **Tweet/Thread** | Ngắn, dense, có replies | Extract assertions chính + notable replies |
| **Article/Gist** | Dài, có cấu trúc | Extract theo sections. Tìm thesis chính + supporting arguments |
| **Paper/Report** | Formal, có abstract/methodology | Extract abstract → findings → implications |
| **Diagram/Image** | Visual | Bóc tách layers, components, flows. Mô tả bằng text + tables |
| **Video/Transcript** | Dài, conversational | Tìm key moments, quotes đắt giá. Bỏ filler/tangents |
| **Repo/Code** | Technical | Extract architecture, patterns, API surface |
| **Quy chế/Chính sách nội bộ** ⭐ | Từ nơi từng làm | Áp dụng **Quy Tắc Ẩn Danh** ở trên TRƯỚC KHI extract. Ưu tiên logic/phương pháp luận hơn số liệu định danh |

**Quy tắc:** Không xử lý mọi raw giống nhau. Report 50 trang cần strategy khác thread 5 tweets.

## Operations Log

File `wiki/_ops_log.md` ghi lại **mọi operations** theo thời gian:
- Append 1 dòng `## [YYYY-MM-DD] action | title` sau mỗi ingest, compile, ask, cleanup, save, breakdown, autoresearch
- KHÔNG sửa entries cũ — chỉ append
- Mọi workflow PHẢI append vào log sau khi hoàn thành

## Dual Output Rule

Mọi task tạo ra kiến thức (hỏi đáp, phân tích, so sánh) phải xem xét produce **2 outputs**:
1. **Output 1:** Trả lời trực tiếp cho Huong
2. **Output 2:** Cập nhật wiki nếu câu trả lời chứa insight mới chưa có trong wiki

Quy tắc: Nếu `/ask` hoặc `/save` tạo ra synthesis có giá trị → hỏi Huong có muốn file-back vào wiki không.

## AutoResearch

Workflow `/autoresearch [chủ đề]` tự động search web, đánh giá nguồn, ingest vào raw/, và tạo báo cáo tổng hợp. Cấu hình tại `raw/_research_program.md`. Output lưu tại `outputs/reports/`. Compile vào wiki CHỈ khi Huong đồng ý.

---

## 📋 Tổng Quan 9 Workflow

| Lệnh | Mục đích | Kích hoạt tự động? | File |
|------|----------|:---:|------|
| `/startup` | Đọc `current-context.md` + hot-buffer, báo cáo tình hình đầu phiên | ✅ Tự động khi phát hiện tín hiệu đầu phiên | `.agents/workflows/startup.md` |
| `/ingest` | Nạp dữ liệu mới (URL, file, folder) vào `raw/` | Theo yêu cầu | `.agents/workflows/ingest.md` |
| `/compile` | Biên dịch `raw/` → `wiki/`, có Contradiction Check | Theo yêu cầu | `.agents/workflows/compile.md` |
| `/ask` | Hỏi đáp trực tiếp trên vault, có Dual Output | Theo yêu cầu | `.agents/workflows/ask.md` |
| `/save` | Trích xuất kiến thức từ hội thoại hiện tại → wiki (giữa phiên) | Theo yêu cầu | `.agents/workflows/save.md` |
| `/breakdown` | Quét entity nhắc nhiều nhưng chưa có trang → đề xuất tạo bài mới | Theo yêu cầu | `.agents/workflows/breakdown.md` |
| `/cleanup` | Audit sức khỏe wiki (giọng văn, cấu trúc, wikilinks, contradiction backlog) | Theo yêu cầu | `.agents/workflows/cleanup.md` |
| `/autoresearch` | Tự động search web, ingest, tổng hợp báo cáo | Theo yêu cầu | `.agents/workflows/autoresearch.md` |
| `/wrapup` | Lưu session summary + cập nhật rolling context cuối phiên | ✅ Agent chủ động hỏi khi phát hiện tín hiệu kết thúc phiên | `.agents/workflows/wrapup.md` |

**Quy trình tổng thể:**
```
Nạp dữ liệu → /ingest → raw/
Biên dịch    → /compile → wiki/ (có Contradiction Check + Quy Tắc Ẩn Danh nếu áp dụng)
Hỏi đáp      → /ask → trả lời từ wiki (Dual Output)
Lưu nhanh    → /save → hội thoại → raw/ → wiki/ (giữa phiên)
Mở rộng      → /breakdown → phát hiện lỗ hổng → đề xuất bài mới
Nghiên cứu   → /autoresearch → search web → raw/ → wiki/
Dọn dẹp      → /cleanup → audit chất lượng wiki
Đầu phiên    → /startup → nhớ lại ngữ cảnh
Cuối phiên   → /wrapup → lưu session + cập nhật rolling context
```

> Chi tiết đầy đủ từng bước, format báo cáo, và xử lý lỗi của mỗi workflow nằm trong file tương ứng dưới `.agents/workflows/` — AGENTS.md này chỉ tổng hợp mục đích và luồng liên kết giữa chúng.

## Agent Integrations

Vault này có thể kết nối với AI agent bên ngoài để truy cập tự động:

### Hermes Agent *(mặc định từ template — chưa cấu hình cho setup hiện tại của Huong)*
Xem `integrations/hermes/` nếu cần dùng trong tương lai:
- `SKILL.md` — Skill drop-in để Hermes đọc wiki
- `SOUL-snippet.md` — Bổ sung system prompt
- `scripts/` — Script đồng bộ (bash + PowerShell + Python wrapper)
- `docker/` — Snippet deploy Docker/Railway

Tích hợp này **chỉ đọc (read-only)** theo thiết kế. Hermes có thể query wiki nhưng không sửa được. Mọi thao tác ghi đi qua local AI agent qua `/ingest` và `/compile`.

**Setup hiện tại của Huong:** Vận hành chính qua **Antigravity** (agent local), không qua Hermes. Section này giữ lại để tương thích ngược với template, không cần cấu hình trừ khi có nhu cầu mới.

---

## 📝 Nhật Ký Tùy Biến

| Ngày | Thay đổi | Lý do |
|------|----------|-------|
| 2026-07-08 | Dịch toàn bộ AGENTS.md sang tiếng Việt, thêm mục "Tổng Quan 9 Workflow" | Tổng hợp từ 9 file workflow riêng lẻ để dễ tra cứu tại 1 nơi |
| 2026-07-08 | Đổi entity type: bỏ `tools/`, `people/` → thêm `frameworks/`, `casestudy/`, `lessons/` | Phù hợp với tri thức nhân sự hơn tri thức công nghệ |
| 2026-07-08 | Thêm mục "Quy Tắc Ẩn Danh" | Bảo mật khi biên dịch quy chế/chính sách từ các nơi từng làm |
| 2026-07-08 | Thêm mục "🔤 Ngôn Ngữ" tường minh | Chuẩn hóa cách Agent xử lý mix Việt-Anh trong nội dung vs. hội thoại |
