JobConnect — Cách chạy website
Frontend: HTML, CSS, JavaScript
Backend: Python FastAPI
Lưu dữ liệu: MySQL

Website chạy chung tại http://127.0.0.1:8000. Không cần PHP, XAMPP, Live Server hoặc file .bat. MySQL Server vẫn phải được bật.
1. Chuẩn bị
Cài Python, MySQL Server và VS Code. Mở thư mục dự án bằng VS Code.

Chỉ nhập các lệnh có nhãn PowerShell vào Terminal. Nội dung có nhãn .env phải điền vào file .env, không nhập vào Terminal.
2. Mở Terminal ở thư mục Backend
Trong VS Code: Terminal → New Terminal, sau đó chạy:
cd Backend
Nếu Terminal đã ở thư mục Backend thì bỏ qua lệnh cd Backend.

3. Tạo môi trường Python (chỉ lần đầu)
py -m venv .venv
Cài thư viện:
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Lần sau không cần tạo .venv hoặc cài lại thư viện.

4. Kết nối MySQL (chỉ lần đầu hoặc khi đổi mật khẩu)
Bật dịch vụ MySQL Server. Trong MySQL Workbench hoặc MySQL Command Line Client, tạo database nếu chưa có:
CREATE DATABASE IF NOT EXISTS job_portal;
Trong thư mục Backend, nếu chưa có file .env, sao chép mẫu bằng Terminal:
Copy-Item .env.example .env
Mở file Backend/.env trong VS Code, sửa dòng kết nối theo mẫu sau rồi lưu bằng Ctrl + S:
DATABASE_URL=mysql+pymysql://root:MAT_KHAU_MYSQL@127.0.0.1:3306/job_portal
JOB_SECRET_KEY=THAY_BANG_CHUOI_NGAU_NHIEN_DAI
- Thay MAT_KHAU_MYSQL bằng mật khẩu MySQL của máy bạn. Nếu tài khoản không có mật khẩu, dùng root@127.0.0.1 thay cho root:MAT_KHAU_MYSQL@127.0.0.1.
- Nếu mật khẩu có ký tự đặc biệt như @, #, %, cần mã hóa cho URL.
- Không chép py -m venv .venv hoặc các lệnh PowerShell vào .env.
- Không đưa file .env chứa mật khẩu lên GitHub.
Kiểm tra kết nối (PowerShell):
.\.venv\Scripts\python.exe -c "from database import engine; c=engine.connect(); print('KET NOI MYSQL THANH CONG'); c.close()"
5. Tạo bảng và dữ liệu mẫu (chỉ lần đầu)
Chỉ chạy khi dùng database mới, chưa có bảng/dữ liệu:
.\.venv\Scripts\python.exe init_db.py
Muốn có dữ liệu thử nghiệm thì chạy thêm:
.\.venv\Scripts\python.exe seed_demo.py
Nếu đã có dữ liệu thật, không tạo lại database, xóa bảng hoặc chạy seed tùy tiện.

6. Chạy website
Trong thư mục Backend, chạy:
.\.venv\Scripts\python.exe -m uvicorn main:app --reload
Mở trình duyệt:

Website: http://127.0.0.1:8000
Kiểm tra API: http://127.0.0.1:8000/docs

Giữ Terminal đang chạy. Muốn tắt website, nhấn Ctrl + C.
Lần sau chỉ cần làm gì?
1. Bật MySQL Server.
2. Mở Terminal trong thư mục Backend.
3. Chạy lệnh ở Bước 6.
4. Truy cập http://127.0.0.1:8000.
Nếu có lỗi
Lỗi	Kiểm tra
Access denied for user 'root'	Sai tài khoản/mật khẩu trong .env hoặc quyền MySQL
getaddrinfo failed	Sai địa chỉ host trong .env; dùng 127.0.0.1, không dán thêm lệnh Python
Can't connect to MySQL server	MySQL chưa bật, sai cổng hoặc sai host
Unknown database 'job_portal'	Chưa tạo database hoặc sai tên
No module named ...	Cài thư viện bằng lệnh ở Bước 3
Address already in use	Cổng 8000 đang được chương trình khác sử dụng


Lưu ý: Website tải giao diện được chưa có nghĩa kết nối MySQL đã thành công. Nếu trang hiện nhưng danh sách việc làm lỗi, hãy kiểm tra kết nối ở Bước 4.
