# Master Workflow Guide: Quy trình Đối soát & Kiểm toán Backend

Tài liệu này hướng dẫn cách sử dụng bộ công cụ tại thư mục `be_merge_checking_dm` để thực hiện kiểm tra chất lượng (Audit) các nhánh feature trước khi merge vào nhánh `dev`.

---

## 1. Bản đồ bộ công cụ (Toolkit Map)

Thư mục này bao gồm 3 tài liệu cốt lõi và 1 kho lưu trữ kết quả:

| Tên tài liệu | Loại | Mục đích sử dụng |
|---|---|---|
| [**Gold Standard**](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/dm_to_chuc_kiem_dinh_gold_standard.md) | **Tham chiếu** | Chứa "mẫu code chuẩn" của module mẫu. Dùng để đối chiếu xem nhánh feature đã viết đúng cấu trúc, logic và tiêu chuẩn chưa. |
| [**Git Workflow**](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/git_workflow_agent_audit.md) | **Giao thức** | Quy định các bước Git (Stash/Checkout/Pop) để Agent di chuyển giữa các nhánh an toàn, không làm mất code trên `dev`. |
| [**Audit Checklist**](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/audit_specification_checklist.md) | **Thước đo** | Danh sách các tiêu chí kiểm tra (5 tầng kiến trúc, CRUD) và mẫu báo cáo để Agent điền thông tin đánh giá. |
| [**danh_gia_branch/**](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/danh_gia_branch/) | **Kho lưu trữ** | Thư mục chứa các file báo cáo đánh giá (`.md`) riêng biệt cho từng nhánh feature sau khi Agent Audit xong. |

---

## 2. Quy trình vận hành (Operational Flow)

Quy trình được chia làm 3 giai đoạn phối hợp giữa **Người dùng (Thủ công)** và **Agent (Tự động)**:

### Giai đoạn 1: Khởi động (Người dùng - Thủ công)
1. **Đồng bộ**: Đảm bảo bạn đang ở nhánh `dev` và đã pull code mới nhất:
   `git checkout dev && git pull origin dev`
2. **Kiểm tra**: Chạy `git status` để đảm bảo không có xung đột nghiêm trọng.
3. **Ra lệnh**: Yêu cầu Agent thực hiện Audit một nhánh feature cụ thể (ví dụ: `be/stu/dm_hinh_thuc_dao_tao`).

### Giai đoạn 2: Thực thi Audit (Agent - Tự động)
Agent sẽ tự động thực hiện các bước sau:
1. **Bảo tồn**: Chạy `git stash` để cất code đang làm dở trên `dev`.
2. **Chuyển nhánh**: Chuyển sang nhánh feature mục tiêu.
3. **Kiểm tra lớp (Layer Audit)**: Mở code nhánh feature và đối chiếu với **Gold Standard** theo các tiêu chí trong **Audit Checklist**.
4. **Chạy Test**: Thực hiện lệnh `pytest` để xác nhận tính hoạt động của code.
5. **Lập báo cáo**: Tạo file báo cáo mới trong thư mục `danh_gia_branch/`.

### Giai đoạn 3: Xem kết quả & Khôi phục (Người dùng & Agent)
1. **Trả máy**: Agent tự động quay về nhánh `dev` và thực hiện `git stash pop`.
2. **Xem báo cáo**: Người dùng mở file báo cáo trong `danh_gia_branch/` để xem:
   - Bảng đối soát (Matrix) so với Gold Standard.
   - Danh sách các lỗi/thiếu sót (Discrepancies).
   - Code snippet đề xuất để sửa lỗi.
3. **Quyết định**: Người dùng quyết định merge nhánh đó hoặc yêu cầu sửa đổi thêm dựa trên báo cáo của Agent.

---

## 3. Các lưu ý quan trọng

- **Tuyệt đối không sửa code trực tiếp trên nhánh feature**: Agent chỉ có nhiệm vụ Audit và đề xuất snippet code trong báo cáo. Việc sửa đổi nên được thực hiện bởi developer của nhánh đó hoặc người quản lý.
- **Kiểm tra file Test**: Nếu một nhánh feature không có file test, Agent sẽ đánh dấu là **"Thiếu" (Missing)** trong báo cáo. Đây là một điểm trừ lớn về chất lượng.
- **Sử dụng Gold Standard**: Khi có tranh chấp về cách viết code (ví dụ: Soft Delete hay Hard Delete), tài liệu Gold Standard luôn là căn cứ cuối cùng để quyết định.

---

*Tài liệu này được soạn thảo để chuẩn hóa quy trình làm việc giữa Người dùng và AI Agent trong dự án HEMIS Backend.*
