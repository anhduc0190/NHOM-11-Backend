
# BÁO CÁO BÀI TẬP LỚN

## Xây dựng Backend Quản lý Cho thuê Thiết bị Xây dựng (ConstructionRental)

---

## PHẦN I: MỞ ĐẦU


### 1. Tên đề tài
**"Xây dựng Backend Quản lý Cho thuê Thiết bị Xây dựng (ConstructionRental)"**


### 2. Tính cấp thiết của đề tài

Quản lý cho thuê thiết bị xây dựng là nhu cầu thực tế của các doanh nghiệp xây dựng, giúp tối ưu hóa việc sử dụng tài sản, giảm thất thoát, tăng hiệu quả kinh doanh. Việc quản lý thủ công dễ dẫn đến sai sót, thất lạc thiết bị, khó kiểm soát doanh thu, bảo trì và bồi thường thiết bị hỏng hóc.

**Giải pháp**: Xây dựng hệ thống Backend tập trung, hiện đại, tự động hóa quy trình quản lý thiết bị, đơn thuê, hóa đơn, thanh toán, báo cáo, phân quyền người dùng, đảm bảo minh bạch, chính xác và dễ mở rộng.


### 3. Mục tiêu đề tài

1. **Thiết kế cơ sở dữ liệu** cho hệ thống quản lý cho thuê thiết bị xây dựng (10+ bảng)
2. **Xây dựng API Backend** với ASP.NET Core hỗ trợ quản lý thiết bị, danh mục, người dùng, đơn thuê, hóa đơn, thanh toán, bồi thường, báo cáo
3. **Triển khai logic nghiệp vụ**: kiểm tra tồn kho, tính phí thuê, quản lý bồi thường, báo cáo doanh thu, thiết bị hỏng
4. **Phát triển hệ thống báo cáo**: doanh thu, thiết bị đang thuê, thiết bị hỏng, lịch sử giao dịch
5. **Đảm bảo bảo mật, phân quyền** (Admin, Nhân viên, Người dùng)


### 4. Phạm vi công việc

- **Phạm vi dữ liệu**: Quản lý hàng trăm thiết bị, nhiều loại danh mục, nhiều người dùng, đơn thuê, hóa đơn, thanh toán, bồi thường
- **Phạm vi chức năng**: Quản lý thiết bị, danh mục, người dùng, đơn thuê, hóa đơn, thanh toán, bồi thường, báo cáo
- **Phạm vi công nghệ**: Backend RESTful API (ASP.NET Core 10, Entity Framework, SQL Server)
- **Phạm vi thời gian**: 1 kỳ học (4-5 tháng)

---


## PHẦN II: PHÂN TÍCH YÊU CẦU HỆ THỐNG

### 1. Phân tích đối tượng sử dụng

| **Nhóm người dùng** | **Vai trò**         | **Nhu cầu chính** |
| **Admin**           | Quản trị hệ thống   | Quản lý người dùng, danh mục, thiết bị, báo cáo tổng hợp    |
| **Nhân viên**       | Quản lý vận hành    | Tạo đơn thuê, trả thiết bị, lập hóa đơn, ghi nhận bồi thường |
| **Khách hàng**      | Người thuê thiết bị | Xem thiết bị, tạo đơn thuê, xem hóa đơn, thanh toán |

**Kết luận**: Hệ thống tập trung vào **Backend quản lý nghiệp vụ cho thuê thiết bị**, hỗ trợ 3 loại người dùng với quyền hạn khác nhau.


### 2. Phân tích yêu cầu chức năng chính

#### **Nhóm chức năng A: Quản lý dữ liệu nền**
**Mục tiêu:** Đảm bảo dữ liệu nền (danh mục, thiết bị, người dùng) luôn chính xác, đầy đủ, dễ quản lý.

- **Quản lý danh mục thiết bị:**
  - Thêm/sửa/xóa danh mục (CRUD), mỗi danh mục có mã, tên, mô tả.
  - Kiểm tra trùng mã, tên khi thêm mới.
  - Giao diện admin cho phép lọc, tìm kiếm danh mục.
- **Quản lý thiết bị:**
  - Thêm/sửa/xóa thiết bị, cập nhật số lượng, trạng thái (sẵn sàng, đang thuê, đang sửa).
  - Mỗi thiết bị liên kết với một danh mục, có thông số kỹ thuật, giá thuê theo ngày/tháng.
  - Kiểm tra tồn kho khi cập nhật số lượng.
  - Giao diện admin cho phép lọc thiết bị theo danh mục, trạng thái.
- **Quản lý người dùng:**
  - Thêm/sửa/xóa người dùng, phân quyền (Admin, Staff, User).
  - Kiểm tra trùng username/email khi đăng ký.
  - Quản lý trạng thái tài khoản (active/inactive).
  - Giao diện admin cho phép tìm kiếm, lọc theo vai trò.

**Luồng nghiệp vụ ví dụ:**
1. Admin đăng nhập → vào trang quản lý danh mục → thêm mới danh mục → kiểm tra trùng mã → lưu vào DB.
2. Admin vào trang quản lý thiết bị → thêm thiết bị mới → chọn danh mục → nhập thông số kỹ thuật → lưu.
3. Admin quản lý người dùng → thêm tài khoản staff → phân quyền → gửi thông báo tạo tài khoản.

#### **Nhóm chức năng B: Nghiệp vụ cho thuê**
**Mục tiêu:** Quản lý toàn bộ quy trình cho thuê thiết bị, từ tạo đơn đến trả thiết bị, lập hóa đơn, xử lý bồi thường.

- **Tạo đơn thuê thiết bị:**
  - Người dùng chọn thiết bị, nhập số lượng, thời gian thuê.
  - Hệ thống kiểm tra tồn kho, xác nhận khả dụng.
  - Sinh đơn thuê, lưu trạng thái "Pending".
- **Quản lý đơn thuê:**
  - Staff xác nhận đơn thuê, cập nhật trạng thái thiết bị (đang thuê).
  - Theo dõi lịch sử thuê, trả thiết bị, cập nhật trạng thái khi trả.
