# Gap Analysis — So sánh Backend Cũ vs Backend Mới

> **Backend cũ**: `be_hemis_stu_old` — Hệ thống hoàn chỉnh (SP-based)
> **Backend mới**: `be_hemis_stu_new` — Skeleton (ORM-based, đang phát triển)
> **Schema DB mới**: `db_hemis_stu_new` — SQL definitions cho DB target mới
> **Scope**: Module Chương Trình Đào Tạo (CTĐT) + Danh mục liên quan

---

## 1. So sánh Kiến trúc tổng quan

| Khía cạnh | Backend Cũ (`be_hemis_stu_old`) | Backend Mới (`be_hemis_stu_new`) |
|---|---|---|
| **DB Driver** | `asyncpg` raw (connection pool) | SQLAlchemy 2.0 Async ORM |
| **CRUD Pattern** | Stored Procedures (FN_Import/Update/Delete) | ORM (select/add/setattr/delete) |
| **Read Pattern** | Database Views (V_*) | ORM select() |
| **Naming Convention** | CamelCase (`MaChuongTrinhDaoTao`) | snake_case (`ma_chuong_trinh_dao_tao`) |
| **Module System** | Registry Pattern (centralized config) | Per-entity files (model/schema/service/endpoint) |
| **Data Mapping** | `DataMappingService` (tên → mã lookup) | ORM FK relationships (chưa implement) |
| **Business Logic** | Trong SP (database layer) | Trong Service (Python layer) |
| **Config** | `pydantic-settings` (inline defaults) | `pydantic-settings` + `.env` |
| **Auth/Security** | Chưa có (TODO) | JWT + RBAC skeleton (chưa kết nối) |
| **Error Handling** | SP trả `"ERROR\|field\|message"` | HTTPException (chưa có custom exceptions) |

---

## 2. Mapping thành phần cũ → mới

### 2.1. File-to-File Mapping

| Thành phần Backend Cũ | File Cũ | Tương đương Backend Mới | File Mới | Trạng thái |
|---|---|---|---|---|
| DB Connection | `app/database.py` | DB Session | `app/db/session.py` + `app/db/base.py` | ✅ Có |
| App Config | `app/config.py` | Core Config | `app/core/config.py` | ✅ Có |
| DB Dependency | `app/api/v1/deps.py` | API Dependency | `app/api/dependencies.py` | ✅ Có |
| Module Registry | `app/modules/registry.py` | — | Không cần (per-entity) | ✅ Thay thế |
| CTĐT Constants | `app/modules/chuong_trinh_dt/constants.py` | ORM Models | `app/models/chuong_trinh_dao_tao.py` | ⚠️ Chỉ 1/6 |
| CTĐT Mapping | `app/modules/chuong_trinh_dt/mapping.py` | ORM Relationships | — | ❌ Chưa có |
| CTĐT Schemas | `app/modules/chuong_trinh_dt/schemas.py` | Pydantic Schemas | `app/schemas/chuong_trinh_dao_tao.py` | ⚠️ Chỉ 1/6 |
| View Service | `app/services/view_service.py` | CTĐT Service | `app/services/chuong_trinh_dao_tao_service.py` | ⚠️ Chỉ 1/6, thiếu pagination/search |
| Import Service | `app/services/import_service.py` | — | — | ❌ Chưa có |
| Update Service | `app/services/update_service.py` | CTĐT Service | (gộp trong service) | ⚠️ Chỉ 1/6 |
| Delete Service | `app/services/delete_service.py` | CTĐT Service | (gộp trong service) | ⚠️ Chỉ 1/6 |
| Data Mapping | `app/services/data_mapping_service.py` | — | — | ❌ Không cần nếu dùng ORM FK |
| Metadata Cache | `app/services/metadata_cache.py` | — | — | ❌ Không cần (ORM tự cast) |
| Base Schemas | `app/schemas/base.py` | Common Schemas | — | ❌ Chưa có |
| Excel Utils | `app/utils/excel.py` | Utils | — | ❌ Chưa có |
| DM Service | `app/modules/danh_muc/service.py` | DM Service | `app/services/danh_muc_service.py` | ⚠️ Chỉ 1/14 |
| DM Schemas | `app/modules/danh_muc/schemas.py` | DM Schemas | `app/schemas/danh_muc.py` | ⚠️ Chỉ 1/14 |
| DM Endpoint | `app/api/v1/endpoints/danh_muc.py` | DM Endpoint | `app/api/v1/endpoints/danh_muc.py` | ⚠️ Chỉ 1/14 |
| CTĐT Endpoints | `app/api/v1/endpoints/chuong_trinh_dt/` | CTĐT Endpoint | `app/api/v1/endpoints/chuong_trinh_dao_tao.py` | ⚠️ Chỉ 1/6 |
| Repository | — | Base Repository | `app/repositories/base.py` | ❌ Placeholder |
| Exceptions | — | Custom Exceptions | `app/exceptions/__init__.py` | ❌ Placeholder |
| Security | — | JWT + RBAC | `app/core/security.py` + `app/core/rbac.py` | ⚠️ Skeleton |

