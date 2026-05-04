Dựa vào tiêu chí từ tệp `DFD đánh giá chung.md` và tuân thủ đúng form mẫu ở tệp `2 - 1 - DFD QUẢN LÝ ĐỊNH DANH & BẢO MẬT - ĐÁNH GIÁ.md`, tiến hành quy trình kiểm định chi tiết cho sơ đồ `3 - LEVEL 1 - DFD ĐĂNG NHẬP & QUÊN MẬT KHẨU` như sau:

Dưới đây là kết quả kiểm định chi tiết:

##### 1. Đánh giá theo Tiêu chí dành riêng cho Level 1 (Sơ đồ phân rã mức 1)

- **Phân rã chức năng (3-7 tiến trình):** **ĐẠT TỐT.** Sơ đồ đã bóc tách hợp lý hệ thống thành 4 quy trình (Process) vừa vặn gồm: P1 (Đăng nhập), P2 (Quản lý thông tin cá nhân), P3 (Quên mật khẩu), và P4 (Khôi phục mật khẩu).
- **Sự xuất hiện của Kho dữ liệu (Data Stores):** **ĐẠT TỐT.** Sơ đồ đã sử dụng các kho dữ liệu cần thiết đúng tính chất của Level 1 bao gồm: Identity DB và Audit Log DB.
- **Luồng logic giữa các tiến trình và DB:** **ĐẠT.** Thể hiện được rõ ràng sự luân chuyển và xác thực tài khoản qua lại với Identity DB, đồng thời cơ chế ghi vết toàn bộ hoạt động vào Audit Log DB được đảm bảo xuyên suốt ở tất cả các tiến trình.

##### 2. Đánh giá theo Tiêu chí Chung (Kỹ thuật vẽ DFD)

- **Ký hiệu:** **ĐẠT.** Tách biệt rõ ràng 4 thành phần (Thực thể ngoài: Group Admin, Group User, Hệ thống Email, Audit Viewer; Quy trình: P1 đến P4; Kho dữ liệu: Identity DB, Audit Log DB; Luồng dữ liệu: các mũi tên 1-21).
- **Nguyên tắc đường luồng dữ liệu (Data Flows):** **ĐẠT.** Không có lỗi vẽ luồng dữ liệu giao tiếp trực tiếp giữa hai tác nhân hay giữa tác nhân với Kho dữ liệu. Mọi tương tác vào/ra đều được luân chuyển thông qua các Process.
- **Tên gọi (Quy tắc Danh từ / Động từ):** **CHƯA ĐẠT (Cần sửa tương tự như DFD số 2).**
    - _Tiêu chí quy định:_ Tiến trình (Process) phải là động từ. Luồng dữ liệu (Data Flow), Kho (Data Store) và Tác nhân (Entity) bắt buộc phải là **danh từ**.
    - _Đánh giá:_ Các Process đặt tên rất chuẩn (Đăng nhập, Quản lý, Quên mật khẩu, Khôi phục mật khẩu). Tuy nhiên, **nhiều Luồng dữ liệu (Flow lines) vẫn đang sử dụng Động từ/Hành động**. Ví dụ: "4. Lưu log", "6. Gửi yêu cầu", "7. Cập nhật thông tin", "11. Gửi yêu cầu", "18. Cập nhật mật khẩu", "20. Lưu log". Mũi tên trong DFD mang ý nghĩa "Gói dữ liệu nào đang được luân chuyển", việc dùng động từ là sai quy tắc.

##### 3. Kiểm tra các lỗi phổ biến (Hố đen / Phép màu / Mất cân bằng)

- **Hố đen (Black Hole):** **KHÔNG MẮC LỖI.** Tất cả các Process (P1, P2, P3, P4) sau khi nhận input từ tác nhân đều có luồng output trả về (thông báo kết quả) hoặc lưu thông tin xuống Identity DB/Audit Log DB.
- **Phép màu (Miracle):** **KHÔNG MẮC LỖI.** Không có Process nào tự sinh ra output mà không có dữ liệu đầu vào. (Ví dụ: P4 sinh mã OTP để gửi đi chỉ diễn ra sau khi P3 gửi lệnh qua hệ thống Email và nhận lại xác nhận).
- **Tính nhất quán (Balancing) so với Level 0:** **ĐẠT.** Nhóm tác nhân bao quát ở Level 0 được kế thừa đầy đủ. Sự xuất hiện thêm của "Hệ thống Email" ở Level 1 này là hoàn toàn hợp lệ, đóng vai trò một bên thứ ba (third-party) phụ trợ giải quyết luồng chi tiết của tính năng gửi mã.

---

##### KẾT LUẬN & ĐỀ XUẤT CHỈNH SỬA

**Đánh giá chung:** Về mặt kiến trúc và xử lý logic luồng công việc, sơ đồ thiết kế **rất chi tiết và logic**. Điểm sáng là đã bổ sung Hệ thống Email như một External Entity độc lập để minh họa luồng cấp phát mã OTP tự động. Dù vậy, biểu đồ vẫn vướng phải **lỗi kỹ thuật định danh Data Flow bằng hành động thay vì danh từ** tương tự như DFD Quản lý định danh trước đó.

**Đề xuất sửa lại nhãn của các đường luồng dữ liệu (Data Flows) thành DANH TỪ:**

- _Luồng 4, 9, 20:_ "Lưu log" -> **"Dữ liệu nhật ký hệ thống" (hoặc Payload Log)**
- _Luồng 6, 11:_ "Gửi yêu cầu" -> **"Yêu cầu quản lý thông tin / Yêu cầu cấp lại mật khẩu"**
- _Luồng 7:_ "Cập nhật thông tin" -> **"Dữ liệu hồ sơ cá nhân mới"**
- _Luồng 14:_ "Lệnh gửi mã xác thực" -> **"Yêu cầu phát hành mã OTP"**
- _Luồng 18:_ "Cập nhật mật khẩu" -> **"Dữ liệu mật khẩu mới (đã mã hóa)"**

