### Đề xuất cấu trúc dự án chuẩn hóa theo Physical Database Separation

---

### I. VẤN ĐỀ CỦA CẤU TRÚC CŨ

#### Bối cảnh kiến trúc hệ thống

Theo tài liệu kiến trúc HEMIS, hệ thống được thiết kế với **5 vùng dữ liệu vật lý độc lập**:

| Tầng                   | Loại                        | Quy tắc truy cập                                        |
| ---------------------- | --------------------------- | ------------------------------------------------------- |
| **Tier 2**             | Identity & Security Gateway | Độc lập hoàn toàn — chỉ Admin Security hoặc User tự gọi |
| **Tier 4 — Staging**   | Vùng đệm nghiệp vụ          | CRUD đầy đủ, Xóa mềm                                    |
| **Tier 4 — Core**      | Single Source of Truth      | Read, Hạn chế Write                                     |
| **Tier 4 — Reporting** | Vùng báo cáo                | Read-only                                               |
| **Tier 4 — Audit Log** | Vùng nhật ký                | Append-only                                             |

Đây là **5 database vật lý riêng biệt**, với các ràng buộc truy cập khác nhau, được thiết kế có chủ đích để đảm bảo toàn vẹn dữ liệu và phân quyền rõ ràng.

---

#### Lỗ hổng của cấu trúc cũ

Cấu trúc cũ tổ chức theo **Package by Layer truyền thống (Monolith)**:

```
app/
├── models/
│   ├── identity.py          ← Thuộc Tier 2 (Identity DB)
│   ├── danh_muc.py          ← Thuộc Tier 4 (Staging/Core DB)
│   └── chuong_trinh_dao_tao.py
├── db/
│   └── session.py           ← MỘT session duy nhất cho tất cả DB
```

> **Lỗ hổng 1 — Vi phạm phân tách vật lý:** `models/identity.py` nằm ngang hàng `models/danh_muc.py` ngụ ý cả hai cùng tầng dữ liệu. Trên thực tế, chúng thuộc hai database vật lý hoàn toàn khác nhau (Tier 2 vs Tier 4). Kiến trúc hệ thống đã tách biệt ở tầng Database nhưng Backend lại gộp chung — đây là mâu thuẫn trực tiếp.

> **Lỗ hổng 2 — Session duy nhất không thể phân biệt DB:** `db/session.py` đơn lẻ không thể phản ánh 5 connection string trỏ vào 5 database khác nhau. Không có gì ngăn developer vô tình dùng sai session khi truy cập Identity DB thay vì Staging DB.

> **Lỗ hổng 3 — Không enforce ràng buộc truy cập:** Không có gì trong cấu trúc thư mục ngăn developer gọi Write vào Reporting DB (vốn chỉ cho phép Read-only), hoặc gọi Delete vào Audit Log DB (vốn chỉ cho phép Append-only).

> **Lỗ hổng 4 — Ranh giới kiến trúc chỉ tồn tại trên giấy:** Mọi service có thể import bất kỳ model nào từ bất kỳ DB nào mà không có cảnh báo hay ràng buộc nào từ cấu trúc thư mục.

---

### II. CẤU TRÚC ĐỀ XUẤT — HEMIS HYBRID ARCHITECTURE

#### Nguyên tắc thiết kế

- **Tầng DB / Model / Repository**: Tổ chức theo **Database vật lý** — phản ánh đúng ranh giới Tier 2 và Tier 4.
- **Tầng API / Service / Schema**: Tổ chức theo **Domain nghiệp vụ** — giữ nguyên tính gắn kết của luồng xử lý.
- **Tầng cấu hình / Test / Docs**: Giữ nguyên, không thay đổi.

---

#### Cấu trúc dự án đầy đủ

