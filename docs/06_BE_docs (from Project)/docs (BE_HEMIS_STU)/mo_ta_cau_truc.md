# Mô tả Cấu trúc Thư mục Backend - Dự án Hemis

Tài liệu này cung cấp cái nhìn chi tiết về chức năng và nhiệm vụ của từng thư mục trong hệ thống Backend. Cấu trúc được thiết kế để tuân thủ nguyên tắc SOLID và hỗ trợ phân quyền Group-based RBAC.

## 1. Thư mục Gốc (`/backend`)

-   **`app/`**: Thư mục chứa toàn bộ mã nguồn chính của ứng dụng.
-   **`tests/`**: Chứa các đoạn mã kiểm thử (Unit test và Integration test).
-   **`docs/`**: Chứa các tài liệu hướng dẫn kỹ thuật và mô tả hệ thống.
-   **`.env`**: File chứa các biến môi trường nhạy cảm (không đưa lên Git).
-   **`.env.example`**: File mẫu hướng dẫn cấu hình biến môi trường cho developer mới.
-   **`requirements.txt`**: Danh sách các thư viện Python cần thiết cho dự án.

---

## 2. Thư mục Ứng dụng chính (`/app`)

### `api/` (Giao diện lập trình)
Nơi tiếp nhận các yêu cầu từ phía Client (Frontend).
-   **`dependencies.py`**: Chứa các phụ thuộc dùng chung (như kết nối DB, xác thực JWT).
-   **`v1/`**: Chứa mã nguồn cho phiên bản 1 của API.
    -   **`api.py`**: Điểm tập trung để đăng ký tất cả các đường dẫn (routes).
    -   **`endpoints/`**: Chứa các file xử lý chi tiết cho từng module (ví dụ: `danh_muc.py`, `chuong_trinh_dao_tao.py`, `identity.py`, `auth.py`).

### `core/` (Cốt lõi hệ thống)
Chứa các thành phần hạ tầng và logic dùng chung không phụ thuộc vào nghiệp vụ.
-   **`config.py`**: Quản lý cấu hình ứng dụng từ file `.env`.
-   **`security.py`**: Các hàm xử lý bảo mật như mã hóa mật khẩu, tạo mã token.
-   **`rbac.py`**: Chứa logic kiểm tra quyền hạn thuần túy (So sánh các mã quyền).
-   **`logging.py`**: Cấu hình ghi nhật ký hệ thống dưới định dạng JSON.

### `db/` (Cơ sở dữ liệu)
-   **`base.py`**: Khởi tạo lớp cơ sở (Declarative Base) cho SQLAlchemy.
-   **`session.py`**: Thiết lập và quản lý các phiên kết nối không đồng bộ (Async Session) tới database.

### `models/` (Thực thể dữ liệu)
-   Định nghĩa các bảng trong database bằng SQLAlchemy. Mỗi file đại diện cho một thực thể (Entity) duy nhất.
-   **`__init__.py`**: Cực kỳ quan trọng, dùng để import tất cả models nhằm đăng ký metadata với database.

### `schemas/` (Cấu trúc dữ liệu)
-   Chứa các Pydantic models dùng để kiểm tra tính hợp lệ của dữ liệu đầu vào và định dạng dữ liệu đầu ra.
-   Tuân thủ quy ước đặt tên: `Create`, `Update`, `Read`, `ReadFull`.

### `repositories/` (Lớp truy cập dữ liệu)
-   Thực hiện các thao tác truy vấn database (CRUD). Giúp tách biệt logic SQL ra khỏi logic nghiệp vụ.
-   **`base.py`**: Chứa lớp BaseRepository với các hàm CRUD chung nhất.

### `services/` (Lớp nghiệp vụ)
-   Nơi xử lý các quy tắc nghiệp vụ (Business Logic) phức tạp. 
-   Đây là lớp trung gian kết nối giữa API và Repository.

### `exceptions/` (Xử lý lỗi)
-   Định nghĩa các loại lỗi tùy chỉnh (Custom Exceptions) cho hệ thống.
-   Giúp chuẩn hóa cách hệ thống phản hồi khi có lỗi xảy ra.

### `utils/` (Tiện ích)
-   Chứa các hàm hỗ trợ kỹ thuật như xử lý file Excel, định dạng ngày tháng. 
-   Quy tắc: Lớp này không được phép gọi (import) ngược lại các lớp nghiệp vụ.

---

## 3. Thư mục Kiểm thử (`/tests`)

-   **`unit/`**: Các bài kiểm tra độc lập cho từng hàm, từng lớp trong Service và Repository.
-   **`integration/`**: Các bài kiểm tra tích hợp, giả lập request từ Client để kiểm tra luồng xử lý từ API xuống tới Database.
