# Quy Định Nguyên Tắc Làm Việc & Code - Dự Án Hemis

Tài liệu này là **"Luật"** của dự án. Mục tiêu không phải là làm khó developer, mà là để bảo vệ chất lượng hệ thống, tránh những rủi ro kỹ thuật và đảm bảo mọi thành viên có thể phối hợp nhịp nhàng. Bất kể bạn là Junior hay Senior, việc tuân thủ tài liệu này là bắt buộc.

---

## PHẦN 1: QUY TRÌNH LÀM VIỆC (TEAM WORKFLOW)

### 1.1. Quy tắc quản lý nhánh (Git Branching)
Hệ thống sử dụng mô hình Git đơn giản hóa:
- **`main`**: Nhánh chứa code chạy thực tế (Production). Tuyệt đối không push code trực tiếp lên nhánh này.
- **`dev`**: Nhánh tích hợp chính. Code từ các tính năng mới sẽ được gom về đây để test (Staging).
- **`feature/<tên-tính-năng>`**: Nhánh cá nhân để phát triển tính năng mới. (Ví dụ: `feature/auth-login`, `feature/admission-import`).
- **`bugfix/<tên-lỗi>`**: Nhánh sửa lỗi phát sinh.

### 1.2. Quy ước viết Commit Message
Commit message cần có ý nghĩa, không viết chung chung như "update", "fix bug". Sử dụng tiền tố (Conventional Commits):
- `feat: [tên module] Thêm tính năng...` (Ví dụ: `feat: [User] Thêm API đăng nhập`)
- `fix: [tên module] Sửa lỗi...` (Ví dụ: `fix: [Role] Lỗi lưu phân quyền sai`)
- `refactor: Tái cấu trúc code...` (Chỉnh sửa code nhưng không thay đổi chức năng)
- `docs: Cập nhật tài liệu...`

### 1.3. Quy trình Pull Request (PR) & Code Review
- Developer hoàn thiện tính năng trên nhánh `feature/*` và tạo PR ghép (merge) vào nhánh `dev`.
- **Luật 1:** PR không được quá lớn (cố gắng giữ dưới 500 dòng code thay đổi) để reviewer dễ đọc.
- **Luật 2:** Phải có ít nhất 1 người (thường là Tech Lead hoặc Senior) Review và Approve thì mới được merge.
- **Luật 3:** Nếu có mã phân quyền mới (Permission Code), bắt buộc phải có sự xác nhận của người chịu trách nhiệm bảo mật (Admin Security).

---

## PHẦN 2: QUY TẮC VIẾT CODE & KIẾN TRÚC BACKEND

Hệ thống Backend được thiết kế theo mô hình **Layered Architecture** kết hợp nguyên tắc **SOLID**. Việc phá vỡ kiến trúc sẽ bị từ chối ngay ở vòng Code Review.

### 2.1. Quy tắc "Mũi Tên Một Chiều" (The Golden Rule)
Mọi lượt gọi (import) giữa các tầng thư mục bắt buộc phải tuân theo sơ đồ từ phải sang trái:
**`core` ← `db` ← `models` ← `repositories` ← `services` ← `api`**

- **TUYỆT ĐỐI KHÔNG** import ngược (Ví dụ: Không import Service vào Repository, không import API vào Core). Nếu vi phạm, sẽ gây lỗi Circular Import làm sập ứng dụng.
- **Lớp ngoại lệ:** `exceptions/` và `schemas/` là lớp lá (Leaf Layer), có thể được import ở bất kỳ đâu. `utils/` chỉ chứa logic phụ trợ, không được phép import `services` hay `repositories`.

### 2.2. Trách nhiệm Đơn lẻ (Single Responsibility)
- **1 Thực thể = 1 File:** Nếu bạn làm tính năng Quản lý Lớp học, hãy tạo file `class.py` trong `models/`, `class.py` trong `schemas/`, `class_repository.py` trong `repositories/`... Không viết gộp logic của Lớp học vào file của Sinh viên.
- **Chống "Fat File":** Một file Service nếu dài quá 500-1000 dòng, hãy cân nhắc tách thành các service nhỏ hơn.

### 2.3. Quy định đặt tên (Naming Conventions)
- **Biến & Hàm (Python):** `snake_case` (ví dụ: `get_user_by_id`, `user_name`).
- **Class:** `PascalCase` (ví dụ: `UserService`, `UserCreate`).
- **Schemas (Pydantic):** Bắt buộc phân định rõ Input/Output bằng hậu tố.
    - Input: `<Entity>Create`, `<Entity>Update`, `<Entity>Filter`.
    - Output: `<Entity>Read`, `<Entity>ReadShort`, `<Entity>ReadFull`.

### 2.4. Quy tắc Xử lý Lỗi & Response
- Ở tầng **Service** hoặc **Repository**: Nếu phát hiện lỗi (ví dụ không tìm thấy User), hãy `raise` một lỗi cụ thể từ thư mục `exceptions/` (VD: `raise UserNotFoundError()`).
- **TUYỆT ĐỐI KHÔNG** sử dụng `raise HTTPException(...)` bên trong thư mục `services` hay `repositories`. Việc định dạng lỗi HTTP là trách nhiệm của tầng API và Global Exception Handler.

### 2.5. Tuân thủ 8 bước phát triển
Mọi API mới đều phải đi qua trình tự:
1. Cập nhật Model (`models/`).
2. Khai báo Permission mới (nếu cần bảo mật).
3. Viết Pydantic Schema (`schemas/`).
4. Viết Repository truy vấn (`repositories/`).
5. Viết Service chứa logic (`services/`).
6. **Viết Unit Test cho Service đó.**
7. Viết Endpoint và móc nối Service (`api/v1/endpoints/`).
8. **Viết Integration Test.**

*Code không có Test sẽ bị reject trong Pull Request.*
