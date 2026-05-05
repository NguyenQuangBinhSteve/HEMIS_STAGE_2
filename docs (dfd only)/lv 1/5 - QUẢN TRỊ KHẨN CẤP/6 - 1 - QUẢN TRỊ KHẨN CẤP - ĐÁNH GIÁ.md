#### 1. Đánh giá theo Tiêu chí dành riêng cho Level 1 (Sơ đồ phân rã mức 1)

- **Phân rã chức năng (3-7 tiến trình):** **ĐẠT.** Dựa trên đặc tả, quy trình được bóc tách thành các bước rõ ràng (từ 1.1 đến 1.5) xử lý việc tạo token, xác thực, khóa/mở khóa và ghi log.
- **Sự xuất hiện của Kho dữ liệu (Data Stores):** **CHƯA ĐẠT (LỖI VỀ KHÁI NIỆM TRONG ĐẶC TẢ).** Sơ đồ có sử dụng _Identity DB_ và _Audit Log DB_. Tuy nhiên, trong văn bản đặc tả (Mục 2), hai thành phần này lại được liệt kê vào nhóm "Đặc tả các tác nhân ngoài (External Entities)". Đây là lỗi sai khái niệm cốt lõi. Kho dữ liệu (Data Store) không được phép phân loại là Tác nhân ngoài.
- **Luồng logic giữa các tiến trình và DB:** **ĐẠT.** Thể hiện mạch lạc cơ chế "kiểm soát kép": xác thực -> kiểm tra quyền -> cập nhật trạng thái -> bắt buộc ghi log.

#### 2. Đánh giá theo Tiêu chí Chung (Kỹ thuật vẽ DFD)

- **Ký hiệu (Shapes & Symbols):** **CHƯA ĐẠT (LỖI KÝ HIỆU GOM NHÓM & ĐÁNH SỐ).**
    - _Lỗi khung chữ nhật gom nhóm:_ Trên bản vẽ xuất hiện một khung chữ nhật ("kích hoạt và xác thực") bao bọc các tiến trình 1.1, 1.2, 1.3. Theo chuẩn Symbol Logic của DFD, **tuyệt đối không sử dụng khung để gom nhóm (grouping box)** các Process. Mỗi Process (hình tròn) phải đứng độc lập. Việc vẽ khung là đặc trưng lai tạp từ biểu đồ UML Activity/BPMN.
    - _Lỗi đánh số Process:_ Đây là sơ đồ DFD Level 1, do đó các tiến trình bắt buộc phải được đánh số là **1.0, 2.0, 3.0, 4.0...** Việc đánh số 1.1, 1.2, 1.3 là quy tắc phân rã chuyên biệt dành cho DFD Level 2.
- **Nguyên tắc đường luồng dữ liệu (Data Flows):** **ĐẠT.** Mọi tương tác của Admin đều đi qua các Process xử lý, không có kết nối trực tiếp sai quy tắc vào Database.
- **Tên gọi (Quy tắc Danh từ / Động từ):** **CHƯA ĐẠT (LỖI SỬ DỤNG ĐỘNG TỪ).**
    - _Quy định:_ Nhãn của luồng dữ liệu (Data Flow) bắt buộc phải là **Danh từ**.
    - _Thực tế:_ Sơ đồ đang sử dụng nhiều cụm động từ mang tính chất hành động (Ví dụ: "Truy vấn quyền", "Kiểm tra quyền", "Cập nhật Locked", "Ghi Log", "Cập nhật Active").

#### 3. Kiểm tra các lỗi phổ biến (Hố đen / Phép màu / Mất cân bằng)

- **Hố đen (Black Hole):** **KHÔNG MẮC LỖI.** Các tiến trình đều nhận yêu cầu vào và đẩy luồng xử lý ra.
- **Phép màu (Miracle):** **KHÔNG MẮC LỖI.** Việc mở/khóa tài khoản đều có đầu vào rõ ràng từ Admin và dữ liệu đối soát từ Identity DB.
- **Tính nhất quán (Balancing) so với Level 0:** **CHƯA ĐẠT (LỆCH TÊN TÁC NHÂN BÊN NGOÀI).**
    - Tại Sơ đồ Level 0, tác nhân này được định danh là **"Admin Break-glass"**.
    - Tại Sơ đồ Level 1, tác nhân bị đổi tên thành **"Break-Glass Admin"**. Theo nguyên tắc Balancing của DFD, tên External Entity khi phân rã từ Level 0 xuống Level 1 phải khớp nhau 100%.

---

#### KẾT LUẬN & ĐỀ XUẤT CHỈNH SỬA

**Đánh giá chung:** Luồng nghiệp vụ được thiết kế chặt chẽ, thể hiện tốt cơ chế bảo mật khẩn cấp (override & log 100%). Tuy nhiên, tài liệu và bản vẽ đang vi phạm nhiều nguyên tắc học thuật của DFD (Ký hiệu hình khối, Đánh số, Định nghĩa DB và Quy tắc đặt tên Data Flow).

**Đề xuất:**

1. **Về Bản vẽ (Diagram):**
    - **Xóa bỏ** hoàn toàn khung chữ nhật "kích hoạt và xác thực" đang bao bọc các tiến trình.
    - Cập nhật lại phương pháp đánh số Process: Chuyển 1.1, 1.2, 1.3... thành **1.0, 2.0, 3.0...**
2. **Về Đặc tả (Text Document):**
    - Tại Mục 2, loại bỏ _Identity DB_ và _Audit Log DB_ khỏi danh sách "Tác nhân ngoài (External Entities)". Tạo một mục riêng để định nghĩa chúng là **"Kho dữ liệu (Data Stores)"**.
3. **Về Đồng bộ Tác nhân (Balancing):**
    - Đổi tên tác nhân "Break-Glass Admin" thành **"Admin Break-glass"** để đồng bộ với Level 0.
4. **Về Nhãn Luồng dữ liệu (Đổi Động từ thành Danh từ):**
    - _Truy vấn quyền_ -> **"Yêu cầu tra cứu quyền hạn"**
    - _Kiểm tra quyền_ -> **"Dữ liệu phân quyền hiện tại"**
    - _Lệnh khóa / Cập nhật Locked_ -> **"Lệnh cập nhật trạng thái Locked"**
    - _Ghi Log / Ghi Log (Mở khóa)_ -> **"Dữ liệu nhật ký thao tác (Payload Log)"**
    - _Cập nhật Active_ -> **"Lệnh cập nhật trạng thái Active"**