- **Lập hóa đơn, ghi nhận thanh toán:**
  - Khi đơn thuê hoàn thành, hệ thống tự động sinh hóa đơn.
  - Người dùng thanh toán qua nhiều phương thức (tiền mặt, chuyển khoản).
  - Staff xác nhận thanh toán, cập nhật trạng thái hóa đơn.
- **Bồi thường thiết bị hỏng:**
  - Nếu thiết bị hỏng/mất, staff ghi nhận bồi thường, sinh phiếu bồi thường.
  - Liên kết với hóa đơn, cập nhật số tiền bồi thường.

**Luồng nghiệp vụ ví dụ:**
1. User đăng nhập → chọn thiết bị → tạo đơn thuê → hệ thống kiểm tra tồn kho → lưu đơn thuê.
2. Staff xác nhận đơn thuê → thiết bị chuyển sang trạng thái "đang thuê" → cập nhật lịch sử.
3. Khi trả thiết bị → staff kiểm tra thiết bị → cập nhật trạng thái → nếu hỏng, ghi nhận bồi thường.
4. Hệ thống sinh hóa đơn → user thanh toán → staff xác nhận → cập nhật trạng thái hóa đơn.

#### **Nhóm chức năng C: Báo cáo & Thống kê**
**Mục tiêu:** Cung cấp thông tin tổng hợp, thống kê giúp quản lý hiệu quả, ra quyết định nhanh.

- **Báo cáo doanh thu:**
  - Thống kê tổng doanh thu theo tháng/quý/năm.
  - Lọc theo trạng thái hóa đơn (Paid, Unpaid, Overdue).
- **Báo cáo thiết bị đang thuê/hỏng:**
  - Thống kê số lượng thiết bị đang thuê, còn lại, đang sửa chữa.
  - Lọc theo danh mục, trạng thái.
- **Báo cáo lịch sử giao dịch:**
  - Thống kê các đơn thuê, hóa đơn, thanh toán theo từng user.
  - Xuất file Excel/PDF nếu cần.
- **Thống kê thiết bị theo danh mục, trạng thái:**
  - Tổng hợp số lượng thiết bị theo từng danh mục.
  - Thống kê trạng thái thiết bị (sẵn sàng, đang thuê, đang sửa).

**Luồng nghiệp vụ ví dụ:**
1. Admin vào trang báo cáo → chọn thời gian → xem tổng doanh thu, số lượng thiết bị đang thuê.
2. Staff xem báo cáo thiết bị hỏng → lên kế hoạch bảo trì.
3. Admin xuất báo cáo lịch sử giao dịch → phân tích hiệu quả kinh doanh.

---


## PHẦN III: THIẾT KẾ CƠ SỞ DỮ LIỆU

### 1. Sơ đồ Lôgic ER (Entity-Relationship)

```
┌──────────────────────────────┐
│ User (PK: user_id)           │
└──────────────┬───────────────┘
               │1
               │
               │N (FK: user_id)
┌──────────────▼───────────────┐
│ RentalOrder (PK: id)         │
│   - user_id (FK -> User)     │
└──────────────┬───────────────┘
               │1
               │
               │N (FK: rental_order_id)
┌──────────────▼───────────────┐
│ RentalOrderItem (PK: id)     │
│   - rental_order_id (FK)     │
│   - equipment_id (FK)        │
└──────────────┬───────────────┘
               │N (FK: equipment_id)
               │
               │1
┌──────────────▼───────────────┐
│ Equipment (PK: id)           │
│   - category_id (FK)         │
└──────────────┬───────────────┘
               │N (FK: category_id)
               │
               │1
┌──────────────▼───────────────┐
│ Category (PK: id)            │
└──────────────────────────────┘

// Equipment 1---N EquipmentSpecification
┌──────────────┐         ┌──────────────────────────────┐
│ Equipment    │1------N │ EquipmentSpecification       │
│ (PK: id)     │         │ (PK: id, FK: equipment_id)   │
└──────────────┘         └──────────────────────────────┘

// RentalOrder 1---N Invoice
┌──────────────┐         ┌──────────────────────────────┐
│ RentalOrder  │1------N │ Invoice                      │
│ (PK: id)     │         │ (PK: id, FK: rental_order_id,│
└──────────────┘         │      customer_id FK: User)   │
                         └──────────────────────────────┘

// Invoice 1---N Payment
┌──────────────┐         ┌──────────────────────────────┐
│ Invoice      │1------N │ Payment                      │
│ (PK: id)     │         │ (PK: id, FK: invoice_id,     │
└──────────────┘         │      confirmed_by_user_id FK)│
                         └──────────────────────────────┘

// Equipment 1---N EquipmentCompensation
// Invoice 1---N EquipmentCompensation
┌──────────────┐         ┌──────────────────────────────┐         ┌──────────────┐
│ Equipment    │1------N │ EquipmentCompensation        │N------1 │ Invoice      │
│ (PK: id)     │         │ (PK: id, FK: equipment_id,   │         │ (PK: id)     │
└──────────────┘         │      invoice_id,             │         └──────────────┘
                         │      recorded_by_user_id FK) │
                         └──────────────────────────────┘

// Equipment 1---N DepreciationRecord
// RentalOrder 1---N DepreciationRecord
┌──────────────┐         ┌──────────────────────────────┐         ┌──────────────┐
│ Equipment    │1------N │ DepreciationRecord           │N------1 │ RentalOrder  │
│ (PK: id)     │         │ (PK: id, FK: equipment_id,   │         │ (PK: id)     │
└──────────────┘         │      rental_order_id,        │         └──────────────┘
                         │      recorded_by_user_id FK) │
                         └──────────────────────────────┘
```

**Chú thích:**
- PK: Primary Key (khóa chính)
- FK: Foreign Key (khóa ngoại)
- Mối quan hệ 1---N: Một bản ghi ở bảng bên trái liên kết với nhiều bản ghi ở bảng bên phải.
- Các quan hệ đều thể hiện rõ chiều và trường liên kết.


**Các bảng chính và cấu trúc:**

1. **User (Người dùng)**
  - `user_id` (int, PK, identity)
  - `username` (nvarchar, unique, not null)
  - `password_hash` (nvarchar, not null)
  - `full_name` (nvarchar, not null)
  - `email` (nvarchar, unique, not null)
  - `phone_number` (nvarchar, not null)
  - `role` (nvarchar, not null, [ADMIN|STAFF|USER])
  - `status` (bit, not null)
  - `created_at` (datetime, not null)

