# Sub-module 4: THONG_TIN_KIEM_DINH

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/THONG_TIN_KIEM_DINH.sql`

---
## 1. Mô tả

Lưu trữ thông tin về kết quả kiểm định chất lượng của chương trình đào tạo, bao gồm tổ chức kiểm định, kết quả, số quyết định, ngày chứng nhận, và thời hạn kiểm định.

---
## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `MA_CHUONG_TRINH_DAO_TAO` | VARCHAR | 50 | `NOT NULL`, `FK → CHUONG_TRINH_DAO_TAO(MA_CHUONG_TRINH)` | Mã CTĐT (liên kết bảng chính) |
| 3 | `MA_TO_CHUC_KIEM_DINH` | VARCHAR | 50 | `NOT NULL`, `FK → DM_TO_CHUC_KIEM_DINH(ID)` | Mã tổ chức kiểm định |
| 4 | `MA_KET_QUA_KIEM_DINH` | VARCHAR | 2 | `NOT NULL`, `FK → DM_KET_QUA_KIEM_DINH(ID)` | Kết quả kiểm định |
| 5 | `SO_QUYET_DINH` | VARCHAR | 50 | — | Số quyết định công nhận |
| 6 | `NGAY_CHUNG_NHAN_KIEM_DINH` | DATE | — | — | Ngày cấp chứng nhận |
| 7 | `THOI_HAN_KIEM_DINH` | DATE | — | — | Ngày hết hạn chứng nhận |
| 8 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 9 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật lần cuối |

---
## 3. Danh sách Index

| STT | Tên Index | Trường | Mô tả |
|-----|----------|--------|-------|
| 1 | `IX_TTKD_MA_CHUONG_TRINH_DAO_TAO` | `MA_CHUONG_TRINH_DAO_TAO` | Truy vấn theo mã CTĐT |
| 2 | `IX_TTKD_MA_TO_CHUC_KIEM_DINH` | `MA_TO_CHUC_KIEM_DINH` | Lọc theo tổ chức kiểm định |

---
## 4. Bảng DM liên quan

| STT | Bảng Danh Mục | Trường tham chiếu | Chi tiết |
|-----|---------------|-------------------|----------|
| 1 | `DM_TO_CHUC_KIEM_DINH` | `MA_TO_CHUC_KIEM_DINH` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#11-dm_to_chuc_kiem_dinh) |
| 2 | `DM_KET_QUA_KIEM_DINH` | `MA_KET_QUA_KIEM_DINH` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#12-dm_ket_qua_kiem_dinh) |
