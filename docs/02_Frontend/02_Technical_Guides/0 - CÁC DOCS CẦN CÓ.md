Để đảm bảo quá trình phát triển UI/UX đồng nhất và cung cấp "nguyên liệu" chuẩn xác cho AI Agent triển khai Frontend (React/TypeScript), chúng ta cần một bộ tài liệu đặc tả tập trung vào giao diện, luồng tương tác và ánh xạ dữ liệu.

Dưới đây là danh sách các tài liệu cần thiết:

### Danh mục tài liệu phát triển UI/UX và Frontend

| **STT** | **Tên tài liệu**                              | **Đối tượng sử dụng**            | **Mục tiêu chính**                                                                       |
| ------- | --------------------------------------------- | -------------------------------- | ---------------------------------------------------------------------------------------- |
| 1       | **UI/UX Design System & Style Guide**         | Designer, FE Developer, AI Agent | Đảm bảo tính nhất quán về visual (màu sắc, typography, component).                       |
| 2       | **Information Architecture (IA) & Sitemap**   | Product Owner, Designer          | Cấu trúc phân cấp thông tin và sơ đồ tổ chức các trang.                                  |
| 3       | **High-Fidelity Wireframes & Prototypes**     | Stakeholders, FE Developer       | Minh họa chi tiết giao diện và các tương tác vật lý trên màn hình.                       |
| 4       | **UI-API Data Mapping Specification**         | FE & BE Developer, AI Agent      | Ánh xạ các trường dữ liệu trên UI với Schema của FastAPI.                                |
| 5       | **RBAC Frontend Enforcement Manifest**        | Security Engineer, FE Developer  | Đặc tả việc ẩn/hiện và chặn truy cập dựa trên Role tại client-side.                      |
| 6       | **State Management & Component Architecture** | FE Lead, AI Agent                | Định nghĩa cấu trúc Folder, quản lý Global State (Zustand/Redux) và Reusable Components. |

---

### Chi tiết nội dung các tài liệu

#### 1. UI/UX Design System & Style Guide

- **Tổng quan:** Bộ quy chuẩn ngôn ngữ thiết kế dùng chung cho toàn bộ hệ thống HEMIS.
- **Kiến trúc:** Dựa trên mô hình Atomic Design (Atoms -> Molecules -> Organisms).
- **Chi tiết kỹ thuật:**
    
    - **Color Palette:** Định nghĩa mã HEX cho Primary, Secondary, Success, Warning, Danger (phải tuân thủ chuẩn WCAG về độ tương phản).
    - **Typography:** Scale font (Heading, Body, Caption) sử dụng đơn vị `rem` để hỗ trợ accessibility.
    - **Component Library:** Đặc tả các UI Components (Button, Input, Table, Modal, Toast) kèm theo các trạng thái (Hover, Active, Disabled, Loading).

- **Bảo mật:** Không áp dụng trực tiếp, nhưng đảm bảo các thông báo lỗi không tiết lộ thông tin nhạy cảm của server.
#### 2. Information Architecture (IA) & Sitemap

- **Tổng quan:** Bản đồ cấu trúc của ứng dụng, xác định vị trí của từng Module (Tài chính, Cán bộ, Tuyển sinh).
- **Kiến trúc:** Phân cấp theo dạng cây (Tree Structure).
- **Chi tiết kỹ thuật:** Định nghĩa rõ ràng URL chuẩn (Slug) cho từng trang (ví dụ: `/dashboard`, `/module/finance/staging`, `/admin/users`).
- **Bảo mật:** Xác định các "Private Routes" yêu cầu Authentication và Authorization trước khi render nội dung.

#### 3. High-Fidelity Wireframes & Prototypes

- **Tổng quan:** Bản thiết kế chi tiết cuối cùng trước khi lập trình.
- **Kiến trúc:** Thiết kế Responsive (Desktop-first cho quản trị, Mobile-friendly cho xem báo cáo).
- **Chi tiết kỹ thuật:**
    
    - Mô tả các hiệu ứng chuyển cảnh (Transitions).
    - Đặc tả Validation cho từng trường nhập liệu (ví dụ: Mã Module tối đa 20 ký tự, không chứa ký tự đặc biệt).
        
- **Bảo mật:** Thiết kế các vùng che khuất dữ liệu (Data Masking) cho các trường nhạy cảm nếu người dùng không đủ quyền xem chi tiết.
    

#### 4. UI-API Data Mapping Specification (Quan trọng cho AI Agent)

- **Tổng quan:** Tài liệu trung gian kết nối giao diện với Backend.
- **Kiến trúc:** Bảng ánh xạ giữa UI Field ID và JSON Key từ FastAPI.
- **Chi tiết kỹ thuật:**
    
    - **Payload Mapping:** Ví dụ trang "Thêm dữ liệu" có trường "Tên Module" tương ứng với field `module_name` trong Pydantic Model.
        
    - **Data Transformation:** Quy định cách hiển thị dữ liệu thô (ví dụ: `2026-04-22` sang `22/04/2026`).
        
- **Bảo mật:** Định nghĩa các trường chỉ đọc (Read-only) từ phía client để tránh việc người dùng cố gắng chỉnh sửa qua Console.
    

#### 5. RBAC Frontend Enforcement Manifest

- **Tổng quan:** Tài liệu hướng dẫn AI Agent cách xử lý giao diện theo quyền.
- **Kiến trúc:** Dựa trên Permission Matrix (Role x Module x Action).
- **Chi tiết kỹ thuật:**
    
    - **Component-level Security:** Nếu Role là `Viewer`, nút "Sửa/Xóa" phải được thêm thuộc tính `disabled` hoặc không render.
    - **Field-level Security:** Dựa trên cấu hình từ Admin System để ẩn/hiện các cột trong bảng dữ liệu.
        
- **Bảo mật:** Khẳng định nguyên tắc: "FE Security chỉ là để cải thiện UX, Validation cuối cùng phải nằm ở Backend".
    

#### 6. State Management & Component Architecture

- **Tổng quan:** Bản thiết kế kỹ thuật cho mã nguồn Frontend.
- **Kiến trúc:** React Functional Components với Hooks. Sử dụng Pattern `Container-Presenter` hoặc `Custom Hooks` để tách biệt Logic và UI.
- **Chi tiết kỹ thuật:**
    
    - **Global State:** Quản lý Auth Token, User Profile và Current Module Scope.
    - **API Layer:** Cấu trúc Axios Interceptors để xử lý Refresh Token và bắt lỗi 401/403 tập trung.
        
- **Bảo mật:** Xử lý lưu trữ Token an toàn (tránh LocalStorage nếu có thể, ưu tiên HttpOnly Cookie hoặc Memory State kết hợp Silent Refresh).
    

---

### Nguồn tham khảo uy tín về viết tài liệu kỹ thuật:

- **Google:** [Technical Writing Courses for Engineers](https://developers.google.com/tech-writing) - Tiêu chuẩn về sự súc tích và chính xác.
- **Microsoft:** [Microsoft Writing Style Guide](https://learn.microsoft.com/en-us/style-guide/welcome/) - Quy chuẩn về thuật ngữ và cấu trúc tài liệu.
- **Netflix Tech Blog:** [Evolution of Design Systems](https://netflixtechblog.com/) - Cách tiếp cận hiện đại về UI/UX ở quy mô lớn.