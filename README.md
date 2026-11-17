<CodeBlock language="text" editable>
# JobConnect – Hướng dẫn cài đặt và kiểm thử

1. Giới thiệu dự án
Thành phần	Công nghệ
Frontend	HTML, CSS, JavaScript
Backend	Python FastAPI
Database	MySQL

2. Chuẩn bị
Cần cài đặt:
- Python 3
- MySQL Server
- Visual Studio Code
3. Cài đặt Backend
Mở Terminal trong VS Code.
Bước 1: Vào thư mục Backend
cd Backend


Bước 2: Tạo môi trường Python
py -m venv .venv


Bước 3: Cài thư viện
.\.venv\Scripts\python.exe -m pip install -r requirements.txt


4. Chạy website
.\.venv\Scripts\python.exe -m uvicorn main:app --reload


Mở trình duyệt:
- Website: http://127.0.0.1:8000
- Swagger API: http://127.0.0.1:8000/docs
Bước 3: Bấm Preview trên GitHub.

Nếu đúng, bạn sẽ thấy các tiêu đề lớn, bảng hai cột và các lệnh nằm trong từng khung riêng, thay vì dồn thành một đoạn.
