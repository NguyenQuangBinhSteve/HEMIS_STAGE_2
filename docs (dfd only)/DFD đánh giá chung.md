# i. CÁC SƠ ĐỒ SẼ CÓ

## 1.1 Mô tả cấu trúc bảng tổng hợp DFD

> - **Mục tiêu:** Tạo một bảng tóm tắt nhanh các sơ đồ DFD đã chốt để bạn dễ dàng theo dõi.
>
> - **Cấu trúc bảng:** Dù yêu cầu ban đầu là "3 cột" nhưng do có liệt kê 4 thành phần trong ngoặc, bảng được thiết kế gồm 4 cột để đảm bảo không mất dữ liệu: **STT | Tên sơ đồ | Loại | Mô tả**.
> 
> - **Nguồn dữ liệu:** Lấy chính xác 100% từ bảng "CÁC SƠ ĐỒ DATA FLOW DIAGRAM (STAGE 2)" trong file `STU_STAGE 2_HEMIS`.

> [!important] **Nguyên tắc ánh xạ:**
> >- Cột _Tên sơ đồ_: Lấy đúng nội dung từ cột "Tên Sơ đồ (Gợi ý đưa vào Docs)".
> >- Cột _Loại_: Lấy đúng nội dung từ cột "Loại Level DFD".
> >- Cột _Mô tả_: Lấy đúng nội dung từ cột "Mô tả chi tiết phạm vi cần vẽ".
> >- Tuyệt đối không chế biên, tóm tắt hay thay đổi câu chữ để tránh nhầm lẫn.

---
## 2.1 Bảng tổng hợp DFD

Nếu kế hoạch trên đã đúng ý bạn, dưới đây là bảng tôi đã trích xuất chính xác theo đúng yêu cầu:

|  STT   | Tên sơ đồ                                       | Loại                      | Mô tả                                                                                                                                                                      |
| :----: | :---------------------------------------------- | :------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1**  | 1 - LEVEL 0 - SƠ ĐỒ NGỮ CẢNH HỆ THỐNG HEMIS     | Level 0 (Context Diagram) | Vẽ HEMIS là 1 Process duy nhất. Xung quanh là các Tác nhân (Proposer, QC, DA, Admins, Viewer). Chỉ thể hiện luồng dữ liệu vào/ra tổng quát, tuyệt đối không vẽ Database.   |
| **2**  | 2 - LEVEL 1 - DFD QUẢN LÝ ĐỊNH DANH & BẢO MẬT   | Level 1                   | Quản lý tài khoản/Phân quyền của Admin Security và cấu hình hệ thống Admin System. Tương tác với DB: DB Identity và DB Audit Log.                                          |
| **3**  | 3 - LEVEL 1 - DFD ĐĂNG NHẬP & QUÊN MẬT KHẨU     | Level 1                   | Luồng Đăng nhập, Đổi/Quên mật khẩu. Tương tác với DB: DB Identity và DB Audit Log.                                                                                         |
| **4**  | 4 - LEVEL 1 - DFD LUÂN CHUYỂN DỮ LIỆU NGHIỆP VỤ | Level 1                   | Phân rã quá trình Nhập liệu -> Kiểm định -> Phê duyệt. Thể hiện dòng chảy dữ liệu đi qua các bước và lưu trữ từ DB Staging sang DB Core.                                   |
| **5**  | 5 - LEVEL 1 - DFD KHAI THÁC BÁO CÁO & KIỂM TOÁN | Level 1                   | Luồng trích xuất dữ liệu của Viewer và Audit Viewer. Tương tác với DB: DB Reporting và DB Audit Log.                                                                       |
| **6**  | 6 - LEVEL 1 - QUẢN TRỊ KHẨN CẤP                 | Level 1                   | Break-Glass khóa tài khoản hệ thống khi có sự cố.                                                                                                                          |
| **7**  | 7 - LEVEL 2 - CHI TIẾT LUỒNG ĐĂNG NHẬP & AUTH   | Level 2                   | Phân rã chi tiết từ nhập tài khoản, check DB, sinh mã OTP, đối soát mã, ghi nhận Audit Log và cấp quyền truy cập (Session).                                                |
| **8**  | 8 - LEVEL 2 - CHI TIẾT LUỒNG QUẢN LÝ TÀI KHOẢN  | Level 2                   | Đi sâu vào các Process của Admin Security: Thêm user (No Role), Phân quyền, Cấp pass và Khóa tài khoản khẩn cấp.                                                           |
| **9**  | 9 - LEVEL 2 - CHI TIẾT LUỒNG NHẬP LIỆU (CRUD)   | Level 2                   | Phân rã luồng nhập liệu thủ công / Import Excel. Đi qua các Process: Validate Schema, Lưu nháp, Cập nhật trạng thái 'Chờ duyệt' vào DB Staging.                            |
| **10** | 10 - LEVEL 2 - CHI TIẾT LUỒNG PHÊ DUYỆT         | Level 2                   | Chi tiết luồng dữ liệu khi QC Xác nhận và DA Phê duyệt. Vẽ rõ Process hệ thống tự động copy/đẩy dữ liệu từ DB Staging sang DB Core sau khi DA duyệt.                       |
| **11** | 11 - LEVEL 2 - CHI TIẾT LUỒNG TRẢ VỀ & THU HỒI  | Level 2                   | Phân rã luồng dữ liệu bị lỗi (Từ chối, Ghi chú sai sót, Thu hồi). Cho thấy cách trạng thái bản ghi được cập nhật lùi về 'Draft' trong DB Staging và thông báo được gửi đi. |
| **12** | 12 - LEVEL 2 - CHI TIẾT LUỒNG XUẤT BÁO CÁO      | Level 2                   | Tác nhân đưa bộ lọc vào hệ thống, Process truy vấn DB Reporting, định dạng biểu đồ/bảng biểu và xuất ra file Excel/PDF.                                                    |
| **13** | 13 - LEVEL 2 - CHI TIẾT LUỒNG GHI & TRA CỨU LOG | Level 2                   | Giải thích cơ chế "Append-only": Cách các Process nghiệp vụ tự động sinh log đẩy vào DB Audit, và cách Audit Viewer truy xuất dữ liệu từ DB này để hiển thị ra UI.         |

