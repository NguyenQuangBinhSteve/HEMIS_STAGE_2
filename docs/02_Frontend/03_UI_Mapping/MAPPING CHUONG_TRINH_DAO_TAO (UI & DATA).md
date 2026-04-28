Dưới đây là bảng mapping đã được cập nhật lại cột **Trường CSDL (DB Field)** theo đúng chuẩn cột "Tên trường" trong tài liệu "Đặc tả core schema" đối với bảng `CHUONG_TRINH_DAO_TAO`, các cột còn lại (Tên hiển thị FE, Component, Ràng buộc) vẫn được giữ nguyên như bạn yêu cầu:

| STT | Trường CSDL (DB Field)             | Tên hiển thị (FE Label)                             | Component FE gợi ý         | Ràng buộc / Mapping Dữ liệu                                                  |
| :-- | :--------------------------------- | :-------------------------------------------------- | :------------------------- | :--------------------------------------------------------------------------- |
| 1   | **ID**                             | ID tự tăng                                          | _Hidden_                   | Khóa chính tự tăng (PK).                                                     |
| 2   | **NGANH_ID**                       | Mã ngành đào tạo                                    | Dropdown / Select          | **Bắt buộc (NOT NULL)**. Tham chiếu danh mục `DM_NGANH`.                     |
| 3   | **MA_CHUONG_TRINH**                | Mã chương trình đào tạo                             | Input Text                 | **Bắt buộc (NOT NULL), Không trùng (UNIQUE)**.                               |
| 4   | **TEN**                            | Tên chương trình                                    | Input Text                 | **Bắt buộc (NOT NULL)**. Tối đa 500 ký tự.                                   |
| 5   | **TEN_TIENG_ANH**                  | Tên chương trình bằng tiếng Anh                     | Input Text                 | Có thể trống. Tối đa 500 ký tự.                                              |
| 6   | **NAM_TUYEN_SINH**                 | Năm bắt đầu tuyển sinh                              | Year Picker / Input Number | **Bắt buộc (NOT NULL)**.                                                     |
| 7   | **TEN_CO_SO_DAO_TAO**              | Tên cơ sở đào tạo nước ngoài                        | Input Text                 | Có thể trống. Tối đa 500 ký tự.                                              |
| 8   | **LOAI_CHUONG_TRINH_DAO_TAO**      | Mã loại chương trình đào tạo                        | Dropdown / Select          | **Bắt buộc (NOT NULL)**. Tham chiếu danh mục `DM_LOAI_CHUONG_TRINH_DAO_TAO`. |
| 9   | **HINH_THUC_DAO_TAO**              | Mã hình thức đào tạo                                | Dropdown / Select          | **Bắt buộc (NOT NULL)**. Tham chiếu danh mục `DM_HINH_THUC_DAO_TAO`.         |
| 10  | **LOAI_CHUONG_TRINH_LIEN_KET**     | Mã loại chương trình liên kết đào tạo               | Dropdown / Select          | Có thể trống. Tham chiếu `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO`.            |
| 11  | **DIA_DIEM_DAO_TAO**               | Địa điểm đào tạo                                    | Input Text / Textarea      | Có thể trống. Tối đa 500 ký tự.                                              |
| 12  | **HOC_CHE_DAO_TAO**                | Mã học chế đào tạo                                  | Dropdown / Select          | **Bắt buộc (NOT NULL)**. Tham chiếu danh mục `DM_HOC_CHE_DAO_TAO`.           |
| 13  | **QUOC_GIA**                       | Mã quốc gia của trường nước ngoài đặt trụ sở chính  | Dropdown / Select          | Có thể trống. Tham chiếu danh mục quốc gia (`DM_NUOC`).                      |
| 14  | **NGAY_BAN_HANH_CHUAN_DAU_RA**     | Ngày tháng năm ban hành Chuẩn đầu ra                | Date Picker                | **Bắt buộc (NOT NULL)**.                                                     |
| 15  | **TRINH_DO_DAO_TAO**               | Mã trình độ đào tạo                                 | Dropdown / Select          | **Bắt buộc (NOT NULL)**. Tham chiếu `DM_TRINH_DO_DAO_TAO`.                   |
| 16  | **THOI_GIAN_DAO_TAO_CHUAN**        | Thời gian đào tạo chuẩn                             | Input Number               | **Bắt buộc (NOT NULL)**.                                                     |
| 17  | **CHUAN_DAU_RA**                   | Chuẩn đầu ra                                        | Textarea / Rich Text       | Có thể trống. Dạng TEXT.                                                     |
| 18  | **DON_VI_CAP_BANG**                | Mã đơn vị cấp bằng                                  | Dropdown / Select          | Có thể trống. Tham chiếu `DM_DON_VI_CAP_BANG`.                               |
| 19  | **LOAI_CHUNG_CHI_DUOC_CHAP_THUAN** | Các loại chứng chỉ được chấp thuận cho chương trình | Input Text / Multi-select  | Có thể trống.                                                                |
| 20  | **DON_VI_THUC_HIEN**               | Đơn vị thực hiện chương trình                       | Input Text                 | Có thể trống.                                                                |
| 21  | **TRANG_THAI**                     | Mã trạng thái chương trình                          | Dropdown / Select          | Có thể trống. Tham chiếu `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO`.               |
| 22  | **CHUAN_DAU_RA_NGOAI_NGU**         | Chuẩn đầu ra ngoại ngữ                              | Textarea / Rich Text       | Có thể trống. Dạng TEXT. Tối đa 500 ký tự.                                   |
| 23  | **CHUAN_DAU_RA_TIN_HOC**           | Chuẩn đầu ra tin học                                | Textarea / Rich Text       | Có thể trống. Dạng TEXT. Tối đa 500 ký tự.                                   |
| 24  | **HOC_PHI_TRONG_NUOC**             | Học phí tại Việt Nam                                | Input Number (Currency)    | Có thể trống. Dạng DECIMAL.                                                  |
| 25  | **HOC_PHI_NUOC_NGOAI**             | Học phí tại nước ngoài                              | Input Number (Currency)    | Có thể trống. Dạng DECIMAL.                                                  |

