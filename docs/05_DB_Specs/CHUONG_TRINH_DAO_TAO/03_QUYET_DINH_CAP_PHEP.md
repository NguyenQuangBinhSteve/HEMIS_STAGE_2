# Sub-module 3: QUYET_DINH_CAP_PHEP

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/QUYET_DINH_CAP_PHEP.sql`

---

## 1. Mô tả

Lưu trữ các quyết định và văn bản phê duyệt liên quan đến chương trình đào tạo, bao gồm quyết định cấp phép chương trình, cho phép đào tạo từ xa, và cho phép đào tạo liên thông.

---

## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `MA_CHUONG_TRINH_DAO_TAO` | VARCHAR | 50 | `NOT NULL`, `FK → CHUONG_TRINH_DAO_TAO(MA_CHUONG_TRINH)` | Mã chương trình đào tạo (liên kết bảng chính) |
| 3 | `MA_LOAI_QUYET_DINH` | VARCHAR | 2 | `NOT NULL`, `FK → DM_LOAI_QUYET_DINH_CTDT(ID)` | Loại quyết định (cấp phép / từ xa / liên thông) |
| 4 | `SO_QD_PHE_DUYET` | VARCHAR | 50 | — | Số quyết định phê duyệt |
| 5 | `NGAY_QD_PHE_DUYET` | DATE | — | — | Ngày ban hành quyết định phê duyệt |
| 6 | `HINH_THUC_DAO_TAO` | VARCHAR | 10 | `NOT NULL`, `FK → DM_HINH_THUC_DAO_TAO(ID)` | Hình thức đào tạo áp dụng cho quyết định |
| 7 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 8 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật bản ghi lần cuối |

---

## 3. Danh sách Index

| STT | Tên Index | Trường | Mô tả |
|-----|----------|--------|-------|
| 1 | `IX_QDCP_MA_CHUONG_TRINH_DAO_TAO` | `MA_CHUONG_TRINH_DAO_TAO` | Tăng tốc truy vấn theo mã CTĐT |
| 2 | `IX_QDCP_MA_LOAI_QUYET_DINH` | `MA_LOAI_QUYET_DINH` | Tăng tốc lọc theo loại quyết định |

---

## 4. Bảng DM liên quan

| STT | Bảng Danh Mục | Trường tham chiếu | Chi tiết |
|-----|---------------|-------------------|----------|
| 1 | `DM_LOAI_QUYET_DINH_CTDT` | `MA_LOAI_QUYET_DINH` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#10-dm_loai_quyet_dinh_ctdt) |
| 2 | `DM_HINH_THUC_DAO_TAO` | `HINH_THUC_DAO_TAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#3-dm_hinh_thuc_dao_tao) |
