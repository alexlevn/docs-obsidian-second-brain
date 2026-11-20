# Hướng dẫn dựng "Second brain" bằng Obsidian + Claude Code

Ý tưởng: bỏ mọi thứ mình đọc (bài báo, PDF, transcript, ghi chú) vào thư mục `raw/`, rồi để Claude Code tự đọc và viết thành một wiki kiến thức có link chéo trong `wiki/`. Obsidian dùng để đọc wiki và xem graph.

Yêu cầu: máy Mac, đã cài Claude Code (lệnh `claude` chạy được trong Terminal), có Homebrew.

## Vault là gì?

- **Vault = 1 thư mục bình thường trên máy.** Mỗi note là 1 file `.md` trong thư mục đó; mở Finder cũng thấy.
- **Obsidian chỉ là cái kính để nhìn.** Nó không giữ dữ liệu ở đâu khác; gỡ Obsidian thì file vẫn còn.
- **Mọi công cụ dùng chung thư mục đó:** Obsidian đọc, Claude Code đọc/ghi, Git lưu lên GitHub.
- Thư mục ẩn `.obsidian/` bên trong chứa setting riêng của vault (vd file đã ẩn khỏi graph).
- Có thể có nhiều vault (vd công việc / cá nhân), mỗi vault là 1 thư mục riêng.

---

## Bước 1: Tạo thư mục vault

1. Mở Terminal.
2. Gõ từng lệnh, mỗi lệnh xong bấm **Enter**:
   ```
   mkdir -p ~/Documents/obsidian-second-brain/raw ~/Documents/obsidian-second-brain/wiki
   cd ~/Documents/obsidian-second-brain
   git init
   ```
3. Tạo file `CLAUDE.md` trong thư mục này, nội dung ở **Phụ lục A** cuối bài. File này dạy Claude cách ingest.
4. Tạo file `wiki/index.md` với 1 dòng `# Index`, và `wiki/log.md` với 1 dòng `# Log`.

## Bước 2: Cài Obsidian

1. Trong Terminal, gõ rồi bấm **Enter**:
   ```
   brew install --cask obsidian
   ```
2. Đợi tới khi thấy dòng `obsidian was successfully installed!`.

## Bước 3: Mở vault

1. Bấm **Cmd + phím cách (Space)**, gõ `Obsidian`, bấm **Enter**.
2. Màn hình đầu có 3 lựa chọn. Bấm **Open folder as vault** (không bấm "Create new vault").
3. Chọn thư mục `Documents/obsidian-second-brain`, bấm **Open**.
4. Nếu hỏi "Trust author and enable plugins?", bấm **Trust**.
5. Kiểm tra: cột trái hiện `raw`, `wiki`, `CLAUDE`.

## Bước 4: Chỉnh setting link

**4.1. Mở Settings**
1. Giữ phím **Cmd**, bấm phím **dấu phẩy `,`** (phím cạnh chữ M).
   - Lưu ý: là **dấu phẩy**, không phải dấu cộng. Cmd + dấu cộng là phóng to màn hình (bấm **Cmd + số 0** để về lại).
   - Cách khác: bấm **icon bánh răng** ở góc dưới bên trái, cạnh tên vault.
2. Cửa sổ Settings hiện ra, đang ở trang **General**.

**4.2. Vào trang Files and links**
1. Nhìn cột trái, đếm từ trên xuống: General, Appearance, Interface, Editor, **Files and links**.
2. Bấm **Files and links** (dòng thứ 5).
3. Cuộn phần bên phải xuống tới nhóm **Links**.

**4.3. Kiểm tra 3 dòng trong nhóm Links**
| Dòng | Cần để |
|---|---|
| New link format | **Shortest path when possible** (bấm ô bên phải để chọn) |
| Automatically update internal links | **Bật** (công tắc tím) |
| Use [[Wikilinks]] | **Bật** (công tắc tím) |

- Công tắc **tím, nút tròn bên phải** = đang bật.
- Công tắc **xám, nút tròn bên trái** = đang tắt → bấm 1 lần để bật.

