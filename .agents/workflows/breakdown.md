---
description: Tìm và tạo bài wiki còn thiếu — mở rộng wiki từ dữ liệu đã có
---

# /breakdown — Mở Rộng Wiki Tự Động

Workflow này scan toàn bộ wiki để phát hiện entities (thực thể) được nhắc nhiều lần nhưng chưa có trang riêng — từ đó đề xuất và tạo bài mới.

## Kích Hoạt

- `/breakdown` hoặc `/mo-rong`
- "Tìm bài wiki còn thiếu"
- "Wiki có lỗ hổng gì không?"
- "Mở rộng wiki"
- "Expand wiki"

## Bước Thực Hiện

### Phase 1: Survey — Chụp X-quang Wiki

// turbo-all

1. Đọc `wiki/_index.md` — danh sách bài hiện có
2. Đọc `wiki/_backlinks.json` — xem entity nào được link nhiều nhưng chưa có trang
3. Kiểm tra:
   - **Backlink targets không có trang**: Entry trong `_backlinks.json` trỏ đến tên bài không tồn tại
   - **Thư mục trống**: Subdirectory trong `wiki/` chưa có bài nào (VD: `comparisons/`, `lessons/`)
   - **Bài bloated** (>100 dòng): Có sub-topic nào nên tách ra?

### Phase 2: Mining — Khai Thác Entities

// turbo-all

Đọc từng bài wiki và tìm **danh từ cụ thể** (Concrete Noun Test) chưa có trang riêng:

#### Concrete Noun Test
Hỏi: **"X là một ___"** — nếu trả lời được bằng 1 danh từ cụ thể, X là entity tiềm năng.

**KHAI THÁC (tạo bài):**
- **Framework/mô hình nhân sự** (VD: mô hình ASK, 9-Box Grid, Balanced Scorecard) xuất hiện ≥2 bài → `frameworks/`
- **Tình huống/case triển khai cụ thể** (đã ẩn danh nếu nguồn là nơi từng làm — xem AGENTS.md → Quy Tắc Ẩn Danh) được nhắc ≥2 bài → `casestudy/`
- **Bài học/kinh nghiệm** lặp lại xuyên suốt nhiều bối cảnh, không gắn 1 tình huống cụ thể → `lessons/`
- Tên sự kiện hoặc bước ngoặt có ngày tháng cụ thể → `casestudy/` (nếu gắn 1 tình huống) hoặc `concepts/` (nếu là mốc chung của ngành)
- Sách, paper, khóa học đáng chú ý được nhắc ≥2 lần → `concepts/` (nếu là ý tưởng) hoặc `frameworks/` (nếu đề xuất một mô hình cụ thể)
- **Concept patterns** — ý tưởng/mẫu hình xuất hiện xuyên suốt nhiều bài → `concepts/`

**KHÔNG KHAI THÁC:**
- Thuật ngữ nhân sự phổ biến (KPI, JD, OKR, PMS) dùng như từ vựng chung — trừ khi có phân tích riêng đủ sâu về thuật ngữ đó
- Entity đã có trang
- Nhắc thoáng qua 1 lần duy nhất
- Thuật ngữ đã có trong `_glossary.md`
- Tên công ty/cá nhân cụ thể chưa được ẩn danh — DỪNG, áp dụng Quy Tắc Ẩn Danh trước khi coi là ứng cử viên

### Phase 3: Ranking — Xếp Hạng Ứng Cử Viên

Tổng hợp thành bảng:

```markdown
| Entity | Loại | Lần nhắc | Bài nhắc đến | Category đề xuất |
|--------|------|----------|-------------|-----------------|
| Mô hình ASK | framework | 3 | khung-nang-luc, danh-gia-nang-luc | frameworks/ |
| Triển khai KPI tại DN 200 người (đã ẩn danh) | case study | 2 | thiet-ke-thang-luong, kpi-bonus | casestudy/ |
```

**Tiêu chí chọn:**
- Nhắc ≥2 bài khác nhau → ứng cử viên mạnh
- Nhắc ≥3 bài → nên tạo ngay
- Viết được ≥3 câu có ý nghĩa (Anti-Thinning) → đủ chất liệu