2. **Category (Danh mục thiết bị)**
  - `id` (int, PK, identity)
  - `code` (nvarchar, unique, not null)
  - `name` (nvarchar, not null)
  - `description` (nvarchar, null)
  - `created_at` (datetime, not null)

3. **Equipment (Thiết bị)**
  - `id` (int, PK, identity)
  - `category_id` (int, FK -> Category.id, not null)
  - `name` (nvarchar, not null)
  - `code` (nvarchar, unique, not null)
  - `description` (nvarchar, null)
  - `total_quantity` (int, not null)
  - `available_quantity` (int, not null)
  - `quantity_in_repair` (int, not null)
  - `daily_rental_price` (decimal(18,2), not null)
  - `monthly_rental_price` (decimal(18,2), not null)
  - `created_at` (datetime, not null)

4. **EquipmentSpecification (Thông số kỹ thuật thiết bị)**
  - `id` (int, PK, identity)
  - `equipment_id` (int, FK -> Equipment.id, not null)
  - `name` (nvarchar, not null)
  - `value` (nvarchar, not null)

5. **EquipmentCompensation (Bồi thường thiết bị)**
  - `id` (int, PK, identity)
  - `equipment_id` (int, FK -> Equipment.id, not null)
  - `invoice_id` (int, FK -> Invoice.id, not null)
  - `amount` (decimal(18,2), not null)
  - `reason` (nvarchar, not null)
  - `recorded_by_user_id` (int, FK -> User.user_id, not null)
  - `recorded_at` (datetime, not null)

6. **RentalOrder (Đơn thuê thiết bị)**
  - `id` (int, PK, identity)
  - `user_id` (int, FK -> User.user_id, not null)
  - `start_date` (date, not null)
  - `end_date` (date, not null)
  - `status` (nvarchar, not null)
  - `created_at` (datetime, not null)

7. **RentalOrderItem (Chi tiết đơn thuê)**
  - `id` (int, PK, identity)
  - `rental_order_id` (int, FK -> RentalOrder.id, not null)
  - `equipment_id` (int, FK -> Equipment.id, not null)
  - `quantity` (int, not null)
  - `price_per_day` (decimal(18,2), not null)
  - `price_per_month` (decimal(18,2), not null)

8. **Invoice (Hóa đơn)**
  - `id` (int, PK, identity)
  - `rental_order_id` (int, FK -> RentalOrder.id, not null)
  - `customer_id` (int, FK -> User.user_id, not null)
  - `total_amount` (decimal(18,2), not null)
  - `status` (nvarchar, not null)
  - `created_at` (datetime, not null)
  - `due_date` (date, not null)

9. **Payment (Thanh toán)**
  - `id` (int, PK, identity)
  - `invoice_id` (int, FK -> Invoice.id, not null)
  - `amount_paid` (decimal(18,2), not null)
  - `payment_method` (nvarchar, not null)
  - `payment_date` (datetime, not null)
  - `confirmed_by_user_id` (int, FK -> User.user_id, not null)

10. **DepreciationRecord (Khấu hao thiết bị)**
   - `id` (int, PK, identity)
   - `equipment_id` (int, FK -> Equipment.id, not null)
   - `rental_order_id` (int, FK -> RentalOrder.id, not null)
   - `amount` (decimal(18,2), not null)
   - `recorded_by_user_id` (int, FK -> User.user_id, not null)
   - `recorded_at` (datetime, not null)
---


## PHẦN IV: THIẾT KẾ API BACKEND

### 1. Kiến trúc Backend

**Mô tả**: Backend tổ chức theo mô hình **Monolithic ASP.NET Core**, sử dụng Entity Framework, SQL Server, chia lớp rõ ràng: Controller (API), Service (Business Logic), Data (DbContext), Model (Entity), hỗ trợ phân quyền, xác thực JWT.

```

┌─────────────────────────────────────┐
│      Frontend (Web/Mobile)          │
└─────────────────────────────────────┘
                 │
                 ↓
┌─────────────────────────────────────┐
│      API Gateway / Authentication   │ (JWT/Session)
└─────────────────────────────────────┘
                 │
        ┌────────┼────────┐
        ↓        ↓        ↓
┌──────────────────────────────────────┐
│        API Routes / Endpoints        │
├──────────────────────────────────────┤
│ ├─ Category API                     │
│ ├─ Equipment API                    │
│ ├─ RentalOrder API                  │
│ ├─ Invoice API                      │
│ ├─ Payment API                      │
│ ├─ User API                         │
│ └─ Report API                       │
└──────────────────────────────────────┘
                 │
        ┌────────┼────────┐
        ↓        ↓        ↓
┌──────────────────────────────────────┐
│      Business Logic Layer            │
├──────────────────────────────────────┤
│ ├─ Category Service                  │
│ ├─ Equipment Service                 │
│ ├─ RentalOrder Service               │
│ ├─ Invoice Service                   │
│ ├─ Payment Service                   │
│ ├─ User Service                      │
│ └─ Report Service                    │
└──────────────────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────┐
│         Database Layer (SQL Server) │
│  (10+ bảng dữ liệu)                 │
└─────────────────────────────────────┘
```

---



