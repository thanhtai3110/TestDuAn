💼 JobConnect
Hướng dẫn cài đặt, chạy và kiểm thử website trên Windows

Dành cho giảng viên, thành viên nhóm và người muốn chạy thử dự án từ mã nguồn trên GitHub.
Thành phần	Công nghệ
Frontend	HTML, CSS, JavaScript thuần
Backend	Python FastAPI
Cơ sở dữ liệu	MySQL
Website	http://127.0.0.1:8000
Kiểm tra API	http://127.0.0.1:8000/docs


Lưu ý: Bản mã nguồn này đã sử dụng frontend/api-bridge.js để gọi Backend. Khi kiểm thử bản tích hợp, chỉ chạy website bằng FastAPI ở cổng 8000. Không cần XAMPP, PHP hoặc Live Server.

1. Chuẩn bị trước khi chạy
Cài đặt Python 3, MySQL Server và VS Code. Đảm bảo MySQL đang hoạt động.

Mở thư mục dự án có cấu trúc:
JobConnect/
├── README.md
├── frontend/
│   ├── index.html
│   ├── app.js
│   ├── portal.js
│   ├── api-bridge.js
│   ├── style.css
│   ├── theme.js
│   └── images/
└── Backend/
    ├── main.py
    ├── database.py
    ├── config.py
    ├── models.py
    ├── schemas.py
    ├── requirements.txt
    ├── .env.example
    ├── init_db.py
    ├── setup_password_reset.py
    ├── seed_demo.py
    ├── demo_jobs.json
    └── password_reset.py
Hai thư mục frontend và Backend phải nằm cùng cấp. Hình ảnh giao diện (nếu có) cần nằm đúng trong frontend/images/.

2. Cài đặt lần đầu
Bước 1 — Mở Terminal tại Backend
Trong VS Code: Terminal → New Terminal. Nếu Terminal đang ở thư mục gốc JobConnect, nhập:
cd Backend
Nếu Terminal đã đứng ở Backend thì bỏ qua lệnh này.
Bước 2 — Tạo môi trường Python
Chạy từng lệnh trong PowerShell:
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Chỉ cần tạo môi trường một lần trên máy.
Bước 3 — Tạo cơ sở dữ liệu MySQL
Mở MySQL Workbench hoặc MySQL Command Line Client, chạy câu lệnh SQL sau:
CREATE DATABASE IF NOT EXISTS job_portal;
Không nhập câu lệnh SQL này vào PowerShell.
Bước 4 — Tạo và chỉnh file .env
Trong PowerShell ở thư mục Backend, chạy lệnh dưới đây nếu chưa có .env:
Copy-Item .env.example .env
Mở Backend/.env bằng VS Code, đặt thông tin kết nối theo mẫu:
DATABASE_URL=mysql+pymysql://root:MAT_KHAU_MYSQL@127.0.0.1:3306/job_portal
Thay MAT_KHAU_MYSQL bằng mật khẩu MySQL thực tế trên máy kiểm thử. Nếu tài khoản root không có mật khẩu, dùng:
DATABASE_URL=mysql+pymysql://root@127.0.0.1:3306/job_portal
Nếu mật khẩu MySQL có ký tự đặc biệt như @, #, %, cần mã hóa mật khẩu theo URL. Không đưa file .env lên GitHub. File này chỉ lưu trên máy kiểm thử.

Kiểm tra MySQL đã kết nối (trong PowerShell ở Backend):
.\.venv\Scripts\python.exe -c "from database import engine; c=engine.connect(); print('KET NOI MYSQL THANH CONG'); c.close()"
Khi thấy dòng KET NOI MYSQL THANH CONG, chuyển sang bước tiếp theo.
Bước 5 — Khởi tạo bảng và dữ liệu test
Chạy lần lượt các lệnh sau trong PowerShell tại Backend:

5.1. Tạo bảng chính:
.\.venv\Scripts\python.exe init_db.py
5.2. Tạo bảng mã xác minh quên mật khẩu:
.\.venv\Scripts\python.exe setup_password_reset.py
5.3. Tạo tài khoản và tin tuyển dụng mẫu:
.\.venv\Scripts\python.exe seed_demo.py
Chỉ thực hiện bước 5.3 với database dành riêng cho việc test. Không chạy nạp dữ liệu mẫu vào cơ sở dữ liệu đang chứa dữ liệu cần giữ.

3. Khởi động website
Trong PowerShell tại thư mục Backend, chạy:
.\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
Mở trình duyệt và truy cập:
Mục đích	Địa chỉ
Mở website JobConnect	http://127.0.0.1:8000
Xem danh sách API (Swagger)	http://127.0.0.1:8000/docs
Kiểm tra máy chủ	http://127.0.0.1:8000/health


Khi truy cập /health, kết quả dự kiến:
{"ok": true}
Giữ nguyên cửa sổ Terminal khi test. Muốn tắt website, nhấn Ctrl + C.
4. Tài khoản sử dụng để kiểm thử
Sau khi chạy seed_demo.py trên database test mới:
Vai trò	Email đăng nhập	Mật khẩu
Admin	admin@demo.vn	123456
Recruiter	recruiter@demo.vn	123456
Candidate	Tự đăng ký email mới	Đúng 8 ký tự


Các mật khẩu trên chỉ dành cho tài khoản demo vừa được tạo. Nếu email đã tồn tại trong MySQL, lệnh seed không đặt lại mật khẩu cũ.

