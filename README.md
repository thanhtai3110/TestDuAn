JobConnect — Hướng dẫn cài đặt và kiểm thử
Hướng dẫn dành cho giảng viên, thành viên nhóm và người kiểm thử sử dụng Windows + VS Code để chạy dự án từ mã nguồn.
1. Thông tin dự án
Thành phần	Công nghệ
Giao diện	HTML, CSS, JavaScript thuần
Máy chủ	Python — FastAPI
Cơ sở dữ liệu	MySQL
Website	http://127.0.0.1:8000
Swagger API	http://127.0.0.1:8000/docs


Lưu ý: Đây là bản Frontend đã kết nối Backend bằng frontend/api-bridge.js. FastAPI phục vụ cả giao diện và API trên cùng cổng 8000. Không mở index.html bằng cách nhấp đúp hoặc Live Server khi kiểm thử bản tích hợp; không cần XAMPP, PHP hay file .bat.

2. Chuẩn bị
Cần cài Python 3, MySQL Server và VS Code. Bật dịch vụ MySQL trước khi chạy dự án.

Cấu trúc thư mục cần có (hai thư mục frontend và Backend nằm cùng cấp):
JobConnect/
├── README.md
├── frontend/
│   ├── index.html
│   ├── app.js
│   ├── portal.js
│   ├── api-bridge.js
│   ├── style.css
│   ├── theme.js
│   └── images/                  # Nếu có ảnh giao diện
└── Backend/
    ├── main.py
    ├── config.py
    ├── database.py
    ├── models.py
    ├── schemas.py
    ├── requirements.txt
    ├── .env.example
    ├── init_db.py
    ├── seed_demo.py
    ├── demo_jobs.json
    ├── password_reset.py
    └── setup_password_reset.py
Mở thư mục JobConnect bằng VS Code, chọn Terminal → New Terminal. Các lệnh dưới đây dùng PowerShell trừ khi được ghi rõ là SQL.
3. Cài đặt lần đầu — thực hiện theo thứ tự
Bước 1 — Vào thư mục Backend
Trong Terminal tại thư mục gốc JobConnect:
cd Backend
Nếu Terminal đã ở Backend thì bỏ qua lệnh này.

