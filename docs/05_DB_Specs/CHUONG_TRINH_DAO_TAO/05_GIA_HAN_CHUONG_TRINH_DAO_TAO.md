# Sub-module 5: GIA_HAN_CHUONG_TRINH_DAO_TAO

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/GIA_HAN_CHUONG_TRINH_DAO_TAO.sql`

---

## 1. Mô tả

Lưu trữ thông tin gia hạn thời gian thực hiện chương trình đào tạo, bao gồm số quyết định gia hạn, ngày gia hạn, và lần gia hạn thứ mấy.

---

## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `MA_CHUONG_TRINH_DAO_TAO` | VARCHAR | 50 | `NOT NULL`, `FK → CHUONG_TRINH_DAO_TAO(MA_CHUONG_TRINH)` | Mã CTĐT (liên kết bảng chính) |
| 3 | `SO_QD_GIA_HAN` | VARCHAR | 50 | `NOT NULL` | Số quyết định gia hạn |
| 4 | `NGAY_GIA_HAN` | DATE | — | `NOT NULL` | Ngày ban hành văn bản gia hạn |
| 5 | `LAN_GIA_HAN` | INT | — | — | Gia hạn lần thứ mấy |
| 6 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 7 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật lần cuối |

---

## 3. Danh sách Index

| STT | Tên Index | Trường | Mô tả |
|-----|----------|--------|-------|
| 1 | `IX_GHCTDT_MA_CHUONG_TRINH_DAO_TAO` | `MA_CHUONG_TRINH_DAO_TAO` | Truy vấn theo mã CTĐT |

---

## 4. Bảng DM liên quan

Bảng `GIA_HAN_CHUONG_TRINH_DAO_TAO` không tham chiếu trực tiếp đến bảng Danh Mục nào. Bảng này liên kết với bảng chính `CHUONG_TRINH_DAO_TAO` thông qua trường `MA_CHUONG_TRINH_DAO_TAO`.
