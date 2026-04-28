# TÀI LIỆU ĐẶC TẢ KỸ THUẬT GIAO DIỆN (UI/UX TECHNICAL SPECIFICATION)

---

## 1. TỔNG QUAN KIẾN TRÚC UI (UI ARCHITECTURE OVERVIEW)

Hệ thống được thiết kế theo kiến trúc Dashboard hiện đại, tập trung vào khả năng quản trị dữ liệu quy mô lớn (Data-heavy) với các đặc tính kỹ thuật sau:

- Main Template: Training Program Management - Main Source Using (DO NOT DELETE)
* **Layout Strategy:** Sử dụng mô hình **Fixed Sidebar** (Cố định trái) kết hợp với **Flexible Main Content** (Nội dung chính co giãn). Vùng nội dung chính áp dụng kỹ thuật **Isolated Scrolling** để quản lý bảng dữ liệu 26 cột.
* **Styling Framework:** Tailwind CSS (Utility-first).
* **Visual Design System:** * **Phong cách:** Soft Glassmorphism (Hiệu ứng kính mờ).
    * **Màu chủ đạo (Primary):** `#094874` (Deep Blue).
    * **Nền hệ thống:** `#F4F3F8` (Light Gray).
    * **Typography:** Public Sans / Google Sans (Sans-serif).

---

## 2. CHI TIẾT THÀNH PHẦN (COMPONENT BREAKDOWN)

### 2.1. Sidebar Component (Khối Điều hướng)

Thành phần cố định bên trái màn hình, quản lý nhận diện thương hiệu và điều hướng cấp cao.

| Thành phần con | Thuộc tính kỹ thuật (Tailwind/CSS) | Đặc tả nội dung |
| :--- | :--- | :--- |
| **SidebarBrandArea** | `flex items-center gap-4 p-6` | Chứa Logo NTTU (w-16) và cụm text: H1 "HEMIS" (bold, text-2xl), H2 "SYSTEM" (light, text-lg). |
| **SidebarNav** | `flex-1 py-4` | Danh sách bọc ngoài các mục menu. |
| **SidebarItem** | `flex items-center gap-3 px-6 py-3` | 06 mục menu nghiệp vụ. Mục Active sử dụng background highlight màu Primary với độ trong suốt thấp. |

### 2.2. Header Component (Khối Tiêu đề & Công cụ)

Thành phần nằm ngang phía trên cùng của vùng nội dung.

| Thành phần con | Thuộc tính kỹ thuật (Tailwind/CSS) | Đặc tả nội dung |
| :--- | :--- | :--- |
| **HeaderContainer** | `sticky top-0 z-[100] bg-white/90 backdrop-blur-md` | Thanh tiêu đề cố định, đảm bảo z-index cao nhất để đè lên các thành phần khi cuộn. |
| **Breadcrumbs** | `text-sm text-on-surface-variant` | Hiển thị phân cấp: Hệ thống > Đào tạo > Chương trình đào tạo. |
| **HeaderSearch** | `relative w-96` | Thanh tìm kiếm input field tích hợp icon định vị tuyệt đối. |
| **UserProfile** | `flex items-center gap-3` | Hiển thị Avatar tròn (w-8 h-8) và Text: "Proposer (Người Thêm Dữ liệu)". |

### 2.3. MainContent - Control & Toolbar (Khối Tác vụ)

Vùng chức năng trung gian nằm giữa Header và Bảng dữ liệu.

| Thành phần con | Thuộc tính kỹ thuật (Tailwind/CSS) | Đặc tả nội dung |
| :--- | :--- | :--- |
| **Toolbar** | `flex items-center justify-between py-4` | Nền trong suốt (Transparent), không shadow. |
| **PrimaryActions** | `flex gap-2` | Chứa các nút "Thêm mới", "Nhập Excel". Màu nền `#094874`, text trắng, bo góc `rounded-xl`. |
| **SecondaryActions** | `flex gap-2` | Chứa các nút "Bộ lọc", "Xuất Excel", "Cài đặt cột". Border màu outline-variant. |

### 2.4. MainContent - TableBoard (Khối Bảng Dữ liệu)

Thành phần cốt lõi hiển thị dữ liệu chi tiết, áp dụng cơ chế khóa cột và hàng.

| Thành phần con | Thuộc tính kỹ thuật (Tailwind/CSS) | Đặc tả nội dung |
| :--- | :--- | :--- |
| **TableBoardContainer** | `relative overflow-auto max-h-[calc(100vh-400px)]` | Vùng chứa có khả năng cuộn nội bộ, không làm giãn layout trang. |
| **DataTable** | `w-full min-w-max border-collapse` | Bảng HTML thô với 26 cột dữ liệu chuyên ngành. |
| **TableHeader (thead)** | `sticky top-0 z-30 bg-white` | Tiêu đề bảng cố định khi cuộn dọc. |
| **FixedLeftCols** | `sticky left-0 z-40 bg-white` | Nhóm cột cố định bên trái: Checkbox, STT, MÃ NGÀNH ĐÀO TẠO. |
| **FixedRightCol** | `sticky right-0 z-40 bg-white` | Cột cố định bên phải: TRẠNG THÁI DỮ LIỆU. |
| **PaginationBar** | `flex items-center justify-between p-4` | Chứa PageSizeSelector (min-w-24) và bộ nút chuyển trang. |

### 2.5. MainContent - Footer Mini Dashboard (Khối Thống kê)

Vùng tóm tắt trạng thái nằm cuối cùng của vùng nội dung.

| Thành phần con  | Thuộc tính kỹ thuật (Tailwind/CSS)          | Đặc tả nội dung                                                          |
| :-------------- | :------------------------------------------ | :----------------------------------------------------------------------- |
| **StatsGrid**   | `grid grid-cols-4 gap-4 mt-auto`            | Bố cục 4 cột cố định cho 4 trạng thái dữ liệu.                           |
| **StatsCard**   | `bg-white border rounded-lg p-4 flex gap-4` | Thẻ tóm tắt bao gồm: StatsIcon (vòng tròn màu) và StatsLabel/StatsCount. |
| **StatsLabels** | `Nháp, Chờ duyệt, Đang duyệt, Đã duyệt`     | 04 nhãn trạng thái nghiệp vụ cố định.                                    |

---

Tài liệu này được trích xuất trực tiếp từ cấu trúc CSS+HTML `Main Template: Training Program Management - Main Source Using (DO NOT DELETE)`, đảm bảo tính đồng bộ tuyệt đối về mặt kỹ thuật cho các yêu cầu phát triển sau này