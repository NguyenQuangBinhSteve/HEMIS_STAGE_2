# Tài liệu Kiến trúc & Flow — Backend Cũ (`be_hemis_stu_old`)

> **Scope**: Module Chương Trình Đào Tạo (CTĐT) và các Danh mục liên quan.
> **Source**: `/home/kali/Development/Projects/be_hemis_stu_old/`

---

## 1. Tổng quan

### 1.1. Technology Stack

| Thành phần | Công nghệ                                  |
| ---------- | ------------------------------------------ |
| Framework  | FastAPI                                    |
| DB Driver  | `asyncpg` (raw connection pool, không ORM) |
| Database   | PostgreSQL                                 |
| Validation | Pydantic v2                                |
| Config     | `pydantic-settings`                        |
| Excel      | `openpyxl`                                 |
| Naming     | CamelCase (API + DB columns)               |

### 1.2. Cấu trúc thư mục (scope CTĐT + Danh mục)

```
be_hemis_stu_old/app/
├── main.py                                    # FastAPI app, lifespan, CORS
├── config.py                                  # Settings (DB URL, CORS, debug...)
├── database.py                                # asyncpg pool wrapper
│
├── api/v1/
│   ├── router.py                              # Đăng ký tất cả module routers
│   ├── deps.py                                # DatabaseDep type alias
│   └── endpoints/
│       ├── danh_muc.py                        # CRUD danh mục (~70 bảng)
│       └── chuong_trinh_dt/
│           ├── __init__.py                    # Gộp 4 sub-routers
│           ├── views.py                       # GET list + Export Excel
│           ├── imports.py                     # POST batch import
│           ├── updates.py                     # PUT single record
│           └── deletes.py                     # DELETE single record
│
├── modules/
│   ├── registry.py                            # Registry pattern cho tất cả modules
│   ├── chuong_trinh_dt/
│   │   ├── constants.py                       # FN_MAPPING, VIEW_MAPPING, params
│   │   ├── mapping.py                         # Data mapping (tên → mã danh mục)
│   │   └── schemas.py                         # Import/Update/View schemas
│   └── danh_muc/
│       ├── schemas.py                         # ~70 bộ Pydantic schemas
│       └── service.py                         # TABLE_CONFIG + raw SQL CRUD
│
├── services/
│   ├── view_service.py                        # Query DB Views + pagination + search
│   ├── import_service.py                      # Gọi SP FN_Import_* + batch
│   ├── update_service.py                      # Gọi SP FN_Update_*
│   ├── delete_service.py                      # Gọi SP FN_Delete_*
│   ├── data_mapping_service.py                # Lookup danh mục: tên → mã
│   └── metadata_cache.py                      # Cache SP signatures để cast kiểu
│
├── schemas/
│   └── base.py                                # PaginatedResponse, ImportResult...
└── utils/
    └── excel.py                               # openpyxl export helper
```

---

## 2. Module System — Registry Pattern

Hệ thống backend cũ sử dụng **Registry Pattern** để quản lý cấu hình cho tất cả modules một cách tập trung.

### 2.1. Cách hoạt động

Mỗi module (nguoi_hoc, can_bo, chuong_trinh_dt...) đăng ký 2 thành phần vào registry:
- **`constants`**: Chứa mapping tên endpoint → tên SP, tên View, danh sách params
- **`mapping`**: Chứa hàm async tra cứu danh mục (tên hiển thị → mã lưu trữ)

Các **shared services** (`ViewService`, `ImportService`, `UpdateService`, `DeleteService`) nhận `module_name` và tra cứu config từ registry để biết gọi SP nào, query View nào.

### 2.2. Code minh họa

**File**: `app/modules/registry.py`
```python
MODULE_CONFIGS = {
    "chuong_trinh_dt": {
        "constants": chuong_trinh_constants,
        "mapping": chuong_trinh_mapping.MAPPER_REGISTRY
    },
    # ... các module khác
}

def get_module_config(module_name: str):
    cfg = MODULE_CONFIGS.get(module_name)
    if isinstance(cfg, dict) and "constants" in cfg:
        return cfg["constants"]
    return cfg
```

### 2.3. Constants — Cấu hình module CTĐT

**File**: `app/modules/chuong_trinh_dt/constants.py`

Module CTĐT có **6 sub-modules**, mỗi sub-module được cấu hình qua 6 dict:

