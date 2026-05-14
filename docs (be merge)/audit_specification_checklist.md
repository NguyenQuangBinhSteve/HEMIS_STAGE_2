# Audit Specification & Checklist: Module Danh mục

Tài liệu này cung cấp quy trình đối soát chuẩn cho các module danh mục. Agent sẽ sử dụng tài liệu này để kiểm tra từng nhánh feature và so sánh với bản mẫu [Gold Standard: dm_to_chuc_kiem_dinh](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/dm_to_chuc_kiem_dinh_gold_standard.md).

---

## 1. Bảng kiểm định 5 Tầng Kiến trúc (5 Layers Audit)

| Tầng (Layer) | Nội dung kiểm tra (Check items) | Trạng thái | Ghi chú |
|---|---|---|---|
| **Model** | Đã khai báo `__table_args__ = {"schema": "core"}`? | `[ ]` | |
| | Có trường `id` (String/Integer) làm Primary Key? | `[ ]` | |
| | Có trường `created_at` và `updated_at` (func.now)? | `[ ]` | |
| | Có logic Soft Delete (trường `deleted_at` hoặc tương đương)? | `[ ]` | |
| **Schema** | Đủ 4 bộ: Base, Create, Update, Read? | `[ ]` | |
| | Schema Update có tất cả các field là `Optional`? | `[ ]` | |
| | Đã cấu hình `from_attributes = True` trong class Config? | `[ ]` | |
| **Service** | Sử dụng `AsyncSession` cho mọi tương tác DB? | `[ ]` | |
| | Hàm Update có dùng `model_dump(exclude_unset=True)`? | `[ ]` | |
| | Có đầy đủ `await db.commit()` và `await db.refresh()`? | `[ ]` | |
| | Có xử lý trả về `None` hoặc Raise lỗi khi không tìm thấy record? | `[ ]` | |
| **Router** | Sử dụng đúng HTTP Methods (GET, POST, PUT, DELETE)? | `[ ]` | |
| | Có xử lý `HTTPException(status_code=404)`? | `[ ]` | |
| | Đã gán đúng `response_model` cho từng endpoint? | `[ ]` | |
| **Test** | Có file integration test riêng cho module? | `[ ]` | |
| | Test covers full flow (Create -> Read -> Update -> Delete)? | `[ ]` | |
| | Đã verify trạng thái 404 sau khi Delete? | `[ ]` | |

---

## 2. Tiêu chuẩn API Endpoints (CRUD Standards)

Mọi module danh mục phải cung cấp tối thiểu 5 endpoints sau:

1. **GET `/` (List all)**:
   - Trả về danh sách các bản ghi chưa bị xóa.
   - Định dạng: `List[SchemaRead]`.
2. **GET `/{id}` (Retrieve)**:
   - Trả về chi tiết 1 bản ghi.
   - Xử lý lỗi: Trả về 404 nếu ID không tồn tại hoặc đã bị xóa.
3. **POST `/` (Create)**:
   - Tạo mới bản ghi.
   - Trả về bản ghi vừa tạo kèm ID.
4. **PUT `/{id}` (Update)**:
   - Cập nhật thông tin bản ghi (Partial update).
   - Phải giữ nguyên các trường không được gửi lên.
5. **DELETE `/{id}` (Delete)**:
   - Thực hiện Soft Delete (cập nhật trạng thái/ngày xóa).
   - Trả về thông báo thành công.

---

## 3. Mẫu báo cáo đánh giá (Evaluation Template)

*Dành cho Agent khi thực hiện báo cáo tại thư mục `danh_gia_branch/`*

### 3.1. Bảng đối soát (Comparison Matrix)

| Tiêu chí | Gold Standard (`dm_to_chuc_kiem_dinh`) | Nhánh Feature (`[branch_name]`) | Đánh giá |
|---|---|---|---|
| **Soft Delete** | Có (`deleted_at`) | ... | `[OK / Khác]` |
| **Update Logic** | `exclude_unset=True` | ... | `[OK / Khác]` |
| **Integration Test**| Full Flow | ... | `[OK / Thiếu]` |
| **Schema Core** | Có (`schema="core"`) | ... | `[OK / Thiếu]` |

### 3.2. Báo cáo sai biệt (Discrepancy Report)

- **Vấn đề 1**: [Mô tả chi tiết sai biệt]
- **Vấn đề 2**: [Mô tả chi tiết sai biệt]

### 3.3. Đề xuất chỉnh sửa (Correction Proposal)

**Tệp tin**: `[Đường dẫn file cần sửa]`
```python
# Code snippet đề xuất sửa đổi dựa trên Gold Standard
```

---

## 4. Hướng dẫn thực hiện

1. **Bước 1**: Sử dụng [Git Workflow](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/git_workflow_agent_audit.md) để chuyển sang nhánh feature.
2. **Bước 2**: Mở mã nguồn và tích (check) vào bảng ở Mục 1 & 2.
3. **Bước 3**: Đối chiếu với tài liệu Gold Standard để tìm sai biệt.
4. **Bước 4**: Tạo file báo cáo `.md` mới trong thư mục `danh_gia_branch/` theo mẫu ở Mục 3.
5. **Bước 5**: Quay lại nhánh `dev` và báo cáo kết quả cho người dùng.
