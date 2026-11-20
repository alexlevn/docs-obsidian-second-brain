---
tags: [nodejs, framework, kiến-trúc]
sources: [raw/NestJS - A progressive Node.js framework.md]
updated: 2026-11-20
---

# NestJS

Framework Node.js (TypeScript) tổ chức app thành các **module** + dependency injection. Trang này ghi phần Modules trong docs. (nguồn: [[raw/NestJS - A progressive Node.js framework]])

## Module
- Class gắn decorator `@Module()`, Nest dùng metadata để dựng **application graph** (quan hệ giữa module và provider).
- Mọi app có ít nhất 1 **root module** (`AppModule`). Module là cách tổ chức component được khuyến nghị.
- Metadata của `@Module()`:
  - `providers`: provider do Nest injector khởi tạo, dùng trong module.
  - `controllers`: controller của module.
  - `imports`: module khác mà module này cần provider của nó.
  - `exports`: phần `providers` cho module khác dùng (truyền provider hoặc token).
- Module **đóng gói provider mặc định**: chỉ inject được provider của chính module hoặc provider được `exports` từ module đã `imports`. `exports` chính là public API của module. (cùng tinh thần với [[modular-monolith]])
- App nhỏ có thể bỏ class root, truyền metadata thẳng vào `NestFactory.create()`. Tự viết class khi cần middleware (`configure()`), lifecycle hook, `app.select(AppModule)` hoặc để `nest generate` đăng ký.

## Feature module
- Gom controller + service cùng domain vào 1 module (vd `CatsModule`), rồi `imports` vào `AppModule`. Giữ ranh giới rõ khi app/team lớn.
- CLI: `nest g module cats`.

## Shared module + re-export
- Module mặc định là **singleton**, mọi module import nó dùng chung 1 instance provider.
- Muốn chia sẻ: thêm provider vào `exports`. Đăng ký service trực tiếp ở nhiều module thì mỗi module có 1 instance riêng, tốn bộ nhớ, state lệch nhau.
- Module có thể re-export module nó import (`imports: [CommonModule], exports: [CommonModule]`).
- Module class có thể inject provider (vd để config), nhưng bản thân module class không inject được vì circular dependency.

## Global module
- `@Global()` làm provider dùng được mọi nơi, không cần `imports`. Chỉ đăng ký 1 lần, thường ở root/core module.
- Không khuyến khích biến mọi thứ thành global: `imports` giữ API module chia sẻ có kiểm soát, tránh coupling không cần thiết.

## Dynamic module
- Module cấu hình lúc runtime qua static method, quy ước đặt tên `forRoot()` (có thể sync hoặc trả `Promise`).
- Object trả về **mở rộng** (không ghi đè) metadata của `@Module()`.
- Ví dụ: `FeatureFlagsModule.forRoot({ newCheckout: true })` đăng ký provider `FEATURE_FLAGS` (token hằng chuỗi), chỉ export `FeatureFlagsService`, flags là chi tiết nội bộ.
- Gọi `forRoot()` ở module khác sẽ tạo instance thứ hai. Muốn dùng chung: gọi 1 lần ở `CoreModule` rồi `exports: [FeatureFlagsModule]` (không gọi lại `forRoot()`).
- Đặt `global: true` trong object trả về để làm dynamic module global (cũng không khuyến khích).
- Option chỉ biết lúc runtime (vd từ `ConfigService`) cần bản async của `forRoot()`; xem thêm `ConfigurableModuleBuilder`.
