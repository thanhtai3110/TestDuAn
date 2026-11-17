
## 3. Cài đặt Backend

Mở Terminal trong Visual Studio Code.

### Bước 1: Vào thư mục Backend

```powershell
cd Backend
```

### Bước 2: Tạo môi trường Python

```powershell
py -m venv .venv
```

### Bước 3: Cài đặt thư viện

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

## 4. Chạy website

Trong Terminal đang ở thư mục Backend, chạy:

```powershell
.\.venv\Scripts\python.exe -m uvicorn main:app --reload
```

Mở trình duyệt:

**Website:** http://127.0.0.1:8000

**Kiểm tra API:** http://127.0.0.1:8000/docs