Bước 2 — Tạo môi trường Python và cài thư viện
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Chỉ cần làm một lần trên mỗi máy (trừ khi tạo lại môi trường hoặc cập nhật thư viện).
Bước 3 — Tạo database
Mở MySQL Workbench hoặc MySQL Command Line Client. Chạy câu lệnh SQL sau trong MySQL, không phải PowerShell:
CREATE DATABASE IF NOT EXISTS job_portal;
Bước 4 — Cấu hình kết nối MySQL
Trong PowerShell đang ở thư mục Backend, nếu chưa có file .env:
Copy-Item .env.example .env
Mở file Backend/.env, thay mật khẩu mẫu bằng mật khẩu MySQL trên máy người kiểm thử:
DATABASE_URL=mysql+pymysql://root:MAT_KHAU_MYSQL@127.0.0.1:3306/job_portal
JOB_SECRET_KEY=THAY_BANG_CHUOI_BI_MAT_DU_DAI
- Nếu MySQL root không có mật khẩu, sử dụng mysql+pymysql://root@127.0.0.1:3306/job_portal.
- Nếu mật khẩu có các ký tự đặc biệt (@, #, %, …), cần URL-encode phần mật khẩu.
- Không chạy các dòng trong .env ở Terminal; không commit .env lên GitHub.
- Bản code hiện có khóa JWT mặc định trong Backend/config.py; chỉ dùng cho môi trường thử nghiệm, không đưa cấu hình mặc định này lên hệ thống công khai.
Kiểm tra kết nối — chạy trong PowerShell:
.\.venv\Scripts\python.exe -c "from database import engine; c=engine.connect(); print('KET NOI MYSQL THANH CONG'); c.close()"
Nếu in ra KET NOI MYSQL THANH CONG, chuyển sang bước tiếp theo.
Bước 5 — Tạo bảng và nạp dữ liệu mẫu
Chạy lần lượt trong PowerShell tại Backend:

5.1. Tạo các bảng cần thiết:
.\.venv\Scripts\python.exe init_db.py
5.2. Tạo bảng phục vụ đặt lại mật khẩu:
.\.venv\Scripts\python.exe setup_password_reset.py
5.3. Nạp tài khoản và việc làm mẫu — chỉ với database kiểm thử mới:
.\.venv\Scripts\python.exe seed_demo.py
Chú ý: Không dùng seed_demo.py tùy tiện trên database thật. init_db.py không tự sửa tất cả cấu trúc bảng đã tồn tại.

4. Chạy website
Trong PowerShell đang ở thư mục Backend, nhập:
.\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
Sau khi máy chủ chạy, mở:
Mục đích	Địa chỉ
Website JobConnect	http://127.0.0.1:8000
Xem / thử API (Swagger)	http://127.0.0.1:8000/docs
Kiểm tra trạng thái máy chủ	http://127.0.0.1:8000/health


Kết quả mong đợi tại /health:
{"ok": true}
Giữ Terminal chạy trong thời gian kiểm thử. Nhấn Ctrl + C khi muốn tắt website.
5. Tài khoản mẫu để test
Các tài khoản dưới đây được tạo bởi seed_demo.py nếu chưa tồn tại trong database kiểm thử:
Vai trò	Email	Mật khẩu	Mục cần kiểm tra
Admin	admin@demo.vn	123456	Quản trị, duyệt tin, quản lý tài khoản
Recruiter	recruiter@demo.vn	123456	Hồ sơ doanh nghiệp, đăng tin, đơn ứng tuyển
Candidate	Tự đăng ký email mới	Đúng 8 ký tự	Tìm/lưu việc, CV, nộp đơn


Mật khẩu 123456 chỉ áp dụng cho tài khoản demo vừa tạo, không thay đổi mật khẩu tài khoản đã tồn tại. Không sử dụng các thông tin đăng nhập này trên hệ thống thật.

6. Hướng dẫn kiểm thử chức năng
6.1. Trang chủ (không cần đăng nhập)
1. Mở http://127.0.0.1:8000.
2. Kiểm tra giao diện, ảnh banner và chuyển Sáng/Tối.
3. Tìm việc theo từ khóa, chọn địa điểm, bấm Tìm việc ngay.
4. Mở chi tiết tin, thử lọc và sắp xếp danh sách.
Mong đợi: Giao diện hiển thị và danh sách tin được lấy từ Backend.
6.2. Candidate — Ứng viên
1. Chọn Đăng ký, điền họ tên, email chưa dùng và mật khẩu đúng 8 ký tự.
2. Đăng nhập bằng tài khoản vừa tạo.
3. Thử Việc đã lưu, Đơn ứng tuyển, Hồ sơ & CV, Thông tin cá nhân.
4. Mở một tin tuyển dụng, thử ứng tuyển và xem lịch sử.
5. Đăng xuất, đăng nhập lại để kiểm tra phiên làm việc.
Mong đợi: Các thao tác có API hoạt động đúng với tài khoản Candidate. Lưu ý: File CV PDF trong bản này còn được lưu ở trình duyệt (IndexedDB), chưa tải lên máy chủ.
6.3. Recruiter — Nhà tuyển dụng
1. Đăng nhập bằng recruiter@demo.vn / 123456 nếu đã chạy seed_demo.py.
2. Chọn Nhà tuyển dụng → xem hoặc cập nhật Hồ sơ doanh nghiệp.
3. Tạo tin tuyển dụng mới và kiểm tra trạng thái chờ duyệt.
4. Xem hồ sơ ứng tuyển và thử cập nhật trạng thái khi có đơn.
Mong đợi: Nhà tuyển dụng chỉ thao tác với tin/đơn thuộc quyền quản lý của mình; tin mới cần được duyệt trước khi hiển thị công khai.
6.4. Admin — Quản trị viên
1. Đăng nhập bằng admin@demo.vn / 123456 nếu đã chạy seed_demo.py.
2. Chọn Quản trị → xem danh sách tin, duyệt hoặc từ chối tin chờ.
3. Mở Quản lý tài khoản → tìm theo họ tên/email/ID, lọc vai trò và trạng thái.
4. Khóa một tài khoản Candidate hoặc Recruiter dùng riêng để test; thử đăng nhập bằng tài khoản vừa khóa.
5. Mở khóa tài khoản đó, kiểm tra có thể đăng nhập lại.
6. Xóa tài khoản test, kiểm tra hộp thoại xác nhận và danh sách được cập nhật.
Mong đợi: Trạng thái và việc xóa được xử lý bằng API/MySQL. Backend không cho phép khóa hoặc xóa tài khoản Admin. Không xóa tài khoản đang chứa dữ liệu cần giữ.
6.5. Quên mật khẩu — cần cấu hình SMTP
1. Tại màn hình đăng nhập, chọn Quên mật khẩu?.
2. Nhập email có tài khoản và yêu cầu gửi mã xác minh.
3. Nhập mã nhận qua email, đặt mật khẩu mới đúng 8 ký tự.
4. Đăng nhập lại với mật khẩu mới.
Chỉ kiểm tra được chức năng gửi email thật sau khi thiết lập Gmail SMTP trong Backend/.env, ví dụ:
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=EMAIL_GMAIL_CUA_BAN
SMTP_PASSWORD=MAT_KHAU_UNG_DUNG_GMAIL
SMTP_FROM=EMAIL_GMAIL_CUA_BAN
Dùng mật khẩu ứng dụng Google hợp lệ, không dùng mật khẩu đăng nhập Gmail thông thường. Chưa thiết lập SMTP thì có thể bỏ qua mục test email và ghi nhận là chưa cấu hình.
7. Kiểm tra API bằng Swagger
Mở http://127.0.0.1:8000/docs:
Chức năng	API
Đăng ký	POST /auth/register
Đăng nhập	POST /auth/login
Danh sách việc làm	GET /api/jobs
Danh sách người dùng (Admin)	GET /api/admin/users
Khóa / mở khóa người dùng (Admin)	PATCH /api/admin/users/{user_id}/status
Xóa người dùng (Admin)	DELETE /api/admin/users/{user_id}
Quên mật khẩu	POST /auth/forgot-password
Đặt lại mật khẩu	POST /auth/reset-password


Với API yêu cầu đăng nhập, cần JWT Bearer token và đúng vai trò. Không công khai token đăng nhập trong ảnh chụp màn hình hay báo cáo.
8. Khắc phục lỗi thường gặp
Hiện tượng	Cách xử lý
405 Method Not Allowed	Kiểm tra đang mở cổng 8000, đúng đường dẫn và đúng phương thức GET / POST / PATCH / DELETE; không gọi API vào Live Server.
404 Not Found	Kiểm tra đường dẫn, tên file và hai thư mục frontend / Backend có nằm cùng cấp.
401 / 403	Đăng nhập lại bằng tài khoản đúng vai trò; token có thể hết hạn.
500, thiếu bảng password_reset_codes	Chạy setup_password_reset.py ở Bước 5.2.
SMTP_NOT_CONFIGURED	Cấu hình các biến SMTP_* trong Backend/.env hoặc bỏ qua phần test email.
Access denied for user 'root'	Kiểm tra tên/mật khẩu MySQL trong .env.
Can't connect to MySQL server	Bật MySQL Server; kiểm tra host/cổng 3306.
Unknown database 'job_portal'	Tạo database ở Bước 3.
No module named ...	Cài lại thư viện theo Bước 2 bằng Python trong .venv.
Address already in use	Cổng 8000 đang bị chiếm; dừng máy chủ cũ.
Website mở được nhưng không có dữ liệu	Kiểm tra /health, kết nối MySQL, đã nạp dữ liệu mẫu và bảng Console/Network của trình duyệt.


9. Các lần chạy sau (rút gọn)
Sau khi cài đặt lần đầu thành công, không cần tạo lại môi trường hay nạp dữ liệu mẫu:
1. Bật MySQL Server.
2. Mở Terminal tại thư mục JobConnect/Backend.
3. Chạy:
   .\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
4. Mở http://127.0.0.1:8000.
Dành cho người kiểm thử: README này mô tả cách chạy và các bước kiểm tra dựa trên mã nguồn, không phải biên bản xác nhận tất cả chức năng đã hoạt động thành công trên mọi máy. Cần có MySQL và các cấu hình môi trường phù hợp.
