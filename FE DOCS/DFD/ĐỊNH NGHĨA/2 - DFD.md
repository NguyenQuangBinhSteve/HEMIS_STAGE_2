# I. Các Quy Luật Thành Phần Trong Data Flow Diagram

Sơ đồ Luồng Dữ liệu (Data Flow Diagram - DFD) là bước đi sâu vào chi tiết của hệ thống, giúp phân rã chiếc "hộp đen" của Sơ đồ Ngữ cảnh thành nhiều phần nhỏ để minh họa chính xác cách dữ liệu được xử lý, biến đổi và lưu trữ.

Dưới đây là phân tích chi tiết về các quy luật logic cốt lõi nằm sau DFD:

### **1. Quy luật về Tác nhân bên ngoài (External Entity)**

- **Có thể có NHIỀU Tác nhân cùng tương tác với một hệ thống hoặc một quy trình:** Đúng như bạn dự đoán, DFD không giới hạn số lượng tác nhân. Một quy trình có thể tiếp nhận dữ liệu từ nhiều nguồn. Ví dụ: Trong sơ đồ DFD của hệ thống Uber, cả **"Khách hàng Uber" (Uber Customer)** và **"Tài xế Uber" (Uber Driver)** đều là các tác nhân độc lập nhưng cùng tương tác với quy trình lấy vị trí GPS, đăng nhập và xử lý thanh toán. Hoặc trong DFD của YouTube, cả **"Đối tác YouTube" (YouTube Partner)** và **"Người xem" (YouTube Viewer)** đều gửi dữ liệu vào cùng một quy trình Đăng nhập và Tìm kiếm video.

- **Điểm đầu và điểm cuối:** Tác nhân bên ngoài luôn là điểm khởi nguồn đưa dữ liệu (Data) vào các quy trình của hệ thống, hoặc là điểm cuối cùng nhận thông tin (Information) trả về sau khi đã xử lý xong.

### **2. Quy luật về Quy trình biến đổi (Process)**

- **Phân rã thành nhiều bước:** Nếu Sơ đồ Ngữ cảnh chỉ dùng 1 vòng tròn duy nhất, thì DFD sử dụng nhiều vòng tròn đại diện cho các bước xử lý dữ liệu khác nhau ở các giai đoạn khác nhau của hệ thống.

- **Quy luật biến đổi (Transformation):** Bất kỳ dữ liệu nào đi qua một quy trình đều bắt buộc phải được biến đổi (transform). Dữ liệu đi vào Process 1 sẽ được xử lý, tính toán hoặc định dạng lại, sau đó được đẩy tiếp sang Process 2 hoặc Process 3 dưới dạng một luồng dữ liệu mang giá trị mới.

### **3. Quy luật về Kho lưu trữ dữ liệu (Data Store)**

- **Sự xuất hiện bắt buộc:** Kho lưu trữ (đại diện cho Database) là ký hiệu đặc trưng chỉ có trong DFD, không được phép dùng trong Sơ đồ Ngữ cảnh.

- **Luồng tương tác hai chiều:** Các quy trình (Process) sẽ thực hiện đẩy dữ liệu vào Kho lưu trữ để ghi lại (Lưu/Thêm/Sửa), hoặc trích xuất dữ liệu từ Kho lưu trữ ra để đối chiếu. Ví dụ: Trong hệ thống Amazon, quy trình tính toán giỏ hàng (Process) sẽ cần truy xuất dữ liệu từ "Kho dữ liệu Khách hàng" (Customer Database) để xử lý trước khi gửi yêu cầu thanh toán đến Ngân hàng.

### **4. Quy luật về Đường luồng dữ liệu (Flow Line)**

