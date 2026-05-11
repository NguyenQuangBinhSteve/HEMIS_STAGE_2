# Phụ Lục: Bảng Danh Mục (DM) Liên Quan

> **Module**: Chương Trình Đào Tạo  
> **Tổng số bảng DM**: 14  
> **Nguồn SQL**: `db_hemis_stu_new/sql_pg/danh_muc/tables/`

Tài liệu này liệt kê toàn bộ giá trị của 14 bảng Danh Mục được tham chiếu bởi các sub-module trong module Chương Trình Đào Tạo.

---

## 1. DM_NGANH

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID` (mã ngành), `TEN_NGANH` (tên ngành)
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.NGANH_ID`
- **Tổng số bản ghi**: ~1.300+ ngành
- **File SQL**: `DM_NGANH.sql`

**Mẫu đại diện** (theo khối ngành):

| ID | TEN_NGANH |
|----|-----------|
| 7140201 | Giáo dục Mầm non |
| 7140209 | Sư phạm Toán học |
| 7140231 | Sư phạm Tiếng Anh |
| 7340101 | Quản trị kinh doanh |
| 7340201 | Tài chính - Ngân hàng |
| 7380101 | Luật |
| 7480101 | Khoa học máy tính |
| 7480201 | Công nghệ thông tin |
| 7480202 | An toàn thông tin |
| 7520201 | Kỹ thuật điện |
| 7580201 | Kỹ thuật xây dựng |
| 7720101 | Y khoa |
| 7720201 | Dược học |
| 7720301 | Điều dưỡng |

> **Lưu ý**: Bảng DM_NGANH chứa hơn 1.300 bản ghi. Xem toàn bộ danh sách tại file SQL gốc: `db_hemis_stu_new/sql_pg/danh_muc/tables/DM_NGANH.sql`

---

## 2. DM_LOAI_CHUONG_TRINH_DAO_TAO

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_LOAI`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.LOAI_CHUONG_TRINH_DAO_TAO`

| ID | TEN_LOAI |
|----|----------|
| 1 | Chương trình đào tạo đại học đại trà |
| 2 | Chương trình chất lượng cao (Thông tư 23) |
| 3 | Chương trình tiến tiến |
| 4 | Chương trình kỹ sư chất lượng cao PFIEV |
| 5 | Chương trình POHE |
| 6 | Chương trình dự bị đại học |
| 7 | Chương trình LKĐTNN do cơ sở đào tạo trong nước cấp bằng |
| 9 | Chương trình LKĐTNN do cơ sở đào tạo nước ngoài cấp bằng |
| 10 | Chương trình LKĐTNN do cơ sở đào tạo nước ngoài và trong nước cùng cấp bằng |
| 11 | Chương trình chất lượng cao (do CSĐT tự xác định) cho các trình độ của GDĐH, cao đẳng sư phạm |
| 12 | Chương trình cao đẳng sư phạm đại trà |
| 13 | Chương trình đào tạo VLVH |
| 14 | Chương trình ĐTTX |
| 15 | Chương trình thạc sĩ định hướng ứng dụng |
| 16 | Chương trình thạc sĩ định hướng nghiên cứu |
| 17 | Chương trình tiến sĩ |
| 18 | Khác |

---

## 3. DM_HINH_THUC_DAO_TAO

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID`, `TEN_HINH_THUC`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.HINH_THUC_DAO_TAO`, `QUYET_DINH_CAP_PHEP.HINH_THUC_DAO_TAO`

| ID | TEN_HINH_THUC |
|----|---------------|
| CQ | Chính quy |
| VHVL | Vừa làm - Vừa học |
| DTTX | Đào tạo từ xa |

---

## 4. DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_LOAI`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.LOAI_CHUONG_TRINH_LIEN_KET`

| ID | TEN_LOAI |
|----|----------|
| 1 | 100% tại Việt Nam |
| 2 | Tại Việt Nam và tại nước ngoài |
| 3 | Khác |

---

## 5. DM_HOC_CHE_DAO_TAO

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_HOC_CHE`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.HOC_CHE_DAO_TAO`

| ID | TEN_HOC_CHE |
|----|-------------|
| 1 | Niên chế |
| 2 | Tín chỉ |
| 3 | Kết hợp tín chỉ và kết quả |

