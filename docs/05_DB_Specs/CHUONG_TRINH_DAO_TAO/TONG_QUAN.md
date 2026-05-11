# Module: Chương Trình Đào Tạo (CTĐT)

> **Dự án**: HEMIS — Hệ thống Quản lý Giáo dục Đại học  
> **Module**: `chuong_trinh_dao_tao`  
> **Phiên bản**: 1.0  
> **Ngày tạo**: 2026-05-11  

---

## 1. Mô tả tổng quan

Module **Chương Trình Đào Tạo** quản lý toàn bộ thông tin liên quan đến các chương trình đào tạo của cơ sở giáo dục đại học, bao gồm: thông tin chương trình, năm áp dụng, quyết định cấp phép, kiểm định chất lượng, gia hạn, và ngôn ngữ giảng dạy.

---

## 2. Danh sách Sub-module (6 bảng)

| STT | Tên bảng | File đặc tả | Mô tả |
|-----|----------|-------------|-------|
| 1 | `CHUONG_TRINH_DAO_TAO` | [01_CHUONG_TRINH_DAO_TAO.md](./01_CHUONG_TRINH_DAO_TAO.md) | Bảng chính — lưu trữ thông tin chi tiết về các chương trình đào tạo |
| 2 | `NAM_AP_DUNG` | [02_NAM_AP_DUNG.md](./02_NAM_AP_DUNG.md) | Thông tin chỉ tiêu và học phí theo từng năm áp dụng |
| 3 | `QUYET_DINH_CAP_PHEP` | [03_QUYET_DINH_CAP_PHEP.md](./03_QUYET_DINH_CAP_PHEP.md) | Các quyết định, văn bản phê duyệt liên quan đến CTĐT |
| 4 | `THONG_TIN_KIEM_DINH` | [04_THONG_TIN_KIEM_DINH.md](./04_THONG_TIN_KIEM_DINH.md) | Thông tin kết quả kiểm định chất lượng CTĐT |
| 5 | `GIA_HAN_CHUONG_TRINH_DAO_TAO` | [05_GIA_HAN_CHUONG_TRINH_DAO_TAO.md](./05_GIA_HAN_CHUONG_TRINH_DAO_TAO.md) | Thông tin gia hạn thời gian thực hiện CTĐT |
| 6 | `NGON_NGU_GIANG_DAY` | [06_NGON_NGU_GIANG_DAY.md](./06_NGON_NGU_GIANG_DAY.md) | Ngôn ngữ sử dụng trong giảng dạy và yêu cầu đầu vào |

---

## 3. Bảng Danh Mục (DM) liên quan (15 bảng)

Chi tiết giá trị của từng bảng DM xem tại: [PHU_LUC_DANH_MUC.md](./PHU_LUC_DANH_MUC.md)

| STT | Bảng Danh Mục                           | Kiểu PK      | Sub-module tham chiếu  | Trường tham chiếu            |
| --- | --------------------------------------- | ------------ | ---------------------- | ---------------------------- |
| 1   | `DM_NGANH`                              | VARCHAR(500) | `CHUONG_TRINH_DAO_TAO` | `NGANH_ID`                   |
| 2   | `DM_LOAI_CHUONG_TRINH_DAO_TAO`          | INT          | `CHUONG_TRINH_DAO_TAO` | `LOAI_CHUONG_TRINH_DAO_TAO`  |
| 3   | `DM_HINH_THUC_DAO_TAO`                  | VARCHAR(500) | `CHUONG_TRINH_DAO_TAO` | `HINH_THUC_DAO_TAO`          |
| 4   | `DM_HINH_THUC_DAO_TAO`                  | VARCHAR(500) | `QUYET_DINH_CAP_PHEP`  | `HINH_THUC_DAO_TAO`          |
| 5   | `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO` | INT          | `CHUONG_TRINH_DAO_TAO` | `LOAI_CHUONG_TRINH_LIEN_KET` |
| 6   | `DM_HOC_CHE_DAO_TAO`                    | INT          | `CHUONG_TRINH_DAO_TAO` | `HOC_CHE_DAO_TAO`            |
| 7   | `DM_NUOC`                               | VARCHAR(500) | `CHUONG_TRINH_DAO_TAO` | `QUOC_GIA`                   |
| 8   | `DM_TRINH_DO_DAO_TAO`                   | INT          | `CHUONG_TRINH_DAO_TAO` | `TRINH_DO_DAO_TAO`           |
| 9   | `DM_DON_VI_CAP_BANG`                    | INT          | `CHUONG_TRINH_DAO_TAO` | `DON_VI_CAP_BANG`            |
| 10  | `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO`    | INT          | `CHUONG_TRINH_DAO_TAO` | `TRANG_THAI`                 |
| 11  | `DM_LOAI_QUYET_DINH_CTDT`               | VARCHAR(500) | `QUYET_DINH_CAP_PHEP`  | `MA_LOAI_QUYET_DINH`         |
| 12  | `DM_TO_CHUC_KIEM_DINH`                  | VARCHAR(500) | `THONG_TIN_KIEM_DINH`  | `MA_TO_CHUC_KIEM_DINH`       |
| 13  | `DM_KET_QUA_KIEM_DINH`                  | VARCHAR(500) | `THONG_TIN_KIEM_DINH`  | `MA_KET_QUA_KIEM_DINH`       |
| 14  | `DM_NGOAI_NGU`                          | VARCHAR(500) | `NGON_NGU_GIANG_DAY`   | `MA_NGON_NGU_GIANG_DAY`      |
| 15  | `DM_KHUNG_NANG_LUC_NGOAI_NGU`           | INT          | `NGON_NGU_GIANG_DAY`   | `MA_KHUNG_NLUC_NNGU_DAU_VAO` |

> **Lưu ý**: `DM_HINH_THUC_DAO_TAO` xuất hiện 2 lần (dòng 3 & 4) vì được tham chiếu bởi 2 sub-module khác nhau: `CHUONG_TRINH_DAO_TAO` và `QUYET_DINH_CAP_PHEP`.

---

## 4. Nguồn dữ liệu

| Thành phần | Đường dẫn |
|-----------|-----------|
| SQL Tables | `db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/` |
| SQL Danh Mục | `db_hemis_stu_new/sql_pg/danh_muc/tables/` |
