Để đảm bảo tính nhất quán giữa bản thiết kế và việc triển khai mã nguồn (React/TypeScript), tài liệu **UI/UX Design System & Style Guide** cho hệ thống HEMIS được cấu trúc theo tiêu chuẩn công nghiệp, tập trung vào khả năng mở rộng và kiểm soát truy cập (RBAC).

Dưới đây là cấu trúc chi tiết của tài liệu:

---

## 1. Tổng quan (Overview)

- **Mục tiêu:** Thiết lập ngôn ngữ thị giác chung, giúp AI Agent và Developer xây dựng giao diện đồng nhất, chuyên nghiệp cho hệ thống quản trị dữ liệu quy mô lớn.
- **Nguyên tắc thiết kế:** Tập trung vào dữ liệu (Data-centric), rõ ràng (Clarity) và bảo mật (Security-first).
    
---

## 2. Kiến trúc thiết kế (Design Architecture)

Hệ thống áp dụng mô hình **Atomic Design** để chia nhỏ các thành phần giao diện:

- **Atoms:** Màu sắc, Typography, Icons, Button gốc.
- **Molecules:** Input group, Search bar, Menu item.
- **Organisms:** Header, Sidebar, Data Table, Form.
- **Templates & Pages:** Bố cục tổng thể của Dashboard, Staging area, Audit log.

| STT | NHÓM              | MÔ TẢ                                                   |
| --- | ----------------- | ------------------------------------------------------- |
| 1   | Atoms             | Màu sắc, Typography, Icons, Button gốc.                 |
| 2   | Molecules         | Input group, Search bar, Menu item.                     |
| 3   | Organisms         | Header, Sidebar, Data Table, Form.                      |
| 4   | Templates & Pages | Bố cục tổng thể của Dashboard, Staging area, Audit log. |

---
## 3. Hệ thống màu sắc (Color Palette)

Hệ thống màu sắc được phân lớp để phục vụ hai mục đích: Nhận diện thương hiệu (Core Brand) và Chỉ thị trạng thái dữ liệu (Semantic/Status).

### 3.1. Màu sắc chủ đạo và Trung tính (Core & Neutral)

Đây là các màu nền tảng dùng cho Layout (Header, Sidebar, Background).

|**STT**|**Tên màu**|**Mã HEX**|**Mô tả**|**Mục đích sử dụng**|
|---|---|---|---|---|
|1|**Primary Blue**|`#094874`|Xanh đậm chuyên nghiệp.|Màu chủ đạo cho Header, Logo, và các nút hành động chính (Primary Action).|
|2|**Secondary Gray**|`#F4F3F8`|Xám nhạt (Cool Gray).|Sử dụng làm màu nền chính (Main Background) để làm nổi bật các khối nội dung.|
|3|**Pure White**|`#FFFFFF`|Trắng tuyệt đối.|Nền của các Card, Table, Form nhập liệu và Sidebar.|
|4|**Text Primary**|`#212529`|Đen xám.|Màu chữ cho tiêu đề và nội dung văn bản chính.|

### 3.2. Màu sắc trạng thái kiểm định (Data Validation States)

Các màu này được thiết lập theo cấp độ "Nhiệt" của dữ liệu: Từ trạng thái tĩnh (Nháp) đến trạng thái chuyển động (Đang duyệt) và trạng thái đóng (Đã duyệt/Core).

|**STT**|**Trạng thái**|**Mã HEX**|**Cảm quan**|**Mô tả mục đích sử dụng**|
|---|---|---|---|---|
|5|**Nháp (Draft)**|`#6C757D`|Xám trung tính|Dữ liệu vừa được khởi tạo bởi Proposer, chưa gửi đi.|
|6|**Chờ Duyệt (QC)**|`#FFC107`|Vàng hổ phách|Dữ liệu đã gửi, nằm trong hàng đợi của Quality Controller.|
|7|**Đang Duyệt (QC)**|`#17A2B8`|Xanh lơ (Info)|QC đã mở hồ sơ và bắt đầu thực hiện đối soát.|
|8|**Đã Duyệt (QC)**|`#91E5A1`|Xanh lá nhạt|QC xác nhận dữ liệu đạt chuẩn, sẵn sàng cho cấp DA.|
|9|**Chờ Duyệt (DA)**|`#FD7E14`|Cam|Dữ liệu quan trọng cần sự phê duyệt cuối cùng từ Data Approver.|
|10|**Đang Duyệt (DA)**|`#6610F2`|Tím Indigo|DA đang kiểm tra các yếu tố pháp lý/nghiệp vụ cao cấp.|
|11|**Đã Duyệt (DA)**|`#28A745`|Xanh lá đậm|**Final State.** Dữ liệu chính thức được đẩy từ Staging sang CORE.|
|12|**Từ chối (Reject)**|`#DC3545`|Đỏ|Dữ liệu bị trả về cho Proposer kèm ghi chú sai sót.|