---

## 6. DM_NUOC

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID` (mã quốc gia), `TEN_NUOC`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.QUOC_GIA`
- **Tổng số bản ghi**: 169 quốc gia

**Danh sách đầy đủ:**

| ID | TEN_NUOC |
|----|----------|
| 0 | Khác |
| 004 | Áp-ga-ni-xtan |
| 008 | An ba ni |
| 012 | An giê ri |
| 024 | Ăng-gô-la |
| 031 | A-déc-bai-gian |
| 032 | Ac-hen-ti-na |
| 036 | Úc |
| 040 | Áo |
| 048 | Ba-ranh |
| 050 | Băng-la-đét |
| 051 | Ar-mê-nia |
| 056 | Bỉ |
| 064 | BuTan |
| 068 | Bô-li-vi-a |
| 070 | Booxx-na He-rơ-xe-gô-vin-na |
| 072 | Bốt-xoa-na |
| 076 | B-ra-xin |
| 090 | Quần đảo Sô-lô-môn |
| 096 | B-ru-nây |
| 100 | Bun-ga-ri |
| 104 | My-an-ma |
| 108 | Bu-run-đi |
| 112 | Be-lo-rut-xia |
| 116 | Căm-Pu-Chia |
| 120 | Can-mơ-run |
| 124 | Ca-na-đa |
| 140 | Cộng hòa Trung Phi |
| 144 | Sri-lan-ka |
| 148 | Sat |
| 149 | Nam Phi |
| 152 | Chi Lê |
| 156 | CHND Trung Hoa |
| 158 | Đài Loan |
| 168 | Cộng hòa Gabon |
| 170 | Cô-lôm-bi-a |
| 180 | Cộng hòa Dân chủ Congo |
| 188 | Cốt-ta-ri-ca |
| 191 | Croát-ti-a |
| 192 | Cuba |
| 203 | Cộng hòa Séc |
| 208 | Đan Mạch |
| 212 | Dominica |
| 218 | Ê-cu-a-đo |
| 222 | En Xan-va-đo |
| 233 | Et-to-ni |
| 242 | Phi-Ghi |
| 246 | Phần Lan |
| 250 | Pháp |
| 268 | Gru-dia |
| 270 | Dăm-bia |
| 275 | Pa-le-xtin |
| 276 | Đức |
| 288 | Gha-na |
| 296 | Ki-ri-ba-ti |
| 300 | Hy Lạp |
| 320 | Goa-tê-ma-la |
| 324 | Ghi nê |
| 328 | Guy-a-na |
| 332 | Ha-i-ti |
| 340 | Ôn-đu-rát |
| 344 | Hồng Công |
| 348 | Hung-ga-ri |
| 352 | Ai-xlen |
| 356 | Ấn Độ |
| 360 | In-đô-nê-xi-a |
| 364 | I-ran |
| 368 | I-rắc |
| 372 | A-rơ-len |
| 376 | I-xra-en |
| 380 | I-ta-li-a |
| 388 | Gia-mai-ca |
| 392 | Nhật Bản |
| 398 | Ka-dắc-xtan |
| 400 | Giooc-đa-ni |
| 404 | Kê-nia |
| 408 | Triều Tiên |
| 410 | Hàn Quốc |
| 414 | Cô-oét |
| 417 | Cư-rơ-gư-dơ-xtan |
| 418 | CHDCND Lào |
| 422 | Li Băng |
| 428 | Latvia |
| 430 | Li-bê-ri-a |
| 434 | Libi |
| 438 | Lit-ten-xten |
| 440 | Lit-va |
| 442 | Luc-xem-bua |
| 446 | Ma Cao |
| 450 | Ma-đa-gát-xka |
| 454 | Ma-la0uy |
| 458 | Ma-lai-xi-a |
| 462 | Man-đi-vơ |
| 466 | Ma-li |
| 470 | Man-ta |
| 480 | Mau-ri-ti-út |
| 484 | Mê hi cô |
| 492 | Ma-na-cô |
| 496 | Mông Cổ |
| 498 | Môn-đô-va |
| 499 | Mông-tê-nê-groo |
| 504 | Ma Rốc |
| 508 | Mô-dăm-bich |
| 512 | Ô man |
| 516 | Nam-mi-bia |
| 524 | Nê-Pan |
| 528 | Hà Lan |
| 548 | Va-nu-a-tu |
| 554 | Ni Di Lân |
| 558 | Ni-ca-ra-goa |
| 562 | Ni-giê |
| 566 | Ni-giê-ri-a |
| 578 | Na Uy |
| 583 | Mi-cờ-rô-nê-xia-a |
| 584 | Quần đảo Ma-rơ-san |
| 586 | Pa-Ki-xtan |
| 591 | Pa-na-ma |
| 598 | Pa-pua Niu Ghi - nê |
| 600 | Pa-ra-goay |
| 604 | Pê - ru |
| 608 | Phi-li-pin |
| 616 | Ba Lan |
| 620 | Bồ Đào Nha |
| 626 | Đông-ti-mo |
| 630 | Pu-ec-to Ri-cô |
| 634 | Quatar |
| 642 | Ru-ma-no |
| 643 | Liên Bang Nga |
| 646 | Ruanđa |
| 682 | Ả rập Xê út |
| 686 | Xê nê gan |
| 688 | Sec-bi-a |
| 694 | Xi-ê-a Lê- ôn |
| 702 | Xin-ga-po |
| 703 | Slô-vac-ki-a |
| 704 | Việt Nam |
| 705 | Slô-ven-ni-a |
| 706 | Sô-ma-li |
| 716 | Dim-ba-buê |
| 724 | Tây Ban Nha |
| 728 | Nam Xu-đăng |
| 736 | Xu-đăng |
| 752 | Thụy Điển |
| 756 | Thụy Sỹ |
| 760 | Si-ri-a |
| 762 | Tat-gi-ki-xtan |
| 764 | Thái Lan |
| 768 | To-go |
| 776 | Tôn-ga |
| 784 | Các Tiểu vương quốc Ả Rập Thống Nhất |
| 788 | Tuy-ni-di |
| 792 | Thổ Nhĩ Kỳ |
| 795 | Turkmênistan |
| 798 | Tu-va-lu |
| 800 | U-gan-da |
| 804 | U-krai-na |
| 807 | Ma-xê-đô-ni-a |
| 818 | Ai cập |
| 826 | Vương Quốc Anh |
| 834 | Tan-da-nia |
| 840 | Mỹ |
| 854 | Buốc-ki-na-pha-xô |
| 858 | U-ru-goay |
| 860 | U-dơ-bê-ki-xtan |
| 862 | Vê-nê-xu-ê-la |
| 882 | Tây Sa-moa |
| 887 | Y-ê-men |
| 894 | Gam-bi-a |

