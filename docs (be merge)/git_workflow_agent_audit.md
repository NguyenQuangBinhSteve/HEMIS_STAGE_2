# Git Workflow for Agent Audit

Tài liệu này hướng dẫn quy trình Git an toàn dành cho Agent khi thực hiện kiểm toán (audit) mã nguồn trên các nhánh feature. Quy trình này đảm bảo không làm gián đoạn công việc hiện tại và tuyệt đối không làm mất dữ liệu trên nhánh `dev`.

---

## 1. Đồng bộ dữ liệu (Global Fetch)
Trước khi bắt đầu, Agent cần cập nhật danh sách các nhánh mới nhất từ máy chủ (GitHub) mà không làm ảnh hưởng đến code hiện tại.

**Giải thích**: Lệnh `fetch` chỉ tải về các thông tin meta và đối tượng mới, không thực hiện merge hay thay đổi bất kỳ file nào trong thư mục làm việc.

```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh dev)──$ git fetch --all
```
**Output mong muốn**:
`Fetching origin`
`From https://github.com/NguyenQuangBinhSteve/HEMIS_BE_STU`
` * [new branch]      be/stu/dm_hinh_thuc_dao_tao -> origin/be/stu/dm_hinh_thuc_dao_tao`
` ...`

---

## 2. Bảo tồn dữ liệu nhánh `dev` (Git Stash)
Nếu Agent đang có các thay đổi chưa commit trên nhánh `dev`, bắt buộc phải cất chúng đi trước khi chuyển sang nhánh khác.

**Giải thích**: `git stash` giống như một chiếc ngăn kéo tạm thời, giúp dọn dẹp thư mục làm việc trở nên sạch sẽ (`working tree clean`).

```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh dev)──$ git stash save "Agent Audit: Temporary stash for dev"
```
**Output mong muốn**:
`Saved working directory and index state On dev: Agent Audit: Temporary stash for dev`

**Kiểm tra lại trạng thái**:
```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh dev)──$ git status
```
**Output mong muốn**: `nothing to commit, working tree clean`

---

## 3. Chuyển nhánh và Đối soát (Checkout & Audit)
Sau khi môi trường đã sạch, Agent thực hiện chuyển sang nhánh feature cần kiểm tra.

**Lệnh**: `git checkout [branch_name]`

```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh dev)──$ git checkout be/stu/dm_hinh_thuc_dao_tao
```
**Output mong muốn**:
`Switched to a new branch 'be/stu/dm_hinh_thuc_dao_tao' (hoặc Switched to branch...)`

### Quy trình Đối soát (Audit Process)
Tại thời điểm này, Agent sẽ mở tài liệu [Gold Standard: dm_to_chuc_kiem_dinh](file:///home/kali/Development/Projects/docs_hemis_stu_explain/be_merge_checking_dm/dm_to_chuc_kiem_dinh_gold_standard.md) và thực hiện so sánh với code trên nhánh feature hiện tại:
- Kiểm tra **Model** tại `app/models/danh_muc.py`.
- Kiểm tra **Schema** tại `app/schemas/danh_muc.py`.
- Kiểm tra **Service** tại `app/services/danh_muc_service.py`.
- Kiểm tra **Router** tại `app/api/v1/endpoints/danh_muc.py`.
- Kiểm tra **Test** tại `tests/integration/danh_muc/test_dm_*.py`.

---

## 4. Khôi phục trạng thái ban đầu (Recovery)
Sau khi kết thúc quá trình Audit, Agent phải quay lại nhánh `dev` và trả lại trạng thái code ban đầu cho người dùng.

### Bước 1: Quay lại nhánh dev
```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh be/stu/dm_hinh_thuc_dao_tao)──$ git checkout dev
```
**Output mong muốn**: `Switched to branch 'dev'`

### Bước 2: Lấy lại dữ liệu đã cất (Pop Stash)
```bash
┌──(kali㉿kali)-[~/Development/Projects/be_hemis_stu_new]
└─(Nhánh dev)──$ git stash pop
```
**Output mong muốn**:
`On branch dev`
`Changes not staged for commit:`
`  (use "git add <file>..." to update what will be committed)`
`  ... (danh sách các file bạn đang làm dở hiện ra lại)`
`Dropped refs/stash@{0} (7c6b...)`

---

## 5. Danh sách các nhánh mục tiêu (Target Branches)
Dưới đây là các nhánh feature cần được Audit theo quy trình trên:

1. `be/stu/dm_hinh_thuc_dao_tao`
2. `be/stu/dm_loai_chuong_trinh_dao_tao`
3. `be/stu/dm_trang_thai_chuong_trinh_dao_tao`
4. `be/stu/dm_don_vi_cap_bang`
5. `be/stu/dm_khung_nang_luc_ngoai_ngu`
6. `be/stu/dm_loai_quyet_dinh_ctdt`

---

## 6. Bảng tra cứu lệnh nhanh (Cheat Sheet)

| Lệnh | Ý nghĩa | Bối cảnh sử dụng |
|---|---|---|
| `git status` | Xem trạng thái file | Kiểm tra xem có code nào chưa lưu không. |
| `git fetch --all` | Tải dữ liệu remote | Cập nhật danh sách nhánh mới nhất từ GitHub. |
| `git stash save "..."` | Cất code vào kho | Làm sạch `dev` trước khi chuyển nhánh. |
| `git checkout [branch]` | Chuyển nhánh | Di chuyển giữa `dev` và các nhánh feature. |
| `git stash pop` | Lấy lại code | Khôi phục công việc đang làm dở trên `dev`. |

**Lưu ý quan trọng**: Nếu khi thực hiện `git stash pop` mà gặp thông báo `CONFLICT`, Agent phải báo cáo ngay lập tức cho người dùng để xử lý thủ công, tuyệt đối không tự ý xóa code xung đột.
