# Audit Report: `be/stu/dm_loai_quyet_dinh_ctdt`

## 1. Tổng quan Module
- **Tên danh mục**: Loại quyết định CTĐT
- **Trạng thái trên `dev`**: **CHƯA CÓ**
- **Trạng thái trên feature branch**: **SAI NỘI DUNG**
- **Phát hiện quan trọng**: Nhánh `be/stu/dm_loai_quyet_dinh_ctdt` hiện đang chứa mã nguồn của module `dm_loai_chuong_trinh_dao_tao` thay vì module mục tiêu.

## 2. Bảng điểm tiêu chí (Scoring Checklist)

| STT | Tiêu chí đánh giá | Trọng số | Điểm | Đánh giá |
|---|---|---|---|---|
| 1 | **Tầng Model**: Định nghĩa đúng SQLAlchemy Model | 10 | 0 | `❌ FAIL` |
| 2 | **Tầng Schema**: Pydantic Schemas đầy đủ | 10 | 0 | `❌ FAIL` |
| 3 | **Tầng Service**: Logic CRUD Async chuẩn | 10 | 0 | `❌ FAIL` |
| 4 | **Tầng Router**: API Endpoint Compact Style | 10 | 0 | `❌ FAIL` |
| 5 | **Integration Test**: Đạt Full Flow (5 bước) | 10 | 0 | `❌ FAIL` |
| 6 | **Naming Standard**: `snake_case` chuẩn | 10 | 0 | `❌ FAIL` |
| 7 | **Logic CRUD**: Xử lý DB session & commit | 10 | 0 | `❌ FAIL` |
| 8 | **Update Logic**: `exclude_unset=True` | 10 | 0 | `❌ FAIL` |
| 9 | **Schema Core Mapping**: Đúng schema DB | 10 | 0 | `❌ FAIL` |
| 10 | **Soft Delete**: Kế hoạch & Logic dự phòng | 10 | 0 | `❌ FAIL` |
| **TỔNG** | | **100** | **0** | **KHÔNG ĐẠT** |

---

## 3. Báo cáo sai biệt & Nguyên nhân (Discrepancy Analysis)

### 3.1. Phân tích nhánh feature
- **Lịch sử commit**: Commit cuối cùng (`3e47443`) ghi nhận `feat: [dm_loai_chuong_trinh_dao_tao]`.
- **Nội dung code**: Toàn bộ logic tại 4 tầng (Model, Schema, Service, Router) và file Test đều liên quan đến `LoaiChuongTrinhDaoTao`.
- **Kết luận**: Nhánh feature bị đặt sai tên hoặc người thực hiện đã đẩy nhầm code của module khác lên nhánh này.

### 3.2. Truy vết trên nhánh `dev` và `main`
- Không tìm thấy bất kỳ dấu vết nào của class `DMLoaiQuyetDinhCTDT` hoặc bảng `dm_loai_quyet_dinh_ctdt` trong mã nguồn.
- Từ khóa chỉ xuất hiện duy nhất trong tài liệu mapping (`docs/mapping_report.md`).

---

## 4. Kết luận & Kiến nghị
Nhánh `be/stu/dm_loai_quyet_dinh_ctdt` đạt điểm **0/100** về nội dung mục tiêu.

**Kiến nghị**: 
1. Không thực hiện merge nhánh này vào `dev`.
2. Yêu cầu thực hiện lại module `dm_loai_quyet_dinh_ctdt` từ đầu trên một nhánh mới hoặc dọn dẹp lại nhánh hiện tại.
3. Đánh dấu module này là "Chưa thực hiện" trong báo cáo tổng hợp.
