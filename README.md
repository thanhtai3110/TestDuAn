JobConnect — Hướng dẫn cài đặt và kiểm thử website
Hướng dẫn dành cho giảng viên, thành viên nhóm hoặc người nhận mã nguồn để chạy và thử website JobConnect trên máy Windows. Tài liệu được viết theo bộ mã nguồn có frontend/api-bridge.js, Backend/main.py, Backend/init_db.py, Backend/seed_demo.py và Backend/password_reset.py.

1. Giới thiệu nhanh
- Frontend: HTML, CSS, JavaScript (không dùng React).
- Backend: Python FastAPI.
- Cơ sở dữ liệu: MySQL.
- Địa chỉ chạy thử: http://127.0.0.1:8000
- Trang xem API: http://127.0.0.1:8000/docs
Lưu ý về phiên bản: Trong bộ mã nguồn này, Frontend đã gọi API Backend qua api-bridge.js và Backend phục vụ luôn giao diện. Vì vậy không mở index.html trực tiếp và không cần Live Server/XAMPP để kiểm thử bản tích hợp. Một số tính năng như lưu file CV còn hoạt động ở trình duyệt.
2. Chuẩn bị
Cài trước:
1. Python 3 (có thể chạy lệnh py --version trong PowerShell).
2. MySQL Server đang hoạt động và có tài khoản truy cập.
3. VS Code hoặc trình soạn thảo bất kỳ.
Sau khi giải nén dự án, kiểm tra cấu trúc:
JobConnect/
├── frontend/
│   ├── index.html
│   ├── app.js
│   ├── portal.js
│   ├── style.css
│   ├── theme.js
│   ├── api-bridge.js
│   └── images/                 # Ảnh giao diện (nếu có)
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
Quan trọng: Hai thư mục frontend và Backend phải nằm cạnh nhau trong cùng thư mục JobConnect, vì Backend/main.py tìm giao diện bằng đường dẫn ../frontend.
3. Cài đặt lần đầu (PowerShell trong VS Code)
Mở thư mục JobConnect bằng VS Code → Terminal → New Terminal.
Bước 1 — Vào Backend
cd Backend
Nếu Terminal đã đứng sẵn trong Backend, bỏ qua bước này.
Bước 2 — Tạo môi trường và cài thư viện
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Bước 3 — Tạo cơ sở dữ liệu MySQL
Khởi động MySQL Server. Mở MySQL Workbench hoặc MySQL Command Line Client, chạy lệnh SQL sau (không nhập lệnh này vào PowerShell):
CREATE DATABASE IF NOT EXISTS job_portal;
Bước 4 — Cấu hình Backend/.env
Nếu chưa có .env, trong Terminal đang ở Backend chạy:
Copy-Item .env.example .env
Mở Backend/.env bằng VS Code. Điền thông tin MySQL thực tế của máy:
DATABASE_URL=mysql+pymysql://root:MAT_KHAU_MYSQL@127.0.0.1:3306/job_portal
JOB_SECRET_KEY=THAY_BANG_CHUOI_BI_MAT_DU_DAI
- Thay MAT_KHAU_MYSQL bằng mật khẩu trên máy đang test. Nếu MySQL root không có mật khẩu, dùng mysql+pymysql://root@127.0.0.1:3306/job_portal.
- Nếu mật khẩu chứa @, #, % hoặc ký tự đặc biệt khác, cần mã hóa theo URL.
- Không đăng .env lên GitHub, không gửi công khai mật khẩu MySQL/Gmail.
- Backend/config.py của bản mã nguồn đính kèm đang khai báo khóa JWT riêng. Nếu triển khai ngoài môi trường test, cần thay khóa mặc định trong mã nguồn bằng cấu hình bảo mật phù hợp.
Kiểm tra MySQL đã kết nối:
.\.venv\Scripts\python.exe -c "from database import engine; c=engine.connect(); print('KET NOI MYSQL THANH CONG'); c.close()"
Bước 5 — Tạo bảng và dữ liệu kiểm thử
.\.venv\Scripts\python.exe init_db.py
.\.venv\Scripts\python.exe setup_password_reset.py
Nếu đang sử dụng database mới để thử nghiệm, nạp tài khoản và tin tuyển dụng mẫu:
.\.venv\Scripts\python.exe seed_demo.py
seed_demo.py tạo tài khoản mẫu nếu chúng chưa tồn tại; chỉ nạp tin tuyển dụng khi bảng jobs đang rỗng. Không tùy tiện chạy dữ liệu mẫu trên database thật của nhóm. init_db.py không tự cập nhật cấu trúc cột của các bảng cũ.
4. Chạy website
Trong Terminal ở thư mục Backend, chạy:
.\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
Mở trình duyệt:
- Website: http://127.0.0.1:8000
- Swagger API: http://127.0.0.1:8000/docs
- Kiểm tra Backend: http://127.0.0.1:8000/health (mong đợi {"ok":true})
Để website hoạt động, giữ Terminal này chạy. Muốn dừng, nhấn Ctrl + C.
5. Tài khoản dùng để kiểm thử
Chỉ áp dụng khi người test đã chạy seed_demo.py trên database thử nghiệm mới:
Vai trò	Tài khoản	Mật khẩu mẫu	Cách thử
Admin	admin@demo.vn	123456	Đăng nhập → Quản trị → duyệt tin / quản lý tài khoản
Recruiter	recruiter@demo.vn	123456	Đăng nhập → Nhà tuyển dụng → hồ sơ công ty / tin tuyển dụng / ứng viên
Candidate	Tự đăng ký email mới	Tự tạo mật khẩu đúng 8 ký tự	Đăng ký → Đăng nhập → lưu việc / ứng tuyển / quản lý CV


