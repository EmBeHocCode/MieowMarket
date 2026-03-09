# MeowMarket

MeowMarket là marketplace dịch vụ số cho thị trường Việt Nam, xây dựng bằng Next.js App Router theo hướng production-ready với dữ liệu lõi đã chạy trên PostgreSQL qua Prisma.

Định hướng giao diện:

- cute
- mềm mại
- thân thiện
- hiện đại
- đủ tin cậy cho flow mua hàng số, gift card, VPS và cloud

## Tổng quan hiện tại

Project đã có:

- public storefront
- auth pages
- user dashboard
- admin dashboard
- Next.js API routes
- Prisma schema cho PostgreSQL
- payment flow mock-ready
- gift card fulfillment mock-ready
- VPS provisioning mock-ready
- media library
- AI tools mock

Trạng thái dữ liệu hiện tại:

- tài khoản, mật khẩu hash, session và password reset token đều nằm trong PostgreSQL
- sản phẩm, danh mục, banner, FAQ, review, coupon, order, payment, ticket, notification, media, audit log đều đọc từ SQL
- homepage metrics, admin charts và các thống kê chính đều tính từ dữ liệu SQL
- seed hiện có sẵn dữ liệu mẫu nên web không bị trống
- phần còn dùng mock chủ yếu là AI tools demo

## Tech stack

- Next.js 14 App Router
- TypeScript
- Tailwind CSS
- FontAwesome
- Zustand
- React Hook Form
- Zod
- Recharts
- Prisma ORM
- PostgreSQL
- bcryptjs
- Framer Motion

## Cấu trúc chính

```text
/src
  /app
    /(public)
    /(auth)
    /(dashboard)
    /(admin)
    /api
  /components
    /ui
    /layout
    /product
    /checkout
    /dashboard
    /admin
    /support
    /ai
    /shared
  /layouts
  /modules
  /services
  /lib
  /hooks
  /types
  /mock
  /config
  /utils
  /constants
  /store
  /validators
  /emails
  /providers
  /assets
  /styles
/prisma
/public
/data
/scripts
/ai-service
```

## Route chính

### Public

- `/`
- `/products`
- `/products/[slug]`
- `/search`
- `/cart`
- `/checkout`
- `/checkout/success`
- `/checkout/failure`
- `/support`
- `/contact`
- `/policies`
- `/promotions`
- `/about`
- `/order-lookup`

### Auth

- `/login`
- `/register`
- `/forgot-password`
- `/reset-password`

### User dashboard

- `/profile`
- `/profile/account`
- `/profile/security`
- `/profile/orders`
- `/profile/orders/[orderCode]`
- `/profile/payments`
- `/profile/purchases`
- `/profile/tickets`
- `/profile/tickets/[ticketId]`
- `/profile/wishlist`
- `/profile/services`
- `/profile/notifications`

### Admin

- `/admin`
- `/admin/products`
- `/admin/categories`
- `/admin/banners`
- `/admin/orders`
- `/admin/payments`
- `/admin/users`
- `/admin/tickets`
- `/admin/coupons`
- `/admin/giftcards`
- `/admin/services`
- `/admin/media`
- `/admin/notifications`
- `/admin/seo`
- `/admin/audit-log`
- `/admin/ai-tools`
- `/admin/settings`

## Dữ liệu và kiến trúc

### Storefront sản phẩm

- service chính: `src/services/product-service.ts`
- dữ liệu public product đang đọc từ Prisma
- seed mặc định hiện có sẵn 12 sản phẩm mẫu trong SQL

### Auth hiện tại

- register/login hoạt động qua API routes
- mật khẩu được hash bằng `bcryptjs`
- tài khoản, session và reset token lưu trong PostgreSQL
- session hiển thị nhanh qua cookie:
  - `meowmarket-session`
  - `meowmarket-role`
  - `meowmarket-user`
- file `data/mock-auth-users.json` chỉ còn là nguồn import legacy khi chạy seed cho môi trường dev cũ

### Dashboard user mới

Tài khoản mới đăng ký sẽ:

- chưa có đơn hàng
- chưa có thanh toán
- chưa có wishlist
- chưa có sản phẩm đã mua
- chưa có service

### Prisma seed hiện tại

`prisma/seed.ts` đang seed:

- admin mặc định
- user mặc định
- import thêm các tài khoản legacy từ `data/mock-auth-users.json` nếu file còn tồn tại
- category tree
- site setting
- banner
- FAQ
- coupon
- 12 sản phẩm mẫu
- review
- favorite
- order, order item, payment
- gift card codes
- service records và VPS instance
- support tickets và ticket messages
- notifications
- media assets
- audit logs

## Tài khoản dev

### Tài khoản mặc định

- User
  - email: `user@meowmarket.vn`
  - password: `123456`
- Admin
  - email: `admin@meowmarket.vn`
  - password: `123456`

### Register

- đăng ký thành công sẽ chuyển về `/login`
- login sẽ dùng chính tài khoản vừa đăng ký

## Favicon và brand assets

Favicon hiện dùng nguồn từ:

- `src/assets/favicons/favicon.gif`

Project đang expose ra browser theo các file:

- `public/favicon.ico`
- `public/favicons/favicon.png`
- `public/favicons/favicon.gif`

