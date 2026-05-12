# Tài liệu Kiến trúc & Flow — Backend Mới (`be_hemis_stu_new`)

> **Scope**: Module Chương Trình Đào Tạo (CTĐT) và các Danh mục liên quan.
> **Source code**: `/home/kali/Development/Projects/be_hemis_stu_new/`
> **Schema DB**: `/home/kali/Development/Projects/db_hemis_stu_new/`

---

## 1. Tổng quan

### 1.1. Technology Stack

| Thành phần | Công nghệ |
|---|---|
| Framework | FastAPI |
| ORM | SQLAlchemy 2.0 (Async) |
| DB Driver | `asyncpg` (qua SQLAlchemy) |
| Database | PostgreSQL |
| Validation | Pydantic v2 |
| Config | `pydantic-settings` |
| Security | JWT (jose) + bcrypt |
| Naming | snake_case (API + DB + Python) |

### 1.2. Cấu trúc thư mục

```
be_hemis_stu_new/app/
├── main.py                                    # FastAPI app, CORS, health check
│
├── core/                                      # Cốt lõi hệ thống (không phụ thuộc nghiệp vụ)
│   ├── config.py                              # pydantic-settings: DB, JWT, CORS
│   ├── security.py                            # Hash password, tạo/verify JWT
│   ├── rbac.py                                # Logic kiểm tra quyền hạn
│   └── logging.py                             # JSON structured logging
│
├── db/                                        # Database layer
│   ├── base.py                                # SQLAlchemy declarative_base()
│   └── session.py                             # AsyncEngine + AsyncSessionLocal
│
├── api/                                       # API layer
│   ├── dependencies.py                        # get_db() → yield AsyncSession
│   └── v1/
│       ├── api.py                             # Đăng ký routers
│       └── endpoints/
│           ├── danh_muc.py                    # CRUD DM_CO_QUAN_CHU_QUAN
│           ├── chuong_trinh_dao_tao.py        # CRUD NAM_AP_DUNG
│           ├── identity.py                    # CRUD NguoiDung
│           └── auth.py                        # Login / Token
│
├── models/                                    # SQLAlchemy ORM models
│   ├── __init__.py                            # Import tất cả models
│   ├── danh_muc.py                            # DMCoQuanChuQuan
│   ├── chuong_trinh_dao_tao.py                # NamApDung
│   └── identity.py                            # NguoiDung
│
├── schemas/                                   # Pydantic schemas (request/response)
│   ├── __init__.py                            # Export tất cả schemas
│   ├── danh_muc.py                            # Base/Create/Update/Read
│   ├── chuong_trinh_dao_tao.py                # Base/Create/Update/Read
│   ├── identity.py
│   └── auth.py                                # LoginRequest, Token, TokenData
│
├── repositories/                              # Data access layer (placeholder)
│   └── base.py                                # BaseRepository (chưa implement)
│
├── services/                                  # Business logic layer
│   ├── __init__.py
│   ├── danh_muc_service.py                    # CRUD qua SQLAlchemy ORM
│   ├── chuong_trinh_dao_tao_service.py        # CRUD qua SQLAlchemy ORM
│   ├── identity_service.py
│   └── auth_service.py
│
├── exceptions/                                # Custom exceptions (placeholder)
│   └── __init__.py
│
└── utils/                                     # Tiện ích kỹ thuật (chưa có file)
```

### 1.3. Thiết kế kiến trúc — Clean Architecture

Backend mới tuân thủ nguyên tắc **tách tầng (Layered Architecture)**:

```
Request → [Endpoint] → [Service] → [Repository*] → [Model/ORM] → Database
                                         ↑
                              * Chưa implement, Service gọi ORM trực tiếp
```

| Layer | Thư mục | Vai trò |
|---|---|---|
| **API** | `api/v1/endpoints/` | Nhận request, validate input, trả response |
| **Service** | `services/` | Business logic, orchestration |
| **Repository** | `repositories/` | Data access (trừu tượng hóa DB queries) — *placeholder* |
| **Model** | `models/` | SQLAlchemy ORM table definitions |
| **Schema** | `schemas/` | Pydantic validation (Create, Update, Read) |

---

## 2. Database Layer

### 2.1. Declarative Base

**File**: `app/db/base.py`
```python
from sqlalchemy.orm import declarative_base
Base = declarative_base()
```