| Dict | Mục đích | Ví dụ |
|---|---|---|
| `FN_MAPPING` | Endpoint → SP Import | `"chuong-trinh-dao-tao"` → `"FN_Import_ChuongTrinhDaoTao"` |
| `FN_PARAMS` | SP → danh sách params | `"FN_Import_ChuongTrinhDaoTao"` → `["MaNganhDaoTao", "MaChuongTrinhDaoTao", ...]` |
| `FN_UPDATE_MAPPING` | Endpoint → SP Update | `"chuong-trinh-dao-tao"` → `"FN_Update_ChuongTrinhDaoTao"` |
| `FN_UPDATE_PARAMS` | SP Update → params (có ID) | `["ID", "MaNganhDaoTao", ...]` |
| `FN_DELETE_MAPPING` | Endpoint → SP Delete | `"chuong-trinh-dao-tao"` → `"FN_Delete_ChuongTrinhDaoTao"` |
| `VIEW_MAPPING` | Endpoint → DB View | `"chuong-trinh-dao-tao"` → `"V_ChuongTrinhDaoTao"` |
| `VIEW_COLUMNS` | View → danh sách cột | `["MaNganhDaoTao", "MaChuongTrinhDaoTao", ...]` |

**6 sub-modules CTĐT:**

| # | Endpoint key | SP Import | DB View |
|---|---|---|---|
| 1 | `chuong-trinh-dao-tao` | `FN_Import_ChuongTrinhDaoTao` | `V_ChuongTrinhDaoTao` |
| 2 | `dao-tao-nam-ap-dung` | `FN_Import_NamApDung` | `V_NamApDung` |
| 3 | `cap-phep-chuong-trinh` | `FN_Import_QuyetDinhCapPhep` | `V_QuyetDinhCapPhep` |
| 4 | `thong-tin-kiem-dinh` | `FN_Import_ThongTinKiemDinh` | `V_ThongTinKiemDinh_CTDT` |
| 5 | `gia-han-chuong-trinh` | `FN_Import_GiaHan` | `V_GiaHan` |
| 6 | `ngon-ngu-giang-day` | `FN_Import_NgonNguGiangDay` | `V_NgonNguGiangDay` |

---

## 3. Database Layer

### 3.1. Connection Pool

**File**: `app/database.py`

Sử dụng `asyncpg` pool trực tiếp (không qua ORM):

```python
class Database:
    def __init__(self):
        self.pool: Optional[asyncpg.Pool] = None

    async def connect(self):
        self.pool = await asyncpg.create_pool(
            url, min_size=10, max_size=100, command_timeout=60
        )

    async def fetch_all(self, query: str, *args):
        async with self.connection() as conn:
            rows = await conn.fetch(query, *args)
            return [dict(row) for row in rows]

    async def call_function(self, func_name: str, *args):
        placeholders = ", ".join(f"${i+1}" for i in range(len(args)))
        query = f"SELECT * FROM {func_name}({placeholders})"
        async with self.connection() as conn:
            rows = await conn.fetch(query, *args)
            return [dict(row) for row in rows]
```

**Đặc điểm**: Mọi thao tác ghi (INSERT/UPDATE/DELETE) đều thông qua **Stored Procedures** (`call_function`), không bao giờ dùng raw INSERT/UPDATE/DELETE SQL.

### 3.2. Dependency Injection

**File**: `app/api/v1/deps.py`
```python
DatabaseDep = Annotated[Database, Depends(get_db)]
```

Singleton `db` instance được tạo trong `database.py`, connect trong `lifespan` của FastAPI app.

---

## 4. Flow hoạt động — Module CTĐT

### 4.1. Flow GET — Xem dữ liệu (Pagination + Search)

```
Client GET /api/v1/chuong-trinh-dt/views/chuong-trinh-dao-tao?page=1&size=20&search=CNTT
    │
    ▼
[Endpoint: views.py] → get_view_data_generic("chuong-trinh-dao-tao", db, page, size, search)
    │
    ▼
[ViewService.get_view_data()]
    ├── 1. Tra cứu config: VIEW_MAPPING["chuong-trinh-dao-tao"] → "V_ChuongTrinhDaoTao"
    ├── 2. Build search clause (Heuristic): tự động chọn cột search dựa vào tên
    ├── 3. Query: SELECT * FROM V_ChuongTrinhDaoTao WHERE (...) ORDER BY ID LIMIT $2 OFFSET $3
    └── 4. Trả về PaginatedResponse { data, total, page, size, pages }
```

**Code minh họa** — File: `app/services/view_service.py`
```python
async def get_view_data(self, view_name, page=1, size=20, search=None, module_name="nguoi_hoc"):
    config = self._get_config(module_name)
    sql_view_name = config.VIEW_MAPPING.get(view_name)  # → "V_ChuongTrinhDaoTao"
    offset = (page - 1) * size

    if search:
        view_columns = config.VIEW_COLUMNS.get(view_name, [])
        search_condition = self._build_heuristic_search_clause(search, view_columns)
        data_query = f"SELECT * FROM {sql_view_name} WHERE ({search_condition}) ORDER BY ID LIMIT $2 OFFSET $3"
    # ...
```

