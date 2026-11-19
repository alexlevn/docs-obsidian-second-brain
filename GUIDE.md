# Hướng dẫn: Obsidian + Claude Code second brain

## 1. Khái niệm cơ bản của Obsidian
- **Vault**: 1 thư mục bình thường trên máy. Obsidian chỉ là app đọc/hiển thị thư mục đó. Thư mục này chính là vault.
- **Note**: mỗi file `.md` là 1 ghi chú. File thuần text nên Claude Code đọc/sửa trực tiếp được.
- **Wikilink `[[...]]`**: gõ `[[shopify-functions]]` là link tới file `shopify-functions.md`. Đây là thứ tạo ra "mạng" kiến thức.
- **Backlinks**: mở 1 trang sẽ thấy những trang nào đang link tới nó. Biết được ý này liên quan tới đâu.
- **Graph view**: vẽ mọi note + link thành đồ thị (cái hình trong reel). Càng nhiều link, đồ thị càng dày.
- **Properties**: khối YAML `---` ở đầu file (tags, nguồn, ngày). Dùng để lọc/tìm.
- **Tags**: `#ai`, `#shopify`... gom nhóm nhanh.
- **Plugins**: core (có sẵn) và community (cài thêm). Mới bắt đầu không cần cài gì.

## 2. Cài đặt
- [ ] Cài Obsidian: `brew install --cask obsidian` (hoặc tải ở obsidian.md). Miễn phí.
- [ ] Mở Obsidian → **Open folder as vault** → chọn `alex-ws/obsidian-second-brain`
- [ ] Settings → Files and links → **New link format: Shortest**, bật **Use [[Wikilinks]]**
- [ ] Settings → Files and links → **Excluded files**: thêm `GUIDE.md`, `CLAUDE.md` (cho gọn graph)
- [ ] Terminal: `cd alex-ws/obsidian-second-brain && claude`
- [ ] Thử: bỏ 1 bài viết vào `raw/` → gõ `ingest raw/<tên-file>` → mở Graph view (Cmd+G) xem kết quả

Mẹo đưa tài liệu vào `raw/`:
- Bài web: cài extension **Obsidian Web Clipper** (Chrome), clip thẳng vào `raw/`.
- PDF: kéo thả file vào `raw/`.
- YouTube: copy transcript vào 1 file `.md`.

## 3. Cách dùng hằng ngày
| Việc | Gõ cho Claude |
|---|---|
| Thêm kiến thức | `ingest raw/bai-viet.md` |
| Hỏi lại | `hỏi: tôi đã đọc gì về Shopify Functions?` |
| Dọn kho (mỗi tuần) | `lint` |

Luật chi tiết nằm trong `CLAUDE.md`; muốn đổi cách Claude xử lý thì sửa file đó.

## 4. Ứng dụng thực tế cho bạn
- **Học công nghệ mới** (vd Shopify extension): clip docs, bài blog, video transcript vào `raw/` → wiki tự thành sổ tay; lần sau code chỉ cần "hỏi:" thay vì search lại.
- **Nghiên cứu khách hàng / thị trường**: mỗi web đối thủ, bài phân tích là 1 file raw → wiki gom theo đối thủ, theo tính năng.
- **Lưu bài học dự án**: cuối mỗi project, bỏ handoff/NEXT-SESSION vào `raw/` → wiki giữ lại các lỗi đã gặp, cách đã giải.
- **Học tiếng Anh**: transcript video → trang từ vựng/cụm từ, link theo chủ đề.
- **Ý tưởng kinh doanh/content**: ghi chú nhanh vào raw, Claude nối với kiến thức đã có, chỉ ra ý nào trùng/bổ sung nhau.

Nguyên tắc: **ít mà đều**. Mỗi ngày ingest 1–2 tài liệu thật sự đọc, hơn là đổ 100 file một lần.