5. Hướng dẫn test các chức năng
5.1. Trang chủ
1. Vào http://127.0.0.1:8000.
2. Kiểm tra banner, danh sách việc làm và chế độ Sáng / Tối.
3. Nhập từ khóa, chọn địa điểm rồi nhấn Tìm việc ngay.
4. Mở chi tiết một tin tuyển dụng.
Kết quả mong đợi: Trang web hiển thị, danh sách việc làm được tải từ Backend.
5.2. Candidate (Ứng viên)
1. Nhấn Đăng ký và tạo tài khoản ứng viên với mật khẩu đúng 8 ký tự.
2. Đăng nhập bằng tài khoản vừa tạo.
3. Thử Việc đã lưu, Đơn ứng tuyển, Hồ sơ & CV và Thông tin cá nhân.
4. Mở một tin, thử ứng tuyển và kiểm tra lịch sử ứng tuyển.
5. Đăng xuất rồi đăng nhập lại.
Lưu ý: Phần CV PDF có thể vẫn được lưu trong trình duyệt (IndexedDB), không phải dữ liệu upload lên MySQL.
5.3. Recruiter (Nhà tuyển dụng)
1. Đăng nhập: recruiter@demo.vn / 123456.
2. Chọn Nhà tuyển dụng.
3. Xem và cập nhật Hồ sơ doanh nghiệp.
4. Tạo tin tuyển dụng mới và kiểm tra trạng thái chờ duyệt.
5. Kiểm tra danh sách đơn ứng tuyển khi có ứng viên nộp đơn.
Kết quả mong đợi: Tin mới cần Admin duyệt trước khi xuất hiện trong danh sách công khai.
5.4. Admin (Quản trị viên)
1. Đăng nhập: admin@demo.vn / 123456.
2. Mở phần Quản trị để kiểm tra và duyệt tin tuyển dụng.
3. Mở Quản lý tài khoản, thử tìm kiếm và lọc danh sách.
4. Khóa một tài khoản test Candidate hoặc Recruiter; thử đăng nhập bằng tài khoản đó.
5. Mở khóa và thử đăng nhập lại.
6. Xóa tài khoản test, kiểm tra hộp thoại xác nhận.
Kết quả mong đợi: API chỉ cho Admin thao tác; không cho khóa hoặc xóa tài khoản Admin. Chỉ xóa tài khoản được tạo riêng để kiểm thử.
5.5. Quên mật khẩu (tùy chọn)
Nếu muốn test gửi mã xác minh tới Gmail thật, phải cấu hình thêm trong Backend/.env:
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=EMAIL_GMAIL_CUA_BAN
SMTP_PASSWORD=MAT_KHAU_UNG_DUNG_GMAIL
SMTP_FROM=EMAIL_GMAIL_CUA_BAN
Sau đó thử Quên mật khẩu → Nhận mã 6 số → Đặt lại mật khẩu 8 ký tự.
Chức năng gửi email yêu cầu mật khẩu ứng dụng Gmail hợp lệ. Nếu chưa cấu hình SMTP thì bỏ qua bài test email; không dùng mật khẩu Gmail thông thường và không commit thông tin SMTP lên GitHub.

6. API cần kiểm tra trong Swagger
Mở http://127.0.0.1:8000/docs.
Chức năng	HTTP	Đường dẫn
Đăng ký	POST	/auth/register
Đăng nhập	POST	/auth/login
Danh sách việc làm	GET	/api/jobs
Danh sách tài khoản Admin	GET	/api/admin/users
Khóa / mở khóa tài khoản	PATCH	/api/admin/users/{user_id}/status
Xóa tài khoản	DELETE	/api/admin/users/{user_id}
Gửi mã quên mật khẩu	POST	/auth/forgot-password
Đặt lại mật khẩu	POST	/auth/reset-password


API quản trị yêu cầu JWT Bearer token của tài khoản Admin.
7. Các lỗi thường gặp
Lỗi	Cách kiểm tra
405 – Method Not Allowed	Sai phương thức GET / POST / PATCH / DELETE hoặc gọi API vào Live Server thay vì cổng 8000.
401 / 403	Token hết hạn hoặc tài khoản không có quyền truy cập.
404 – Not Found	Kiểm tra đúng đường dẫn và cấu trúc thư mục.
500 – Missing password_reset_codes	Chạy lại setup_password_reset.py trong Backend.
Access denied (MySQL)	Kiểm tra mật khẩu hoặc tên tài khoản trong Backend/.env.
Can't connect to MySQL	Bật MySQL Server và kiểm tra cổng 3306.
Unknown database job_portal	Tạo database theo Bước 3.
No module named ...	Cài lại thư viện theo Bước 2.
Address already in use	Cổng 8000 đang được một tiến trình khác sử dụng.
SMTP chưa cấu hình	Bổ sung SMTP_* hoặc bỏ qua bài test gửi email.


8. Những lần chạy sau
Sau khi hoàn thành cài đặt lần đầu, chỉ cần:
1. Bật MySQL Server.
2. Mở VS Code, vào Terminal tại thư mục Backend.
3. Chạy:
   .\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
4. Truy cập http://127.0.0.1:8000.
Ghi chú cho người kiểm thử: README hướng dẫn các bước dự kiến theo mã nguồn dự án, không phải chứng nhận rằng tất cả chức năng đã được kiểm thử thành công trên mọi máy tính.