### 2. Danh sách API Endpoints

    #### 1. Quản trị hệ thống (Admin)

    | Method | Endpoint                                 | Mô tả                       | Quyền hạn |
    |--------|------------------------------------------|-----------------------------|-----------|
    | GET    | `/admin`                                 | Trang quản trị tổng quan    | ADMIN     |
    | GET    | `/admin/users`                           | Danh sách người dùng        | ADMIN     |
    | GET    | `/admin/users/add`                       | Trang thêm người dùng       | ADMIN     |
    | POST   | `/admin/users/add`                       | Thêm người dùng mới         | ADMIN     |
    | GET    | `/admin/users/{id}/edit`                 | Trang sửa người dùng        | ADMIN     |
    | POST   | `/admin/users/{id}/edit`                 | Sửa thông tin người dùng    | ADMIN     |
    | POST   | `/admin/users/{id}/delete`               | Xóa người dùng              | ADMIN     |
    | GET    | `/admin/categories`                      | Danh sách danh mục thiết bị | ADMIN     |
    | GET    | `/admin/categories/{id}/edit`            | Trang sửa danh mục          | ADMIN     |
    | POST   | `/admin/categories/{id}/edit`            | Sửa danh mục                | ADMIN     |
    | GET    | `/admin/equipment`                       | Danh sách thiết bị          | ADMIN     |
    | GET    | `/admin/equipment/{id}/edit`             | Trang sửa thiết bị          | ADMIN     |
    | POST   | `/admin/equipment/{id}/edit`             | Sửa thiết bị                | ADMIN     |
    | GET    | `/admin/invoices`                        | Danh sách hóa đơn           | ADMIN     |
    | GET    | `/admin/invoices/add`                    | Trang thêm hóa đơn          | ADMIN     |
    | POST   | `/admin/invoices/add`                    | Thêm hóa đơn mới            | ADMIN     |
    | GET    | `/admin/invoices/{id}`                   | Xem chi tiết hóa đơn        | ADMIN     |
    | GET    | `/admin/invoices/{id}/edit`              | Trang sửa hóa đơn           | ADMIN     |
    | POST   | `/admin/invoices/{id}/edit`              | Sửa hóa đơn                 | ADMIN     |
    | POST   | `/admin/invoices/{id}/delete`            | Xóa hóa đơn                 | ADMIN     |
    | POST   | `/admin/invoices/{id}/confirm`           | Xác nhận thanh toán hóa đơn | ADMIN     |
    | GET    | `/admin/invoices/{id}/compensate`        | Trang tăng đền bù hóa đơn   | ADMIN     |
    | POST   | `/admin/invoices/{id}/compensate`        | Tăng tiền đền bù hóa đơn    | ADMIN     |

#### 2. Người dùng (User)

    | Method | Endpoint                | Mô tả                    | Quyền hạn |
    |--------|-------------------------|--------------------------|-----------|
    | GET    | `/user`                 | Trang người dùng         | USER      |
    | GET    | `/user/rent`            | Trang đăng ký đơn thuê   | USER      |
    | POST   | `/user/rent`            | Đăng ký đơn thuê         | USER      |
    | GET    | `/user/equipment`       | Xem thiết bị khả dụng    | USER      |
    | GET    | `/user/orders`          | Xem đơn thuê của bạn     | USER      |
    | GET    | `/user/payment`         | Trang thanh toán hóa đơn | USER      |
    | POST   | `/user/payment`         | Gửi yêu cầu thanh toán   | USER      |

    #### 3. Đăng nhập/Đăng xuất

    | Method | Endpoint   | Mô tả             | Quyền hạn |
    |--------|------------|-------------------|-----------|
    | GET    | `/login`   | Trang đăng nhập   | ALL       |
    | POST   | `/login`   | Đăng nhập         | ALL       |

    #### 4. API chức năng (RESTful, cho Frontend/Client)

    | Method | Endpoint                        | Mô tả                         | Quyền hạn    |
    |--------|----------------------------------|------------------------------|--------------|
    | GET    | `/api/categories`                | Lấy danh mục thiết bị        | ADMIN, STAFF |
    | POST   | `/api/categories`                | Thêm danh mục                | ADMIN        |
    | GET    | `/api/equipment`                 | Lấy danh sách thiết bị       | ADMIN, STAFF |
    | POST   | `/api/equipment`                 | Thêm thiết bị                | ADMIN        |
    | PUT    | `/api/equipment/{id}`            | Cập nhật thiết bị            | ADMIN        |
    | DELETE | `/api/equipment/{id}`            | Xóa thiết bị                 | ADMIN        |
    | GET    | `/api/users`                     | Lấy danh sách người dùng     | ADMIN        |
    | POST   | `/api/users`                     | Thêm người dùng              | ADMIN        |
    | GET    | `/api/rentalorders`              | Lấy đơn thuê                 | ADMIN, STAFF |
    | POST   | `/api/rentalorders`              | Tạo đơn thuê                 | STAFF        |
    | GET    | `/api/invoices`                  | Lấy hóa đơn                  | ADMIN, STAFF |
    | POST   | `/api/invoices`                  | Tạo hóa đơn                  | STAFF        |
    | GET    | `/api/payments`                  | Lấy thanh toán               | ADMIN, STAFF |
    | POST   | `/api/payments`                  | Ghi nhận thanh toán          | STAFF        |
    | GET    | `/api/reports/revenue`           | Báo cáo doanh thu            | ADMIN        |
    | GET    | `/api/reports/equipment-status`  | Báo cáo trạng thái thiết bị  | ADMIN, STAFF |

---

### 3. Cấu trúc Request/Response mẫu

#### **Ví dụ 1: Tạo đơn thuê thiết bị (POST /api/rentalorders)**

**Request Body**:
```json
{
  "user_id": 2,
  "start_date": "2026-03-01",
  "end_date": "2026-03-10",
  "items": [
    { "equipment_id": 1, "quantity": 2 },
    { "equipment_id": 3, "quantity": 1 }
  ]
}
```

**Response Success (201)**:
```json
{
  "success": true,
  "message": "Tạo đơn thuê thành công",
  "data": {
    "rental_order_id": 101,
    "user_id": 2,
    "start_date": "2026-03-01",
    "end_date": "2026-03-10",
    "status": "ACTIVE",
    "items": [
      { "equipment_id": 1, "quantity": 2 },
      { "equipment_id": 3, "quantity": 1 }
    ]
  }
}
```

---

#### **Ví dụ 2: Tạo hóa đơn từ đơn thuê (POST /api/invoices)**

**Request Body**:
```json
{
  "rental_order_id": 101
}
```

**Response Success (201)**:
```json
{
  "success": true,
  "message": "Hóa đơn đã được tạo thành công",
  "data": {
    "invoice_id": 2001,
    "rental_order_id": 101,
    "total_amount": 15000000,
    "status": "UNPAID",
    "created_at": "2026-03-10T10:00:00Z",
    "due_date": "2026-03-25"
  }
}
```

---

#### **Ví dụ 3: Báo cáo trạng thái thiết bị (GET /api/reports/equipment-status)**