---

## 3. Gap Analysis — Module CTĐT (6 Sub-modules)

### 3.1. Trạng thái từng sub-module

| # | Sub-module | Bảng DB mới | Model | Schema | Service | Endpoint | **Status** |
|---|---|---|---|---|---|---|---|
| 1 | **Chương trình ĐT** | `CHUONG_TRINH_DAO_TAO` (26 cột) | ❌ | ❌ | ❌ | ❌ | **THIẾU hoàn toàn** |
| 2 | **Năm áp dụng** | `NAM_AP_DUNG` (9 cột) | ✅ | ✅ | ✅ | ✅ | **Có, thiếu pagination/search** |
| 3 | **Quyết định cấp phép** | `QUYET_DINH_CAP_PHEP` (8 cột) | ❌ | ❌ | ❌ | ❌ | **THIẾU hoàn toàn** |
| 4 | **Thông tin kiểm định** | `THONG_TIN_KIEM_DINH` (9 cột) | ❌ | ❌ | ❌ | ❌ | **THIẾU hoàn toàn** |
| 5 | **Gia hạn CTĐT** | `GIA_HAN_CHUONG_TRINH_DAO_TAO` (7 cột) | ❌ | ❌ | ❌ | ❌ | **THIẾU hoàn toàn** |
| 6 | **Ngôn ngữ giảng dạy** | `NGON_NGU_GIANG_DAY` (6 cột) | ❌ | ❌ | ❌ | ❌ | **THIẾU hoàn toàn** |

**Tóm tắt**: **1/6** sub-module đã implement (chỉ `NAM_AP_DUNG`), thiếu **5/6**.

### 3.2. Chi tiết cần bổ sung cho mỗi sub-module thiếu

Mỗi sub-module cần tạo/cập nhật ở **4 layer**:

| Layer | File | Nội dung cần bổ sung |
|---|---|---|
| **Model** | `app/models/chuong_trinh_dao_tao.py` | Thêm class SQLAlchemy cho mỗi bảng |
| **Schema** | `app/schemas/chuong_trinh_dao_tao.py` | Thêm bộ Base/Create/Update/Read |
| **Service** | `app/services/chuong_trinh_dao_tao_service.py` | Thêm CRUD methods cho mỗi entity |
| **Endpoint** | `app/api/v1/endpoints/chuong_trinh_dao_tao.py` | Thêm 5 routes (GET list, GET detail, POST, PUT, DELETE) |

---

## 4. Gap Analysis — Danh mục liên quan CTĐT (14 bảng)