Tất cả ORM models kế thừa từ `Base`. SQLAlchemy sử dụng metadata từ `Base` để biết cấu trúc bảng.

### 2.2. Async Engine & Session

**File**: `app/db/session.py`
```python
engine: AsyncEngine = create_async_engine(
    settings.DATABASE_URL,
    echo=settings.DB_ECHO,
    pool_pre_ping=True,
    pool_size=10,
    max_overflow=20
)

AsyncSessionLocal = sessionmaker(
    bind=engine,
    class_=AsyncSession,
    expire_on_commit=False,
    autoflush=False
)
```

- `pool_pre_ping=True`: Kiểm tra connection còn sống trước khi dùng
- `expire_on_commit=False`: Giữ data trên object sau commit (không cần refresh)
- `autoflush=False`: Không tự động flush changes

### 2.3. Session Dependency (Lifecycle)

**File**: `app/api/dependencies.py`
```python
async def get_db() -> AsyncGenerator[AsyncSession, None]:
    async with AsyncSessionLocal() as session:
        try:
            yield session
        finally:
            await session.close()
```

Mỗi request nhận 1 session riêng, tự động đóng khi request kết thúc.

### 2.4. Config

**File**: `app/core/config.py`
```python
class Settings(BaseSettings):
    APP_NAME: str = "Hemis Backend API"
    APP_VERSION: str = "1.0.0"
    API_V1_PREFIX: str = "/api/v1"
    DATABASE_URL: str                    # postgresql+asyncpg://...
    DB_ECHO: bool = False
    SECRET_KEY: str                      # JWT signing key
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60
    BACKEND_CORS_ORIGINS: str = '[\"http://localhost:3000\"]'

    class Config:
        env_file = ".env"
```

---

## 3. ORM Models — Hiện trạng

### 3.1. Module CTĐT — Chỉ có `NamApDung`

**File**: `app/models/chuong_trinh_dao_tao.py`
```python
class NamApDung(Base):
    __tablename__ = "NAM_AP_DUNG"

    id = Column(Integer, primary_key=True, index=True)
    ma_chuong_trinh_dao_tao = Column(String(50), nullable=False)
    ma_chuong_trinh_nam_ap_dung = Column(String(50), nullable=False, unique=True)
    so_tin_chi = Column(Integer, nullable=False)
    tong_hoc_phi = Column(BigInteger)
    nam_ap_dung = Column(Integer, nullable=False)
    chi_tieu_hang_nam = Column(Integer)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())
```

### 3.2. Danh mục — Chỉ có `DMCoQuanChuQuan`

**File**: `app/models/danh_muc.py`
```python
class DMCoQuanChuQuan(Base):
    __tablename__ = "DM__CO_QUAN_CHU_QUAN"

    id = Column(Integer, primary_key=True, index=True)
    ten_co_quan_chu_quan = Column(String(200), nullable=False)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())
```

### 3.3. Model Registry

**File**: `app/models/__init__.py`
```python
from app.models.danh_muc import DMCoQuanChuQuan
from app.models.chuong_trinh_dao_tao import NamApDung
from app.models.identity import NguoiDung

__all__ = ["DMCoQuanChuQuan", "NamApDung", "NguoiDung"]
```

---

## 4. Flow hoạt động — Module CTĐT

> **Lưu ý**: Hiện chỉ có sub-module `NAM_AP_DUNG` được implement đầy đủ.

### 4.1. Flow GET — Lấy danh sách

```
Client GET /api/v1/chuong-trinh-dao-tao/nam-ap-dung
    │
    ▼
[Endpoint] chuong_trinh_dao_tao.py
    │   @router.get("/nam-ap-dung", response_model=List[NamApDung])
    │   async def read_nam_ap_dung(db: AsyncSession = Depends(get_db)):
    │       return await chuong_trinh_dao_tao_service.get_all_nam_ap_dung(db)
    │
    ▼
[Service] chuong_trinh_dao_tao_service.py
    │   async def get_all_nam_ap_dung(self, db: AsyncSession):
    │       result = await db.execute(select(NamApDung))
    │       return result.scalars().all()
    │
    ▼
[SQLAlchemy ORM]  →  SELECT * FROM NAM_AP_DUNG
    │
    ▼
Response: List[NamApDung]  →  JSON array
```

### 4.2. Flow POST — Tạo mới

