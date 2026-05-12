# Sub-module 1: CHUONG_TRINH_DAO_TAO

> **Module**: Chương Trình Đào Tạo  
> **Schema**: `chuong_trinh_dao_tao`  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/CHUONG_TRINH_DAO_TAO.sql`

---
## 1. Mô tả

Bảng chính của module — lưu trữ thông tin chi tiết về các chương trình đào tạo của cơ sở giáo dục, bao gồm: mã chương trình, ngành đào tạo, hình thức đào tạo, trình độ, chuẩn đầu ra, học phí, và trạng thái hoạt động.

---
## 2. Đặc tả các trường

| STT | Tên trường | Kiểu dữ liệu | Độ dài | Ràng buộc | Mô tả |
|-----|-----------|---------------|--------|-----------|-------|
| 1 | `ID` | SERIAL | — | `PK` | Khóa chính, tự tăng |
| 2 | `NGANH_ID` | VARCHAR | 20 | `NOT NULL`, `FK → DM_NGANH(ID)` | Mã ngành đào tạo |
| 3 | `MA_CHUONG_TRINH` | VARCHAR | 50 | `NOT NULL`, `UNIQUE` | Mã chương trình đào tạo (duy nhất toàn hệ thống) |
| 4 | `TEN` | VARCHAR | 500 | `NOT NULL` | Tên chương trình đào tạo |
| 5 | `TEN_TIENG_ANH` | VARCHAR | 500 | — | Tên chương trình bằng tiếng Anh |
| 6 | `NAM_TUYEN_SINH` | INT | — | `NOT NULL` | Năm bắt đầu tuyển sinh |
| 7 | `TEN_CO_SO_DAO_TAO` | VARCHAR | 500 | — | Tên cơ sở đào tạo nước ngoài (dùng cho CTĐT liên kết) |
| 8 | `LOAI_CHUONG_TRINH_DAO_TAO` | INT | — | `NOT NULL`, `FK → DM_LOAI_CHUONG_TRINH_DAO_TAO(ID)` | Loại chương trình đào tạo |
| 9 | `HINH_THUC_DAO_TAO` | VARCHAR | 10 | `NOT NULL`, `FK → DM_HINH_THUC_DAO_TAO(ID)` | Hình thức đào tạo (CQ, VHVL, DTTX) |
| 10 | `LOAI_CHUONG_TRINH_LIEN_KET` | INT | — | `FK → DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO(ID)` | Loại chương trình liên kết đào tạo |
| 11 | `DIA_DIEM_DAO_TAO` | VARCHAR | 500 | — | Địa điểm tổ chức đào tạo |
| 12 | `HOC_CHE_DAO_TAO` | INT | — | `NOT NULL`, `FK → DM_HOC_CHE_DAO_TAO(ID)` | Học chế đào tạo (Niên chế / Tín chỉ / Kết hợp) |
| 13 | `QUOC_GIA` | VARCHAR | 10 | `FK → DM_NUOC(ID)` | Quốc gia (áp dụng cho CTĐT liên kết quốc tế) |
| 14 | `NGAY_BAN_HANH_CHUAN_DAU_RA` | DATE | — | `NOT NULL` | Ngày ban hành Chuẩn đầu ra của chương trình |
| 15 | `TRINH_DO_DAO_TAO` | INT | — | `NOT NULL`, `FK → DM_TRINH_DO_DAO_TAO(ID)` | Trình độ đào tạo (Đại học / Thạc sĩ / Tiến sĩ...) |
| 16 | `THOI_GIAN_DAO_TAO_CHUAN` | INT | — | `NOT NULL` | Thời gian đào tạo chuẩn (tính theo tháng hoặc năm) |
| 17 | `CHUAN_DAU_RA` | TEXT | Không giới hạn | — | Nội dung chuẩn đầu ra của chương trình |
| 18 | `DON_VI_CAP_BANG` | INT | — | `FK → DM_DON_VI_CAP_BANG(ID)` | Đơn vị cấp bằng tốt nghiệp |
| 19 | `LOAI_CHUNG_CHI_DUOC_CHAP_THUAN` | VARCHAR | 500 | — | Các loại chứng chỉ được chấp thuận thay thế |
| 20 | `DON_VI_THUC_HIEN` | VARCHAR | 500 | — | Đơn vị thực hiện chương trình đào tạo |
| 21 | `TRANG_THAI` | INT | — | `FK → DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO(ID)` | Trạng thái hoạt động của chương trình |
| 22 | `CHUAN_DAU_RA_NGOAI_NGU` | TEXT | Không giới hạn | — | Yêu cầu chuẩn đầu ra về ngoại ngữ |
| 23 | `CHUAN_DAU_RA_TIN_HOC` | TEXT | Không giới hạn | — | Yêu cầu chuẩn đầu ra về tin học |
| 24 | `HOC_PHI_TRONG_NUOC` | BIGINT | — | — | Học phí áp dụng tại Việt Nam (VNĐ) |
| 25 | `HOC_PHI_NUOC_NGOAI` | BIGINT | — | — | Học phí áp dụng tại nước ngoài (VNĐ) |
| 26 | `CREATED_AT` | TIMESTAMP | — | `DEFAULT CURRENT_TIMESTAMP` | Thời gian tạo bản ghi |
| 27 | `UPDATED_AT` | TIMESTAMP | — | — | Thời gian cập nhật bản ghi lần cuối |

---
## 3. Danh sách Index

| STT | Tên Index                  | Trường             | Mô tả                                     |
| --- | -------------------------- | ------------------ | ----------------------------------------- |
| 1   | `IX_CTDT_NGANH_ID`         | `NGANH_ID`         | Tăng tốc truy vấn theo ngành đào tạo      |
| 2   | `IX_CTDT_MA_CHUONG_TRINH`  | `MA_CHUONG_TRINH`  | Tăng tốc tra cứu theo mã chương trình     |
| 3   | `IX_CTDT_TRINH_DO_DAO_TAO` | `TRINH_DO_DAO_TAO` | Tăng tốc lọc theo trình độ đào tạo        |
| 4   | `IX_CTDT_TRANG_THAI`       | `TRANG_THAI`       | Tăng tốc lọc theo trạng thái chương trình |

---
## 4. Bảng DM liên quan

| STT | Bảng Danh Mục | Trường tham chiếu | Chi tiết |
|-----|---------------|-------------------|----------|
| 1 | `DM_NGANH` | `NGANH_ID` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#1-dm_nganh) |
| 2 | `DM_LOAI_CHUONG_TRINH_DAO_TAO` | `LOAI_CHUONG_TRINH_DAO_TAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#2-dm_loai_chuong_trinh_dao_tao) |
| 3 | `DM_HINH_THUC_DAO_TAO` | `HINH_THUC_DAO_TAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#3-dm_hinh_thuc_dao_tao) |
| 4 | `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO` | `LOAI_CHUONG_TRINH_LIEN_KET` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#4-dm_loai_chuong_trinh_lien_ket_dao_tao) |
| 5 | `DM_HOC_CHE_DAO_TAO` | `HOC_CHE_DAO_TAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#5-dm_hoc_che_dao_tao) |
| 6 | `DM_NUOC` | `QUOC_GIA` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#6-dm_nuoc) |
| 7 | `DM_TRINH_DO_DAO_TAO` | `TRINH_DO_DAO_TAO` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#7-dm_trinh_do_dao_tao) |
| 8 | `DM_DON_VI_CAP_BANG` | `DON_VI_CAP_BANG` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#8-dm_don_vi_cap_bang) |
| 9 | `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO` | `TRANG_THAI` | [Xem phụ lục](./PHU_LUC_DANH_MUC.md#9-dm_trang_thai_chuong_trinh_dao_tao) |