**4.4. Ẩn file hướng dẫn khỏi graph**
1. Cuộn phần bên phải xuống tận cuối, tìm dòng **Excluded files**.
2. Bấm nút **Manage** bên phải dòng đó.
3. Màn hình đổi sang trang "Excluded files", hiện chữ "No exclusions added."
4. Bấm **dấu `+`** nhỏ ở góc trên bên phải.
5. Cửa sổ **Add exclusion** hiện ra, kèm danh sách nhỏ gợi ý `wiki`, `raw`.
   - **Không bấm** vào `wiki` hay `raw`: chọn là ẩn mất cả thư mục kiến thức.
6. Gõ `CLAUDE.md`, bấm nút tím **Done**.
7. Lặp lại bước 4–6 cho các file hướng dẫn khác (vd `TUTORIAL.md`, `NEXT-SESSION.md`).
8. Bấm **Esc** để đóng Settings.

## Bước 5: Thêm tài liệu đầu tiên vào raw/

1. Ở cột trái Obsidian, bấm **chuột phải** vào thư mục **raw** (touchpad: bấm 2 ngón).
2. Bấm **New note**.
3. Note mới tên "Untitled" hiện ra, tên đang bôi sáng. Gõ tên ngắn không dấu, vd `karpathy-second-brain`, bấm **Enter**.
4. Kiểm tra: thanh trên cùng hiện `raw / karpathy-second-brain`.
5. Dòng đầu gõ nguồn: `Nguồn: <link bài gốc>`, bấm **Enter** 2 lần.
6. Mở bài gốc trên trình duyệt, bôi đen nội dung, bấm **Cmd + C**.
7. Quay lại Obsidian, bấm vào dòng trống, bấm **Cmd + V**.
8. Xoá các dòng rác: hashtag kèm link theo dõi dài, menu web như "Links to this page"... Cách xoá: đặt chuột trước dòng đầu, giữ chuột trái kéo tới hết dòng cuối, bấm **Delete**. Obsidian tự lưu, không cần bấm Save.

## Bước 6: Cho Claude ingest

1. Mở một cửa sổ Terminal **mới**.
2. Gõ rồi bấm **Enter**:
   ```
   cd ~/Documents/obsidian-second-brain
   ```
3. Gõ `claude` rồi bấm **Enter**. Claude tự đọc `CLAUDE.md` để biết luật.
4. Gõ rồi bấm **Enter**:
   ```
   ingest raw/karpathy-second-brain.md
   ```
5. Đợi khoảng 20 giây. Claude báo đã tạo trang nào trong `wiki/` và cập nhật `index.md`, `log.md`.

## Bước 7: Xem kết quả

1. Quay lại Obsidian, bấm mũi tên **›** cạnh thư mục **wiki** để mở.
2. Bấm 1 trang (vd `second-brain`). Chữ **tím** là link, bấm vào sẽ nhảy sang trang đó.
3. Bấm **Cmd + G** để mở Graph view.
4. Mỗi **chấm** là 1 trang, **đường kẻ** là link giữa 2 trang. Chấm `log` đứng riêng là bình thường.
5. Kéo chấm bằng chuột, lăn chuột để phóng to/thu nhỏ.

## Bước 8: Lưu lên GitHub

1. Trong Terminal, bấm **Cmd + T** mở tab mới, rồi `cd ~/Documents/obsidian-second-brain`.
2. Gõ lần lượt:
   ```
   git add .
   git commit -m "Init vault and ingest first source"
   gh repo create obsidian-second-brain --private --source=. --push
   ```
3. Để repo **private** vì đây là kho kiến thức cá nhân.

## Bước 9: Ingest tài liệu thứ 2

Mục tiêu: thấy Claude **sửa trang cũ** chứ không chỉ tạo trang mới.

1. Chọn bài **liên quan tới trang đã có**. Vd đã có trang `obsidian` → chọn bài chính thức `https://obsidian.md/help/links` (Internal links).
2. Làm như Bước 5: tạo note `raw/obsidian-internal-links`, ghi nguồn, copy toàn bài, dán, xoá phần "Links to this page" ở cuối.
3. Trong Claude của vault, gõ:
   ```
   ingest raw/obsidian-internal-links.md
   ```
