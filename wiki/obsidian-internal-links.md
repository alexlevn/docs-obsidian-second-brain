---
tags: [công-cụ, pkm, obsidian]
sources: [raw/obsidian-internal-links.md]
updated: 2026-11-20
---

# Obsidian internal links

Internal link là cách nối note, attachment và file khác trong [[obsidian]] để tạo mạng lưới kiến thức. Đây là cơ chế nền cho link chéo `[[ten-trang]]` trong [[second-brain]]. (nguồn: [[raw/obsidian-internal-links]])

## Định dạng
- Wikilink (mặc định, gọn): `[[Three laws of motion]]` hoặc có `.md`.
- Markdown: `[text](Three%20laws%20of%20motion.md)`, phải URL-encode (dấu cách → `%20`). Tắt Wikilinks ở Settings → Files and links nếu cần interoperability.
- Link tới note trong folder: ghi path từ vault root, dùng `/`: `[[Projects/Three laws of motion]]`.
- Link tới note chưa tồn tại: Obsidian tạo note đúng folder path đó.
- Ký tự nên tránh trong tên: `# | ^ : %% [[ ]]`.
- File không phải markdown cần đuôi file: `[[Figure 1.png]]`. Thêm `!` phía trước để embed.
- Rename file: Obsidian tự cập nhật link (có thể tắt ở "Automatically update internal links").

## Link tới heading
- Cùng note: `[[#Heading]]`. Note khác: `[[Note#Heading]]`. Subheading: `[[Note#H1#H2]]`.
- Tìm heading toàn vault: `[[##` hoặc `[[## team]]`.

## Link tới block
- Cú pháp `[[Note#^id]]`. Gõ `^` sẽ có gợi ý; tìm toàn vault bằng `[[^^block]]`.
- Đoạn văn thường: ghi ` ^id` cuối dòng. Block có cấu trúc (list, quote, callout, table): đặt id ở dòng riêng, trống một dòng trước và sau.
- Id chỉ gồm A-Z, a-z, 0-9 và dấu `-` (vd `^quote-of-the-day`).
- Không link được tới phần cụ thể bên trong quote, callout, table.
- Block reference chỉ chạy trong Obsidian, không phải markdown chuẩn.

## Đổi text hiển thị
- Wikilink: `[[Example|Custom name]]`. Markdown: `[Custom name](Example.md)`.
- Muốn một note có nhiều tên dùng lại nhiều nơi thì dùng alias; chỉ đổi một chỗ thì dùng display text.

## Preview
- Bật plugin Page preview, rồi hover vào link (trong chế độ edit giữ `Cmd`/`Ctrl`).
