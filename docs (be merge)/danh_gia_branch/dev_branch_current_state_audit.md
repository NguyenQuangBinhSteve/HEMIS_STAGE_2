# Current State Audit: Nhánh `dev` (Kiểm tra & Tổng hợp)

**Ngày cập nhật**: 14/05/2026

Báo cáo này tổng hợp tình trạng hiện diện của các module danh mục trên nhánh `dev`, phục vụ cho việc quản lý merge và kiểm soát chất lượng (Audit).

---

## 1. Kết quả kiểm tra 6 nhánh feature mục tiêu

Đây là các nhánh nằm trong kế hoạch Audit và Merge hiện tại:

| STT | Tên nhánh Feature | Tình trạng trên `dev` | Ghi chú nguồn gốc |
|---|---|---|---|
| 1 | `be/stu/dm_hinh_thuc_dao_tao` | **ĐÃ CÓ** | Merge chủ động (đã audit) |
| 2 | `be/stu/dm_loai_chuong_trinh_dao_tao` | **ĐÃ CÓ** | Đã audit (vô tình merge từ `main`) |
| 3 | `be/stu/dm_trang_thai_chuong_trinh_dao_tao` | **ĐÃ CÓ** | Merge chủ động (đã audit) |
| 4 | `be/stu/dm_don_vi_cap_bang` | **ĐÃ CÓ** | Merge chủ động (đã audit) |
| 5 | `be/stu/dm_khung_nang_luc_ngoai_ngu` | **ĐÃ CÓ** | Đã audit (vô tình merge từ `main`) |
| 6 | `be/stu/dm_loai_quyet_dinh_ctdt` | **CHƯA CÓ** | [Audit FAIL (Sai nội dung code)](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/danh_gia_branch/issue_report_loai_quyet_dinh_mismatch.md) |

---

## 2. Các danh mục đã tồn tại sẵn (Pre-existing)

Đây là các danh mục đã được thực hiện từ các giai đoạn trước, hiện đã có sẵn trên `dev`:

| STT | Tên danh mục | Model | Trạng thái Audit |
|---|---|---|---|
| 1 | `dm_co_quan_chu_quan` | `DMCoQuanChuQuan` | Legacy (Hard Delete) |
| 2 | `dm_to_chuc_kiem_dinh` | `DMToChucKiemDinh` | Legacy (Hard Delete) |
| 3 | `dm_ket_qua_kiem_dinh` | `DMKetQuaKiemDinh` | Legacy (Hard Delete) |
| 4 | `dm_vi_tri_lam_viec` | `DMViTriLamViec` | Legacy (Hard Delete) |
| 5 | `dm_vi_tri_viec_lam` | `DMViTriViecLam` | Legacy (Hard Delete) |
| 6 | `dm_xa` | `DMXa` | Legacy (Hard Delete) |
| 7 | `dm_xep_hang_q` | `DMXepHangQ` | Legacy (Hard Delete) |

---

## 3. Tổng kết tình hình
- Hiện có **12** module danh mục đã hiện diện trên nhánh `dev`.
- Module `dm_loai_quyet_dinh_ctdt` bị xác định sai nội dung trên nhánh feature (chứa code của module khác). Chi tiết xem tại [Báo cáo sự cố kỹ thuật](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/danh_gia_branch/issue_report_loai_quyet_dinh_mismatch.md).
- Toàn bộ các module hiện tại đều đang sử dụng **Hard Delete** (Legacy). Việc nâng cấp lên **Soft Delete** sẽ được thực hiện đồng loạt sau khi hoàn tất Audit tất cả các nhánh feature.
