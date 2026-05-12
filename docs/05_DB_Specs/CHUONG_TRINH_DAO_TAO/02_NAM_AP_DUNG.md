# Sub-module 2: NAM_AP_DUNG

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/NAM_AP_DUNG.sql`

---
## 1. Mô tả

Lưu trữ thông tin chỉ tiêu tuyển sinh và học phí theo từng năm áp dụng của chương trình đào tạo. Mỗi bản ghi đại diện cho một phiên bản năm áp dụng cụ thể của một CTĐT.

---
## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `MA_CHUONG_TRINH_DAO_TAO` | VARCHAR | 50 | `NOT NULL`, `FK → CHUONG_TRINH_DAO_TAO(MA_CHUONG_TRINH)` | Mã chương trình đào tạo (liên kết bảng chính) |
| 3 | `MA_CHUONG_TRINH_NAM_AP_DUNG` | VARCHAR | 50 | `NOT NULL`, `UNIQUE` | Mã chương trình theo năm áp dụng (duy nhất) |
| 4 | `SO_TIN_CHI` | INT | — | `NOT NULL` | Tổng số tín chỉ của chương trình |
| 5 | `TONG_HOC_PHI` | BIGINT | — | — | Tổng học phí toàn khóa học (VNĐ) |
| 6 | `NAM_AP_DUNG` | INT | — | `NOT NULL` | Năm bắt đầu áp dụng |
| 7 | `CHI_TIEU_HANG_NAM` | INT | — | — | Chỉ tiêu tuyển sinh hằng năm |
| 8 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 9 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật bản ghi lần cuối |

---
## 3. Danh sách Index

| STT | Tên Index | Trường | Mô tả |
|-----|----------|--------|-------|
| 1 | `IX_NAD_MA_CHUONG_TRINH_DAO_TAO` | `MA_CHUONG_TRINH_DAO_TAO` | Tăng tốc truy vấn theo mã CTĐT |
| 2 | `IX_NAD_NAM_AP_DUNG` | `NAM_AP_DUNG` | Tăng tốc lọc theo năm áp dụng |

---
## 4. Bảng DM liên quan

Bảng `NAM_AP_DUNG` không tham chiếu trực tiếp đến bảng Danh Mục nào. Bảng này liên kết với bảng chính `CHUONG_TRINH_DAO_TAO` thông qua trường `MA_CHUONG_TRINH_DAO_TAO`.