```
D:\HEMIS\HEMIS-PROJECT\HEMIS-Backend\
│
├── app\
│   │
│   ├── api\                                          ✅ GIỮ NGUYÊN VỊ TRÍ
│   │   ├── dependencies.py                           ✅ GIỮ NGUYÊN
│   │   └── v1\
│   │       └── endpoints\
│   │           ├── auth.py                           ✅ GIỮ NGUYÊN
│   │           ├── identity.py                       ✅ GIỮ NGUYÊN
│   │           ├── danh_muc.py                       ✅ GIỮ NGUYÊN
│   │           ├── chuong_trinh_dao_tao.py           ✅ GIỮ NGUYÊN
│   │           └── audit.py                          🆕 THÊM MỚI — Endpoint tra cứu Audit Log (Read-only)
│   │
│   ├── core\
│   │   └── config.py                                 ✅ GIỮ NGUYÊN
│   │
│   ├── db\                                           🔄 TÁI CẤU TRÚC HOÀN TOÀN
│   │   ├── base.py                                   ✅ GIỮ NGUYÊN — Base declarative cho ORM
│   │   ├── session_identity.py                       🆕 Connection đến Tier 2 — Identity DB
│   │   ├── session_staging.py                        🆕 Connection đến Tier 4 — Staging DB
│   │   ├── session_core.py                           🆕 Connection đến Tier 4 — Core DB
│   │   ├── session_reporting.py                      🆕 Connection đến Tier 4 — Reporting DB (Read-only)
│   │   └── session_audit.py                          🆕 Connection đến Tier 4 — Audit Log DB (Append-only)
│   │
│   ├── models\                                       🔄 TÁI CẤU TRÚC — Tách theo DB vật lý
│   │   ├── identity\
│   │   │   ├── __init__.py
│   │   │   └── identity.py                           🔄 DI CHUYỂN từ models/identity.py
│   │   ├── staging\
│   │   │   ├── __init__.py
│   │   │   ├── danh_muc.py                           🔄 DI CHUYỂN từ models/danh_muc.py
│   │   │   └── chuong_trinh_dao_tao.py               🔄 DI CHUYỂN từ models/chuong_trinh_dao_tao.py
│   │   ├── core\
│   │   │   ├── __init__.py
│   │   │   ├── danh_muc.py                           🆕 THÊM MỚI — Mirror model, Read + Hạn chế Write
│   │   │   └── chuong_trinh_dao_tao.py               🆕 THÊM MỚI — Mirror model, Read + Hạn chế Write
│   │   ├── reporting\
│   │   │   └── __init__.py                           🆕 Placeholder — mở rộng sau, Read-only
│   │   └── audit_log\
│   │       └── __init__.py                           🆕 Placeholder — mở rộng sau, Append-only
│   │
│   ├── repositories\                                 🔄 TÁI CẤU TRÚC — Tách theo DB vật lý VÀ Domain
│   │   ├── identity\
│   │   │   ├── __init__.py
│   │   │   └── identity_repo.py                      🔄 Tách ra từ repositories/__init__.py cũ
│   │   ├── staging\
│   │   │   ├── __init__.py
│   │   │   ├── danh_muc_repo.py                      🆕 TÁCH THEO DOMAIN — tránh staging_repo.py monolith
│   │   │   └── chuong_trinh_dao_tao_repo.py          🆕 TÁCH THEO DOMAIN
│   │   ├── core\
│   │   │   ├── __init__.py
│   │   │   ├── danh_muc_repo.py                      🆕 Read + Hạn chế Write
│   │   │   └── chuong_trinh_dao_tao_repo.py          🆕 Read + Hạn chế Write
│   │   └── audit\
│   │       ├── __init__.py
│   │       └── audit_repo.py                         🆕 Append-only — KHÔNG có update()/delete() method
│   │
│   ├── schemas\                                      ✅ GIỮ NGUYÊN HOÀN TOÀN
│   │   ├── auth.py
│   │   ├── identity.py
│   │   ├── danh_muc.py
│   │   ├── chuong_trinh_dao_tao.py
│   │   └── workflow.py                               🆕 THÊM MỚI — Schema cho luồng Staging → Core
│   │
│   ├── services\                                     🔄 BỔ SUNG — Làm rõ trách nhiệm từng service
│   │   ├── auth_service.py                           ✅ GIỮ NGUYÊN
│   │   ├── identity_service.py                       ✅ GIỮ NGUYÊN — Chỉ gọi repositories/identity/
│   │   ├── danh_muc_service.py                       ✅ GIỮ NGUYÊN — Chỉ gọi staging/ và core/
│   │   ├── chuong_trinh_dao_tao_service.py           ✅ GIỮ NGUYÊN — Chỉ gọi staging/ và core/
│   │   └── workflow_service.py                       🆕 THÊM MỚI — Orchestrate cross-DB: Staging→Core→Audit
│   │
│   ├── exceptions.py                                 ✅ GIỮ NGUYÊN
│   ├── utils.py                                      ✅ GIỮ NGUYÊN
│   └── main.py                                       ✅ GIỮ NGUYÊN
│
├── docs\
│   └── mo_ta_cau_truc.md                             ✅ GIỮ NGUYÊN
│
├── tests\
│   ├── integration\
│   │   └── __init__.py                               ✅ GIỮ NGUYÊN
│   └── unit\
│       └── __init__.py                               ✅ GIỮ NGUYÊN
│
├── venv\                                             ✅ GIỮ NGUYÊN
└── .env                                              ✅ GIỮ NGUYÊN
```

---

### III. GIẢI TRÌNH TỪNG QUYẾT ĐỊNH THIẾT KẾ