**Response Success (200)**:
```json
{
  "success": true,
  "message": "Báo cáo trạng thái thiết bị",
  "data": [
    {
      "equipment_id": 1,
      "name": "Máy xúc 1",
      "category": "Máy xúc",
      "total_quantity": 5,
      "available_quantity": 2,
      "quantity_in_repair": 1
    },
    {
      "equipment_id": 2,
      "name": "Máy trộn bê tông",
      "category": "Máy trộn",
      "total_quantity": 3,
      "available_quantity": 3,
      "quantity_in_repair": 0
    }
  ]
}
```

#### **Ví dụ 4: Thêm người dùng mới (POST /api/users)**

**Request Body**:
```json
{
  "username": "nguyenvana",
  "password": "123456",
  "full_name": "Nguyễn Văn A",
  "email": "vana@example.com",
  "phone": "0912345678",
  "role": "Customer"
}
```
**Response Success (201)**:
```json
{
  "success": true,
  "message": "Tạo người dùng thành công",
  "data": {
    "user_id": 10,
    "username": "nguyenvana",
    "full_name": "Nguyễn Văn A",
    "role": "Customer"
  }
}
```

---

#### **Ví dụ 5: Thêm thiết bị mới (POST /api/equipment)**

**Request Body**:
```json
{
  "category_id": 2,
  "name": "Máy ủi Komatsu",
  "code": "EQP-002",
  "description": "Máy ủi công suất lớn",
  "total_quantity": 4,
  "available_quantity": 4,
  "daily_rental_price": 1200000
}
```
**Response Success (201)**:
```json
{
  "success": true,
  "message": "Thêm thiết bị thành công",
  "data": {
    "equipment_id": 12,
    "name": "Máy ủi Komatsu",
    "category_id": 2
  }
}
```

---

#### **Ví dụ 6: Thanh toán hóa đơn (POST /api/payments)**

**Request Body**:
```json
{
  "invoice_id": 2001,
  "amount_paid": 15000000,
  "payment_method": "BankTransfer"
}
```
**Response Success (201)**:
```json
{
  "success": true,
  "message": "Thanh toán thành công",
  "data": {
    "payment_id": 501,
    "invoice_id": 2001,
    "amount_paid": 15000000,
    "payment_date": "2026-03-11T09:00:00Z"
  }
}
```

---

#### **Ví dụ 7: Cập nhật thông tin thiết bị (PUT /api/equipment/{id})**

**Request Body**:
```json
{
  "name": "Máy xúc Hitachi ZX200",
  "description": "Đã bảo trì tháng 3/2026",
  "available_quantity": 3
}
```
**Response Success (200)**:
```json
{
  "success": true,
  "message": "Cập nhật thiết bị thành công",
  "data": {
    "equipment_id": 1,
    "name": "Máy xúc Hitachi ZX200",
    "available_quantity": 3
  }
}
```

---

#### **Ví dụ 8: Xác nhận hóa đơn đã thanh toán (POST /admin/invoices/{id}/confirm)**

**Request**: (Không cần body, chỉ cần gọi endpoint)

**Response Success (200)**:
```json
{
  "success": true,
  "message": "Hóa đơn đã được xác nhận thanh toán",
  "data": {
    "invoice_id": 2001,
    "status": "Paid",
    "paid_at": "2026-03-12T14:30:00Z"
  }
}
```

---


## PHẦN V: PHÂN CÔNG NHIỆM VỤ SINH VIÊN

### 1. Thống kê quy mô nhóm


| **Yếu tố**           | **Chi tiết**       |
| Tổng số người        | 3 sinh viên        |
| Tổng số bảng         | 10+ bảng           |
| Tổng số API endpoint | ~40 endpoints      |
| Ngôn ngữ lập trình   | C# ASP.NET Core 10 |
| Cơ sở dữ liệu        | SQL Server         |

### 2. Bảng phân công chi tiết


#### **SV1: Quản lý dữ liệu nền & API cơ bản**

      **Bảng dữ liệu:**
      - Category (Danh mục thiết bị)
      - Equipment (Thiết bị)

      **API Endpoints phụ trách:**
      | Method | Endpoint                         | Mô tả                         |
      | GET    | `/api/categories`                | Lấy danh mục thiết bị         |
      | POST   | `/api/categories`                | Thêm danh mục                 |
      | PUT    | `/api/categories/{id}`           | Cập nhật danh mục             |
      | DELETE | `/api/categories/{id}`           | Xóa danh mục                  |
      | GET    | `/api/equipment`                 | Lấy danh sách thiết bị        |
      | POST   | `/api/equipment`                 | Thêm thiết bị                 |
      | PUT    | `/api/equipment/{id}`            | Cập nhật thiết bị             |
      | DELETE | `/api/equipment/{id}`            | Xóa thiết bị                  |
      | GET    | `/api/equipment?category_id=...` | Lọc thiết bị theo danh mục    |
      | GET    | `/admin/categories`              | Quản trị danh mục (giao diện) |
      | GET    | `/admin/equipment`               | Quản trị thiết bị (giao diện) |

      **Yêu cầu chi tiết:**
      - Thiết kế bảng dữ liệu chuẩn hóa, có khóa ngoại, index hợp lý
      - Viết đầy đủ CRUD API, validate dữ liệu đầu vào (bắt buộc, kiểu dữ liệu, ràng buộc logic)
      - Xử lý lỗi rõ ràng (status code, message)
      - Viết test case cho từng API (unit test, integration test)
      - Đảm bảo phân quyền đúng (chỉ ADMIN thao tác thêm/sửa/xóa)
      - Viết tài liệu hướng dẫn sử dụng API (Swagger, README)
      - Đảm bảo code clean, dễ đọc, tuân thủ naming convention
      - Comment code các hàm phức tạp

      ---