**Heuristic Search**: Tự động chọn cột tìm kiếm. Nếu giá trị tìm là số → search trong các cột có prefix `ma`, `so`, `tong`. Nếu là chữ → search tất cả cột trừ `ngay*`, `date*`, `id`.

### 4.2. Flow Import — Tạo dữ liệu qua Stored Procedure

```
Client POST /api/v1/chuong-trinh-dt/imports/chuong-trinh-dao-tao
Body: [{ "MaNganhDaoTao": "...", "TenChuongTrinh": "...", "LoaiChuongTrinhDaoTao": "Chương trình tiến tiến" }]
    │
    ▼
[Endpoint: imports.py] → import_from_json_generic("chuong-trinh-dao-tao", records, db)
    │
    ▼
[ImportService.import_batch()] → loop qua từng record:
    │
    ▼
[ImportService.import_single_record()]
    ├── 1. Lấy FN name:  FN_MAPPING["chuong-trinh-dao-tao"] → "FN_Import_ChuongTrinhDaoTao"
    ├── 2. Lấy param list: FN_PARAMS["FN_Import_ChuongTrinhDaoTao"] → ["MaNganhDaoTao", ...]
    ├── 3. Data Mapping:  mapping.py → lookup("LoaiChuongTrinhDaoTao") → mã số
    ├── 4. Type casting:  MetadataCache → cast giá trị theo signature SP
    ├── 5. Gọi SP:        SELECT * FROM FN_Import_ChuongTrinhDaoTao($1, $2, ..., $24)
    └── 6. Parse result:  "INSERTED|UPDATED|ERROR" → ImportResult
```

**Data Mapping** — File: `app/modules/chuong_trinh_dt/mapping.py`

Trước khi gọi SP, hệ thống tra cứu bảng danh mục để chuyển **tên hiển thị → mã lưu trữ**:

```python
async def map_chuong_trinh_dao_tao(mapper: DataMappingService, record: Dict):
    result = record.copy()
    if record.get("LoaiChuongTrinhDaoTao"):
        result["MaLoaiChuongTrinhDaoTao"] = await mapper.lookup_code(
            "DM_LoaiChuongTrinhDaoTao",  # Bảng danh mục
            "TenLoaiHinh",                # Cột tìm kiếm (tên)
            "Ma",                         # Cột trả về (mã)
            record["LoaiChuongTrinhDaoTao"]
        )
    # ... 11 danh mục khác
```

**Bảng danh mục được lookup trong CTĐT mapping:**

| # | Field đầu vào | Bảng DM | Cột tìm | Cột trả về |
|---|---|---|---|---|
| 1 | `LoaiChuongTrinhDaoTao` | `DM_LoaiChuongTrinhDaoTao` | `TenLoaiHinh` | `Ma` |
| 2 | `HinhThucDaoTao` | `DM_HinhThucDaoTao` | `TenHinhThuc` | `Ma` |
| 3 | `LoaiChuongTrinhLienKetDaoTao` | `DM_LoaiChuongTrinhLienKetDaoTao` | `TenLoai` | `Ma` |
| 4 | `HocCheDaoTao` | `DM_HocCheDaoTao` | `TenHocChe` | `Ma` |
| 5 | `QuocGiaTruSoChinh` | `DM_QuocTich` | `TenNuoc` | `Ma` |
| 6 | `TrinhDoDaoTao` | `DM_TrinhDoDaoTao` | `TenTrinhDo` | `Ma` |
| 7 | `DonViCapBang` | `DM_DonViCapBang` | `TenDonVi` | `Ma` |
| 8 | `TrangThaiChuongTrinh` | `DM_TrangThaiChuongTrinhDaoTao` | `TenTrangThai` | `Ma` |
| 9 | `LoaiQuyetDinh` | `DM_LoaiQuyetDinhCTDT` | `TenQuyetDinh` | `Ma` |
| 10 | `ToChucKiemDinh` | `DM_ToChucKiemDinh` | `TenToChuc` | `Ma` |
| 11 | `KetQuaKiemDinh` | `DM_KetQuaKiemDinh` | `TenKetQua` | `Ma` |
| 12 | `NgonNguGiangDay` | `DM_NgoaiNgu` | `TenNgoaiNgu` | `Ma` |
| 13 | `TrinhDoNgonNguDauVao` | `DM_KhungNangLucNgoaiNgu` | `TenBac` | `Ma` |

