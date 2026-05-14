# Technical Issue Report: Mismatch in `be/stu/dm_loai_quyet_dinh_ctdt`

## 1. Mô tả sự cố
Trong quá trình Audit Giai đoạn 1 cho module **Loại quyết định CTĐT**, chúng tôi phát hiện một sự sai lệch nghiêm trọng giữa tên nhánh feature và nội dung mã nguồn thực tế được triển khai.

- **Tên nhánh**: `be/stu/dm_loai_quyet_dinh_ctdt`
- **Nội dung dự kiến**: CRUD API cho bảng `dm_loai_quyet_dinh_ctdt`.
- **Nội dung thực tế**: CRUD API cho bảng `dm_loai_chuong_trinh_dao_tao`.

## 2. Bằng chứng kỹ thuật (Technical Evidence)

### 2.1. Lịch sử Commit
Commit cuối cùng trên nhánh này:
```bash
commit 3e47443ef239791ba5845bf8e062f47e7a283074
Author: NguyenQuangBinhSteve <binhsteve123@gmail.com>
Date:   ...
    feat: [dm_loai_chuong_trinh_dao_tao] expose CRUD API for training-program type catalog
```
*Ghi chú: Message commit đã chỉ định rõ là module `loai_chuong_trinh_dao_tao`.*

### 2.2. Kiểm tra mã nguồn (Source Code Audit)
Kết quả lệnh `grep` trên nhánh này:
- Tìm kiếm `DMLoaiQuyetDinhCTDT`: **0 kết quả**.
- Tìm kiếm `DMLoaiChuongTrinhDaoTao`: **Tìm thấy tại tất cả 5 tầng**.

Cụ thể tại `app/models/danh_muc.py`:
```python
# Loại chương trình đào tạo
class DMLoaiChuongTrinhDaoTao(Base):
    __tablename__ = "dm_loai_chuong_trinh_dao_tao"
    # ...
```

## 3. Phân tích nguyên nhân
Có hai khả năng xảy ra:
1. **Đặt sai tên nhánh**: Người phát triển định làm module `Loại chương trình` nhưng lại đặt tên nhánh là `Loại quyết định`.
2. **Push nhầm Code**: Người phát triển làm song song nhiều module và đã thực hiện `git push` nhầm nội dung của module này lên nhánh của module kia.

## 4. Hệ quả & Rủi ro
- **Hệ quả**: Module `dm_loai_quyet_dinh_ctdt` hiện vẫn đang ở trạng thái **TRỐNG** (Chưa thực hiện) trong toàn bộ dự án.
- **Rủi ro**: Nếu merge nhánh này vào `dev`, sẽ gây ra trùng lặp code với module `dm_loai_chuong_trinh_dao_tao` (vốn đã được merge từ `main`).

## 5. Khuyến nghị hành động
1. **Hủy bỏ (Discard)** các thay đổi trên nhánh `be/stu/dm_loai_quyet_dinh_ctdt`.
2. **Khởi tạo lại** nhánh mới hoặc dọn dẹp nhánh hiện tại để thực hiện đúng module `dm_loai_quyet_dinh_ctdt`.
3. **Cập nhật báo cáo Audit**: Đánh dấu module này là "FAIL - Sai nội dung" để tránh nhầm lẫn cho các bên liên quan.