- **Thể hiện sự luân chuyển chằng chịt:** Flow Line trong DFD không chỉ nối Tác nhân với Hệ thống, mà nó thể hiện sự di chuyển của dữ liệu giữa Tác nhân với Quy trình, từ Quy trình này sang Quy trình khác, và từ Quy trình kết nối với Kho lưu trữ.
- **Chuỗi logic liên tục:** Luồng dữ liệu cho thấy bức tranh liền mạch: Tác nhân nhập liệu -> Quy trình 1 tiếp nhận -> Quy trình 2 biến đổi -> Quy trình 3 lưu vào/trích xuất từ Database -> Trả kết quả cuối cùng về cho Tác nhân.

---
# II. Giải thích các luồn Data Flow Diagram

Dựa trên logic của hệ thống ký hiệu (Symbol Logic), Sơ đồ Luồng dữ liệu (Data Flow Diagram - DFD) là bước "phá vỡ" chiếc hộp đen của Sơ đồ Ngữ cảnh ra thành nhiều phần nhỏ để xem xét chi tiết bên trong.

Khác với Sơ đồ ngữ cảnh (chỉ có 1 quy trình duy nhất), DFD sử dụng đến 4 loại ký hiệu: **Tác nhân bên ngoài (External Entity)**, **Quy trình (Process)**, **Đường luồng dữ liệu (Flow Line)**, và đặc biệt là sự xuất hiện của **Kho lưu trữ (Data Store)**.
<img src="FE DOCS/PIC DFD/DFD Diagram.png" alt="DFD Diagram" width="600">
### 1. Khởi nguồn từ Tác nhân bên ngoài (External Entity Input):  

Luồng dữ liệu luôn bắt đầu khi một tác nhân bên ngoài (có thể là một người dùng, một sản phẩm hoặc một hệ thống khác) nhập hoặc đưa dữ liệu (data) vào hệ thống thông tin thông qua một đường luồng dữ liệu (Flow Line).

### 2. Đi qua Chuỗi Quy trình biến đổi (Multiple Processes):  

Thay vì chỉ đi vào 1 vòng tròn duy nhất, dữ liệu trong DFD sẽ đi qua một chuỗi các quy trình xử lý.

- Dữ liệu đầu vào sẽ đi đến **Process 1** (Quy trình 1). Tại đây, dữ liệu được biến đổi (transform) và được mũi tên đẩy tiếp sang **Process 2**.
- **Process 2** tiếp tục thực hiện chức năng xử lý của nó, biến đổi dữ liệu một lần nữa và truyền tiếp sang **Process 3**.
- Sự luân chuyển chéo giữa các quy trình này cho thấy rõ "đường đi nước bước" của dữ liệu bên trong hệ thống.

### 3. Tương tác với Kho lưu trữ (Data Store):  

Trong quá trình xử lý (ví dụ tại Process 3), hệ thống sẽ cần nơi để cất giữ thông tin hoặc lấy thông tin cũ ra đối chiếu. Quy trình này sẽ đẩy dữ liệu vào một **Kho lưu trữ (Data Store - thường là Cơ sở dữ liệu)** để lưu lại, hoặc nó sẽ vẽ mũi tên trích xuất dữ liệu từ Kho lưu trữ này ra để phục vụ việc xử lý. Đây là điểm khác biệt cốt lõi của DFD so với Context Diagram.

### 4. Trả kết quả về cho Tác nhân (Information Output):  

Sau khi dữ liệu đã đi qua các bước xử lý và được lưu trữ thành công, quy trình cuối cùng (Process 3) sẽ xuất một đường luồng dữ liệu đẩy ngược ra ngoài hệ thống, trả về cho Tác nhân bên ngoài (External Entity). Lúc này, dữ liệu thô ban đầu đã được hệ thống biến đổi thành **Thông tin (Information)** có giá trị mà tác nhân đó mong muốn nhận được.

#### Tóm tắt lại luồng logic:  
Tác nhân đưa dữ liệu vào -> Quy trình 1 xử lý -> Quy trình 2 biến đổi -> Quy trình 3 lưu vào/lấy ra từ Cơ sở dữ liệu -> Trả kết quả (Thông tin) về lại cho Tác nhân.