#### **SV2: Nghiệp vụ cho thuê & hóa đơn**

      **Bảng dữ liệu:**
      - RentalOrder (Đơn thuê)
      - RentalOrderItem (Chi tiết đơn thuê)
      - Invoice (Hóa đơn)

      **API Endpoints phụ trách:**
      | Method | Endpoint                     | Mô tả                       |
      | GET    | `/api/rentalorders`          | Lấy danh sách đơn thuê      |
      | POST   | `/api/rentalorders`          | Tạo đơn thuê                |
      | PUT    | `/api/rentalorders/{id}`     | Cập nhật đơn thuê           |
      | DELETE | `/api/rentalorders/{id}`     | Xóa đơn thuê                |
      | GET    | `/api/invoices`              | Lấy danh sách hóa đơn       |
      | POST   | `/api/invoices`              | Tạo hóa đơn từ đơn thuê     |
      | PUT    | `/api/invoices/{id}`         | Cập nhật hóa đơn            |
      | DELETE | `/api/invoices/{id}`         | Xóa hóa đơn                 |
      | POST   | `/api/invoices/{id}/confirm` | Xác nhận thanh toán hóa đơn |
      | GET    | `/user/rent`                 | Trang đăng ký đơn thuê      |
      | POST   | `/user/rent`                 | Đăng ký đơn thuê            |
      | GET    | `/user/orders`               | Xem đơn thuê cá nhân        |

      **Yêu cầu chi tiết:**
      - Thiết kế bảng dữ liệu chuẩn hóa, có liên kết chặt chẽ giữa đơn thuê, hóa đơn, chi tiết đơn thuê
      - Viết đầy đủ CRUD API, validate dữ liệu đầu vào (ngày thuê, ngày trả, số lượng, trạng thái)
      - Cài đặt logic tính phí thuê theo ngày/tháng, sinh hóa đơn tự động khi hoàn thành đơn thuê
      - Xử lý các trạng thái đơn thuê/hóa đơn (Pending, Active, Paid, Overdue...)
      - Viết test case logic tính tiền, test edge case (ngày trùng, số lượng vượt kho...)
      - Đảm bảo phân quyền đúng (STAFF tạo đơn thuê, ADMIN xác nhận hóa đơn)
      - Viết tài liệu hướng dẫn sử dụng API, mô tả rõ quy trình nghiệp vụ
      - Đảm bảo code clean, dễ đọc, tuân thủ naming convention
      - Comment code các hàm phức tạp

      ---

#### **SV3: Thanh toán, bồi thường, báo cáo**

      **Bảng dữ liệu:**
      - Payment (Thanh toán)
      - EquipmentCompensation (Bồi thường thiết bị)
      - DepreciationRecord (Khấu hao thiết bị)

      **API Endpoints phụ trách:**
      | Method | Endpoint                        | Mô tả                       |
      | GET    | `/api/payments`                 | Lấy danh sách thanh toán    |
      | POST   | `/api/payments`                 | Ghi nhận thanh toán         |
      | GET    | `/api/reports/revenue`          | Báo cáo doanh thu           |
      | GET    | `/api/reports/equipment-status` | Báo cáo trạng thái thiết bị |
      | GET    | `/api/equipmentcompensations`   | Lấy danh sách bồi thường    |
      | POST   | `/api/equipmentcompensations`   | Ghi nhận bồi thường         |
      | GET    | `/api/depreciationrecords`      | Lấy danh sách khấu hao      |
      | POST   | `/api/depreciationrecords`      | Ghi nhận khấu hao           |
      | GET    | `/user/payment`                 | Trang thanh toán hóa đơn    |
      | POST   | `/user/payment`                 | Gửi yêu cầu thanh toán      |

      **Yêu cầu chi tiết:**
      - Thiết kế bảng dữ liệu chuẩn hóa, có liên kết hóa đơn, thiết bị, người dùng
      - Viết đầy đủ CRUD API, validate dữ liệu đầu vào (số tiền, phương thức, lý do bồi thường...)
      - Cài đặt logic báo cáo tổng hợp: doanh thu, thiết bị đang thuê, thiết bị hỏng, bồi thường
      - Xử lý các trường hợp thanh toán thiếu, quá hạn, bồi thường nhiều lần
      - Viết test case cho các báo cáo, kiểm thử hiệu năng với dữ liệu lớn
      - Đảm bảo phân quyền đúng (STAFF ghi nhận thanh toán, ADMIN xem báo cáo)
      - Viết tài liệu hướng dẫn sử dụng API, mô tả rõ quy trình nghiệp vụ
      - Đảm bảo code clean, dễ đọc, tuân thủ naming convention
      - Comment code các hàm phức tạp

---

### 3. Bảng tóm tắt phân công

| **SV**            | **Bảng dữ liệu phụ trách**                         | **Nhóm API/Chức năng**                             | **Số lượng endpoint** |
| Nguyễn Anh Đức    | Category, Equipment                                | Quản lý danh mục, thiết bị (CRUD, filter, giao diện admin) | 10+                   |
| Nguyễn Việt Hoàng | RentalOrder, RentalOrderItem, Invoice              | Đơn thuê, hóa đơn, logic tính tiền, xác nhận thanh toán     | 11+                   | 
| Lưu Phương Thảo   | Payment, EquipmentCompensation, DepreciationRecord | Thanh toán, bồi thường, báo cáo tổng hợp                   | 10+                   |

---


## PHẦN VI: CHI TIẾT LOGIC NGHIỆP VỤ (TÍNH PHÍ THUÊ, BÁO CÁO)

### 1. Logic tính phí thuê thiết bị

- Tính phí thuê theo số ngày/tháng, loại thiết bị, số lượng
- Kiểm tra tồn kho trước khi tạo đơn thuê
- Sinh hóa đơn tự động khi đơn thuê hoàn thành

### 2. Logic báo cáo

#### a. Báo cáo doanh thu
- **Mục tiêu:** Thống kê tổng doanh thu từ các hóa đơn đã thanh toán trong một khoảng thời gian (theo tháng, quý, năm, hoặc toàn bộ).
- **Logic thực hiện:**
  1. Truy vấn tất cả các hóa đơn có `status = 'Paid'` trong khoảng thời gian cần báo cáo.
  2. Tính tổng trường `total_amount` của các hóa đơn này.
  3. Có thể group theo tháng/năm để hiển thị biểu đồ hoặc bảng chi tiết.
- **Truy vấn mẫu:**
  ```sql
  SELECT SUM(total_amount) AS total_revenue, MONTH(created_at) AS month
  FROM Invoice
  WHERE status = 'Paid' AND created_at BETWEEN @fromDate AND @toDate
  GROUP BY MONTH(created_at)
  ```
