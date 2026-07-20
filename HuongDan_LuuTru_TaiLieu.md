# Hướng Dẫn Lưu Trữ & Đặt Tên Tài Liệu — WORK-Brain

> File tra cứu nhanh. Lưu tại `raw/misc/huong-dan-luu-tru.md` hoặc `wiki/_glossary.md` liền kề, hoặc để ở gốc vault cho dễ tìm.
> Không phải nội dung tri thức — đây là "sổ tay vận hành cá nhân", không cần `/ingest` hay `/compile`.

---

## 1. Bản đồ 3 công cụ đang có

| Công cụ | Vai trò | Vị trí |
|---|---|---|
| `Template_HanhTrinh_Hub.md` | **Hub** — bối cảnh chung 1 tổ chức: phòng ban, con người, vai trò, timeline giai đoạn. Viết 1 lần/tổ chức | Nhân bản → `raw/misc/` |
| `Template_HanhTrinh_DuAn.md` | **Spoke** — chi tiết 1 dự án/công việc cụ thể trong tổ chức đó, link ngược về Hub | Nhân bản → `raw/misc/` (cùng chỗ với Hub) |
| `Template_KhoaHoc.md` | Ghi framework/phương pháp học được từ **khóa đào tạo** | Nhân bản → `raw/papers/` |
| `WORK_HanhTrinh_Index.xlsx` | Tra cứu nhanh: danh sách tổ chức + index bài học theo loại kỹ năng | Google Drive `02_WORK/` |

**Mô hình Hub + Spoke:** 1 tổ chức = 1 file Hub (viết 1 lần, không lặp lại) + nhiều file Spoke (1 file/dự án). Hub chứa bối cảnh/con người/vai trò dùng chung; Spoke chứa thách thức/xử lý/kết quả/bài học riêng từng dự án — tránh lặp lại thông tin chung ở mọi dự án.

**Nguyên tắc chọn template:** trải nghiệm chung cả giai đoạn làm việc → Hub. Chi tiết 1 dự án/công việc cụ thể → Spoke. Kiến thức/mô hình có cấu trúc từ đào tạo → Khóa Học.

**Vị trí lưu bản THÂN các file template (rỗng, để tái sử dụng):**
```
WORK_BRAIN/_templates/Template_HanhTrinh_Hub.md
WORK_BRAIN/_templates/Template_HanhTrinh_DuAn.md
WORK_BRAIN/_templates/Template_KhoaHoc.md
```
KHÔNG để trong `raw/` — vì `raw/` chỉ chứa nguồn thật, không bao giờ sửa. Khi ghi chú thật, mở file trong `_templates/`, **Duplicate/Save As** sang `raw/misc/` hoặc `raw/papers/` theo quy tắc đặt tên ở Mục 2, không sửa trực tiếp file gốc.

---

## 2. Quy tắc đặt tên file theo loại tài liệu

| Loại tài liệu | Thư mục lưu | Quy tắc đặt tên | Ví dụ |
|---|---|---|---|
| Hub — tổng quan 1 tổ chức | `raw/misc/` | `hanhtrinh-[ten-to-chuc]-tongquan.md` | `hanhtrinh-nganhang-abc-tongquan.md` |
| Spoke — 1 dự án cụ thể trong tổ chức | `raw/misc/` | `hanhtrinh-[ten-to-chuc]-duan-[ten-du-an].md` | `hanhtrinh-nganhang-abc-duan-tuyendung-so.md` |
| Ghi chú 1 khóa học | `raw/papers/` | `khoahoc-[ten-mo-hinh-hoac-khoa]-[to-chuc]-[YYYY].md` | `khoahoc-mo-hinh-ask-vcb-2020.md` |
| Tài liệu gốc PDF/Word (giữ nguyên, không convert) | `raw/papers/` (cùng chỗ với file .md liên quan) | **Trùng tên** với file .md tương ứng, chỉ khác đuôi | `khoahoc-mo-hinh-ask-vcb-2020.pdf` |
| Tài liệu gốc đã convert sang .md (toàn văn thô) | `raw/papers/` | Thêm hậu tố `-taileugoc` để phân biệt với bản tự viết | `khoahoc-mo-hinh-ask-vcb-2020-taileugoc.md` |
| Quy chế/chính sách nội bộ từ nơi đã từng làm | `raw/misc/` | Gắn tag `internal-policy` trong frontmatter, áp dụng Quy Tắc Ẩn Danh | — |

**Quy tắc chung:** kebab-case (gạch nối, không dấu cách, không viết hoa), tên phải tự mô tả được nội dung khi nhìn thoáng qua — không đặt `tailieu-1.md`, `note-moi.md`.

---

## 3. Xử lý tài liệu PDF/Word gốc — chọn 1 trong 2 cách

### Cách 1 — Chỉ lưu tham khảo, không cần tìm kiếm nội dung bên trong (mặc định, ưu tiên)

Dùng khi: đã tự đọc và tóm tắt ý chính vào file `.md` (mục "Nội Dung Cốt Lõi") rồi — PDF chỉ giữ để backup/đối chiếu.

1. Copy PDF gốc vào cùng thư mục, đặt tên **trùng** với file `.md` (chỉ khác đuôi)
2. Trong mục "Nguồn Tham Khảo" của file `.md`, trỏ bằng wikilink:
   ```
   - [[khoahoc-mo-hinh-ask-vcb-2020.pdf]] — slide gốc khóa học
   ```
   Thêm dấu `!` phía trước (`![[...]]`) nếu muốn xem preview ngay trong note.