```
Client POST /api/v1/chuong-trinh-dao-tao/nam-ap-dung
Body: { "ma_chuong_trinh_dao_tao": "CT001", "ma_chuong_trinh_nam_ap_dung": "CT001-2024", ... }
    │
    ▼
[Endpoint]
    │   async def create_nam_ap_dung(obj_in: NamApDungCreate, db = Depends(get_db)):
    │       return await service.create_nam_ap_dung(db, obj_in)
    │
    ▼
[Service]
    │   async def create_nam_ap_dung(self, db, obj_in: NamApDungCreate):
    │       db_obj = NamApDung(**obj_in.model_dump())   # Schema → Model
    │       db.add(db_obj)
    │       await db.commit()
    │       await db.refresh(db_obj)
    │       return db_obj
    │
    ▼
[SQLAlchemy ORM]  →  INSERT INTO NAM_AP_DUNG (...) VALUES (...) RETURNING *
```

### 4.3. Flow PUT — Cập nhật

```
Client PUT /api/v1/chuong-trinh-dao-tao/nam-ap-dung/5
Body: { "so_tin_chi": 150, "nam_ap_dung": 2025 }
    │
    ▼
[Service]
    │   async def update_nam_ap_dung(self, db, id, obj_in: NamApDungUpdate):
    │       db_obj = await self.get_nam_ap_dung_by_id(db, id)    # SELECT WHERE id=5
    │       if not db_obj: return None
    │       update_data = obj_in.model_dump(exclude_unset=True)   # Chỉ lấy field được gửi
    │       for field, value in update_data.items():
    │           setattr(db_obj, field, value)                     # Gán từng field
    │       await db.commit()
    │       return db_obj
    │
    ▼
[SQLAlchemy ORM]  →  UPDATE NAM_AP_DUNG SET so_tin_chi=150, nam_ap_dung=2025 WHERE id=5
```

### 4.4. Flow DELETE — Xóa

```
Client DELETE /api/v1/chuong-trinh-dao-tao/nam-ap-dung/5
    │
    ▼
[Service]
    │   async def delete_nam_ap_dung(self, db, id):
    │       db_obj = await self.get_nam_ap_dung_by_id(db, id)
    │       if not db_obj: return False
    │       await db.delete(db_obj)
    │       await db.commit()
    │       return True
    │
    ▼
[SQLAlchemy ORM]  →  DELETE FROM NAM_AP_DUNG WHERE id=5
    │
    ▼
Response: { "message": "Xóa thành công" }
```

---

## 5. Schema DB mới (từ `db_hemis_stu_new`)

> **Source SQL**: `/home/kali/Development/Projects/db_hemis_stu_new/sql_pg/`

### 5.1. Bảng chính CTĐT (6 bảng)

| # | Bảng | Số cột | File SQL |
|---|---|---|---|
| 1 | `CHUONG_TRINH_DAO_TAO` | 26 | `chuong_trinh_dao_tao/tables/CHUONG_TRINH_DAO_TAO.sql` |
| 2 | `NAM_AP_DUNG` | 9 | `chuong_trinh_dao_tao/tables/NAM_AP_DUNG.sql` |
| 3 | `QUYET_DINH_CAP_PHEP` | 8 | `chuong_trinh_dao_tao/tables/QUYET_DINH_CAP_PHEP.sql` |
| 4 | `THONG_TIN_KIEM_DINH` | 9 | `chuong_trinh_dao_tao/tables/THONG_TIN_KIEM_DINH.sql` |
| 5 | `GIA_HAN_CHUONG_TRINH_DAO_TAO` | 7 | `chuong_trinh_dao_tao/tables/GIA_HAN_CHUONG_TRINH_DAO_TAO.sql` |
| 6 | `NGON_NGU_GIANG_DAY` | 6 | `chuong_trinh_dao_tao/tables/NGON_NGU_GIANG_DAY.sql` |

### 5.2. Bảng chính — `CHUONG_TRINH_DAO_TAO` (chi tiết)