Lưu ý: Mật khẩu 123456 chỉ do script seed_demo.py đặt cho tài khoản demo mới tạo, không có nghĩa tài khoản cũ cũng dùng mật khẩu này. Không sử dụng tài khoản/mật khẩu mẫu trong môi trường thật.
6. Kịch bản kiểm thử website
A. Giao diện và tìm việc (không cần đăng nhập)
1. Mở trang chủ, kiểm tra banner, danh sách việc làm và nút chuyển giao diện sáng/tối.
2. Nhập từ khóa, chọn địa điểm và bấm Tìm việc ngay.
3. Mở chi tiết một tin tuyển dụng; thử bộ lọc và sắp xếp.
Kết quả mong đợi: Trang hiển thị bình thường, có thể xem/lọc các tin đang được Backend trả về.
B. Ứng viên (Candidate)
1. Chọn Đăng ký, nhập họ tên, email mới và mật khẩu đúng 8 ký tự.
2. Đăng nhập với tài khoản vừa tạo.
3. Thử Việc đã lưu, Đơn ứng tuyển, Hồ sơ & CV, Thông tin cá nhân.
4. Mở tin tuyển dụng và thử nộp đơn; xem lại lịch sử ứng tuyển.
5. Thử đăng xuất và đăng nhập lại.
Kết quả mong đợi: Đăng ký, đăng nhập và các thao tác có API phản hồi đúng; CV PDF trong bản hiện tại được lưu ở trình duyệt (IndexedDB), chưa phải upload file CV lên MySQL/máy chủ.
C. Nhà tuyển dụng (Recruiter)
1. Đăng nhập recruiter@demo.vn (nếu đã seed).
2. Mở Nhà tuyển dụng.
3. Xem/cập nhật hồ sơ doanh nghiệp.
4. Tạo tin tuyển dụng mới; kiểm tra tin chờ duyệt.
5. Xem các đơn ứng tuyển và thử cập nhật trạng thái khi có dữ liệu phù hợp.
Kết quả mong đợi: Chỉ nhà tuyển dụng được sử dụng chức năng của Recruiter; tin mới có thể cần Admin duyệt trước khi xuất hiện công khai.
D. Quản trị viên (Admin)
1. Đăng nhập admin@demo.vn (nếu đã seed).
2. Mở Quản trị → Kiểm duyệt tin: thử duyệt hoặc từ chối tin đang chờ.
3. Mở Quản trị → Quản lý tài khoản.
4. Thử tìm theo tên/email, lọc vai trò và trạng thái.
5. Khóa một tài khoản thử nghiệm; xác minh tài khoản bị khóa không thể đăng nhập. Sau đó Mở khóa và thử lại.
6. Xóa một tài khoản dùng riêng cho kiểm thử, kiểm tra hộp thoại xác nhận và danh sách sau khi xóa.
Kết quả mong đợi: Những thao tác trên dùng API thật và cập nhật MySQL. Không dùng tài khoản Admin hoặc tài khoản có dữ liệu quan trọng để thử xóa. Backend không cho phép khóa/xóa tài khoản có vai trò Admin.
E. Quên mật khẩu (chỉ khi cấu hình Gmail SMTP)
1. Trong màn hình đăng nhập, chọn Quên mật khẩu?.
2. Nhập email đăng ký và yêu cầu gửi mã.
3. Mở email để lấy mã OTP 6 số, nhập mã và mật khẩu mới đúng 8 ký tự.
4. Thử đăng nhập lại với mật khẩu mới.
Yêu cầu: Cần cấu hình SMTP_HOST, SMTP_PORT, SMTP_USER, SMTP_PASSWORD, SMTP_FROM trong Backend/.env với thông tin Gmail/SMTP hợp lệ. Nếu chưa cấu hình SMTP, bước gửi email sẽ báo lỗi; đây không phải chế độ OTP demo.
7. Kiểm tra API riêng trên Swagger
Mở http://127.0.0.1:8000/docs. Các API cần chú ý:
Chức năng	Phương thức và đường dẫn
Đăng ký	POST /auth/register
Đăng nhập	POST /auth/login
Xem danh sách việc làm	GET /api/jobs
Danh sách tài khoản (Admin)	GET /api/admin/users
Khóa/mở khóa tài khoản (Admin)	PATCH /api/admin/users/{user_id}/status
Xóa tài khoản (Admin)	DELETE /api/admin/users/{user_id}
Gửi mã quên mật khẩu	POST /auth/forgot-password
Đặt lại mật khẩu	POST /auth/reset-password