### 4.3. Flow Update — Cập nhật qua Stored Procedure

```
Client PUT /api/v1/chuong-trinh-dt/updates/chuong-trinh-dao-tao/{id}
Body: { "TenChuongTrinh": "...", "HinhThucDaoTao": "Chính quy" }
    │
    ▼
[UpdateService.update_record()]
    ├── 1. Lấy FN: FN_UPDATE_MAPPING["chuong-trinh-dao-tao"] → "FN_Update_ChuongTrinhDaoTao"
    ├── 2. Data Mapping: "Chính quy" → lookup DM_HinhThucDaoTao → mã "CQ"
    ├── 3. Build named params: chỉ gửi các field có giá trị
    │      SELECT * FROM FN_Update_ChuongTrinhDaoTao(p_ID => $1, p_TenChuongTrinh => $2, p_MaHinhThucDaoTao => $3)
    └── 4. Trả về { result, id, update_type }
```

**Đặc điểm**: SP Update dùng **named parameters** (`p_Field => $N`), chỉ gửi field được thay đổi, các field khác giữ nguyên trong DB.

### 4.4. Flow Delete — Xóa qua Stored Procedure

```
Client DELETE /api/v1/chuong-trinh-dt/deletes/chuong-trinh-dao-tao/{id}
    │
    ▼
[DeleteService.delete_record()]
    ├── 1. Lấy FN: FN_DELETE_MAPPING["chuong-trinh-dao-tao"] → "FN_Delete_ChuongTrinhDaoTao"
    ├── 2. Gọi: SELECT * FROM FN_Delete_ChuongTrinhDaoTao($1)   -- $1 = record_id
    └── 3. Trả về { result: "DELETED", id, delete_type }
```

### 4.5. Flow Export Excel

```
Client POST /api/v1/chuong-trinh-dt/views/chuong-trinh-dao-tao/excel
Body: { "ids": [1, 2, 3] }  (optional)
    │
    ▼
[Endpoint: views.py] → export_excel_generic()
    ├── 1. ViewService.get_all_view_data() → query tất cả (hoặc theo ids/search)
    ├── 2. ViewService.get_columns_mapping() → map tên cột kỹ thuật → tên hiển thị
    ├── 3. create_excel_from_data() → openpyxl tạo file Excel
    └── 4. StreamingResponse trả về file .xlsx
```

---

## 5. Danh mục — Quản lý bằng TABLE_CONFIG

### 5.1. Kiến trúc

**File**: `app/modules/danh_muc/service.py`

Backend cũ quản lý **~70 bảng danh mục** thông qua một dict `TABLE_CONFIG` lớn. Mỗi entry mô tả:

```python
TABLE_CONFIG = {
    "hinh-thuc-dao-tao": {
        "table": "DM_HinhThucDaoTao",    # Tên bảng PostgreSQL
        "pk": "Ma",                       # Tên primary key column
        "pk_type": "str",                 # Kiểu PK: "str", "int", "serial"
        "columns": {                      # Mapping: schema field → DB column
            "ma": "Ma",
            "ten_hinh_thuc": "TenHinhThuc"
        }
    },
    # ... 69 bảng khác
}
```

### 5.2. CRUD Pattern

Tất cả danh mục dùng chung các hàm CRUD trong `DanhMucService`, tự động build SQL dựa trên `TABLE_CONFIG`:

- **GET all**: `SELECT * FROM {table} ORDER BY {pk}`
- **GET by ID**: `SELECT * FROM {table} WHERE {pk} = $1`
- **CREATE**: `INSERT INTO {table} ({columns}) VALUES ($1, $2...) RETURNING *`
- **UPDATE**: `UPDATE {table} SET {col}=$N WHERE {pk}=$1 RETURNING *`
- **DELETE**: `DELETE FROM {table} WHERE {pk}=$1 RETURNING *`

### 5.3. Danh mục liên quan CTĐT (14 bảng)