```sql
-- File: db_hemis_stu_new/sql_pg/chuong_trinh_dao_tao/tables/CHUONG_TRINH_DAO_TAO.sql
CREATE TABLE CHUONG_TRINH_DAO_TAO (
    ID SERIAL PRIMARY KEY,
    NGANH_ID VARCHAR(20) NOT NULL,                    -- FK → DM_NGANH
    MA_CHUONG_TRINH VARCHAR(50) NOT NULL UNIQUE,
    TEN VARCHAR(500) NOT NULL,
    TEN_TIENG_ANH VARCHAR(500),
    NAM_TUYEN_SINH INT NOT NULL,
    TEN_CO_SO_DAO_TAO VARCHAR(500),
    LOAI_CHUONG_TRINH_DAO_TAO INT NOT NULL,           -- FK → DM_LOAI_CHUONG_TRINH_DAO_TAO
    HINH_THUC_DAO_TAO VARCHAR(10) NOT NULL,           -- FK → DM_HINH_THUC_DAO_TAO
    LOAI_CHUONG_TRINH_LIEN_KET INT,                   -- FK → DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO
    DIA_DIEM_DAO_TAO VARCHAR(500),
    HOC_CHE_DAO_TAO INT NOT NULL,                     -- FK → DM_HOC_CHE_DAO_TAO
    QUOC_GIA VARCHAR(10),                             -- FK → DM_NUOC
    NGAY_BAN_HANH_CHUAN_DAU_RA DATE NOT NULL,
    TRINH_DO_DAO_TAO INT NOT NULL,                    -- FK → DM_TRINH_DO_DAO_TAO
    THOI_GIAN_DAO_TAO_CHUAN INT NOT NULL,
    CHUAN_DAU_RA TEXT,
    DON_VI_CAP_BANG INT,                              -- FK → DM_DON_VI_CAP_BANG
    LOAI_CHUNG_CHI_DUOC_CHAP_THUAN VARCHAR(500),
    DON_VI_THUC_HIEN VARCHAR(500),
    TRANG_THAI INT,                                   -- FK → DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO
    CHUAN_DAU_RA_NGOAI_NGU TEXT,
    CHUAN_DAU_RA_TIN_HOC TEXT,
    HOC_PHI_TRONG_NUOC BIGINT,
    HOC_PHI_NUOC_NGOAI BIGINT,
    CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UPDATED_AT TIMESTAMP
);
```

### 5.3. Bản đồ FK — CTĐT → Danh mục

| Cột trong CHUONG_TRINH_DAO_TAO | Tham chiếu bảng DM |
|---|---|
| `NGANH_ID` | `DM_NGANH` |
| `LOAI_CHUONG_TRINH_DAO_TAO` | `DM_LOAI_CHUONG_TRINH_DAO_TAO` |
| `HINH_THUC_DAO_TAO` | `DM_HINH_THUC_DAO_TAO` |
| `LOAI_CHUONG_TRINH_LIEN_KET` | `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO` |
| `HOC_CHE_DAO_TAO` | `DM_HOC_CHE_DAO_TAO` |
| `QUOC_GIA` | `DM_NUOC` |
| `TRINH_DO_DAO_TAO` | `DM_TRINH_DO_DAO_TAO` |
| `DON_VI_CAP_BANG` | `DM_DON_VI_CAP_BANG` |
| `TRANG_THAI` | `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO` |

| Cột trong sub-modules | Tham chiếu bảng DM |
|---|---|
| `QUYET_DINH_CAP_PHEP.MA_LOAI_QUYET_DINH` | `DM_LOAI_QUYET_DINH_CTDT` |
| `QUYET_DINH_CAP_PHEP.HINH_THUC_DAO_TAO` | `DM_HINH_THUC_DAO_TAO` |
| `THONG_TIN_KIEM_DINH.MA_TO_CHUC_KIEM_DINH` | `DM_TO_CHUC_KIEM_DINH` |
| `THONG_TIN_KIEM_DINH.MA_KET_QUA_KIEM_DINH` | `DM_KET_QUA_KIEM_DINH` |
| `NGON_NGU_GIANG_DAY.MA_NGON_NGU_GIANG_DAY` | `DM_NGOAI_NGU` |
| `NGON_NGU_GIANG_DAY.MA_KHUNG_NLUC_NNGU_DAU_VAO` | `DM_KHUNG_NANG_LUC_NGOAI_NGU` |

### 5.4. Danh mục liên quan (14 bảng)