---

## 4. Thành phần bố cục trang (Page Components)

Để AI Agent có thể code Frontend chính xác, các Component được quy định sử dụng màu sắc từ bảng trên như sau:

- **Header:** Sử dụng `Primary Blue` làm nền, chữ `Pure White`.
- **Sidebar:** Nền `Pure White`, đường kẻ phân cách (Border) sử dụng xám nhạt từ `Secondary Gray`.
- **Main Section:** Nền `Secondary Gray`. Các thẻ chứa nội dung (Cards) dùng `Pure White`.
- **Footer:** Sử dụng `Pure White`, chữ xám nhạt để không làm xao nhãng.
---

## 5. Hệ thống nút bấm (Button System)

Mỗi Button sẽ ánh xạ trực tiếp từ Role và Trạng thái dữ liệu.

|**Loại Button**|**Màu sắc áp dụng**|**Logic áp dụng**|
|---|---|---|
|**Primary Action**|`#094874`|Dùng cho "Thêm mới", "Lưu tạm", "Đăng nhập".|
|**Send/Approve**|`#28A745`|Dùng cho "Gửi duyệt", "Phê duyệt cuối cùng".|
|**Warning/Recall**|`#FD7E14`|Dùng cho "Thu hồi dữ liệu" (Proposer).|
|**Danger/Reject**|`#DC3545`|Dùng cho "Xóa mềm", "Từ chối hồ sơ".|
|**Ghost/Neutral**|`#F4F3F8`|Dùng cho "Hủy bỏ", "Quay lại".|

---

## 6. Chi tiết kỹ thuật (Technical Implementation)

- **Variables:** Khai báo toàn bộ màu sắc trong file `theme.ts` hoặc CSS Variables để AI Agent dễ dàng triệu hồi.
    
    TypeScript
    
    ```
    export const HEMIS_THEME = {
      primary: '#094874',
      bg_main: '#F4F3F8',
      status: {
        draft: '#6C757D',
        qc_pending: '#FFC107',
        da_approved: '#28A745'
      }
    }
    ```
    
- **Type Safety:** Sử dụng enum cho các trạng thái dữ liệu để tránh nhầm lẫn màu sắc khi render Badge/Status Tag.
    

---

## 7. Bảo mật (Security)

- **Visual Concealment:** Dữ liệu ở trạng thái `Nháp` hoặc `Từ chối` chỉ hiển thị với Proposer.
    
- **Audit Consistency:** Màu sắc hiển thị trong Audit Log phải khớp 100% với trạng thái thực tế của bản ghi để tránh hiểu lầm trong quá trình hậu kiểm.
    

---

**Nguồn tham khảo tiêu chuẩn:**

