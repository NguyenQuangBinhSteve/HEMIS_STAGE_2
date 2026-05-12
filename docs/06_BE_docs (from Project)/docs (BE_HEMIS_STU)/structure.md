# Tài liệu Kiến trúc & Cấu trúc Thư mục Backend - Hemis Project (v5 - Final)

Tài liệu này là chuẩn mực phát triển cao nhất cho dự án Hemis Backend, đảm bảo tính nhất quán, bảo mật và khả năng bảo trì.

## 1. Kiến trúc Tổng quan (Layered Architecture)

Hệ thống tuân thủ nghiêm ngặt mô hình 7 lớp với luồng phụ thuộc một chiều:

-   **API Layer (`app/api`)**: Quản lý các endpoints và xác thực. **Chịu trách nhiệm gọi Service/Repository để lấy danh sách quyền của User.**
-   **Service Layer (`app/services`)**: Xử lý logic nghiệp vụ chính. Không tự thực hiện lấy quyền từ DB trừ khi có nghiệp vụ đặc thù.
-   **Repository Layer (`app/repositories`)**: Lớp duy nhất tương tác với Database qua SQLAlchemy.
-   **Model Layer (`app/models`)**: Ánh xạ quan hệ database. Phải được import tập trung tại `app/models/__init__.py`.
-   **Schema Layer (`app/schemas`)**: Định nghĩa cấu trúc dữ liệu Input (Create/Update) và Output (Read).
-   **Core Layer (`app/core`)**: Hạ tầng lõi và **Logic RBAC thuần túy** (chỉ so sánh quyền, không truy vấn DB).
-   **Exception Layer (`app/exceptions`)**: Chuẩn hóa lỗi hệ thống thành response.

---

## 2. Sơ đồ Cấu trúc Thư mục

```text
/backend
├── .env                           # Thông số nhạy cảm (không commit)
├── .env.example                   # File mẫu cho developer mới (Bắt buộc)
├── app/
│   ├── main.py                    # Khởi tạo app, CORS, Exception Handler
│   ├── api/
│   │   ├── dependencies.py        # Global: get_db, JWT verification
│   │   └── v1/
│   │       ├── router.py          # Register routers v1
│   │       ├── dependencies.py    # v1: get_current_user (Fetch permissions), require_permission
│   │       └── endpoints/         # health.py, users.py, auth.py...
│   ├── core/
│   │   ├── config.py              # Pydantic-settings
│   │   ├── rbac.py                # Pure RBAC Logic (Comparing only)
│   │   ├── security.py            # JWT & Hashing
│   │   └── logging.py             # Structured Logging
│   ├── db/
│   │   ├── base.py                # SQLAlchemy Base
│   │   └── session.py             # Async Session
│   ├── models/
│   │   └── __init__.py            # Import tất cả models tại đây
│   ├── schemas/                   # <Entity>Create/Update/Read/ReadFull
│   ├── repositories/              # BaseRepository ABC
│   ├── services/                  # Business Logic Layer
│   ├── exceptions/                # Centralized Error Handling
│   └── utils/                     # Technical helpers (Không import service/repo)
├── tests/
│   ├── unit/                      # Test logic Services
│   └── integration/               # Test luồng API hoàn chỉnh (Bắt buộc)
└── docs/
    └── structure.md               # Tài liệu này
```

---

## 3. Cơ chế Phân quyền (RBAC - Deny Overrides)

Hệ thống sử dụng quy ước để đảm bảo an toàn tối đa:

-   **Quy ước Vai trò Cấm (Deny Roles)**: Bất kỳ vai trò nào có mã (`MA_VAI_TRO`) kết thúc bằng hậu tố **`_DENY`** sẽ được coi là vai trò thu hồi quyền.
-   **An toàn hệ thống**: Lớp `Service` khi thực hiện tạo/cập nhật Vai trò phải có logic validate hậu tố `_DENY`. Đây là từ khóa hệ thống, không được sử dụng tùy tiện cho các vai trò thông thường.
-   **Logic xử lý**:
    1.  `api/v1/dependencies.py`: Thực hiện **LẤY** (Fetch) danh sách quyền từ DB.
    2.  `core/rbac.py`: Thực hiện **SO SÁNH** (Compare) và áp dụng luật "Explicit Deny Wins".
    3.  `Service`: Sử dụng kết quả đã được xác thực để thực thi nghiệp vụ.

---

## 4. Quy trình Phát triển 8 Bước (Standard Workflow)

1.  **Define Model**: Tạo bảng và đăng ký vào `models/__init__.py`.
2.  **Define Permission**: Đề xuất mã quyền (PR review bởi Admin Security/Lead).
3.  **Define Schemas**: Tạo class theo quy ước Input/Output.
4.  **Implement Repository**: Viết logic truy vấn DB.
5.  **Implement Service**: Viết logic nghiệp vụ (và validate hậu tố `_DENY` nếu là Role service).
6.  **Write Unit Test**: Kiểm tra logic nghiệp vụ độc lập.
7.  **Create Endpoint**: Đăng ký vào `api.py`.
8.  **Write Integration Test**: Test toàn bộ luồng API từ Request đến DB (Bắt buộc).

---

## 5. Các Quy tắc Vàng (Golden Rules)

### Phụ thuộc một chiều (Dependency Direction)
Mọi lượt import phải tuân theo sơ đồ sau (chỉ được import từ lớp bên trái):
**`core` ← `db` ← `models` ← `repositories` ← `services` ← `api`**

*Lưu ý: `exceptions/` và `schemas/` là lớp dùng chung (Leaf Layers), được phép import từ bất kỳ lớp nào trong chuỗi trên.*

### Khác
-   **Circular Dependency**: Tuyệt đối không import ngược từ phải sang trái trong sơ đồ trên.
-   **Error Handling**: Chỉ `raise` custom exceptions trong Service. Không dùng `HTTPException` ngoài lớp API.
-   **Utils**: Tuyệt đối không import từ `services` hay `repositories` vào lớp `utils`.
