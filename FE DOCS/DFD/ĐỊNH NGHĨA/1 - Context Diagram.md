# I. Các Quy Luật Thành Phần Context Diagram

**Sơ đồ Ngữ cảnh (Context Diagram)** là bước đầu tiên và tổng quát nhất trong việc mô hình hóa hệ thống. Nó đóng vai trò như một bản đồ toàn cảnh, giúp bạn nhìn thấy ranh giới của hệ thống và cách nó tương tác với thế giới bên ngoài trước khi đi sâu vào chi tiết.
### 1. Quy luật về Quy trình (Process)

- **Chỉ có DUY NHẤT một quy trình:** Trong Context Diagram, toàn bộ hệ thống thông tin (dù lớn hay phức tạp đến đâu) đều được gộp lại và biểu diễn bằng **một vòng tròn (Process) duy nhất** nằm ở trung tâm sơ đồ.

- **Cách đặt tên:** Vòng tròn này thường được đặt tên theo chính hệ thống đó, kèm theo chữ "Information System" (Ví dụ: _YouTube Information System_, _Uber Information System_, _Netflix Information System_). Sơ đồ ngữ cảnh không chia nhỏ hệ thống thành các quy trình con (như Đăng nhập, Thêm dữ liệu...) – việc chia nhỏ là nhiệm vụ của Data Flow Diagram (DFD).

### 2. Quy luật về Tác nhân bên ngoài (External Entity)

- **Có thể có NHIỀU tác nhân (Đúng như bạn đã ví dụ):** Sơ đồ không giới hạn số lượng tác nhân bên ngoài. Bạn có thể có 1, 2, hoặc rất nhiều tác nhân cùng lúc tương tác với hệ thống trung tâm.

- **Bản chất của tác nhân:** Tác nhân bên ngoài không chỉ là con người (người dùng). Nó có thể là:
    - **Con người:** Ví dụ như _Người chơi (Player)_ trong trò Rắn săn mồi, _Khách hàng (Customer)_ trong Amazon, _Hành khách / Tài xế_ trong Uber.
    - **Một hệ thống thông tin khác:** Ví dụ như _Ngân hàng (Bank)_ là một hệ thống bên ngoài giúp xử lý thanh toán cho YouTube, Netflix, eBay.
    - **Một sản phẩm/thiết bị:** Như _Xe ô tô (Car)_ bị chụp lại biển số trong hệ thống Camera vượt đèn đỏ.

### 3. Quy luật về Luồng dữ liệu (Flow Line)

- **Luôn là quan hệ 2 chiều (Vào - Ra):** Các mũi tên luồng dữ liệu minh họa mối quan hệ giữa Tác nhân và Hệ thống. Nó thể hiện dữ liệu tác nhân **đưa vào (Input)** hệ thống là gì, và thông tin hệ thống **trả về (Output)** cho tác nhân là gì.

- **Bắt buộc phải có nhãn dán (Labeling):** Bạn không được phép vẽ một mũi tên trống. Mỗi mũi tên luồng dữ liệu bắt buộc phải có chữ viết chú thích chính xác loại dữ liệu đang được luân chuyển.
    - _Ví dụ ở Amazon:_ Khách hàng nhập "Tiêu chí tìm kiếm" (Search criteria) vào hệ thống, hệ thống trả ra "Chi tiết sản phẩm" (Product details) cho khách hàng.

### 4. Quy luật về Kho lưu trữ (Data Store)

- **TUYỆT ĐỐI KHÔNG SỬ DỤNG Kho lưu trữ (Database):** Trong Sơ đồ Ngữ cảnh, bạn không được phép vẽ các kho lưu trữ dữ liệu (như bảng Users, bảng Modules, hay Tầng Core/Staging). Việc lưu trữ dữ liệu được coi là hoạt động "bên trong" (internal) của hệ thống. Kho lưu trữ chỉ bắt đầu xuất hiện khi bạn phân rã hệ thống ra thành các cấp độ chi tiết hơn trong Data Flow Diagram (DFD).

---
# II. Mô tả luồn qua lại ở các thành phần 

Trong Sơ đồ ngữ cảnh (Context Diagram), logic hoạt động được xây dựng dựa trên ba ký hiệu chính: Thực thể bên ngoài (External Entity), Quy trình (Process - là một vòng tròn duy nhất đại diện cho toàn bộ hệ thống thông tin) và Đường luồng dữ liệu (Flow Lines).

Sự tương tác qua lại giữa **Thực thể bên ngoài** và **Quy trình (Hệ thống thông tin)** được thể hiện cốt lõi thông qua 2 luồng dữ liệu (vào và ra) như sau:
![[Context Diagram Prototype.png]]
### **1. Luồng dữ liệu đi vào hệ thống (Data entered into the information system by External Entity):**

- Đây là đường mũi tên hướng từ Thực thể bên ngoài (có thể là người dùng, sản phẩm hoặc hệ thống khác) chỉ vào vòng tròn Hệ thống thông tin.
- Nó minh họa **những dữ liệu (data) mà thực thể bên ngoài cung cấp hoặc đưa vào hệ thống**.
- Nguyên tắc bắt buộc là trên đường mũi tên này **phải có chú thích bằng chữ** để xác định chính xác loại dữ liệu nào đang đi vào hệ thống.

### **2. Luồng thông tin trả về (Information returned to External Entity after processing by Information System):**

- Sau khi hệ thống tiếp nhận dữ liệu đầu vào và xử lý, đường mũi tên thứ hai sẽ hướng từ vòng tròn Hệ thống thông tin quay ngược trở lại Thực thể bên ngoài.
- Nó thể hiện **đầu ra (output) của hệ thống, tức là những thông tin (information) được luân chuyển trả lại** cho thực thể đó.
- Tương tự như luồng vào, mũi tên luồng ra này cũng **bắt buộc phải được viết rõ ràng** để thể hiện thông tin gì đang thoát ra khỏi hệ thống.

Sự tương tác qua lại của hai luồng Flow Lines này (cái gì đi vào và cái gì đi ra) đóng vai trò then chốt giúp chúng ta **hiểu được toàn bộ logic hoạt động tổng thể của hệ thống thông tin** một cách trực quan nhất.