| # | Endpoint key | Bảng DB | PK Type |
|---|---|---|---|
| 1 | `hinh-thuc-dao-tao` | `DM_HinhThucDaoTao` | str |
| 2 | `loai-chuong-trinh-dao-tao` | `DM_LoaiChuongTrinhDaoTao` | int |
| 3 | `loai-chuong-trinh-lien-ket-dao-tao` | `DM_LoaiChuongTrinhLienKetDaoTao` | int |
| 4 | `hoc-che-dao-tao` | `DM_HocCheDaoTao` | int |
| 5 | `trinh-do-dao-tao` | `DM_TrinhDoDaoTao` | int |
| 6 | `nganh-dao-tao` | `DM_NganhDaoTao` | str |
| 7 | `quoc-tich` | `DM_QuocTich` | str |
| 8 | `don-vi-cap-bang` | `DM_DonViCapBang` | int |
| 9 | `trang-thai-chuong-trinh-dao-tao` | `DM_TrangThaiChuongTrinhDaoTao` | int |
| 10 | `loai-quyet-dinh-ctdt` | `DM_LoaiQuyetDinhCTDT` | str |
| 11 | `to-chuc-kiem-dinh` | `DM_ToChucKiemDinh` | str |
| 12 | `ket-qua-kiem-dinh` | `DM_KetQuaKiemDinh` | str |
| 13 | `ngoai-ngu` | `DM_NgoaiNgu` | str |
| 14 | `khung-nang-luc-ngoai-ngu` | `DM_KhungNangLucNgoaiNgu` | int |

---

## 6. Đặc điểm kỹ thuật nổi bật

### 6.1. Stored Procedure Pattern
- Mọi thao tác ghi đều qua SP: `FN_Import_*`, `FN_Update_*`, `FN_Delete_*`
- SP trả về result string: `"INSERTED"`, `"UPDATED"`, `"ERROR|field|message"`
- Cho phép validate business logic phức tạp trong database layer

### 6.2. MetadataCache
- **File**: `app/services/metadata_cache.py`
- Khi app khởi động, cache tất cả SP signatures (tên param + kiểu dữ liệu)
- `convert_value_for_sp()` tự động cast Python value sang PostgreSQL type dựa trên cache

### 6.3. Heuristic Search
- **File**: `app/services/view_service.py` → `_build_heuristic_search_clause()`
- Tự động xác định cột tìm kiếm dựa vào prefix tên cột và kiểu dữ liệu tìm kiếm
- Nếu search value là số → tìm trong các cột `Ma*`, `So*`, `ID`
- Nếu search value là chữ → tìm trong tất cả cột text, loại trừ `Ngay*`, `Date*`

### 6.4. Batch Import với Async Concurrency
- **File**: `app/services/import_service.py`
- `import_batch()` loop từng record, gọi `import_single_record()` tuần tự
- Có code chuẩn bị cho multiprocessing (chia chunks, fork processes) nhưng hiện tại chạy sequential

### 6.5. CamelCase Convention
- Tất cả field names trong API request/response đều CamelCase: `MaChuongTrinhDaoTao`, `TenChuongTrinh`
- DB columns cũng CamelCase: `V_ChuongTrinhDaoTao.MaChuongTrinhDaoTao`
- Sử dụng `CaseInsensitiveModel` để handle lowercase PostgreSQL column names

---

## 7. API Endpoints — Module CTĐT

**Base path**: `/api/v1/chuong-trinh-dt`

| Method | Path | Mô tả |
|---|---|---|
| **Views** (GET) | | |
| GET | `/views/chuong-trinh-dao-tao` | Danh sách CTĐT (pagination + search) |
| GET | `/views/dao-tao-nam-ap-dung` | Danh sách Năm áp dụng |
| GET | `/views/cap-phep-chuong-trinh` | Danh sách QĐ cấp phép |
| GET | `/views/thong-tin-kiem-dinh` | Danh sách Kiểm định |
| GET | `/views/gia-han-chuong-trinh` | Danh sách Gia hạn |
| GET | `/views/ngon-ngu-giang-day` | Danh sách Ngôn ngữ giảng dạy |
| **Export** (POST) | | |
| POST | `/views/{sub-module}/excel` | Xuất Excel cho từng sub-module |
| **Import** (POST) | | |
| POST | `/imports/chuong-trinh-dao-tao` | Import batch CTĐT |
| POST | `/imports/dao-tao-nam-ap-dung` | Import batch Năm áp dụng |
| POST | `/imports/cap-phep-chuong-trinh` | Import batch QĐ cấp phép |
| POST | `/imports/thong-tin-kiem-dinh` | Import batch Kiểm định |
| POST | `/imports/gia-han-chuong-trinh` | Import batch Gia hạn |
| POST | `/imports/ngon-ngu-giang-day` | Import batch Ngôn ngữ giảng dạy |
| **Update** (PUT) | | |
| PUT | `/updates/{sub-module}/{id}` | Cập nhật 1 record |
| **Delete** (DELETE) | | |
| DELETE | `/deletes/{sub-module}/{id}` | Xóa 1 record |

**Tổng**: 6 GET + 6 POST Export + 6 POST Import + 6 PUT + 6 DELETE = **30 endpoints**