- **Kết quả trả về:**
  - Tổng doanh thu
  - Doanh thu theo từng tháng/quý/năm
  - Số lượng hóa đơn đã thanh toán

#### b. Báo cáo trạng thái thiết bị
- **Mục tiêu:** Thống kê số lượng thiết bị theo trạng thái (đang thuê, sẵn sàng, đang sửa chữa, đã hỏng, v.v.)
- **Logic thực hiện:**
  1. Truy vấn bảng `Equipment` để lấy tổng số lượng, số lượng còn lại (`available_quantity`), số lượng đang sửa (`quantity_in_repair`).
  2. Có thể join với bảng `RentalOrderItem` để xác định thiết bị nào đang được thuê.
  3. Thống kê theo từng danh mục thiết bị nếu cần.
- **Truy vấn mẫu:**
  ```sql
  SELECT e.id, e.name, e.total_quantity, e.available_quantity, e.quantity_in_repair
  FROM Equipment e
  ```
- **Kết quả trả về:**
  - Danh sách thiết bị với trạng thái hiện tại
  - Thống kê tổng số thiết bị đang thuê, còn lại, đang sửa

#### c. Báo cáo bồi thường thiết bị
- **Mục tiêu:** Thống kê các trường hợp bồi thường thiết bị do hỏng hóc, mất mát, tổng số tiền bồi thường theo từng thiết bị hoặc người dùng.
- **Logic thực hiện:**
  1. Truy vấn bảng `EquipmentCompensation` trong khoảng thời gian cần báo cáo.
  2. Có thể group theo thiết bị hoặc người dùng để thống kê tổng số lần và tổng số tiền bồi thường.
- **Truy vấn mẫu:**
  ```sql
  SELECT equipment_id, SUM(amount) AS total_compensation, COUNT(*) AS compensation_count
  FROM EquipmentCompensation
  WHERE recorded_at BETWEEN @fromDate AND @toDate
  GROUP BY equipment_id
  ```
- **Kết quả trả về:**
  - Tổng số tiền bồi thường theo thiết bị/người dùng
  - Danh sách các trường hợp bồi thường

#### d. Báo cáo khấu hao thiết bị
- **Mục tiêu:** Thống kê số tiền khấu hao thiết bị theo từng kỳ thuê, từng thiết bị.
- **Logic thực hiện:**
  1. Truy vấn bảng `DepreciationRecord` theo khoảng thời gian hoặc theo thiết bị.
  2. Tính tổng số tiền khấu hao, số lần ghi nhận.
- **Truy vấn mẫu:**
  ```sql
  SELECT equipment_id, SUM(amount) AS total_depreciation
  FROM DepreciationRecord
  WHERE recorded_at BETWEEN @fromDate AND @toDate
  GROUP BY equipment_id
  ```
- **Kết quả trả về:**
  - Tổng số tiền khấu hao theo thiết bị
  - Lịch sử ghi nhận khấu hao

#### e. Báo cáo tổng hợp & hiệu năng
- **Mục tiêu:** Tổng hợp nhiều chỉ số (doanh thu, số lượng thiết bị, bồi thường, khấu hao) trên một dashboard.
- **Logic thực hiện:**
  1. Kết hợp nhiều truy vấn con (subquery) hoặc sử dụng stored procedure để lấy dữ liệu tổng hợp.
  2. Tối ưu truy vấn với index, phân trang khi dữ liệu lớn.
  3. Hỗ trợ filter theo thời gian, danh mục, trạng thái.
- **Kết quả trả về:**
  - Dashboard tổng hợp các chỉ số chính
  - Hỗ trợ xuất file Excel/PDF nếu cần

#### f. Lưu ý về hiệu năng và bảo mật
- Sử dụng index cho các trường ngày tháng, trạng thái, khóa ngoại để tăng tốc truy vấn.
- Phân quyền truy cập báo cáo (chỉ ADMIN hoặc STAFF được xem báo cáo nhạy cảm).
- Kiểm thử với dữ liệu lớn để đảm bảo thời gian phản hồi hợp lý.

---

## PHẦN VII: YÊU CẦU KỸ THUẬT & MÔI TRƯỜNG LÀM VIỆC


### 1. Stack công nghệ

| **Lớp**               | **Công nghệ**         | **Phiên bản** |
| **Backend**           | ASP.NET Core          | 10.0          |
| **Database**          | SQL Server            | 2019+         |
| **ORM**               | Entity Framework Core | 10.0          |
| **Authentication**    | JWT                   | -             |
| **Testing**           | xUnit                 | -             |
| **API Documentation** | Swagger               | -             |
| **Version Control**   | Git + GitHub          | -             |


### 2. Cấu trúc thư mục Backend (ASP.NET Core)

```
ConstructionRental/
├── Controllers/           # Chứa các API Controller (RESTful endpoint)
│   ├── AdminController.cs         # Quản trị hệ thống
│   ├── UserController.cs          # Quản lý người dùng
│   ├── EquipmentController.cs     # Quản lý thiết bị
│   ├── RentalOrderController.cs   # Quản lý đơn thuê
│   ├── InvoiceController.cs       # Quản lý hóa đơn
│   ├── PaymentController.cs       # Quản lý thanh toán
│   ├── HomeController.cs          # Trang chủ, login/logout
│   ├── ReportController.cs        # Báo cáo tổng hợp
│   └── LoginController.cs         # Đăng nhập/đăng xuất
├── Models/                # Định nghĩa các entity/model ánh xạ bảng dữ liệu
│   ├── User.cs
│   ├── Category.cs
│   ├── Equipment.cs
│   ├── RentalOrder.cs
│   ├── RentalOrderItem.cs
│   ├── Invoice.cs
│   ├── Payment.cs
│   ├── EquipmentCompensation.cs
│   ├── DepreciationRecord.cs
│   ├── EquipmentSpecification.cs
│   └── RentalStatus.cs
├── Data/                  # DbContext, cấu hình kết nối database
│   └── ConstructionRentalDbContext.cs
├── Services/              # Lớp nghiệp vụ (Business Logic)
│   ├── Interfaces/        # Định nghĩa interface cho service
│   │   ├── IEquipmentService.cs
│   │   ├── ICategoryService.cs
│   │   ├── IRentalService.cs
│   │   ├── IUserService.cs
│   │   ├── IFinancialService.cs
│   │   └── IService.cs
│   └── Implementations/   # Cài đặt các service
│       ├── EquipmentService.cs
│       ├── CategoryService.cs
│       ├── RentalService.cs
│       ├── UserService.cs
│       ├── FinancialService.cs
│       ├── ReportService.cs
│       └── InventoryService.cs
├── Migrations/            # EF Core migration (tạo/sửa bảng, version DB)
│   ├── 20260316082655_InitialCreate.cs
│   ├── 20260316083225_AddUserManagementAndFinancialFeatures.cs
│   ├── ConstructionRentalDbContextModelSnapshot.cs
│   └── 20260316090000_SeedInitialData.cs
├── Properties/            # Thông tin cấu hình project (AssemblyInfo, launchSettings)
│   ├── launchSettings.json
│   └── AssemblyInfo.cs
│
├── wwwroot/               # Static files (ảnh, css, js, tài liệu export)
│   └── static/
├── appsettings.json        # File cấu hình (connection string, JWT, ...)
├── appsettings.Development.json # Cấu hình riêng môi trường dev
├── Program.cs              # Entry point, cấu hình middleware, DI
├── ConstructionRental.csproj # File cấu hình project .NET
```

