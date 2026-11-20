---
tags: [pkm, ai]
sources: [raw/karpathy-second-brain.md]
updated: 2026-11-20
---

# Second brain (bộ não thứ hai)

Dùng AI để xây một wiki sống từ mọi thứ mình đọc, thay vì chỉ dùng AI để viết code. Ý tưởng được gán cho [[andrej-karpathy]], triển khai bằng [[obsidian]] + [[claude-code]]. (nguồn: [[raw/karpathy-second-brain]])

## Cấu trúc 3 phần
- `raw/`: lưu nguồn gốc (bài báo, PDF, transcript, ghi chú cá nhân, nghiên cứu).
- `wiki/`: các trang kiến thức do Claude tạo và liên tục cập nhật.
- `CLAUDE.md`: hướng dẫn để AI biết cách xử lý cả kho dữ liệu.

## Quy trình
- Bỏ tài liệu mới vào `raw/`, nói "ingest tài liệu này".
- Claude đọc, trích xuất, tìm liên hệ với kiến thức đã có, rồi cập nhật wiki.
- Các liên hệ được nối bằng link chéo `[[...]]`: cú pháp xem [[obsidian-internal-links]].

## Giá trị
- Càng dùng lâu càng có giá trị, giống "lãi kép của tri thức": thông tin mới làm thông tin cũ hữu ích hơn.
- Không phải giải thích lại bối cảnh mỗi lần mở Claude; AI làm việc trên kho đã tích lũy.
- Hỏi lại thứ đã đọc, tìm liên hệ giữa hai ý cách nhau nhiều tháng, tiếp tục nghiên cứu không từ số 0.
- Thiết lập khoảng 5 phút.
