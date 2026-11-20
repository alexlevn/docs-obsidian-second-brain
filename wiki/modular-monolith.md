---
tags: [kiến-trúc, nodejs]
sources: [raw/yudi-nugraha-ai-olutions-engineer.md, raw/NestJS - A progressive Node.js framework.md]
updated: 2026-11-20
---

# Modular Monolith

Kiến trúc deploy 1 đơn vị như monolith, nhưng ranh giới giữa các module được ép bằng công cụ chứ không chỉ bằng quy ước. Nằm giữa Feature-Based Structure và Microservices. (nguồn: [[raw/yudi-nugraha-ai-olutions-engineer]])

## Vấn đề giải quyết
- Feature-Based (vd [[nestjs]], nơi module đã đóng gói provider qua `exports` nhưng vẫn không cấm import file nội bộ) cho phép import thẳng class nội bộ của module khác. Không ai ngăn, tới lúc refactor hoặc tách service mới thấy coupling đau.
- Quy tắc duy nhất: module khác chỉ được dùng những gì module đó **chủ động expose**.

## Cấu trúc
- Mỗi module có `public/` (hợp đồng: `index.ts`, module, DTO, event, port) và `internal/` (controller, service, repository, entity, test).
- `shared/` chứa event bus in-process, base entity.
- Luật: chỉ import từ `modules/*/public/index.ts`, cấm import `modules/*/internal/`.
- Ép luật bằng `eslint-plugin-boundaries` (lỗi ngay trong editor và CI), chạy thêm `test:arch` trong pipeline.

## Giao tiếp giữa module
- **Interface injection (port/adapter)**: module A định nghĩa port nó cần (vd `INotificationPort`), module B cung cấp adapter, nối ở `AppModule` (composition root). Dùng khi cần kết quả đồng bộ.
- **In-process event bus**: A publish event, B subscribe, hai bên không biết nhau. Dùng khi có thể async và muốn decouple tối đa.

## Database
- Một DB chung, mỗi module dùng prefix bảng riêng (`orders__orders`, `notifications__logs`).
- Cấm query chéo bảng của module khác, cần dữ liệu thì đi qua public API.

## Test
- Unit test trong module: như Feature-Based, mock repo và port.
- Contract test: chỉ test qua public API của module.
- Architecture test: ESLint trong CI.

## So sánh
| | Feature-Based | Modular Monolith | Microservices |
| --- | --- | --- | --- |
| Ranh giới module | Quy ước thư mục | Public API ép bằng ESLint | Network |
| Deploy | 1 | 1 | N |
| Độ phức tạp vận hành | Thấp | Thấp | Cao |
| Tách thành microservices | Khó | Dễ | không áp dụng |
| Database | Chung | Chung, prefix theo module | Riêng |

## Khi nào dùng
- Đã vượt Feature-Based, coupling chéo module bắt đầu đau.
- Có thể tách microservices sau này, muốn ranh giới sạch từ giờ.
- Team 10–30 dev nhưng vẫn muốn 1 deployment.
- Cần data consistency cao, không hợp distributed transaction.

## Ý chính
- Câu hỏi đúng không phải "khi nào lên microservices" mà là "ranh giới domain đã đủ rõ để tách chưa". Modular Monolith giúp trả lời trước khi trả giá vận hành của hệ phân tán.
