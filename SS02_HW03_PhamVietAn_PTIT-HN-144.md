# Bài 3: Cài đặt Nginx và cấu hình website tĩnh

## Bước 1: Đăng nhập vào Droplet

**Câu lệnh trên máy cá nhân:**

```powershell
ssh -i C:\Users\crou\.ssh\id_ed25519 devops@167.99.72.156
```

## Bước 2: Cập nhật danh sách gói và cài đặt Nginx

**Các câu lệnh cài đặt:**

```bash
sudo apt update
sudo apt install nginx -y
```

**Câu lệnh kiểm tra trạng thái:**

```bash
sudo systemctl status nginx --no-pager
```

**Câu lệnh kích hoạt tự khởi động:**

```bash
sudo systemctl enable nginx
```

## Bước 3: Tạo thư mục chứa website

**Các câu lệnh trên Droplet:**

```bash
sudo mkdir -p /var/www/ptit-web/html
sudo chown -R $USER:$USER /var/www/ptit-web/html
sudo chmod -R 755 /var/www/ptit-web
```

## Bước 4: Tạo trang index.html

**Câu lệnh mở tệp:**

```bash
nano /var/www/ptit-web/html/index.html
```

**Nội dung tệp `index.html`:**

```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PTIT DevOps Course</title>
</head>
<body>
    <h1>Welcome to PTIT DevOps Course - Session 02</h1>
</body>
</html>
```

Lưu tệp bằng tổ hợp phím `Ctrl + O`, nhấn `Enter`, sau đó thoát bằng `Ctrl + X`.

## Bước 5: Tạo Server Block cho website

**Câu lệnh tạo tệp cấu hình:**

```bash
sudo nano /etc/nginx/sites-available/ptit-web.conf
```

**Nội dung tệp `ptit-web.conf`:**

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name _;

    root /var/www/ptit-web/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Trong đó:

- `listen 80` cho phép Nginx nhận yêu cầu HTTP trên IPv4.
- `listen [::]:80` cho phép nhận yêu cầu HTTP trên IPv6.
- `server_name _` tiếp nhận yêu cầu gửi trực tiếp tới IP khi chưa có tên miền.
- `root` chỉ định thư mục chứa mã nguồn website.
- `try_files` kiểm tra tài nguyên được yêu cầu và trả về mã lỗi `404` nếu không tồn tại.

## Bước 6: Kích hoạt cấu hình và tắt trang mặc định

**Câu lệnh tạo liên kết tượng trưng:**

```bash
sudo ln -s /etc/nginx/sites-available/ptit-web.conf /etc/nginx/sites-enabled/ptit-web.conf
```

Nếu liên kết đã tồn tại, không cần chạy lại lệnh trên.

Tắt cấu hình mặc định để tránh xung đột.

**Câu lệnh:**

```bash
sudo rm /etc/nginx/sites-enabled/default
```

Lệnh trên chỉ xóa liên kết kích hoạt trong `sites-enabled`, không xóa tệp cấu hình gốc trong `sites-available`.

## Bước 7: Kiểm tra cú pháp và nạp lại Nginx

**Câu lệnh kiểm tra cú pháp:**

```bash
sudo nginx -t
```

**Kết quả hiển thị:**

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Khi cú pháp hợp lệ, nạp lại cấu hình.

**Câu lệnh:**

```bash
sudo systemctl reload nginx
```

**Câu lệnh kiểm tra trạng thái:**

```bash
sudo systemctl is-active nginx
```

**Kết quả hiển thị:**

```text
active
```

## Bước 8: Kiểm tra website

**Câu lệnh kiểm tra trên Droplet:**

```bash
curl http://localhost
```

**Kết quả hiển thị:**

```html
<h1>Welcome to PTIT DevOps Course - Session 02</h1>
```

**Câu lệnh kiểm tra trên máy cá nhân:**

```powershell
curl.exe http://167.99.72.156
```

**Địa chỉ truy cập trên trình duyệt:**

```text
http://167.99.72.156
```

## Log Terminal kiểm tra

**Log Terminal ghi nhận quá trình kiểm tra Nginx và website:**

```text
devops@ptit-devops-ss02:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
devops@ptit-devops-ss02:~$ sudo systemctl reload nginx
devops@ptit-devops-ss02:~$ sudo systemctl is-active nginx
active
devops@ptit-devops-ss02:~$ curl http://localhost
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PTIT DevOps Course</title>
</head>
<body>
    <h1>Welcome to PTIT DevOps Course - Session 02</h1>
</body>
</html>
```

## Kết luận

Nginx đã được cài đặt và cấu hình để phục vụ website tĩnh từ thư mục `/var/www/ptit-web/html` qua cổng 80. Cấu hình mặc định đã được tắt, Server Block `ptit-web.conf` đã được kích hoạt và kiểm tra cú pháp thành công.
