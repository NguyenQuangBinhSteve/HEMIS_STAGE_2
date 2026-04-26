**Symbol Logic Diagrams** (Sơ đồ Logic Ký hiệu) thực chất là nền tảng quy định ý nghĩa và cách sử dụng các ký hiệu đồ họa (symbols) để mô tả luồng dữ liệu và các thành phần bên trong một hệ thống thông tin, cụ thể là trong **Sơ đồ bối cảnh (Context Diagrams)** và **Sơ đồ luồng dữ liệu (Data Flow Diagrams - DFD)**.

### Lý do cần tìm hiểu Symbol Logic trước khi làm Data Flow Diagrams (DFD)

- **Đóng vai trò là công cụ lập kế hoạch:** Sơ đồ bối cảnh (sử dụng các ký hiệu cơ bản nhất) được xem là công cụ lập kế hoạch giúp bạn xây dựng một Sơ đồ luồng dữ liệu (DFD) phức tạp hơn sau này.
- **Hiểu rõ ranh giới và logic của hệ thống:** Việc nắm vững logic ký hiệu giúp bạn phân định rõ ràng đâu là các yếu tố nằm ngoài hệ thống (External Entity) và đâu là các bước xử lý (Process) bên trong hệ thống. Nếu không hiểu logic này, bạn sẽ dễ bị nhầm lẫn khi vẽ luồng di chuyển và biến đổi của dữ liệu giữa nhiều quy trình phức tạp trong DFD.
- **Đảm bảo tính nhất quán:** Mỗi ký hiệu mang một quy tắc riêng (ví dụ: Context Diagram chỉ có duy nhất 1 hình tròn, DFD thì có nhiều hình tròn và thêm kho dữ liệu). Nắm vững nguyên tắc này giúp sơ đồ thiết kế ra đạt chuẩn và dễ dàng truyền đạt cho các nhóm phát triển (Dev) hoặc phân tích hệ thống (BA).
  
<img src="FE DOCS/PIC DFD/Symbol Logic Diagram.png" alt="Symbol Logic Diagram" width="600">
### Sơ đồ này dùng để làm gì?

Sơ đồ này được sử dụng để minh họa trực quan cách một hệ thống thông tin hoạt động một cách tổng thể. Nó cho thấy luồng dữ liệu bắt đầu từ đâu (ai/cái gì gửi dữ liệu vào hệ thống), dữ liệu được hệ thống biến đổi qua các quy trình như thế nào, dữ liệu được lưu trữ ở đâu và cuối cùng kết quả (thông tin) sẽ được trả về cho ai.

### Tại sao dự án HEMIS V2 hiện tại lại rất cần có DFD? 

Với bối cảnh dự án HEMIS Datawarehouse V2 mà bạn đang thiết kế, DFD là công cụ không thể thiếu vì những lý do sau:

- **Hệ thống có nhiều kho dữ liệu (Data Stores) độc lập:** Kiến trúc của bạn chia DB thành 4 tầng rạch ròi: _Staging, Core, Reporting, và Audit Log_. DFD sẽ giúp mô tả chính xác dữ liệu từ Proposer đi vào kho Staging như thế nào, sau khi Data Approver (DA) duyệt thì dữ liệu đó được chuyển tiếp (luân chuyển) sang kho Core ra sao, và luồng Audit Log ghi nhận dữ liệu song song như thế nào.

- **Luồng quy trình (Processes) phức tạp:** Dữ liệu trong HEMIS phải trải qua quy trình biến đổi trạng thái gắt gao (Draft -> Chờ duyệt QC -> Đã duyệt DA -> Core). DFD sẽ bóc tách hệ thống HEMIS thành các "Process" nhỏ này để Backend và Frontend nhìn thấy rõ đầu vào (Input) và đầu ra (Output) của từng API.

- **Kiểm soát chặt chẽ Tác nhân bên ngoài (External Entities):** Hệ thống có rất nhiều Role (Proposer, QC, DA, Admin System, Admin Security, Viewer). DFD sẽ giúp team kỹ thuật trực quan hóa được nguyên tắc "Phân tách trách nhiệm" (SoD) và "Zero Trust", đảm bảo tác nhân nào chỉ được gửi/nhận đúng loại dữ liệu của tác nhân đó thông qua các Flow Lines.

### Bảng chi tiết các thành phần (Symbols) trong Sơ đồ

Dưới đây là 4 thành phần ký hiệu cốt lõi được sử dụng trong Context Diagram và DFD:

| STT | Tên thành phần (Symbol)              | Đặc điểm & Mô tả chi tiết                                                                                                                                                                                                                                                                                                                                                                    | Áp dụng trong Sơ đồ                                        |
| :-- | :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------- |
| 1   | **Thực thể ngoài (External Entity)** | Là các yếu tố tồn tại bên ngoài hệ thống thông tin (có thể là một người dùng, một sản phẩm, hoặc một hệ thống thông tin khác). Thực thể này có vai trò nhập dữ liệu (input) vào hệ thống hoặc nhận thông tin/dữ liệu (output) trả về từ hệ thống.                                                                                                                                            | Cả Context Diagram và DFD.                                 |
| 2   | **Quy trình / Xử lý (Process)**      | Đại diện cho một bước hoặc một hệ thống thực hiện nhiệm vụ biến đổi dữ liệu.<br><br>_Lưu ý quan trọng:_ Trong **Sơ đồ bối cảnh (Context Diagram)**, chỉ có **duy nhất 1 vòng tròn** đại diện cho toàn bộ hệ thống thông tin đó. Nhưng trong **DFD**, vòng tròn này sẽ được chia nhỏ thành **nhiều vòng tròn (nhiều Process)** đại diện cho các bước xử lý dữ liệu ở các giai đoạn khác nhau. | Cả Context Diagram và DFD.                                 |
| 3   | **Đường luồng dữ liệu (Flow Line)**  | Là các đường mũi tên minh họa sự di chuyển của dữ liệu giữa các thực thể ngoài và hệ thống, hoặc giữa các quy trình với nhau. Các đường mũi tên này luôn phải đi kèm với nhãn (text) ghi rõ loại dữ liệu nào đang được truyền đi hoặc biến đổi.                                                                                                                                              | Cả Context Diagram và DFD.                                 |
| 4   | **Kho lưu trữ dữ liệu (Data Store)** | Là nơi lưu trữ dữ liệu của hệ thống (thường đại diện cho Database/Cơ sở dữ liệu). Đây là nơi dữ liệu được gửi đến để lưu trữ hoặc được truy xuất ra để phục vụ cho một Quy trình (Process) nào đó.                                                                                                                                                                                           | **Chỉ dùng trong DFD** (Không dùng trong Context Diagram). |

Bạn có muốn tôi thử áp dụng các ký hiệu logic này để phân tích luồng dữ liệu cho một chức năng cụ thể thuộc phân hệ **Chương trình đào tạo** mà chúng ta vừa làm Database Mapping ở trên không?