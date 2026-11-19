# Second brain vault (Obsidian + Claude Code)

Kho kiến thức cá nhân. Viết bằng tiếng Việt, giữ nguyên thuật ngữ tiếng Anh.

## Cấu trúc
- `raw/`: tài liệu gốc (bài báo, PDF, transcript, ghi chú). **Chỉ đọc, không sửa, không xoá.**
- `wiki/`: trang kiến thức do Claude viết. Mỗi trang = 1 khái niệm / người / công cụ / chủ đề.
- `wiki/index.md`: mục lục toàn bộ wiki, chia nhóm theo chủ đề.
- `wiki/log.md`: nhật ký, mỗi lần ingest thêm 1 dòng `YYYY-MM-DD: <nguồn> → <các trang đã tạo/sửa>`.

## Quy tắc trang wiki
- Tên file kebab-case, tiếng Anh không dấu: `wiki/shopify-functions.md`.
- Đầu trang có frontmatter:
  ```yaml
  ---
  tags: [chủ-đề]
  sources: [raw/ten-file.md]
  updated: YYYY-MM-DD
  ---
  ```
- Mở đầu bằng 1–2 câu tóm tắt. Ngắn gọn, gạch đầu dòng.
- Link chéo bằng `[[ten-trang]]` mỗi khi nhắc tới khái niệm đã có trang.
- Mỗi ý quan trọng ghi nguồn: `(nguồn: [[raw/ten-file]])`.

## Lệnh
- **"ingest <file>"**: đọc file trong `raw/` → tìm trang liên quan có sẵn → cập nhật trang cũ trước, chỉ tạo trang mới khi chưa có → thêm link chéo hai chiều → cập nhật `index.md` + `log.md` → báo ngắn đã tạo/sửa trang nào.
- **"hỏi: <câu hỏi>"**: trả lời dựa trên `wiki/` (đọc `index.md` trước), dẫn link trang. Wiki chưa có thì nói rõ là chưa có, không bịa.
- **"lint"**: tìm trang mâu thuẫn nhau, trang mồ côi (không ai link tới), link hỏng, trang quá dài cần tách → báo danh sách, chưa sửa khi chưa được bảo.