#### 3.1 Tầng `db/` — Tách session theo DB vật lý

**Thay đổi:** Từ 1 file `session.py` → 5 file session riêng biệt.

Mỗi DB vật lý có connection string, pool size, và timeout riêng. Việc tách session giúp developer không thể dùng nhầm connection, và cho phép cấu hình quyền truy cập ngay ở tầng connection (ví dụ: `session_reporting.py` chỉ dùng DB user có quyền SELECT).

---

#### 3.2 Tầng `models/` — Tách theo DB vật lý

**Thay đổi:** Từ flat list → thư mục con `identity/`, `staging/`, `core/`, `reporting/`, `audit_log/`.

Ranh giới vật lý của DB phải được phản ánh trong cấu trúc code. Developer mở thư mục `models/staging/` biết ngay mình đang làm việc với Staging DB, không thể nhầm sang Identity DB.

`reporting/` và `audit_log/` hiện là placeholder nhưng phải được phát triển tiếp với ràng buộc rõ ràng: Reporting không có Create/Update/Delete method; Audit Log không có Update/Delete method ở bất kỳ tầng nào.

---

#### 3.3 Tầng `repositories/` — Tách theo DB vật lý VÀ Domain

**Thay đổi:** Từ flat `staging_repo.py` → thư mục con theo DB, bên trong tách tiếp theo Domain.

Một file `staging_repo.py` duy nhất cho toàn bộ Staging DB là anti-pattern — file này sẽ phình to không kiểm soát khi hệ thống mở rộng. Tách theo Domain bên trong từng tầng DB giữ được Single Responsibility.

**Ràng buộc bắt buộc theo kiến trúc:**

- `repositories/core/` — chỉ Read và Write có kiểm soát, không có Delete.
- `repositories/audit/` — chỉ có `append()` / `create()`, tuyệt đối không có `update()` hay `delete()`.
- `repositories/identity/` — chỉ được gọi từ `identity_service.py` và `auth_service.py`.

---

#### 3.4 Tầng `services/` — Làm rõ trách nhiệm cross-DB

**Thay đổi:** Thêm `workflow_service.py`, làm rõ ranh giới của từng service hiện có.

Luồng nghiệp vụ Staging → Core → Audit Log là luồng cross-DB phức tạp nhất trong hệ thống. Nếu để các service nghiệp vụ (`danh_muc_service.py`) tự xử lý cross-DB, sẽ dẫn đến logic phân tán và khó kiểm soát transaction.

|Service|Được gọi vào DB nào|Không được gọi vào DB nào|
|---|---|---|
|`auth_service.py`|Identity|Staging, Core, Reporting, Audit|
|`identity_service.py`|Identity, Audit (log hành động)|Staging, Core, Reporting|
|`danh_muc_service.py`|Staging, Core (đọc)|Identity, Reporting, Audit trực tiếp|
|`chuong_trinh_dao_tao_service.py`|Staging, Core (đọc)|Identity, Reporting, Audit trực tiếp|
|`workflow_service.py`|Staging → Core → Audit|Identity, Reporting|

---

#### 3.5 Tầng `schemas/` và `api/` — Giữ nguyên

Hai tầng này phục vụ nghiệp vụ, không phụ thuộc vào cấu trúc DB vật lý. Tổ chức theo Domain nghiệp vụ là đúng đắn và không cần điều chỉnh.

---

### IV. TỔNG KẾT SO SÁNH

|Tiêu chí|Cấu trúc cũ|Cấu trúc mới|
|---|---|---|
|Phân tách Identity vs Business DB|❌ Nằm cùng thư mục `models/`|✅ Tách biệt `models/identity/` vs `models/staging/`|
|Session theo DB vật lý|❌ 1 session duy nhất|✅ 5 session tương ứng 5 DB|
|Enforce Read-only cho Reporting|❌ Không có gì ngăn Write|✅ Session + Repository riêng, chỉ có Read method|
|Enforce Append-only cho Audit Log|❌ Không có gì ngăn Update/Delete|✅ `audit_repo.py` chỉ có `append()` method|
|Repository phình to|❌ Toàn bộ Staging vào 1 file|✅ Tách theo Domain trong từng tầng DB|
|Cross-DB workflow rõ ràng|❌ Logic phân tán trong từng service|✅ Tập trung vào `workflow_service.py`|
|Tầng API / Schema|✅ Đúng hướng|✅ Giữ nguyên|
|Cấu hình / Test / Docs|✅ Ổn|✅ Giữ nguyên|

---

_Tài liệu được soạn dựa trên tài liệu kiến trúc HEMIS (FULL_HEMIS_DOCS_CLEAR.md) và phân tích cấu trúc dự án hiện tại._