---
# ii. TIÊU CHÍ ĐÁNH GIÁ

> Để đánh giá sơ đồ DFD (Data Flow Diagram) một cách khách quan, bạn có thể áp dụng bộ tiêu chí dưới đây. Tôi chia làm 2 phần: Tiêu chí chung (đúng kỹ thuật) và Tiêu chí riêng cho từng Level.

## 2.1 Tiêu chí chung (Áp dụng cho mọi Level)

> - **Ký hiệu**: Sử dụng đúng 4 thành phần (Process, Data Store, External Entity, Data Flow).
> - **Luồng dữ liệu**: Không có luồng dữ liệu trực tiếp giữa hai thực thể ngoài, hoặc giữa thực thể ngoài với kho dữ liệu (phải qua Process).
> - **Tên gọi**: Tiến trình (Process) phải là động từ; Luồng dữ liệu, Kho và Thực thể phải là danh từ.
> - **Tính nhất quán**: Dữ liệu vào/ra ở Level cao phải xuất hiện đầy đủ ở Level thấp hơn (Balancing).

---

## 2.2 Tiêu chí chấm điểm chi tiết theo Level

### a) Level 0 (Sơ đồ ngữ cảnh - Context Diagram)

> - **Đúng trọng tâm:** Chỉ được phép có duy nhất 01 Process tổng quát.
> - **Phạm vi:** Xác định đầy đủ các thực thể ngoài (External Entities) tương tác với hệ thống.
> - **Sự đầy đủ:** Thể hiện được các luồng dữ liệu chính vào và ra khỏi hệ thống.
> - **_Lưu ý:_** Tuyệt đối không có kho dữ liệu (Data Store) ở level này.

### b) Level 1 (Sơ đồ phân rã mức 1)

> - **Phân rã chức năng:** Chia được hệ thống thành các phân hệ/chức năng chính (thường từ 3-7 tiến trình).
> - **Kho dữ liệu:** Bắt đầu xuất hiện các kho dữ liệu lớn để lưu trữ thông tin giữa các tiến trình.
> - **Luồng logic:** Các tiến trình phải kết nối với nhau hoặc với kho dữ liệu một cách hợp lý.

### c) Level 2 (Sơ đồ chi tiết)

> - **Độ chi tiết:** Phân rã một chức năng cụ thể từ Level 1 thành các bước nhỏ, logic.
> - **Tính logic:** Thể hiện rõ các kiểm tra điều kiện, xử lý dữ liệu chi tiết 
> - **Khớp nối:** Các luồng vào/ra của Level 2 phải khớp hoàn toàn với luồng của tiến trình cha ở Level 1.

---

## 2.3 Cách kiểm tra bài làm của các bạn

> [!NOTE] Để biết các bạn làm có ổn không, bạn hãy thử "soi" lỗi phổ biến này:
> 
> 1. Hố đen (Black Hole): Tiến trình chỉ có luồng vào mà không có luồng ra.
> 2. Phép màu (Miracle): Tiến trình tự nhiên có luồng dữ liệu ra mà không có đầu vào.
> 3. Mất cân bằng (Imbalance): Ở Level 0 bảo có 2 luồng vào, nhưng xuống Level 1 lại vẽ thành 3 luồng từ thực thể đó vào.