| # | Bảng DM | SQL mới | Model mới | Schema mới | Service mới | Endpoint mới | **Status** |
|---|---|---|---|---|---|---|---|
| 1 | `DM_NGANH` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 2 | `DM_LOAI_CHUONG_TRINH_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 3 | `DM_HINH_THUC_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 4 | `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 5 | `DM_HOC_CHE_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 6 | `DM_NUOC` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 7 | `DM_TRINH_DO_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 8 | `DM_DON_VI_CAP_BANG` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 9 | `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 10 | `DM_LOAI_QUYET_DINH_CTDT` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 11 | `DM_TO_CHUC_KIEM_DINH` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 12 | `DM_KET_QUA_KIEM_DINH` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 13 | `DM_NGOAI_NGU` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |
| 14 | `DM_KHUNG_NANG_LUC_NGOAI_NGU` | ✅ | ❌ | ❌ | ❌ | ❌ | THIẾU |

**Tóm tắt**: SQL definitions đã sẵn sàng **14/14**. Backend mới implement **0/14** (chỉ có `DM_CO_QUAN_CHU_QUAN` không thuộc scope CTĐT).

---

## 5. Gap Analysis — Tính năng

| Tính năng | Backend Cũ | Backend Mới | Gap | Ưu tiên |
|---|---|---|---|---|
| **CRUD cơ bản** (5 endpoints/entity) | ✅ 30 endpoints (6 sub-modules × 5 ops) | ⚠️ 5 endpoints (chỉ NAM_AP_DUNG) | Thiếu 25 endpoints | 🔴 Cao |
| **DM CRUD** | ✅ ~70 bảng | ⚠️ 1 bảng (DM_CO_QUAN_CHU_QUAN) | Thiếu 14 bảng DM CTĐT | 🔴 Cao |
| **Pagination** | ✅ `PaginatedResponse{data,total,page,size,pages}` | ❌ Trả về toàn bộ list | Cần implement | 🔴 Cao |
| **Search/Filter** | ✅ Heuristic search (tự chọn cột) | ❌ Không có | Cần implement | 🟡 Trung bình |
| **Excel Export** | ✅ StreamingResponse + openpyxl | ❌ Không có | Cần implement | 🟡 Trung bình |
| **Excel/JSON Import** (batch) | ✅ Batch import qua SP | ❌ Không có | Cần implement | 🟡 Trung bình |
| **Data Mapping** (tên → mã) | ✅ `DataMappingService` | ❌ Không cần (ORM FK thay thế) | Kiến trúc khác | ✅ Đã giải quyết |
| **Metadata Cache** (SP signatures) | ✅ Cache type casting | ❌ Không cần (ORM tự cast) | Kiến trúc khác | ✅ Đã giải quyết |
| **BaseRepository** | ❌ Không có | ⚠️ Placeholder (chưa implement) | Cần implement | 🟡 Trung bình |
| **Custom Exceptions** | ❌ Generic errors | ⚠️ Placeholder (chưa implement) | Cần implement | 🟢 Thấp |
| **Structured Logging** | ❌ print() | ⚠️ Placeholder | Cần implement | 🟢 Thấp |
| **JWT Authentication** | ❌ Không có | ⚠️ Skeleton có, chưa kết nối | Cần kết nối | 🟢 Thấp |
| **RBAC** | ❌ Không có | ⚠️ Skeleton có, chưa kết nối | Cần kết nối | 🟢 Thấp |

---

## 6. Mapping cột dữ liệu — Bảng chính `CHUONG_TRINH_DAO_TAO`

