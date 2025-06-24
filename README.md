# Ứng Dụng Trắc Nghiệm Online

<p align="center">
  <img src="images/logo.png" alt="Logo Trắc Nghiệm Online" width="200"/>
</p>

## Giới thiệu

Ứng dụng Trắc Nghiệm Online là một hệ thống client-server cho phép người dùng tham gia trả lời các câu hỏi trắc nghiệm với giao diện đẹp mắt, hỗ trợ tính điểm và bảng xếp hạng.

## Mục lục

- [Tính năng chính](#tính-năng-chính)
- [Cấu trúc dự án](#cấu-trúc-dự-án)
- [Yêu cầu hệ thống](#yêu-cầu-hệ-thống)
- [Cài đặt và sử dụng](#cài-đặt-và-sử-dụng)
- [Tài liệu chi tiết](#tài-liệu-chi-tiết)
- [Đóng góp](#đóng-góp)
- [Giấy phép](#giấy-phép)

## Tính năng chính

- Hệ thống client-server sử dụng socket để giao tiếp
- Giao diện người dùng trực quan với ttkbootstrap
- Cơ sở dữ liệu MySQL để lưu trữ câu hỏi và điểm số
- Tính năng đếm giờ cho mỗi câu hỏi
- Hiển thị kết quả và hiệu ứng đẹp mắt
- Lưu lịch sử điểm số và bảng xếp hạng

## Cấu trúc dự án

```
LapTrinhMang-Nhom10/
├── client.py                # Ứng dụng giao diện người dùng
├── server.py                # Máy chủ xử lý logic game
├── score_history.json       # Lịch sử điểm số
├── database/
│   └── sql_init.sql         # Script khởi tạo cơ sở dữ liệu
├── images/                  # Thư mục chứa hình ảnh minh họa
├── README.md                # Tài liệu chính
├── INSTALLATION.md          # Hướng dẫn cài đặt
├── ARCHITECTURE.md          # Kiến trúc hệ thống
├── DATABASE.md              # Cơ sở dữ liệu
├── CLIENT.md                # Chi tiết về client
├── SERVER.md                # Chi tiết về server
├── SCREENSHOTS.md           # Hình ảnh minh họa
└── DEPLOY.md                # Hướng dẫn triển khai
```

## Yêu cầu hệ thống

- Python 3.7+
- MySQL Server
- Các thư viện Python: socket, tkinter, ttkbootstrap, mysql-connector-python

## Cài đặt và sử dụng

### Cài đặt nhanh

1. Cài đặt các thư viện cần thiết:

```bash
pip install mysql-connector-python ttkbootstrap
```

2. Khởi tạo cơ sở dữ liệu:

```bash
mysql -u root -p < database/sql_init.sql
```

3. Cấu hình kết nối trong file `server.py`:

```python
db = mysql.connector.connect(
    host="localhost",
    user="root",
    password="your_password",  # Điền mật khẩu của bạn
    database="quiz_game"
)
```

4. Chạy server:

```bash
python server.py
```

5. Chạy client:

```bash
python client.py
```

Xem thêm hướng dẫn chi tiết tại [INSTALLATION.md](./INSTALLATION.md).

## Tài liệu chi tiết

- [Kiến trúc hệ thống](./ARCHITECTURE.md)
- [Cơ sở dữ liệu](./DATABASE.md)
- [Ứng dụng Client](./CLIENT.md)
- [Máy chủ Server](./SERVER.md)
- [Hình ảnh minh họa](./SCREENSHOTS.md)
- [Hướng dẫn triển khai](./DEPLOY.md)

## Đóng góp

Mọi đóng góp cho dự án đều được chào đón. Vui lòng:

1. Fork dự án
2. Tạo nhánh tính năng mới (`git checkout -b feature/AmazingFeature`)
3. Commit thay đổi của bạn (`git commit -m 'Add some AmazingFeature'`)
4. Push lên nhánh của bạn (`git push origin feature/AmazingFeature`)
5. Tạo Pull Request

## Giấy phép

Dự án này được phân phối dưới giấy phép MIT. Xem file `LICENSE` để biết thêm chi tiết.
