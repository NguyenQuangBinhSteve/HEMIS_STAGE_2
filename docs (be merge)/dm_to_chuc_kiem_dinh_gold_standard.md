# Gold Standard Documentation: Module `dm_to_chuc_kiem_dinh`

Tài liệu này định nghĩa cấu trúc chuẩn (Gold Standard) cho các module danh mục. Mọi module danh mục phải tuân thủ nghiêm ngặt các quy tắc về **kiến trúc 5 tầng** và **logic Xóa mềm (Soft Delete)** dưới đây.

---

## 1. Router Implementation
**File Path**: `app/api/v1/endpoints/danh_muc.py`

### Code Snippet
```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession
from typing import List
from app.api.dependencies import get_db
from app.schemas.danh_muc import DMToChucKiemDinh, DMToChucKiemDinhCreate, DMToChucKiemDinhUpdate
from app.services.danh_muc_service import danh_muc_service

router = APIRouter()

@router.get("/to-chuc-kiem-dinh", response_model=List[DMToChucKiemDinh], summary="Lấy danh sách Tổ chức kiểm định")
async def read_to_chuc_kiem_dinh(db: AsyncSession = Depends(get_db)):
    """
    API lấy toàn bộ danh sách.
    Logic: Chỉ trả về các bản ghi có deleted_at IS NULL.
    """
    return await danh_muc_service.get_all_to_chuc_kiem_dinh(db)

@router.get("/to-chuc-kiem-dinh/{id}", response_model=DMToChucKiemDinh, summary="Lấy chi tiết Tổ chức kiểm định")
async def read_to_chuc_kiem_dinh_by_id(id: str, db: AsyncSession = Depends(get_db)):
    """
    API lấy chi tiết theo ID.
    Xử lý: Trả về 404 nếu không tìm thấy hoặc đã bị xóa mềm.
    """
    db_obj = await danh_muc_service.get_to_chuc_kiem_dinh_by_id(db, id)
    if not db_obj:
        raise HTTPException(status_code=404, detail="Không tìm thấy Tổ chức kiểm định")
    return db_obj

@router.post("/to-chuc-kiem-dinh", response_model=DMToChucKiemDinh, summary="Tạo mới Tổ chức kiểm định")
async def create_to_chuc_kiem_dinh(obj_in: DMToChucKiemDinhCreate, db: AsyncSession = Depends(get_db)):
    """
    API tạo mới bản ghi.
    Input: SchemaCreate (id có thể null nếu DB tự sinh).
    """
    return await danh_muc_service.create_to_chuc_kiem_dinh(db, obj_in)

@router.put("/to-chuc-kiem-dinh/{id}", response_model=DMToChucKiemDinh, summary="Cập nhật Tổ chức kiểm định")
async def update_to_chuc_kiem_dinh(id: str, obj_in: DMToChucKiemDinhUpdate, db: AsyncSession = Depends(get_db)):
    """
    API cập nhật bản ghi (Partial Update).
    Xử lý: Chỉ cập nhật các trường được gửi lên (exclude_unset=True).
    """
    db_obj = await danh_muc_service.update_to_chuc_kiem_dinh(db, id, obj_in)
    if not db_obj:
        raise HTTPException(status_code=404, detail="Không tìm thấy Tổ chức kiểm định")
    return db_obj

@router.delete("/to-chuc-kiem-dinh/{id}", summary="Xóa Tổ chức kiểm định")
async def delete_to_chuc_kiem_dinh(id: str, db: AsyncSession = Depends(get_db)):
    """
    API Xóa mềm (Soft Delete).
    Thực tế: Cập nhật trường deleted_at thành thời gian hiện tại thay vì xóa khỏi DB.
    """
    success = await danh_muc_service.delete_to_chuc_kiem_dinh(db, id)
    if not success:
        raise HTTPException(status_code=404, detail="Không tìm thấy Tổ chức kiểm định")
    return {"message": "Xóa thành công"}
```

---

## 2. Model Analysis (Bổ sung Soft Delete)
**File Path**: `app/models/danh_muc.py`

### Code Snippet
```python
from sqlalchemy import Column, Integer, String, DateTime
from sqlalchemy.sql import func
from app.db.base import Base

class DMToChucKiemDinh(Base):
    __tablename__ = "dm_to_chuc_kiem_dinh"
    __table_args__ = {"schema": "core"}

    id = Column(String(500), primary_key=True, index=True)
    ten_to_chuc = Column(String(500), nullable=False)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())
    deleted_at = Column(DateTime, nullable=True) # <-- CỘT QUAN TRỌNG CHO XÓA MỀM
```

---

## 3. Schema Analysis
**File Path**: `app/schemas/danh_muc.py`

