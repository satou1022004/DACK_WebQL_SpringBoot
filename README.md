# ☕ WebQuanLyCafe – Hệ thống quản lý quán cà phê

Ứng dụng web quản lý quán cà phê xây dựng bằng **Spring Boot**, hỗ trợ khách đặt món tại bàn, nhân viên xử lý đơn theo thời gian thực và quản trị viên theo dõi doanh thu, nhân sự, thực đơn.

> **Đồ án môn Lập trình Web / Quản lý** – Sinh viên: **Nguyễn Quang Thành** – MSSV: **2280602948** – Lớp 22DTHE8 – Đại học HUTECH

---

## 📌 Tính năng

### 👤 Khách hàng (Customer)
- Vào quán bằng **chế độ khách** (không cần tài khoản) hoặc **đăng ký / đăng nhập** tài khoản.
- Chọn số bàn, xem thực đơn theo danh mục.
- Thêm món vào giỏ, tạo đơn, xác nhận hoặc huỷ đơn.
- Theo dõi tiến độ đơn **realtime** (Server-Sent Events).
- Khách có tài khoản: xem hồ sơ và lịch sử giao hàng (delivery).

### 🧑‍🍳 Nhân viên (Staff)
- Xem **hàng đợi đơn** cập nhật realtime (SSE).
- Xác nhận đơn, ghi nhận thanh toán (tiền mặt / chuyển khoản).
- Xem ca làm của bản thân.
- Xem, thêm mới, cập nhật sản phẩm (giá, tình trạng còn hàng...).

### 🛠️ Quản trị viên (Admin)
- Dashboard thống kê trong ngày.
- Quản lý **sản phẩm** và **danh mục** (CRUD).
- Quản lý **nhân viên** và **phân ca** theo thứ / buổi (sáng – chiều – tối).
- Báo cáo **doanh thu** theo khoảng thời gian, theo sản phẩm, có biểu đồ và **xuất file CSV**.

---

## 🧰 Công nghệ sử dụng

| Thành phần | Công nghệ |
|---|---|
| Ngôn ngữ | Java 17 |
| Framework | Spring Boot 4.0.5 (Web, Validation, Security, Data JPA) |
| Giao diện | Thymeleaf + HTML/CSS/JavaScript thuần |
| Cơ sở dữ liệu | MySQL |
| Bảo mật | Spring Security (session + cookie `JSESSIONID`, phân quyền theo vai trò, mã hoá mật khẩu BCrypt) |
| Realtime | Server-Sent Events (SseEmitter) |
| Công cụ | Maven (Maven Wrapper), Lombok |

---

## 🗂️ Cấu trúc thư mục

```
WebQuanLyCafe/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/com/example/WebCafe/
    │   │   ├── config/        # SecurityConfig, xử lý lỗi 401/403
    │   │   ├── controller/    # REST API + controller trả trang HTML
    │   │   ├── dto/           # request / response
    │   │   ├── model/         # entity JPA + enums
    │   │   ├── repository/    # Spring Data JPA
    │   │   ├── security/      # Roles, CustomerPrincipal (khách vãng lai)
    │   │   └── service/       # nghiệp vụ (interface + Impl, SSE events)
    │   └── resources/
    │       ├── schema/        # WebCafe.sql, mockdata.sql
    │       ├── static/js/     # cart-store, menu-page, order-page, customer-session
    │       ├── templates/     # các trang Thymeleaf
    │       └── application.properties
    └── test/
```

---

## 🚀 Hướng dẫn cài đặt và chạy

### 1. Yêu cầu
- **JDK 17** trở lên
- **MySQL 8** (hoặc tương thích)
- Maven (hoặc dùng sẵn `mvnw` / `mvnw.cmd` trong project)

### 2. Clone project
```bash
git clone https://github.com/satou1022004/DACK_WebQL_SpringBoot.git
cd DACK_WebQL_SpringBoot/WebQuanLyCafe
```

### 3. Tạo cơ sở dữ liệu
Chạy lần lượt hai file SQL trong MySQL:

```bash
mysql -u root -p < src/main/resources/schema/WebCafe.sql
mysql -u root -p < src/main/resources/schema/mockdata.sql
```

- `WebCafe.sql`: tạo database `WebCafe`, các bảng và dữ liệu ca làm.
- `mockdata.sql`: dữ liệu mẫu (tài khoản, bàn, danh mục, sản phẩm, đơn hàng). Chạy **sau** `WebCafe.sql`, trên database trống.

### 4. Cấu hình kết nối
Mở `src/main/resources/application.properties` và chỉnh cho đúng máy của bạn:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/WebCafe?useSSL=false&serverTimezone=Asia/Ho_Chi_Minh&characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=<mật khẩu MySQL của bạn>
```

### 5. Chạy ứng dụng
```bash
# Linux / macOS
./mvnw spring-boot:run

