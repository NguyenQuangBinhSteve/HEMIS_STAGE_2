Dưới đây là phần tiếp theo của tài liệu **Information Architecture (IA) & Sitemap**, tập trung vào các phân hệ tổng quan (Dashboard), quản trị bảo mật (Phân quyền) và luồng xác thực (Login) dựa trên mô hình RBAC của hệ thống HEMIS V2.

---
## I. SITEMAP VÀ ĐƯỜNG DẪN CHI TIẾT THEO VAI TRÒ DASHBOARD

Chào bạn, tôi đã hiểu ý bạn. Việc trình bày dưới dạng danh sách (list) như mẫu bạn đưa sẽ giúp tài liệu gọn gàng, trực quan và dễ dàng copy/paste hơn rất nhiều so với dùng bảng.

Dưới đây là bản làm lại hoàn chỉnh cho **Phần I**, kết hợp cả phân hệ Chương Trình Đào Tạo và phân hệ Admin Security theo đúng chuẩn định dạng mà bạn yêu cầu:

---

## I. SITEMAP VÀ ĐƯỜNG DẪN CHI TIẾT THEO VAI TRÒ DASHBOARD

**Phân hệ:** Chương Trình Đào Tạo (Module CTĐT) **Mục tiêu:** Định nghĩa URL chuẩn (Slug) duy nhất cho từng Sub-module. Giao diện (Tabs trạng thái, Nút chức năng) sẽ tự động thích ứng (Render) dựa trên Role của người dùng (Proposer, QC, DA, Viewer) truy cập vào đường dẫn đó.

**Cấu trúc URL chuẩn:** `/chuong-trinh-dao-tao/...`

- **`[Page]` Thông tin Chương Trình Đào Tạo** (`/chuong-trinh-dao-tao/thong-tin-chung`)
    - _Chức năng:_ Quản lý thông tin cốt lõi của CTĐT. Render UI theo phân quyền: Thêm/Sửa/Xóa mềm/Import/Xuất mẫu (Proposer); Xác nhận/Từ chối (QC, DA).
- **`[Page]` Năm áp dụng** (`/chuong-trinh-dao-tao/nam-ap-dung`)
    - _Chức năng:_ Quản lý thời gian áp dụng của CTĐT. Giao diện thay đổi theo RBAC.
- **`[Page]` Quyết định cấp phép** (`/chuong-trinh-dao-tao/quyet-dinh-cap-phep`)
    - _Chức năng:_ Quản lý các văn bản, quyết định cấp phép liên quan đến CTĐT. Giao diện thay đổi theo RBAC.
- **`[Page]` Thông tin kiểm định** (`/chuong-trinh-dao-tao/thong-tin-kiem-dinh`)
    - _Chức năng:_ Quản lý thông tin chất lượng kiểm định. Giao diện thay đổi theo RBAC.
- **`[Page]` Gia hạn chương trình đào tạo** (`/chuong-trinh-dao-tao/gia-han`)
    - _Chức năng:_ Quản lý các mốc thời gian và hồ sơ gia hạn. Giao diện thay đổi theo RBAC.
- **`[Page]` Ngôn ngữ giảng dạy** (`/chuong-trinh-dao-tao/ngon-ngu-giang-day`)
    - _Chức năng:_ Quản lý ngôn ngữ áp dụng cho từng CTĐT. Giao diện thay đổi theo RBAC.

### BẢNG ĐẶC TẢ CHI TIẾT SITEMAP MODULE CHƯƠNG TRÌNH ĐÀO TẠO

