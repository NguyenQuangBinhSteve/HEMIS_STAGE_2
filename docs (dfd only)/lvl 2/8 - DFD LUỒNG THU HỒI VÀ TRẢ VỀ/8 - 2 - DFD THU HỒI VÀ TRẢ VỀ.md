Việc kiểm định rà soát tính logic dựa trên sự luân chuyển dữ liệu thực tế giữa **Proposer**, **Quality Controller** và hệ thống lưu trữ.

##### 1. Đánh giá theo Tiêu chí dành riêng cho Level 2 (Sơ đồ chi tiết)

- **Độ chi tiết:** **ĐẠT**. Sơ đồ bóc tách được các tiến trình xử lý từ đối soát ghi chú đến cập nhật trạng thái và xử lý ngoại lệ.
    
- **Phân rã chức năng:** **KHÔNG ĐẠT**.
    
    - **Thiếu tác nhân Proposer:** Trong luồng thu hồi dữ liệu, cả Proposer và QC đều có quyền thu hồi bản ghi khi ở trạng thái "Chờ kiểm". Sơ đồ hiện tại đang bỏ sót hoàn toàn luồng thu hồi từ phía Proposer (Lưu ý, hiện tại QC thu hồi rồi Proposer được thông báo là đúng, tuynhiên Proposer thu hồi thì QC không được thông báo).
        
    - **Thiếu tiến trình Đăng nhập/Xác thực:** Không có tiến trình kiểm tra danh tính và quyền hạn người dùng thông qua **Identity DB** trước khi thực hiện lệnh thu hồi nhạy cảm.
        
- **Sự xuất hiện của Kho dữ liệu (Data Stores):** **CHƯA ĐẠT**.
    
    - Thiếu kho dữ liệu **Identity DB** phục vụ cho việc đối soát quyền hạn của Proposer và QC.
        
- **Khớp nối & Thứ tự:** **ĐẠT MỘT PHẦN**. Các luồng đã bắt đầu có STT, nhưng tên gọi flow vẫn chưa chuẩn hóa hoàn toàn sang danh từ.
    

##### 2. Đánh giá theo Tiêu chí Chung (Kỹ thuật vẽ DFD)

- **Nguyên tắc đường luồng dữ liệu (Data Flows):** **KHÔNG ĐẠT**.
    
    - **Vi phạm quy tắc cân bằng DB (Flow ra = Flow vào):** Các kho dữ liệu **Staging DB** và **Audit Log DB** đang gặp lỗi mất cân bằng. Ví dụ: Luồng nạp log (1.3, 3.2) ghi vào DB nhưng không có luồng trả về xác nhận lưu thành công để tiến trình tiếp tục.
        
- **Tên gọi (Quy tắc Danh từ / Động từ):** **CHƯA ĐẠT**.
    
    - Nhiều nhãn luồng dữ liệu vẫn đang dùng cụm động từ chỉ hành động. Ví dụ: "1.2 Tải dữ liệu", "1.3 Ghi log truy cập", "2.1 Nhập ghi chú", "3.1 Cập nhật trạng thái".
        

##### 3. Kiểm tra các lỗi phổ biến (Hố đen / Phép màu / Mất cân bằng)

- **Hố đen (Black Hole):** **MẮC LỖI**.
    
    - **Audit Log DB** là một hố đen hoàn toàn vì dữ liệu chỉ có chiều nạp vào, không thấy chiều xuất ra để phục vụ kiểm toán.
        
- **Phép màu (Miracle):** **MẮC LỖI**.
    
    - Tiến trình **P1. Tải và Ghi log 2.1** tự nhiên lấy được dữ liệu bản ghi từ Staging DB mà không thấy luồng yêu cầu truy vấn (Query) nạp vào DB trước đó.
        

---

##### KẾT LUẬN & ĐỀ XUẤT CHỈNH SỬA

**Đánh giá chung:** Sơ đồ mô tả nghiệp vụ tốt nhưng sai lệch nghiêm trọng về logic luồng dữ liệu (đặc biệt là thiếu luồng từ Proposer) và vi phạm nguyên tắc cân bằng kho dữ liệu.

**Đề xuất chỉnh sửa khẩn cấp:**

1. **Về Tác nhân & Bảo mật:** Bổ sung thực thể **Proposer** và tiến trình **Xác thực quyền** kết nối với **Identity DB**.
    
2. **Về Logic Kho dữ liệu:**
    
    - Thiết lập quy tắc: Mọi hành động ghi vào DB (Staging/Audit) đều phải có luồng **"Xác nhận cập nhật thành công"** trả ngược về tiến trình để đồng bộ hóa (Atomic Transaction).
        
    - Xóa bỏ lỗi Hố đen của **Audit Log DB** bằng cách bổ sung luồng dữ liệu ra.
        
3. **Chuẩn hóa nhãn Flow sang DANH TỪ:**
    
    - (1.1) -> **"Yêu cầu thu hồi bản ghi"**
        
    - (2.1) -> **"Dữ liệu ghi chú thu hồi"**
        
    - (3.1) -> **"Thông tin trạng thái mới (Draft)"**
        
    - (1.3, 3.2) -> **"Dữ liệu nhật ký hệ thống"**