# Windows
mvnw.cmd spring-boot:run
```

Mở trình duyệt tại: **http://localhost:8080**

---

## 🔑 Tài khoản dùng thử

Mật khẩu của **tất cả** tài khoản mẫu: `123456`

| Vai trò | Tên đăng nhập | Ghi chú |
|---|---|---|
| Admin | `admin01` | Quản trị toàn bộ hệ thống |
| Staff | `staff01` … `staff05` | Nhân viên |
| Customer | `cust01` … `cust10` | Khách có tài khoản |
| Customer (khách) | – | Chọn chế độ khách trên trang đăng nhập, không cần tài khoản |

---

## 🔐 Phân quyền

| Đường dẫn | Quyền truy cập |
|---|---|
| `/`, `/login`, `/register`, `/menu`, `/order`, `/api/menu`, `/api/auth/**` | Công khai |
| `/api/customer/**` | `CUSTOMER` |
| `/staff/**`, `/api/staff/**` | `STAFF` |
| `/admin/**`, `/api/admin/**` | `ADMIN` |
| Các đường dẫn còn lại | Từ chối |

---

## 🔄 Luồng xử lý đơn hàng

```
Khách chọn bàn → thêm món vào giỏ → xác nhận đơn
        │
        ▼
   PENDING ──(staff xác nhận)──► PREPARING ──► DONE ──(thanh toán)──► PAID
```

- Trạng thái đơn: `PENDING` → `PREPARING` → `DONE` → `PAID`
- Phương thức thanh toán: `CASH`, `BANKING`
- Trạng thái giao hàng: `PENDING`, `CONFIRMED`, `SHIPPING`, `DELIVERED`, `CANCELLED`
- Khách và nhân viên nhận cập nhật tức thời qua SSE.

---

## 🌐 Tổng hợp API chính

<details>
<summary><b>Auth</b> – <code>/api/auth</code></summary>

| Method | Endpoint | Mô tả |
|---|---|---|
| POST | `/api/auth/login` | Đăng nhập (`mode`: `CUSTOMER` / `STAFF` / `ADMIN`) |
| POST | `/api/auth/logout` | Đăng xuất |
| GET | `/api/auth/me` | Thông tin phiên hiện tại |
| POST | `/api/auth/register` | Đăng ký khách hàng |

</details>

<details>
<summary><b>Customer</b> – <code>/api/customer</code></summary>

| Method | Endpoint | Mô tả |
|---|---|---|
| POST / GET | `/table` | Chọn / xem bàn hiện tại |
| GET | `/menu` | Danh sách món |
| GET | `/profile` | Hồ sơ khách (chỉ tài khoản đã đăng ký) |
| GET | `/deliveries` | Danh sách giao hàng |
| GET / POST | `/orders` | Danh sách đơn / tạo giỏ hàng |
| GET | `/orders/{id}` | Chi tiết đơn |
| POST | `/orders/{id}/items` | Thêm món vào đơn |
| POST | `/orders/{id}/confirm` | Xác nhận đơn |
| POST | `/orders/{id}/cancel` | Huỷ đơn |
| GET | `/orders/{id}/events` | Theo dõi tiến độ (SSE) |

</details>

<details>
<summary><b>Staff</b> – <code>/api/staff</code></summary>

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/queue` | Hàng đợi đơn |
| GET | `/queue/events` | Cập nhật hàng đợi (SSE) |
| POST | `/orders/{id}/confirm` | Xác nhận đơn |
| POST | `/orders/{id}/payment` | Ghi nhận thanh toán |
| GET | `/me/shifts` | Ca làm của tôi |
| GET / POST | `/products` | Xem / thêm sản phẩm |
| PATCH | `/products/{id}` | Cập nhật sản phẩm |
| GET | `/categories` | Danh sách danh mục |

</details>

<details>
<summary><b>Admin</b> – <code>/api/admin</code></summary>

| Method | Endpoint | Mô tả |
|---|---|---|
| GET / POST / PUT / DELETE | `/products`, `/products/{id}` | Quản lý sản phẩm |
| GET / POST / PUT / DELETE | `/categories`, `/categories/{id}` | Quản lý danh mục |
| GET | `/shifts` | Danh sách ca làm |
| GET / POST / PUT / DELETE | `/staff`, `/staff/{userId}` | Quản lý nhân viên |
| GET / PUT | `/staff/{userId}/shifts` | Xem / cập nhật ca làm của nhân viên |
| GET | `/revenue` | Báo cáo doanh thu |
| GET | `/revenue/export` | Xuất doanh thu ra CSV |
| GET | `/dashboard/today` | Thống kê hôm nay |

</details>

---

## 🗄️ Mô hình dữ liệu

Các bảng chính: `users`, `staff`, `admins`, `customers`, `shifts`, `staff_shifts`, `cafe_tables`, `categories`, `products`, `orders`, `order_items`, `payments`, `deliveries`.

- `staff`, `admins`, `customers` kế thừa thông tin chung từ `users` (khoá chính `user_id`).
- `staff` ↔ `shifts` quan hệ nhiều–nhiều qua `staff_shifts`.
- Mỗi `delivery` gắn với đúng một `order` và một `customer`.

---

## 📝 Ghi chú

- Mặc định `spring.jpa.hibernate.ddl-auto=update`, nên nên tạo database bằng file SQL trước khi chạy để dữ liệu mẫu khớp với cấu trúc bảng.
- Không đưa mật khẩu database thật lên GitHub; khi triển khai hãy dùng biến môi trường hoặc profile riêng.

---

## 👨‍💻 Tác giả

**Nguyễn Quang Thành** – MSSV 2280602948 – Lớp 22DTHE8 – HUTECH
GitHub: [satou1022004](https://github.com/satou1022004)