|STT|Tên Dashboard (Sub-module)|Đường dẫn chuẩn (URL Slug)|Chức năng & Dữ liệu hiển thị (Thích ứng theo Role)|
|:-:|:--|:--|:--|
|**1**|**Thông tin Chương Trình Đào Tạo**|`/chuong-trinh-dao-tao/thong-tin-chung`|Quản lý thông tin cốt lõi của CTĐT. Render UI theo phân quyền: Thêm/Sửa/Xóa mềm/Import/Xuất mẫu (Proposer); Xác nhận/Từ chối (QC, DA).|
|**2**|**Năm áp dụng**|`/chuong-trinh-dao-tao/nam-ap-dung`|Quản lý thời gian áp dụng của CTĐT. Giao diện thay đổi theo RBAC.|
|**3**|**Quyết định cấp phép**|`/chuong-trinh-dao-tao/quyet-dinh-cap-phep`|Quản lý các văn bản, quyết định cấp phép liên quan đến CTĐT. Giao diện thay đổi theo RBAC.|
|**4**|**Thông tin kiểm định**|`/chuong-trinh-dao-tao/thong-tin-kiem-dinh`|Quản lý thông tin chất lượng kiểm định. Giao diện thay đổi theo RBAC.|
|**5**|**Gia hạn chương trình đào tạo**|`/chuong-trinh-dao-tao/gia-han`|Quản lý các mốc thời gian và hồ sơ gia hạn. Giao diện thay đổi theo RBAC.|
|**6**|**Ngôn ngữ giảng dạy**|`/chuong-trinh-dao-tao/ngon-ngu-giang-day`|Quản lý ngôn ngữ áp dụng cho từng CTĐT. Giao diện thay đổi theo RBAC.|

_(**Lưu ý kỹ thuật:** Tất cả người dùng có quyền truy cập module này đều dùng chung 1 URL duy nhất. Frontend sẽ đọc Permission Matrix (Role x Module x Action) để quyết định Component nào được phép hiển thị)._

---
## II. SITEMAP VÀ ĐƯỜNG DẪN PHÂN QUYỀN (ROLE MANAGEMENT)

**Phân hệ:** Quản lý Định danh & Bảo mật **Vai trò phụ trách:** Admin Security (và Break-Glass trong trường hợp khẩn cấp). **Mục tiêu:** Cấp phát tài khoản, thiết lập RBAC, khóa tài khoản nhằm đảm bảo an ninh hệ thống. Mọi thao tác đều được ghi vào Audit Log.

**Cấu trúc URL chuẩn:** `/quan-ly-tai-khoan/...`

- **`[Page]` Quản lý danh sách tài khoản** (`/quan-ly-tai-khoan`)
    - _Chức năng:_ Hiển thị toàn bộ User trên hệ thống, phân lọc theo Role (Proposer, QC, DA...) và Trạng thái (Active/Inactive/Locked).
- **`[Page]` Khởi tạo tài khoản mới** (`/quan-ly-tai-khoan/them-moi`)
    - _Chức năng:_ Form điền thông tin định danh nhân sự (Tên, Email, SĐT). Trạng thái tài khoản sau khi tạo mặc định là "No Role".
- **`[Page]` Phân quyền người dùng** (`/quan-ly-tai-khoan/phan-quyen`)
    - _Chức năng:_ Gán hoặc thay đổi vai trò (Ví dụ: Từ No Role thành Proposer). Tự động kích hoạt trạng thái "Active" khi cấp Role thành công.
- **`[Page]` Cấp lại mật khẩu thủ công** (`/quan-ly-tai-khoan/cap-mat-khau`)
    - _Chức năng:_ Thiết lập lại mật khẩu cho Group User trong trường hợp họ không thể tự khôi phục. Mật khẩu mới được mã hóa Hash đẩy vào Identity DB.
- **`[Page]` Khóa tài khoản / Đình chỉ** (`/quan-ly-tai-khoan/khoa-tai-khoan`)
    - _Chức năng:_ Chuyển trạng thái tài khoản thành "LOCKED" ngay lập tức để chặn quyền truy cập khi có rủi ro bảo mật.
#### BẢNG ĐẶC TẢ CHI TIẾT SITEMAP PHÂN QUYỀN

**Phân hệ:** Quản lý Định danh & Bảo mật (Identity & Security Management) **Vai trò phụ trách:** Admin Security (và Admin Break-glass trong trường hợp khẩn cấp). **Mục tiêu:** Cấp phát tài khoản, thiết lập phân quyền RBAC (Role-Based Access Control) và khóa tài khoản nhằm đảm bảo an ninh hệ thống. Mọi thao tác đều được hệ thống tự động ghi nhận vào Audit Log DB.