**Mô tả chi tiết các thư mục/chức năng:**
- **Controllers/**: Chứa các lớp controller định nghĩa các endpoint API, nhận request từ client, gọi service xử lý logic và trả response.
- **Models/**: Định nghĩa các entity tương ứng với bảng dữ liệu, có thể chứa cả các DTO (Data Transfer Object) phục vụ truyền dữ liệu.
- **Data/**: Chứa DbContext (kết nối, ánh xạ bảng, seed data), cấu hình EF Core.
- **Services/Interfaces/**: Định nghĩa các interface cho các service nghiệp vụ (theo chuẩn SOLID, dễ test/mock).
- **Services/Implementations/**: Cài đặt chi tiết các service nghiệp vụ (xử lý logic, validate, gọi DB).
- **Migrations/**: Lưu các file migration để quản lý version database, tạo/sửa bảng tự động.
- **Properties/**: Thông tin cấu hình project, launch profile, version.
- **wwwroot/**: Chứa file tĩnh (ảnh, css, js, export file, tài liệu download).
- **appsettings.json**: File cấu hình chính (connection string, JWT, các tham số hệ thống).
- **Program.cs**: Điểm khởi động ứng dụng, cấu hình DI, middleware, routing, swagger, ...
- **.csproj**: File cấu hình project .NET (package, target framework, ...).


### 3. Yêu cầu môi trường

**Máy tính cá nhân:**
- .NET 10.0 trở lên
- SQL Server 2019+
- Visual Studio 2022 hoặc VS Code
- Postman/Swagger UI (test API)
- Git

---

## KẾT LUẬN##

Bài tập lớn **"Xây dựng Backend Quản lý Cho thuê Thiết bị Xây dựng (ConstructionRental)"** là một dự án thực tiễn, giúp sinh viên tiếp cận toàn diện quy trình phát triển phần mềm backend hiện đại.

### 1. Kết quả đạt được
- Hoàn thiện hệ thống backend quản lý cho thuê thiết bị xây dựng với đầy đủ các chức năng: quản lý dữ liệu nền, nghiệp vụ cho thuê, hóa đơn, thanh toán, bồi thường, báo cáo.
- Thiết kế cơ sở dữ liệu chuẩn hóa, rõ ràng về quan hệ, khóa chính, khóa ngoại, đảm bảo tính toàn vẹn dữ liệu.
- Xây dựng API RESTful theo chuẩn, có phân quyền, xác thực JWT, tài liệu hóa đầy đủ bằng Swagger.
- Áp dụng các kỹ thuật kiểm thử (unit test, integration test) giúp nâng cao chất lượng và độ tin cậy của hệ thống.
- Mỗi thành viên đều hoàn thành phần việc được giao, phối hợp hiệu quả trong quá trình phát triển.

### 2. Kỹ năng và kiến thức tích lũy
- Thành thạo quy trình thiết kế và triển khai hệ thống backend thực tế với ASP.NET Core, Entity Framework, SQL Server.
- Hiểu sâu về thiết kế cơ sở dữ liệu quan hệ, tối ưu hóa truy vấn, xây dựng các báo cáo tổng hợp.
- Nắm vững kỹ năng làm việc nhóm, sử dụng Git/GitHub để quản lý mã nguồn, phân chia công việc rõ ràng.
- Rèn luyện tư duy kiểm thử, phát hiện và xử lý lỗi, viết test case bao phủ nhiều tình huống thực tế.

### 3. Thách thức và bài học kinh nghiệm
- Việc đồng bộ hóa dữ liệu giữa các bảng, xử lý các trường hợp edge case (ví dụ: thiết bị hết kho, hóa đơn quá hạn, bồi thường nhiều lần) đòi hỏi sự phối hợp chặt chẽ và kiểm thử kỹ lưỡng.
- Quản lý phân quyền, bảo mật API và tối ưu hiệu năng cho các báo cáo lớn là những vấn đề thực tế mà nhóm đã giải quyết thành công.
- Việc tài liệu hóa chi tiết (Swagger, README, hướng dẫn test API) giúp việc bảo trì, mở rộng hệ thống dễ dàng hơn.

### 4. Định hướng phát triển
- Có thể mở rộng hệ thống để tích hợp frontend (Web/Mobile), bổ sung các tính năng nâng cao như thống kê biểu đồ, xuất báo cáo PDF/Excel, gửi thông báo tự động.
- Tích hợp CI/CD để tự động hóa kiểm thử và triển khai.
- Nâng cấp bảo mật, logging, giám sát hệ thống khi triển khai thực tế.

### 5. Tổng kết
Dự án đã giúp sinh viên vận dụng kiến thức lý thuyết vào thực tiễn, phát triển kỹ năng lập trình, teamwork, giải quyết vấn đề và tư duy hệ thống. Đây là nền tảng vững chắc để tiếp tục học tập, nghiên cứu và làm việc trong lĩnh vực phát triển phần mềm backend chuyên nghiệp.

---