4. Kết quả đúng phải có 2 phần:
   - **Tạo mới**: `wiki/obsidian-internal-links.md`
   - **Sửa**: `wiki/obsidian.md` (thêm link sang trang mới + thêm nguồn), `index.md`, `log.md`
5. Mở trang mới trong Obsidian: thấy link tím tới `obsidian`, `second-brain` = kiến thức mới đã nối vào kiến thức cũ.

## Bước 10: Hỏi lại kho

1. Hỏi câu wiki **có**:
   ```
   hỏi: làm sao link tới một heading trong note khác?
   ```
   → Claude trả lời `[[Note#Heading]]` và dẫn link `[[obsidian-internal-links]]`.
2. Hỏi câu wiki **chưa có** (để kiểm tra Claude không bịa):
   ```
   hỏi: Obsidian Sync giá bao nhiêu?
   ```
   → Claude phải nói "wiki chưa có", và gợi ý đưa nguồn vào `raw/` để ingest.
3. Lưu ý: chữ mờ trong ô gõ của Claude Code chỉ là **gợi ý**, chưa gửi. Không bấm Tab/Enter nếu không muốn làm việc đó.

## Bước 11: Lint (dọn kho, mỗi tuần 1 lần)

1. Gõ:
   ```
   lint
   ```
2. Claude báo danh sách: mâu thuẫn, trang mồ côi, link hỏng, trang quá dài, gợi ý thêm link. **Chưa sửa gì.**
3. Muốn sửa thì gõ tiếp: `sửa theo gợi ý lint đi`.
4. Commit sau mỗi buổi:
   ```
   git add .
   git commit -m "Ingest <tên tài liệu>"
   git push
   ```
   Dòng cuối có `main -> main` là xong.

## Dùng hằng ngày

| Việc | Gõ cho Claude (trong thư mục vault) |
|---|---|
| Thêm kiến thức | `ingest raw/<tên-file>.md` |
| Hỏi lại | `hỏi: tôi đã đọc gì về X?` |
| Dọn kho (mỗi tuần) | `lint` |

Nguyên tắc: **ít mà đều**, mỗi ngày 1–2 tài liệu thật sự đọc. Sau mỗi buổi thì commit.

---

## Phụ lục A: nội dung CLAUDE.md

````markdown
# Second brain vault (Obsidian + Claude Code)

Kho kiến thức cá nhân. Viết bằng tiếng Việt, giữ nguyên thuật ngữ tiếng Anh.

## Cấu trúc
- `raw/`: tài liệu gốc. **Chỉ đọc, không sửa, không xoá.**
- `wiki/`: trang kiến thức do Claude viết. Mỗi trang = 1 khái niệm / người / công cụ / chủ đề.
- `wiki/index.md`: mục lục toàn bộ wiki, chia nhóm theo chủ đề.
- `wiki/log.md`: mỗi lần ingest thêm 1 dòng `YYYY-MM-DD: <nguồn> → <các trang đã tạo/sửa>`.

## Quy tắc trang wiki
- Tên file kebab-case, tiếng Anh không dấu.
- Đầu trang có frontmatter: tags, sources, updated.
- Mở đầu bằng 1–2 câu tóm tắt. Ngắn gọn, gạch đầu dòng.
- Link chéo bằng `[[ten-trang]]` mỗi khi nhắc tới khái niệm đã có trang.
- Mỗi ý quan trọng ghi nguồn.

## Lệnh
- **"ingest <file>"**: đọc file → cập nhật trang cũ trước, chỉ tạo trang mới khi chưa có → link chéo hai chiều → cập nhật index + log → báo ngắn.
- **"hỏi: <câu hỏi>"**: trả lời dựa trên `wiki/`, dẫn link trang. Chưa có thì nói chưa có, không bịa.
- **"lint"**: tìm trang mâu thuẫn, trang mồ côi, link hỏng, trang quá dài → báo danh sách, chưa sửa.
````
