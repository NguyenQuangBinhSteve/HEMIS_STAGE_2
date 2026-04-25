### Phần 1: Sơ đồ Mức 0 (Level 0 - Context Diagram / Sơ đồ Ngữ cảnh)

**Giải thích logic Sơ đồ Mức 0:** Sơ đồ này nhìn hệ thống YouTube như một "hộp đen" duy nhất. Nó không quan tâm bên trong YouTube xử lý thuật toán ra sao hay dùng cơ sở dữ liệu nào, mà chỉ tập trung vào việc: Ai tương tác với YouTube? Họ đưa dữ liệu gì vào và nhận lại thông tin gì?

**Bảng mô tả các thành phần Sơ đồ Mức 0:**
![[Context Diagram Youtube.png]]

|  STT  | Loại thành phần                | Tên thành phần                         | Mô tả chức năng và Luồng dữ liệu (Flow Lines)                                                                                                                                                                                                                                                                                                                                     |
| :---: | :----------------------------- | :------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Quy trình (Process)**        | **YouTube Information System**         | Một vòng tròn duy nhất đại diện cho toàn bộ nền tảng YouTube.                                                                                                                                                                                                                                                                                                                     |
| **2** | **Tác nhân (External Entity)** | **Người xem YouTube (YouTube Viewer)** | Là người dùng thông thường (tài khoản không kiếm tiền).<br><br>**- Dữ liệu đưa vào:** Chi tiết tài khoản, tải lên video, các tương tác mạng xã hội (Like, Comment, Share, Subscribe). **(Account Details Social media Upload Videos)**<br><br>**- Thông tin nhận về:** Xem các video trực tuyến, nhận các phản hồi/bình luận từ mạng xã hội. **(View Stream Video Social media)** |
| **3** | **Tác nhân (External Entity)** | **Đối tác YouTube (YouTube Partner)**  | Là người dùng có tài khoản đã bật kiếm tiền.<br><br>**- Dữ liệu đưa vào & Nhận về:** Tương tự như Người xem (Viewer). **(Account Details Social media Upload Videos)**<br><br>**- Thông tin nhận về ĐẶC BIỆT:** Nhận thêm các **Biên lai thanh toán (Payment receipts)** từ hệ thống cho các video đã được gắn quảng cáo. **(View Stream Video Social media Payment receipts)**   |
| **4** | **Tác nhân (External Entity)** | **Nhà quảng cáo (Advertiser)**         | Các cá nhân/tổ chức trả tiền để hiển thị quảng cáo.<br><br>**- Dữ liệu đưa vào:** Thanh toán tiền quảng cáo, cung cấp phương tiện quảng cáo (video ad, pop-up). **(Payment for Advertisements Ad Media)**                                                                                                                                                                         |
| **5** | **Tác nhân (External Entity)** | **Ngân hàng (Bank)**                   | Hệ thống xử lý dòng tiền bên ngoài YouTube.<br><br>**- Dữ liệu nhận được:** Nhận tiền từ nhà quảng cáo, thanh toán tiền cho Đối tác YouTube (Partner). **(Payments to Partner, Payments from Advertiser)**<br><br>**- Thông tin trả về:** gửi các biên lai thanh toán quay ngược lại hệ thống YouTube để xác nhận. (View). **(Payment receipts)**                                 |

---

### Phần 2: Sơ đồ Mức 1 (Level 1 - Data Flow Diagram / Sơ đồ Luồng Dữ liệu)

**Giải thích logic Sơ đồ Mức 1:** Đây là bước "mở hộp đen" của YouTube ra. Lúc này, hệ thống duy nhất ở Mức 0 được bóc tách thành nhiều quy trình (Processes) nhỏ hơn. Đồng thời, Sơ đồ Mức 1 bắt buộc phải có sự xuất hiện của Kho lưu trữ dữ liệu (Data Store) để phân loại người dùng và quản lý video.

**Bảng mô tả các thành phần Sơ đồ Mức 1:**
![[Data Flow Diagram Youtube.png]]

|STT|Loại thành phần|Tên thành phần|Mô tả chức năng và Luồng dữ liệu luân chuyển (Flow Lines)|
|:-:|:--|:--|:--|
|**1**|**Tác nhân (External Entity)**|**Người xem / Đối tác / Nhà quảng cáo / Ngân hàng**|Giữ nguyên 4 tác nhân từ Sơ đồ Mức 0 làm điểm bắt đầu và kết thúc của các luồng dữ liệu.|
|**2**|**Kho lưu trữ (Data Store)**|**Cơ sở dữ liệu Tài khoản (Accounts Database)**|Nơi lưu trữ thông tin đăng nhập. Dữ liệu từ đây được trích xuất để hệ thống xác định xem người dùng này là Đối tác (Partner - được kiếm tiền) hay chỉ là Người xem (Viewer).|
|**3**|**Quy trình (Process)**|**Đăng nhập & Xác thực**|**- Luồng vào:** Người dùng nhập chi tiết đăng nhập.**- Xử lý:** Đối chiếu dữ liệu với Cơ sở dữ liệu Tài khoản.|
|**4**|**Quy trình (Process)**|**Tìm kiếm & Xem Video**|**- Luồng vào:** Thông qua URL hoặc công cụ tìm kiếm.**- Xử lý:** Trích xuất video từ hệ thống.**- Luồng ra:** Trả video hiển thị về cho người dùng xem.|
|**5**|**Quy trình (Process)**|**Tải lên & Chỉnh sửa**|**- Luồng vào:** Người dùng kéo thả video vào trình duyệt, nhập thêm mô tả, cắt ghép, thêm thẻ meta (meta tags).**- Luồng ra:** Video hoàn chỉnh được xuất bản lên hệ thống.|
|**6**|**Quy trình (Process)**|**Tương tác MXH (Social Media)**|**- Luồng vào:** Hành động Like, Bình luận, Đăng ký, Chia sẻ.**- Luồng ra:** Trả phản hồi (Feedback) về cho chủ video và người dùng.|
|**7**|**Quy trình (Process)**|**Quản lý Quảng cáo & Thanh toán**|**- Luồng vào:** Nhà quảng cáo nộp tiền và gửi file quảng cáo.**- Xử lý:** Gắn quảng cáo vào video của Đối tác (Partner). Tính toán hiệu suất (số lượt click, lượt xem).**- Luồng ra:** Lệnh chuyển tiền gửi đến Ngân hàng, Ngân hàng trả tiền cho Partner và trả Biên lai thanh toán về hệ thống.|

---

**Tóm tắt sự liên kết giữa hai cấp độ:** Sơ đồ Mức 0 (Context Diagram) đã đóng vai trò là công cụ lập kế hoạch hoàn hảo. Bằng cách xác định được rõ ràng rằng hệ thống YouTube phải xử lý 2 nhóm người dùng có tính chất hơi khác nhau (có kiếm tiền và không kiếm tiền) cộng với sự tham gia của Nhà quảng cáo, Sơ đồ Mức 1 (DFD) đã vẽ ra được chính xác quy trình **Đăng nhập** cần một **Cơ sở dữ liệu tài khoản** để phân loại, cũng như quy trình **Quản lý quảng cáo** kết nối trực tiếp đến **Ngân hàng** như thế nào.