---

## 7. DM_TRINH_DO_DAO_TAO

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_TRINH_DO`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.TRINH_DO_DAO_TAO`

| ID | TEN_TRINH_DO |
|----|--------------|
| 1 | Sơ cấp I |
| 8 | Sơ cấp II |
| 9 | Sơ cấp III |
| 2 | Trung cấp |
| 3 | Cao đẳng |
| 4 | Đại học |
| 5 | Thạc sĩ |
| 6 | Tiến sĩ |
| 7 | Tiến sĩ khoa học |

---

## 8. DM_DON_VI_CAP_BANG

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_DON_VI`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.DON_VI_CAP_BANG`

| ID | TEN_DON_VI |
|----|------------|
| 1 | CS nước ngoài cấp |
| 2 | CS Việt Nam cấp |
| 3 | Cấp 2 bằng |

---

## 9. DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_TRANG_THAI`
- **Tham chiếu bởi**: `CHUONG_TRINH_DAO_TAO.TRANG_THAI`

| ID | TEN_TRANG_THAI |
|----|----------------|
| 1 | Đang tuyển sinh và đào tạo |
| 2 | Dừng tuyển sinh (vẫn đang đào tạo) |
| 3 | Chương trình mới phê duyệt chưa thực hiện tuyển sinh |
| 4 | Khác |
| 5 | Dừng tuyển sinh và đào tạo |

---