### Phase 4: Trình Bày Đề Xuất

Trình bày cho người dùng review trước khi tạo:

```markdown
## 📊 Đề Xuất Mở Rộng Wiki

### Ứng cử viên mạnh (≥3 nhắc):
1. **[Entity]** → `wiki/[category]/[slug].md`
   - Nhắc trong: [[bài-1]], [[bài-2]], [[bài-3]]
   - Chất liệu sẵn có: [tóm tắt ngắn]

### Ứng cử viên tiềm năng (2 nhắc):
1. **[Entity]** → `wiki/[category]/[slug].md`
   - Nhắc trong: [[bài-1]], [[bài-2]]

### Comparisons đề xuất:
1. **A vs B** — vì cả 2 đã có trang, nhiều điểm so sánh

### Bạn muốn tạo bài nào? (gõ số hoặc "tất cả")
```

### Phase 5: Tạo Bài (Sau Khi Người Dùng Approve)

Cho mỗi bài được approve:

1. **Grep tất cả wiki** tìm mọi mention của entity đó
2. **Thu thập chất liệu** từ các bài hiện có + raw/ nếu cần
3. **Nếu là `casestudy/` từ nguồn nơi từng làm:** xác nhận đã áp dụng Quy Tắc Ẩn Danh (AGENTS.md) trước khi viết
4. **Viết bài** theo chuẩn AGENTS.md (Entity-Type Template tương ứng: Concept, Framework, Case Study, Lessons, Comparison):
   - Frontmatter đầy đủ (title, aliases, tags, related, summary)
   - Tối thiểu 200 từ cho concepts/frameworks
   - Giọng văn Bách Khoa Toàn Thư
   - ≥2 wikilinks đến bài khác
5. **Thêm wikilinks ngược** — các bài hiện có nhắc đến entity → thêm `[[link]]`
6. **Cập nhật `_index.md`** — thêm entry mới
7. **Cập nhật `_glossary.md`** nếu entity là thuật ngữ mới

### Phase 6: Rebuild

// turbo

1. Chạy `python wiki/_build_backlinks.py`
2. Cập nhật `_index.md` — đếm lại tổng bài, cập nhật ngày

### Báo Cáo Kết Quả

```markdown
## 📈 Breakdown Wiki — YYYY-MM-DD

### Bài mới tạo:
- [[bài-1]] — tóm tắt
- [[bài-2]] — tóm tắt

### Wikilinks thêm vào bài cũ:
- [[bài-cũ-1]] — thêm link đến [[bài-mới]]

### Thống kê:
- Tổng bài wiki: [N] (trước: [M])
- Backlinks rebuilt: [X] targets
```

## Quy Tắc

- **KHÔNG tạo bài mà không hỏi** — Phase 4 luôn chờ approval
- **The Golden Rule:** Bài wiki không phải Wikipedia về entity. Bài viết về **vai trò của entity trong hệ thống kiến thức** này.
  - Bài về một framework ≠ mô tả lý thuyết suông. Mà là: framework đó áp dụng thế nào trong công việc thực tế, hiệu quả ra sao, ai từng dùng, trong bối cảnh nào.
- **Anti-Thinning:** Không tạo stub vô nghĩa. ≥3 câu meaningful hoặc không tạo.
- **Re-read hiện có** trước khi thêm wikilinks vào bài cũ
- **Ẩn danh trước khi công nhận là ứng cử viên** — nếu entity gắn với công ty/cá nhân cụ thể từ nơi từng làm, xử lý theo Quy Tắc Ẩn Danh trước khi đưa vào bảng xếp hạng Phase 3

## Xử Lý Lỗi

| Tình huống | Cách xử lý |
|-----------|------------|
| Không tìm thấy ứng cử viên nào | Báo "Wiki đã phủ tốt. Cần nạp raw mới để mở rộng." |
| Entity xuất hiện nhưng chưa đủ chất liệu | Ghi nhận, đề xuất `/ingest` thêm nguồn |
| Không chắc entity thuộc category nào | Hỏi người dùng |
| Entity gắn với công ty/cá nhân cụ thể chưa ẩn danh | DỪNG, hỏi người dùng cách ẩn danh trước khi tạo bài |