Metadata favicon nằm tại:

- `src/app/layout.tsx`

## Cài đặt local

### 1. Cài dependency

```bash
npm install
```

### 2. Tạo file môi trường

```bash
copy .env.example .env
```

Ví dụ:

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/meowmarket"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

### 3. Khởi động PostgreSQL local

```bash
npm run db:start
```

### 4. Generate Prisma client

```bash
npm run prisma:generate
```

### 5. Đồng bộ schema ra database

```bash
npm run db:push
```

### 6. Seed dữ liệu nền

```bash
npm run prisma:seed
```

### 7. Chạy project

```bash
npm run dev
```

Mặc định script này sẽ cố chạy tại:

```text
http://localhost:3000
```

## Dev workflow

`npm run dev` đã được đổi sang script:

- `scripts/dev-single-instance.js`

Script này sẽ:

- dừng các tiến trình Next.js cũ của chính project
- giải phóng port `3000`, `3001`, `3002`
- xóa cache `.next`
- chạy lại MeowMarket cố định ở `3000`

Điểm cần lưu ý:

- script có thể dừng app khác nếu app đó đang chiếm `3000`, `3001` hoặc `3002`
- khi sửa code lúc dev server đang chạy, Next.js sẽ tự hot reload
- không cần chạy lại `npm run dev` sau mỗi lần sửa giao diện hay logic thông thường
- nếu đổi dependency, script dev, env, hoặc config lớn thì nên chạy lại dev server

Nếu cần chạy raw dev mặc định của Next.js:

```bash
npm run dev:raw
```

## Scripts

```bash
npm run dev
npm run dev:raw
npm run build
npm run start
npm run lint
npm run db:start
npm run db:stop
npm run db:down
npm run db:push
npm run prisma:generate
npm run prisma:migrate
npm run prisma:seed
```

## Mock systems đang có

### Gift card fulfillment

- model: `GiftCardCode`
- status:
  - `AVAILABLE`
  - `RESERVED`
  - `SOLD`
  - `USED`
  - `EXPIRED`
- flow mock:
  - reserve code
  - assign code vào order
  - hiển thị code trong order detail
  - sẵn cấu trúc để gửi email sau này

### VPS / Cloud fulfillment

- tạo `ServiceRecord`
- tạo `VpsInstance` mock sau thanh toán
- hiển thị trong `My Services`
- sẵn cấu trúc để sau này gọi provider API thật

### Payment mock

- VNPay
- MoMo
- ZaloPay
- callback/webhook mock
- success/failure pages

## Những phần vẫn đang là mock

- đơn hàng mẫu trong dashboard
- ticket mẫu
- analytics mẫu
- notification mẫu
- một phần admin tables
- AI insights mẫu

Những phần này đã được tách riêng để sau này thay bằng:

- Prisma queries
- CRUD APIs
- queue / webhook / external provider integrations

## Mục tiêu và kế hoạch phát triển tiếp theo

### Giai đoạn 1: Hoàn thiện tính năng lõi

- thay auth dev bằng Auth.js hoặc JWT thật
- hoàn thiện CRUD sản phẩm, danh mục, banner và coupon trong admin
- bổ sung trang chi tiết đơn hàng và chi tiết thanh toán đầy đủ
- thêm upload ảnh thật cho sản phẩm, banner, logo và avatar
- chuẩn hóa empty state, loading state và trạng thái lỗi ở toàn bộ public pages

### Giai đoạn 2: Hoàn thiện vận hành thương mại

- gắn payment gateway thật cho VNPay, MoMo và ZaloPay
- thêm webhook thanh toán và đồng bộ trạng thái đơn tự động
- gắn email service cho xác nhận đơn, giao mã gift card và reset mật khẩu
- hoàn thiện gift card inventory flow với nhập mã, reserve mã, sold log và delivery log
- hoàn thiện VPS provisioning adapter để nối với nhà cung cấp thật

### Giai đoạn 3: Mở rộng dashboard và vận hành nội bộ

- hoàn thiện admin tables thành CRUD đầy đủ có filter, search và pagination
- thêm audit log chi tiết hơn cho user, order, payment và media
- thêm notification center realtime hoặc polling tốt hơn
- bổ sung dashboard analytics theo doanh thu, tồn kho, sản phẩm bán chạy và tăng trưởng người dùng
- thêm vai trò và phân quyền chi tiết hơn ngoài `USER` và `ADMIN`

### Giai đoạn 4: Tối ưu chất lượng và scale

- thêm test automation cho auth, checkout, order flow và admin flow
- bổ sung seed dữ liệu phong phú hơn cho sản phẩm, đơn hàng và ticket
- chuẩn bị tách backend riêng nếu cần, ví dụ NestJS hoặc service nội bộ
- kết nối Next.js API với FastAPI AI service cho AI tools
- tối ưu SEO, hiệu năng, cache và triển khai production

## Kiểm tra chất lượng

Đã kiểm tra:

- `npm run lint`
- `npm run build`

Ở trạng thái hiện tại, cả hai đều pass.

## Giấy phép

Project này được phát hành theo giấy phép MIT.

- Xem chi tiết tại file `LICENSE`
- Copyright (c) 2026 EmBeHocCode