**Cấu trúc URL chuẩn:** `/admin-security/quan-ly-tai-khoan/[action]`

|  STT  | Tên Trang (Page)                | Đường dẫn chuẩn (URL Slug)          | Chức năng nghiệp vụ chính                                                                                                                                 |
| :---: | :------------------------------ | :---------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Quản lý danh sách tài khoản** | `/quan-ly-tai-khoan`                | Hiển thị toàn bộ User trên hệ thống, thực hiện phân lọc theo Role (Proposer, QC, DA, Viewer...) và Trạng thái (Active, Inactive, Locked).                 |
| **2** | **Khởi tạo tài khoản mới**      | `/quan-ly-tai-khoan/them-moi`       | Cung cấp form điền thông tin định danh nhân sự (Họ tên, Email, SĐT). Tài khoản sau khi được khởi tạo thành công sẽ mang trạng thái mặc định là "No Role". |
| **3** | **Phân quyền người dùng**       | `/quan-ly-tai-khoan/phan-quyen`     | Gán hoặc thay đổi vai trò (Ví dụ: Từ No Role thành Proposer, QC hoặc DA). Hệ thống tự động kích hoạt trạng thái "Active" khi cấp Role thành công.         |
| **4** | **Cấp lại mật khẩu thủ công**   | `/quan-ly-tai-khoan/cap-mat-khau`   | Cấp lại mật khẩu mới cho Group User trong trường hợp họ không thể tự khôi phục. Mật khẩu mới bắt buộc được mã hóa Hash trước khi đẩy vào Identity DB.     |
| **5** | **Khóa tài khoản / Đình chỉ**   | `/quan-ly-tai-khoan/khoa-tai-khoan` | Đổi trạng thái tài khoản thành "LOCKED" ngay lập tức để chặn quyền truy cập của User hoặc Admin khác khi phát hiện rủi ro bảo mật.                        |

_(**Lưu ý bảo mật:** Theo nguyên tắc Phân tách trách nhiệm - SoD, vai trò **Admin System** hoàn toàn bị chặn và không có quyền truy cập vào các đường dẫn cấu hình bảo mật này)._

---

## III. SITEMAP VÀ ĐƯỜNG DẪN LOGIN (AUTHENTICATION)

**Phân hệ:** Xác thực danh tính & Quản lý thông tin cá nhân (Auth & Personal Info) **Vai trò phụ trách:** Tất cả người dùng (ALL - Group Admin & Group User). **Mục tiêu:** Cửa ngõ bảo mật vào hệ thống, tự phục vụ khôi phục mật khẩu (Self-service password reset) và thiết lập hồ sơ cá nhân.

**Cấu trúc URL chuẩn:** `/auth/...` và `/user/cai-dat-tai-khoan`

### 1. Luồng Xác thực cốt lõi (Authentication Flow)

- **`[Page]` Đăng nhập** (`/dang-nhap`)
    - _Chức năng:_ Form nhập `Username` và `Password`. Hệ thống định tuyến người dùng về đúng Dashboard dựa trên Role sau khi đối soát thành công với Identity DB.
- **`[Page]` Yêu cầu Quên mật khẩu** (`/quen-mat-khau`)
    - _Chức năng:_ Nhập Email/Username để yêu cầu cấp lại quyền truy cập.
- **`[Page]` Xác thực mã OTP** (`/xac-thuc-otp`)
    - _Chức năng:_ Giao diện nhập mã OTP 6 số được gửi tự động qua hệ thống Email bên thứ 3.
- **`[Page]` Khôi phục / Thiết lập mật khẩu mới** (`/dat-lai-mat-khau`)
    - _Chức năng:_ Nhập mật khẩu mới và xác nhận mật khẩu (yêu cầu độ phức tạp bảo mật: tối thiểu 8 ký tự, chữ, số, ký tự đặc biệt).

### 2. Luồng Cài đặt tài khoản cá nhân (Personal Settings Flow)

Sau khi Đăng nhập thành công, bất kỳ người dùng nào cũng có quyền truy cập trang Cài đặt tài khoản của chính mình.

