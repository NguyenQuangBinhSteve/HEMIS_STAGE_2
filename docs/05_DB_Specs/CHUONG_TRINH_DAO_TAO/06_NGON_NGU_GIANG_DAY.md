# Sub-module 6: NGON_NGU_GIANG_DAY

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/NGON_NGU_GIANG_DAY.sql`

---
## 1. Mô tả

Lưu trữ thông tin ngôn ngữ sử dụng trong giảng dạy của chương trình đào tạo và yêu cầu trình độ ngoại ngữ đầu vào. Một CTĐT có thể có nhiều ngôn ngữ giảng dạy.

---
## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `MA_CHUONG_TRINH_DAO_TAO` | VARCHAR | 50 | `NOT NULL`, `FK → CHUONG_TRINH_DAO_TAO(MA_CHUONG_TRINH)` | Mã CTĐT (liên kết bảng chính) |
| 3 | `MA_NGON_NGU_GIANG_DAY` | VARCHAR | 10 | `NOT NULL`, `FK → DM_NGOAI_NGU(ID)` | Mã ngôn ngữ giảng dạy |
| 4 | `MA_KHUNG_NLUC_NNGU_DAU_VAO` | INT | — | `FK → DM_KHUNG_NANG_LUC_NGOAI_NGU(ID)` | Trình độ ngôn ngữ đầu vào yêu cầu (Bậc 1–6) |
| 5 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 6 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật lần cuối |

---
## 3. Danh sách Index

| STT | Tên Index                         | Trường                    | Mô tả                       |
| --- | --------------------------------- | ------------------------- | --------------------------- |
| 1   | `IX_NNGD_MA_CHUONG_TRINH_DAO_TAO` | `MA_CHUONG_TRINH_DAO_TAO` | Truy vấn theo mã CTĐT       |
| 2   | `IX_NNGD_MA_NGON_NGU_GIANG_DAY`   | `MA_NGON_NGU_GIANG_DAY`   | Lọc theo ngôn ngữ giảng dạy |

---
## 4. Bảng DM liên quan

| STT | Bảng Danh Mục | Trường tham chiếu | Chi tiết |
|-----|---------------|-------------------|----------|
| 1 | `DM_NGOAI_NGU` | `MA_NGON_NGU_GIANG_DAY` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#13-dm_ngoai_ngu) |
| 2 | `DM_KHUNG_NANG_LUC_NGOAI_NGU` | `MA_KHUNG_NLUC_NNGU_DAU_VAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#14-dm_khung_nang_luc_ngoai_ngu) |