### Code Snippet
```python
from pydantic import BaseModel
from datetime import datetime
from typing import Optional

class DMToChucKiemDinhBase(BaseModel):
    ten_to_chuc: str

class DMToChucKiemDinhCreate(DMToChucKiemDinhBase):
    id: Optional[str] = None

class DMToChucKiemDinhUpdate(BaseModel):
    ten_to_chuc: Optional[str] = None

class DMToChucKiemDinh(DMToChucKiemDinhBase):
    id: str
    created_at: Optional[datetime]
    updated_at: Optional[datetime]
    deleted_at: Optional[datetime] = None # Hiển thị thông tin xóa (nếu cần)

    class Config:
        from_attributes = True
```

---

## 4. Service Logic (Logic Xóa mềm)
**File Path**: `app/services/danh_muc_service.py`

### Code Snippet
```python
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy.future import select
from sqlalchemy.sql import func
from typing import List, Optional
from app.models.danh_muc import DMToChucKiemDinh
from app.schemas.danh_muc import DMToChucKiemDinhCreate, DMToChucKiemDinhUpdate

class DanhMucService:
    async def get_all_to_chuc_kiem_dinh(self, db: AsyncSession) -> List[DMToChucKiemDinh]:
        # Filter: Chỉ lấy các bản ghi chưa bị xóa mềm
        result = await db.execute(
            select(DMToChucKiemDinh).where(DMToChucKiemDinh.deleted_at == None)
        )
        return list(result.scalars().all())

    async def get_to_chuc_kiem_dinh_by_id(self, db: AsyncSession, id: str) -> Optional[DMToChucKiemDinh]:
        # Filter: ID khớp và chưa bị xóa mềm
        result = await db.execute(
            select(DMToChucKiemDinh).where(
                DMToChucKiemDinh.id == id,
                DMToChucKiemDinh.deleted_at == None
            )
        )
        return result.scalar_one_or_none()

    async def create_to_chuc_kiem_dinh(self, db: AsyncSession, obj_in: DMToChucKiemDinhCreate) -> DMToChucKiemDinh:
        db_obj = DMToChucKiemDinh(**obj_in.model_dump())
        db.add(db_obj)
        await db.commit()
        await db.refresh(db_obj)
        return db_obj

    async def update_to_chuc_kiem_dinh(self, db: AsyncSession, id: str, obj_in: DMToChucKiemDinhUpdate) -> Optional[DMToChucKiemDinh]:
        db_obj = await self.get_to_chuc_kiem_dinh_by_id(db, id)
        if not db_obj:
            return None
        
        update_data = obj_in.model_dump(exclude_unset=True)
        for field, value in update_data.items():
            setattr(db_obj, field, value)
            
        db.add(db_obj)
        await db.commit()
        await db.refresh(db_obj)
        return db_obj

    async def delete_to_chuc_kiem_dinh(self, db: AsyncSession, id: str) -> bool:
        """
        LOGIC XÓA MỀM:
        Thay vì db.delete(), ta cập nhật deleted_at = func.now()
        """
        db_obj = await self.get_to_chuc_kiem_dinh_by_id(db, id)
        if not db_obj:
            return False
        
        db_obj.deleted_at = func.now() # Cập nhật ngày xóa
        db.add(db_obj)
        await db.commit()
        return True
```

---

## 5. Checkpoints đối soát (Gold Standard Rules)

| Phân loại | Quy tắc bắt buộc |
|---|---|
| **Xóa mềm** | Model phải có `deleted_at`. Service phải dùng `db_obj.deleted_at = func.now()` thay vì `db.delete()`. |
| **Truy vấn** | Tất cả lệnh `select` phải có điều kiện `.where(Model.deleted_at == None)`. |
| **API** | DELETE endpoint trả về 200 OK kèm thông báo thay vì xóa cứng dữ liệu. |
| **Test** | Sau khi gọi DELETE, gọi lại GET detail phải trả về 404 để verify xóa mềm thành công. |

---

## 9. Danh sách danh mục Legacy (Hard Delete)
*Danh sách này chứa các module đã merge nhưng cần được refactor sang Soft Delete theo mẫu trên.*

1. `dm_co_quan_chu_quan`
2. `dm_khung_nang_luc_ngoai_ngu`
3. `dm_ket_qua_kiem_dinh`
4. `dm_vi_tri_lam_viec`
5. `dm_vi_tri_viec_lam`
6. `dm_loai_chuong_trinh_dao_tao`
7. `dm_xa`
8. `dm_xep_hang_q`

---
*Cập nhật ngày: 14/05/2026 - Chuẩn hóa logic Soft Delete toàn hệ thống.*
