Việc kiểm định tập trung vào tính toàn vẹn của bảo mật hệ thống và sự cân bằng giữa các thành phần lưu trữ dữ liệu. Kết quả cụ thể như sau:

##### 1. Đánh giá theo Tiêu chí dành riêng cho Level 2 (Sơ đồ chi tiết)

- **Độ chi tiết**: **GẦN ĐẠT**. Sơ đồ bóc tách được các tiến trình nghiệp vụ cốt lõi từ nhập dữ liệu đến phê duyệt.
    
- **Phân rã chức năng**: **KHÔNG ĐẠT**.
    
    - **Thiếu tiến trình Đăng nhập/Xác thực**: Một lỗi hệ thống nghiêm trọng khi thiếu tiến trình kiểm tra quyền hạn của **Proposer, QC, DA** trước khi cho phép tương tác với dữ liệu nhạy cảm.
        
    - **Thiếu chức năng Thu hồi**: Sơ đồ hoàn toàn bỏ sót tiến trình **Thu hồi dữ liệu** (Recall) từ phía **Quality Controller (QC)** khi cần can thiệp lùi trạng thái hồ sơ (tuy bên phía Proposer đã có).
        
- **Sự xuất hiện của Kho dữ liệu (Data Stores)**: **CHƯA ĐẠT**.
    
    - Thiếu kho dữ liệu **Identity DB** – thành phần bắt buộc để tiến trình xác thực có thể đối soát quyền hạn người dùng.
        
- **Khớp nối & Thứ tự**: **CHƯA ĐẠT**. Các luồng dữ liệu đang bị rối và khó truy vết do **thiếu số thứ tự (STT)** trên từng Flow.
    
##### 2. Đánh giá theo Tiêu chí Chung (Kỹ thuật vẽ DFD)

- **Nguyên tắc đường luồng dữ liệu (Data Flows)**: **KHÔNG ĐẠT**.
    
    - **Lỗi mất cân bằng tại Data Store**: Vi phạm quy tắc vàng của DFD khi **số luồng đầu vào không khớp với số luồng đầu ra** tại các DB.
        
    - Các kho dữ liệu (Staging, Core, Audit Log) hầu hết chỉ nhận dữ liệu mà thiếu luồng phản hồi kết quả hoặc luồng đọc thông tin để đối soát.
        
- **Tên gọi (Quy tắc Danh từ / Động từ)**: **CHƯA ĐẠT**.
    
    - Nhiều nhãn luồng vẫn dùng động từ (ví dụ: "Lưu bản ghi...", "Đẩy dữ liệu...", "Ghi log") thay vì cụm danh từ đại diện cho gói tin.
        

##### 3. Kiểm tra các lỗi phổ biến (Hố đen / Phép màu / Mất cân bằng)

- **Hố đen (Black Hole)**: **MẮC LỖI**.
    
    - **Core DB** đang là hố đen vì dữ liệu chỉ có chiều nạp vào, không thấy chiều xuất ra để phục vụ khai thác thông tin. Tương tự với **Audit Log DB**, quá ít luồng đi ra.
        
- **Phép màu (Miracle)**: **MẮC LỖI**.
    
    - Các tiến trình như **2.6 (QC)** và **2.7 (DA)** đang tự sinh ra kết quả mà không có luồng dữ liệu thô hoặc hồ sơ chờ duyệt từ **Staging DB** nạp vào để xử lý. Tương tự với **Audit Log DB**, có luồng đi ra đến Audit Viewer nhưng không có luồng *dữ liệu nhật ký hệ thống* đi vào **Audit Log DB** 
        
---
##### KẾT LUẬN & ĐỀ XUẤT CHỈNH SỬA

**Đánh giá chung**: Sơ đồ thể hiện tốt quy trình nghiệp vụ thô nhưng làm chưa tốt trong việc đáp ứng các tiêu chuẩn học thuật về DFD và bảo mật hệ thống.

**Đề xuất chỉnh sửa khẩn cấp**:

1. **Về Bảo mật**: Bổ sung tiến trình **Đăng nhập** và kho dữ liệu **Identity DB** ngay tại điểm bắt đầu của luồng.
    
2. **Về Nghiệp vụ**: Thiết lập thêm tiến trình **Thu hồi dữ liệu** cho QC.
    
3. **Về Logic Kho**: Đảm bảo mọi Database đều có luồng đọc dữ liệu ra tương xứng với luồng ghi vào (Xác nhận trạng thái) để xóa bỏ lỗi hố đen.
    
4. **Về Hình thức**: Đánh số thứ tự (STT) cho từng luồng dữ liệu để làm rõ dòng chảy logic.
    
5. **Chuẩn hóa nhãn Flow**: Chuyển toàn bộ tên luồng sang **Danh từ** (Ví dụ: "Dữ liệu bản ghi mới", "Bản ghi nhật ký thao tác").