## 10. DM_LOAI_QUYET_DINH_CTDT

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID`, `TEN_QUYET_DINH`
- **Tham chiếu bởi**: `QUYET_DINH_CAP_PHEP.MA_LOAI_QUYET_DINH`

| ID | TEN_QUYET_DINH |
|----|----------------|
| 01 | Phê duyệt cấp phép chương trình |
| 02 | Cho phép đào tạo từ xa |
| 03 | Cho phép đào tạo liên thông |

---

## 11. DM_TO_CHUC_KIEM_DINH

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID`, `TEN_TO_CHUC`
- **Tham chiếu bởi**: `THONG_TIN_KIEM_DINH.MA_TO_CHUC_KIEM_DINH`

| ID | TEN_TO_CHUC |
|----|-------------|
| VNU-CEA | Trung tâm Kiểm định chất lượng giáo dục - ĐHQG Hà Nội |
| VNU-HCM CEA | Trung tâm Kiểm định chất lượng giáo dục - ĐHQG TP.HCM |
| CEA-UD | Trung tâm Kiểm định chất lượng giáo dục - ĐH Đà Nẵng |
| CEA-AVU&C | Trung tâm Kiểm định CLGD trực thuộc Hiệp hội các trường ĐH, CĐ Việt Nam |
| VU-CEA | Trung tâm Kiểm định chất lượng giáo dục - Trường ĐH Vinh |
| CEA-SAIGON | Trung tâm Kiểm định chất lượng giáo dục Sài Gòn |
| CEA-THANGLONG | Trung tâm Kiểm định chất lượng giáo dục Thăng Long |
| AUN-QA | ASEAN University Network - Quality Assurance |
| CTI | Commission des Titres d'Ingénieur (Pháp) |
| ABET | Accreditation Board for Engineering and Technology (Hoa Kỳ) |
| ACBSP | Accreditation Council for Business Schools and Programs (Hoa Kỳ) |
| FIBAA | Foundation for International Business Administration Accreditation |
| AMBA | Association of MBAs |
| IACBE | International Accreditation Council for Business Education |
| ENAEE | European Network for Accreditation of Engineering Education |
| HCERES | Hội đồng đánh giá nghiên cứu và giáo dục đại học Pháp |
| ASIIN | Tổ chức kiểm định ngành kỹ thuật, CNTT, KHTN và toán học |
| AACSB | Hiệp hội phát triển giảng dạy kinh doanh bậc đại học |
| AAQ | Tổ chức Kiểm định và Đảm bảo chất lượng Thụy Sĩ |
| ACQUIN | Viện Đảm bảo chất lượng, Kiểm định và Cấp chứng nhận |
| ZEvA | Tổ chức Kiểm định và Đánh giá trung ương |
| TEQSA | Cơ quan Tiêu chuẩn và Chất lượng Giáo dục ĐH Australia |

---

## 12. DM_KET_QUA_KIEM_DINH

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID`, `TEN_KET_QUA`
- **Tham chiếu bởi**: `THONG_TIN_KIEM_DINH.MA_KET_QUA_KIEM_DINH`

| ID | TEN_KET_QUA |
|----|-------------|
| 01 | Đạt |
| 02 | Đạt có điều kiện |
| 03 | Chưa kiểm định |

---

## 13. DM_NGOAI_NGU

- **Kiểu PK**: `VARCHAR(500)`
- **Cấu trúc**: `ID`, `TEN_NGOAI_NGU`
- **Tham chiếu bởi**: `NGON_NGU_GIANG_DAY.MA_NGON_NGU_GIANG_DAY`

| ID | TEN_NGOAI_NGU |
|----|---------------|
| en | Tiếng Anh |
| de | Tiếng Đức |
| fr | Tiếng Pháp |
| jp | Tiếng Nhật |
| cn | Tiếng Trung Quốc |
| ru | Tiếng Nga |
| kr | Tiếng Hàn Quốc |
| vn | Tiếng Việt |
| nnk | Ngoại ngữ khác |

---

## 14. DM_KHUNG_NANG_LUC_NGOAI_NGU

- **Kiểu PK**: `INT`
- **Cấu trúc**: `ID`, `TEN_BAC`
- **Tham chiếu bởi**: `NGON_NGU_GIANG_DAY.MA_KHUNG_NLUC_NNGU_DAU_VAO`

| ID | TEN_BAC |
|----|---------|
| 1 | Bậc 1 |
| 2 | Bậc 2 |
| 3 | Bậc 3 |
| 4 | Bậc 4 |
| 5 | Bậc 5 |
| 6 | Bậc 6 |