| # | Tên cũ (CamelCase) | Tên DB mới (UPPER_SNAKE) | Tên ORM mới (snake_case) | Kiểu cũ | Kiểu mới | Ghi chú |
|---|---|---|---|---|---|---|
| 1 | `MaNganhDaoTao` | `NGANH_ID` | `nganh_id` | str | VARCHAR(20) | Tên cột đổi |
| 2 | `MaChuongTrinhDaoTao` | `MA_CHUONG_TRINH` | `ma_chuong_trinh` | str | VARCHAR(50) | Tên cột rút gọn |
| 3 | `TenChuongTrinh` | `TEN` | `ten` | str | VARCHAR(500) | Tên cột rút gọn |
| 4 | `TenChuongTrinhTiengAnh` | `TEN_TIENG_ANH` | `ten_tieng_anh` | str | VARCHAR(500) | |
| 5 | `NamBatDauTuyenSinh` | `NAM_TUYEN_SINH` | `nam_tuyen_sinh` | int | INT | |
| 6 | `TenCoSoDaoTaoNuocNgoai` | `TEN_CO_SO_DAO_TAO` | `ten_co_so_dao_tao` | str | VARCHAR(500) | Tên cột rút gọn |
| 7 | `MaLoaiChuongTrinhDaoTao` | `LOAI_CHUONG_TRINH_DAO_TAO` | `loai_chuong_trinh_dao_tao` | int | INT | Bỏ prefix `Ma` |
| 8 | `MaHinhThucDaoTao` | `HINH_THUC_DAO_TAO` | `hinh_thuc_dao_tao` | str | VARCHAR(10) | Bỏ prefix `Ma` |
| 9 | `MaLoaiChuongTrinhLienKetDaoTao` | `LOAI_CHUONG_TRINH_LIEN_KET` | `loai_chuong_trinh_lien_ket` | int | INT | Rút gọn |
| 10 | `DiaDiemDaoTao` | `DIA_DIEM_DAO_TAO` | `dia_diem_dao_tao` | str | VARCHAR(500) | |
| 11 | `MaHocCheDaoTao` | `HOC_CHE_DAO_TAO` | `hoc_che_dao_tao` | int | INT | Bỏ prefix `Ma` |
| 12 | `MaQuocGiaTruSoChinh` | `QUOC_GIA` | `quoc_gia` | str | VARCHAR(10) | Rút gọn |
| 13 | `NgayBanHanhChuanDauRa` | `NGAY_BAN_HANH_CHUAN_DAU_RA` | `ngay_ban_hanh_chuan_dau_ra` | **str** (dd/MM/yyyy) | **DATE** | ⚠️ Đổi kiểu |
| 14 | `MaTrinhDoDaoTao` | `TRINH_DO_DAO_TAO` | `trinh_do_dao_tao` | int | INT | Bỏ prefix `Ma` |
| 15 | `ThoiGianDaoTaoChuan` | `THOI_GIAN_DAO_TAO_CHUAN` | `thoi_gian_dao_tao_chuan` | int | INT | |
| 16 | `ChuanDauRa` | `CHUAN_DAU_RA` | `chuan_dau_ra` | str | TEXT | |
| 17 | `MaDonViCapBang` | `DON_VI_CAP_BANG` | `don_vi_cap_bang` | int | INT | Bỏ prefix `Ma` |
| 18 | `CacLoaiChungChiChapThuan` | `LOAI_CHUNG_CHI_DUOC_CHAP_THUAN` | `loai_chung_chi_duoc_chap_thuan` | str | VARCHAR(500) | Tên cột đổi |
| 19 | `DonViThucHienChuongTrinh` | `DON_VI_THUC_HIEN` | `don_vi_thuc_hien` | str | VARCHAR(500) | Rút gọn |
| 20 | `MaTrangThaiChuongTrinh` | `TRANG_THAI` | `trang_thai` | int | INT | Rút gọn |
| 21 | `ChuanDauRaNgoaiNgu` | `CHUAN_DAU_RA_NGOAI_NGU` | `chuan_dau_ra_ngoai_ngu` | str | TEXT | |
| 22 | `ChuanDauRaTinHoc` | `CHUAN_DAU_RA_TIN_HOC` | `chuan_dau_ra_tin_hoc` | str | TEXT | |
| 23 | `HocPhiTaiVietNam` | `HOC_PHI_TRONG_NUOC` | `hoc_phi_trong_nuoc` | **Decimal** | **BIGINT** | ⚠️ Đổi kiểu + tên |
| 24 | `HocPhiTaiNuocNgoai` | `HOC_PHI_NUOC_NGOAI` | `hoc_phi_nuoc_ngoai` | **Decimal** | **BIGINT** | ⚠️ Đổi kiểu |