API bảo vệ bằng JWT cần token từ đăng nhập. Trong Swagger, sử dụng Authorize nếu có, hoặc gửi Bearer token khi kiểm thử qua công cụ API. Không đưa token đăng nhập thật vào báo cáo công khai.
8. Một số lỗi thường gặp
Lỗi	Cách kiểm tra
405 Method Not Allowed	Kiểm tra phương thức GET/POST/PATCH/DELETE, URL và có đang mở đúng http://127.0.0.1:8000 thay vì Live Server hay không.
404 ở trang web hoặc file JS	Kiểm tra frontend đặt cùng cấp với Backend; xem các đường dẫn trong index.html.
401 Unauthorized / 403 Forbidden	Đăng nhập lại hoặc dùng đúng vai trò Admin/Recruiter/Candidate.
500, password_reset_codes doesn't exist	Chạy setup_password_reset.py trong thư mục Backend.
503, SMTP_NOT_CONFIGURED	Chưa cấu hình Gmail SMTP; bỏ qua test quên mật khẩu hoặc cấu hình SMTP hợp lệ.
Access denied for user 'root'	Kiểm tra thông tin MySQL trong Backend/.env.
Can't connect to MySQL server	Bật MySQL, kiểm tra cổng 3306 và địa chỉ 127.0.0.1.
Unknown database 'job_portal'	Tạo database theo Bước 3.
No module named ...	Cài lại requirements.txt bằng Python của .venv.
Address already in use	Cổng 8000 đang có chương trình khác chạy; tắt tiến trình cũ.
Website có giao diện nhưng không có tin	Kiểm tra /health, kết nối MySQL, đã seed dữ liệu hay chưa, và Console/Network của trình duyệt.


9. Những lần chạy sau
Nếu đã cài đặt và tạo database xong, chỉ cần:
1. Bật MySQL Server.
2. Mở Terminal trong thư mục Backend.
3. Chạy:
.\.venv\Scripts\python.exe -m uvicorn main:app --reload --host 127.0.0.1 --port 8000
4. Truy cập http://127.0.0.1:8000.
Ghi chú cho người chấm/test: Đây là hướng dẫn dựa trên mã nguồn đã gửi, không phải biên bản xác nhận mọi chức năng đã chạy thành công trên máy khác. Các chức năng liên quan Gmail, MySQL và quyền tài khoản cần được kiểm thử trên môi trường cài đặt thực tế.