| # | Bảng DM | PK Type | Có seed data |
|---|---|---|---|
| 1 | `DM_NGANH` | VARCHAR(20) | ✅ (~1200 rows) |
| 2 | `DM_LOAI_CHUONG_TRINH_DAO_TAO` | INT | ✅ (18 rows) |
| 3 | `DM_HINH_THUC_DAO_TAO` | VARCHAR(500) | ✅ (3 rows) |
| 4 | `DM_LOAI_CHUONG_TRINH_LIEN_KET_DAO_TAO` | INT | ✅ |
| 5 | `DM_HOC_CHE_DAO_TAO` | INT | ✅ |
| 6 | `DM_NUOC` | VARCHAR(10) | ✅ (~200 rows) |
| 7 | `DM_TRINH_DO_DAO_TAO` | INT | ✅ |
| 8 | `DM_DON_VI_CAP_BANG` | INT | ✅ |
| 9 | `DM_TRANG_THAI_CHUONG_TRINH_DAO_TAO` | INT | ✅ (5 rows) |
| 10 | `DM_LOAI_QUYET_DINH_CTDT` | VARCHAR(500) | ✅ (3 rows) |
| 11 | `DM_TO_CHUC_KIEM_DINH` | VARCHAR(50) | ✅ |
| 12 | `DM_KET_QUA_KIEM_DINH` | VARCHAR(2) | ✅ |
| 13 | `DM_NGOAI_NGU` | VARCHAR(10) | ✅ |
| 14 | `DM_KHUNG_NANG_LUC_NGOAI_NGU` | INT | ✅ |

---

## 6. API Endpoints — Hiện trạng

**Base path**: `/api/v1`

### Module CTĐT (chỉ NAM_AP_DUNG)

| Method | Path | Mô tả |
|---|---|---|
| GET | `/chuong-trinh-dao-tao/nam-ap-dung` | Lấy danh sách |
| GET | `/chuong-trinh-dao-tao/nam-ap-dung/{id}` | Lấy chi tiết |
| POST | `/chuong-trinh-dao-tao/nam-ap-dung` | Tạo mới |
| PUT | `/chuong-trinh-dao-tao/nam-ap-dung/{id}` | Cập nhật |
| DELETE | `/chuong-trinh-dao-tao/nam-ap-dung/{id}` | Xóa |

### Danh mục (chỉ DM_CO_QUAN_CHU_QUAN)

| Method | Path | Mô tả |
|---|---|---|
| GET | `/danh-muc/co-quan-chu-quan` | Lấy danh sách |
| GET | `/danh-muc/co-quan-chu-quan/{id}` | Lấy chi tiết |
| POST | `/danh-muc/co-quan-chu-quan` | Tạo mới |
| PUT | `/danh-muc/co-quan-chu-quan/{id}` | Cập nhật |
| DELETE | `/danh-muc/co-quan-chu-quan/{id}` | Xóa |

**Tổng hiện tại**: 10 endpoints (5 CTĐT + 5 DM)

---

## 7. Đặc điểm kỹ thuật nổi bật

### 7.1. ORM-based (không dùng Stored Procedure)
- Tất cả CRUD thực hiện qua SQLAlchemy ORM: `select()`, `db.add()`, `setattr()`, `db.delete()`
- Không dùng raw SQL, không dùng SP
- Business logic nằm trong Python (Service layer), không trong database

### 7.2. snake_case Convention
- Tất cả field names trong API: `ma_chuong_trinh_dao_tao`, `so_tin_chi`
- DB column names: `MA_CHUONG_TRINH_DAO_TAO` (UPPER_SNAKE_CASE trong SQL, SQLAlchemy map sang snake_case)

### 7.3. Pydantic v2 Schema Convention
- **Base**: Các field bắt buộc cho entity
- **Create**: Kế thừa Base, dùng cho POST request
- **Update**: Tất cả field Optional, dùng `exclude_unset=True` khi update
- **Read** (response): Kế thừa Base + id + timestamps + `Config.from_attributes = True`

### 7.4. Security Skeleton
- JWT authentication đã có skeleton trong `core/security.py`
- RBAC đã có skeleton trong `core/rbac.py`
- Chưa kết nối vào API endpoints (chưa có `Depends(get_current_user)`)

### 7.5. Repository Pattern (Placeholder)
- `repositories/base.py` đã tạo nhưng chưa implement
- Hiện tại Service gọi ORM trực tiếp (bypass Repository layer)
- Theo thiết kế, Repository sẽ chứa `BaseRepository[T]` generic CRUD