### Cách 2 — Convert toàn văn sang .md để tìm kiếm được (FTS5)

Dùng khi: tài liệu dài, tra cứu lại nhiều lần, cần `search_knowledge("từ khóa")` tìm ra được không cần nhớ mở đúng file.

```bash
python raw/_ingest.py duong-dan-file.pdf
python raw/_ingest.py duong-dan-file.docx
```
*(nếu báo thiếu thư viện: `pip install PyMuPDF`)*

Hỗ trợ thêm: `.txt`, `.html`, `.json`, `.csv`.

**Sau khi convert, luôn kiểm tra lại 1 lượt** — bảng/số liệu dễ vỡ dòng, lệch cột khi convert tự động.

### Khi nào KHÔNG convert
- PDF là bản scan/ảnh (không phải text thật) → chữ ra lỗi, vô nghĩa. Dùng Cách 1, tự đọc và tóm tắt tay.
- Tài liệu có sơ đồ/bảng phức tạp mang thông tin thị giác quan trọng → giữ PDF gốc (Cách 1), đừng ép convert.

---

## 4. Tránh nhầm lẫn: file "tự viết" vs file "trích xuất thô"

Khi có cả 2 loại cho cùng 1 nguồn, **luôn phân biệt rõ bằng tên file**:

```
khoahoc-mo-hinh-ask-vcb-2020.md              ← chị tự viết theo Template_KhoaHoc (framework, đánh giá, bài học)
khoahoc-mo-hinh-ask-vcb-2020-taileugoc.md    ← script convert nguyên văn từ PDF (thô, chưa xử lý)
```

Trong file tự viết, trỏ sang bản gốc ở mục "Nguồn Tham Khảo":
```
- [[khoahoc-mo-hinh-ask-vcb-2020-taileugoc]] — tài liệu gốc, convert từ PDF
```

Lý do bắt buộc phải phân biệt: nếu không, khi `/compile` Agent có thể hiểu nhầm 2 file là 2 nguồn độc lập cần gộp, thay vì hiểu đúng đây là "bản chưng cất" + "bản thô" của cùng 1 nguồn.

---

## 5. Bảo mật — nhắc lại

- Repo `AI_brain_Huongasu` đã chuyển **Private** ✅ — vẫn nên duy trì thói quen ẩn danh khi viết về nơi đã từng làm, phòng trường hợp sau này đổi ý mở Public hoặc chia sẻ repo cho ai đó.
- Tài liệu quy chế/chính sách từ nơi cũ → áp dụng Quy Tắc Ẩn Danh trong `AGENTS.md` (che tên công ty/người/số liệu định danh) **trước khi** `/compile` vào wiki. Raw giữ nguyên tên thật cũng được (vì đã Private), nhưng **wiki thì luôn phải ẩn danh** — vì wiki là nơi tri thức được tổng hợp, dễ bị đọc/chia sẻ lại hơn raw.

---

## 6. Quy trình tổng — từ ghi chú thô đến bài wiki hoàn chỉnh

```
1. Tổ chức mới lần đầu ghi chú → tạo file Hub trước (Template_HanhTrinh_Hub.md)
2. Mỗi dự án/công việc đáng nhớ trong tổ chức đó → tạo file Spoke riêng (Template_HanhTrinh_DuAn.md),
   link ngược về Hub + cập nhật bảng "Các Dự Án" trong Hub trỏ tới Spoke
3. (Nếu có PDF/Word gốc) → xử lý theo Cách 1 hoặc Cách 2 ở mục 3
4. /ingest   → xác nhận file đã nằm đúng chỗ trong raw/
5. /compile  → Agent tự phân loại, tạo/cập nhật bài wiki (frameworks/casestudy/lessons/concepts)
   — Hub thường không tự thành 1 bài wiki riêng (chỉ là bối cảnh); Spoke thường compile thành wiki/casestudy/
6. Cập nhật WORK_HanhTrinh_Index.xlsx:
   - Sheet DanhSach_ToChuc: đổi trạng thái ghi chép → "Hoàn thành"
   - Sheet Index_BaiHoc: thêm dòng bài học mới, link tới bài wiki vừa tạo
7. Định kỳ chạy /breakdown → phát hiện framework/case lặp lại nhiều lần, gợi ý tách bài riêng
```

---

## 7. Cheat sheet — tra cứu 10 giây

| Tôi có... | Làm gì |
|---|---|
| 1 tổ chức mới, chưa ghi gì | `Template_HanhTrinh_Hub.md` → `raw/misc/` (viết 1 lần) |
| 1 dự án cụ thể trong tổ chức đã có Hub | `Template_HanhTrinh_DuAn.md` → `raw/misc/` (link về Hub) |
| Kiến thức từ khóa học | `Template_KhoaHoc.md` → `raw/papers/` |
| File PDF/Word gốc, chỉ cần lưu tham khảo | Đặt cùng tên file .md liên quan, link bằng `[[...]]` |
| File PDF/Word gốc, cần tìm kiếm toàn văn | `python raw/_ingest.py [file]`, đặt hậu tố `-taileugoc` |
| PDF dạng scan/ảnh | Không convert — tự đọc, tóm tắt tay vào "Nội Dung Cốt Lõi" |
| Quy chế từ nơi từng làm | Gắn tag `internal-policy`, ẩn danh khi lên wiki |
| Vừa hoàn thành 1 file ghi chú | `/ingest` → `/compile` → cập nhật Excel Index |