**Thay đổi đáng chú ý:**
- Bỏ prefix `Ma` cho các FK columns → dùng tên gốc của entity
- `NgayBanHanhChuanDauRa`: String format `dd/MM/yyyy` → native `DATE` type
- `HocPhi*`: `Decimal` → `BIGINT` (lưu đơn vị VNĐ nguyên)

---

## 7. Breaking Changes — API

### 7.1. API Path thay đổi

| Loại | Backend Cũ | Backend Mới |
|---|---|---|
| GET list | `/api/v1/chuong-trinh-dt/views/chuong-trinh-dao-tao` | `/api/v1/chuong-trinh-dao-tao/chuong-trinh-dao-tao` |
| Import | `/api/v1/chuong-trinh-dt/imports/chuong-trinh-dao-tao` | `/api/v1/chuong-trinh-dao-tao/chuong-trinh-dao-tao` (POST) |
| Update | `/api/v1/chuong-trinh-dt/updates/chuong-trinh-dao-tao/{id}` | `/api/v1/chuong-trinh-dao-tao/chuong-trinh-dao-tao/{id}` (PUT) |
| Delete | `/api/v1/chuong-trinh-dt/deletes/chuong-trinh-dao-tao/{id}` | `/api/v1/chuong-trinh-dao-tao/chuong-trinh-dao-tao/{id}` (DELETE) |
| DM list | `/api/v1/danh-muc/hinh-thuc-dao-tao` | `/api/v1/danh-muc/hinh-thuc-dao-tao` (giữ nguyên) |

**Thay đổi chính**: Bỏ `/views/`, `/imports/`, `/updates/`, `/deletes/` sub-paths → dùng HTTP method phân biệt.

### 7.2. Field Names thay đổi

```
# Request/Response cũ (CamelCase)
{ "MaChuongTrinhDaoTao": "CT001", "TenChuongTrinh": "CNTT", "HocPhiTaiVietNam": 15000000.00 }

# Request/Response mới (snake_case)
{ "ma_chuong_trinh": "CT001", "ten": "CNTT", "hoc_phi_trong_nuoc": 15000000 }
```

---

## 8. Đề xuất thứ tự bổ sung

| Ưu tiên | Hạng mục | Lý do |
|---|---|---|
| **1** | BaseRepository + PaginatedResponse + Custom Exceptions | Hạ tầng chung, phải có trước |
| **2** | 14 bảng Danh mục CTĐT (Model + Schema + Service + Endpoint) | CTĐT phụ thuộc FK vào DM |
| **3** | 5 sub-modules CTĐT còn thiếu (đặc biệt bảng chính `CHUONG_TRINH_DAO_TAO`) | Core business |
| **4** | Pagination + Search cho GET list endpoints | UX cơ bản |
| **5** | Excel Export / Import | Tính năng nâng cao |
| **6** | JWT + RBAC kết nối vào endpoints | Security |

---

## 9. Tổng kết số liệu

| Metric | Backend Cũ (CTĐT scope) | Backend Mới (hiện tại) | Cần bổ sung |
|---|---|---|---|
| **API Endpoints CTĐT** | 30 | 5 | +25 |
| **API Endpoints DM** | 70 (5 × 14 DM) | 5 (1 DM) | +65 |
| **ORM Models CTĐT** | — (SP-based) | 1 | +5 |
| **ORM Models DM** | — (raw SQL) | 1 | +14 |
| **Pydantic Schemas CTĐT** | 12 classes | 3 classes | +20 (4/entity × 5) |
| **Pydantic Schemas DM** | 56 classes (4/entity × 14) | 3 classes | +53 |
| **Tính năng** | Pagination, Search, Excel Import/Export | CRUD cơ bản | +4 tính năng |