- **`[Page]` Cài đặt tài khoản chung** (`/cai-dat-tai-khoan`)
    - _Tab:_ **Sửa thông tin cá nhân** (`/cai-dat-tai-khoan/thong-tin`)
        - _Chức năng:_ Cập nhật Avatar (Tối đa 2MB), Họ tên, Số điện thoại và Email liên hệ. Dữ liệu mới ghi đè trực tiếp xuống Identity DB.
    - _Tab:_ **Đổi mật khẩu** (`/cai-dat-tai-khoan/doi-mat-khau`)
        - _Chức năng:_ Nhập mật khẩu cũ để xác thực, sau đó thiết lập mật khẩu mới. Bắt buộc ghi nhận log thay đổi bảo mật.
#### BẢNG ĐẶC TẢ CHI TIẾT SITEMAP XÁC THỰC & QUẢN LÝ THÔNG TIN

**Phân hệ:** Xác thực danh tính & Quản lý thông tin cá nhân (Auth & Personal Info) **Vai trò phụ trách:** Tất cả người dùng (ALL - Group Admin & Group User). **Mục tiêu:** Cửa ngõ bảo mật vào hệ thống, cung cấp các luồng chức năng đăng nhập, tự phục vụ khôi phục mật khẩu (Self-service password reset) và thiết lập hồ sơ cá nhân.

**Cấu trúc URL chuẩn:** `/auth/[action]` và `/user/cai-dat-tai-khoan/[action]`

|  STT  | Tên Trang (Page)                       | Đường dẫn chuẩn (URL Slug)        | Chức năng nghiệp vụ chính                                                                                                                                 |
| :---: | :------------------------------------- | :-------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Đăng nhập**                          | `/dang-nhap`                      | Form nhập `Username` và `Password`. Hệ thống định tuyến người dùng về đúng Dashboard tương ứng dựa trên Role sau khi đối soát thành công với Identity DB. |
| **2** | **Yêu cầu Quên mật khẩu**              | `/quen-mat-khau`                  | Cung cấp giao diện nhập Email/Username để người dùng yêu cầu khôi phục lại quyền truy cập.                                                                |
| **3** | **Xác thực mã OTP**                    | `/xac-thuc-otp`                   | Giao diện yêu cầu nhập mã OTP (6 số) được hệ thống tự động gửi qua dịch vụ Email bên thứ 3 nhằm xác minh danh tính.                                       |
| **4** | **Khôi phục / Thiết lập mật khẩu mới** | `/dat-lai-mat-khau`               | Form nhập mật khẩu mới và xác nhận (bắt buộc tuân thủ độ phức tạp bảo mật: tối thiểu 8 ký tự, bao gồm chữ hoa, chữ thường, số, ký tự đặc biệt).           |
| **5** | **Cài đặt tài khoản chung**            | `/cai-dat-tai-khoan`              | Trang tổng hợp (Parent Layout) chứa các tab cài đặt cá nhân, chỉ truy cập được sau khi người dùng đăng nhập thành công.                                   |
| **6** | **Sửa thông tin cá nhân**              | `/cai-dat-tai-khoan/thong-tin`    | Cập nhật thông tin định danh: Avatar (Tối đa 2MB), Họ tên, Số điện thoại và Email. Dữ liệu mới ghi đè trực tiếp xuống Identity DB.                        |
| **7** | **Đổi mật khẩu cá nhân**               | `/cai-dat-tai-khoan/doi-mat-khau` | Yêu cầu xác thực mật khẩu cũ trước khi thiết lập mật khẩu mới. Quá trình cập nhật sử dụng mã hóa Hash để lưu vào DB.                                      |

_(**Lưu ý bảo mật:** Mọi thao tác từ luồng Xác thực danh tính (Login, Cấp lại mật khẩu) đến luồng Cài đặt tài khoản (Đổi thông tin, Đổi mật khẩu) đều bắt buộc phải được hệ thống tự động ghi nhận vào kho dữ liệu Audit Log DB nhằm đảm bảo khả năng truy vết và hậu kiểm an ninh)._