- **Ant Design:** [Enterprise Color System](https://ant.design/docs/spec/colors)
    
- **IBM Carbon Design:** [Color Palettes for Data](https://www.google.com/search?q=https://carbondesignsystem.com/guidelines/color/overview/)
    
- **Tailwind UI:** [Modern Dashboard Palettes](https://tailwindcss.com/docs/customizing-colors)
    

Cấu trúc này đã sẵn sàng để chuyển sang bước thiết kế **High-Fidelity Wireframes**. Bạn có muốn tôi tiến hành soạn thảo Tech Spec cho thành phần **Form Nhập liệu (Payload)** – nơi trực tiếp xử lý các bảng dữ liệu từ Schema của Bộ không?
---

## 4. Thành phần bố cục trang (Page Components)

### 4.1 Header (Thanh đầu trang)

- **Chức năng:** Chứa Logo, Breadcrumbs (Đường dẫn) và User Profile.
    
- **Buttons:**
    
    - `NotificationButton`: Xem thông báo phê duyệt mới (Badge count).
    - `ProfileDropdown`: Chứa các nút "Sửa thông tin", "Đổi mật khẩu", "Đăng xuất".
        

### 4.2 Sidebar (Thanh điều hướng cạnh)

- **Chức năng:** Điều hướng giữa các Module (Cán bộ, Tài chính, Tuyển sinh).
- **Logic RBAC:** Chỉ hiển thị các Menu Item mà Role của User có quyền truy cập.
- **Buttons:** * `NavMenuItem`: Chuyển trang (Active state rõ ràng).
    - `CollapseButton`: Thu gọn Sidebar để tăng không gian xem dữ liệu.

### 4.3 Sidebar & Sidebar Section (Vùng nội dung)

- **Chức năng:** Phân chia các khu vực nghiệp vụ trong một trang.
- **Buttons:**
    
    - `FilterButton`: Mở panel bộ lọc dữ liệu.
    - `ExpandSectionButton`: Đóng/Mở các vùng Payload dữ liệu lớn.
        
### 4.4 Footer (Thanh chân trang)

- **Chức năng:** Hiển thị phiên bản hệ thống (Version), trạng thái kết nối Database.
    
- **Buttons:**
    
    - `SupportButton`: Gửi yêu cầu hỗ trợ kỹ thuật.
    - `BackToTopButton`: Di chuyển nhanh lên đầu trang đối với danh sách dữ liệu dài.
        
---

## 5. Hệ thống nút bấm (Button System)

Đặc tả chi tiết các loại Button để AI Agent sử dụng đúng context.

- **Primary Button:** * _Usage:_ "Thêm mới", "Lưu", "Gửi duyệt".
    
    - _Best Practice:_ Chỉ có tối đa 1 nút Primary trên một khung nhìn chính.
        
- **Secondary/Ghost Button:** * _Usage:_ "Hủy", "Quay lại", "Xuất Template Excel".
- **Danger Button:** * _Usage:_ "Xóa mềm", "Từ chối hồ sơ". Cần đi kèm Modal xác nhận (Confirmation).
- **Action Icon Button:** * _Usage:_ Đặt trong Data Table (Sửa, Xem chi tiết, Thu hồi).
- **Status Button (Toggle):** * _Usage:_ Admin System dùng để Ẩn/Hiện các cột dữ liệu.
    

---

## 6. Chi tiết kỹ thuật (Technical Implementation)

- **Unit:** Sử dụng `rem` thay vì `px` để đảm bảo tính dễ tiếp cận (Accessibility).
- **Grid System:** 12-column grid, sử dụng Tailwind CSS hoặc CSS Grid.
- **State Management:** Tất cả trạng thái Button (Loading, Disabled) phải được quản lý chặt chẽ để tránh Double-click khi gọi API FastAPI.
- **TypeScript Interface:**
```TypeScript
    interface ButtonProps {
      variant: 'primary' | 'secondary' | 'danger' | 'success';
      size: 'sm' | 'md' | 'lg';
      isLoading?: boolean;
      permission?: string; // RBAC: key định danh quyền
      onClick: () => void;
    }
```
---

## 7. Bảo mật (Security)

- **RBAC Enforcement:** Button chỉ được render khi `User.permissions` chứa key tương ứng. Nếu không có quyền, tuyệt đối không render (không chỉ đơn giản là `hidden` bằng CSS).
- **Data Masking:** Giao diện phải hỗ trợ các vùng hiển thị `***` cho dữ liệu nhạy cảm trước khi được phê duyệt hoặc tùy theo Role.
    

---

## Nguồn tham khảo uy tín:

- **Microsoft Fluent UI:** [Buttons & Colors Guidance](https://www.google.com/search?q=https://developer.microsoft.com/en-us/fluentui%23/controls/web/button)
- **Google Material Design:** [Design Systems for Enterprise](https://m3.material.io/)
- **Atlassian Design System:** [Data-heavy UI Patterns](https://atlassian.design/)
    
