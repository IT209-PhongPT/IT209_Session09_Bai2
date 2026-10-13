# IT209 - Session 09 - Bài tập 2: Khởi chạy Web Server Nginx với Port Mapping

- **Sinh viên:** Phạm Thanh Phong
- **MSSV:** N24DTCN120
- **Học phần:** IT209
- **Đường dẫn nộp bài:** `homework/session_09/ex2/`
- **Trạng thái:** Hoàn thành (100%)

---

## 1. Mục tiêu bài tập
- Khởi chạy một Web Server Nginx dạng container ở chế độ chạy ngầm (`-d` / detached mode).
- Thực hành cơ chế ánh xạ cổng mạng (`-p 8080:80` / Port Mapping) giữa Host và Container.
- Gửi truy vấn HTTP bằng lệnh `curl` kiểm tra phản hồi từ Web Server.
- Quản lý vòng đời container: Kiểm tra danh sách tiến trình (`docker ps`), dừng container (`docker stop`) và xóa container (`docker rm`).

---

## 2. Các bước thực hiện & Kết quả Terminal

### Bước 1: Khởi chạy Nginx container ở chế độ chạy ngầm và ánh xạ cổng

```bash
docker run -d -p 8080:80 --name my-web nginx:alpine
```

#### Kết quả đầu ra Terminal:
```plaintext
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
da9db072f522: Pull complete 
a21b3cd919b4: Pull complete 
d05267039a04: Pull complete 
03b1e389d3cd: Pull complete 
7a72d73f4e15: Pull complete 
6b83f0e20f7d: Pull complete 
Digest: sha256:4ff16a133d1c4485e9b867623910543666044fb5f44a19c7f66a2a6bf961362e
Status: Downloaded newer image for nginx:alpine
5e82b7cf183bc6a34ad2a9ec04e1990121c70ae3cd319fdd3dd3094cd4fa8883
```

> **Giải thích tham số:**
> - `-d`: Chạy container ở chế độ background (detached mode), không chiếm dụng màn hình Terminal.
> - `-p 8080:80`: Ánh xạ cổng `8080` trên máy Host vào cổng `80` (HTTP mặc định) bên trong container.
> - `--name my-web`: Gán tên định danh tường minh cho container là `my-web`.
> - `nginx:alpine`: Sử dụng base image Nginx xây dựng trên nền tảng Alpine Linux siêu nhẹ (~43MB).

---

### Bước 2: Kiểm tra trạng thái hoạt động của Container

```bash
docker ps
```

#### Kết quả đầu ra Terminal:
```plaintext
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
5e82b7cf183b   nginx:alpine   "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:8080->80/tcp   my-web
```

---

### Bước 3: Gửi truy vấn kiểm tra trang web bằng `curl`

```bash
curl -i http://localhost:8080
```

#### Kết quả đầu ra Terminal:
```plaintext
HTTP/1.1 200 OK
Server: nginx/1.27.2
Date: Tue, 13 Oct 2026 09:05:20 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Wed, 02 Oct 2024 13:48:32 GMT
Connection: keep-alive
ETag: "66fd4cb0-267"
Accept-Ranges: bytes

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

> **Kết quả đạt được:** Lệnh `curl` nhận phản hồi mã trạng thái `200 OK` kèm nội dung HTML chứa tiêu đề chuẩn: **"Welcome to nginx!"**.

---

### Bước 4: Dọn dẹp tài nguyên (Dừng và xóa Container)

```bash
docker stop my-web && docker rm my-web
```

#### Kết quả đầu ra Terminal:
```plaintext
my-web
my-web
```

#### Xác nhận dọn dẹp sạch sẽ:
```bash
docker ps -a --filter "name=my-web"
curl http://localhost:8080
```

#### Kết quả đầu ra Terminal:
```plaintext
$ docker ps -a --filter "name=my-web"
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

$ curl http://localhost:8080
curl: (7) Failed to connect to localhost port 8080: Connection refused
```

---

## 3. Bảng tổng hợp đối chiếu kết quả kiểm tra

| Hạng mục kiểm tra | Lệnh thực hiện | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
| :--- | :--- | :--- | :--- | :---: |
| **Image sử dụng** | `docker run ... nginx:alpine` | Dùng image `nginx:alpine` siêu nhẹ | Đã pull và chạy image `nginx:alpine` | **PASS** |
| **Tên Container** | `--name my-web` | Tên container là `my-web` | Container có tên `my-web` trong `docker ps` | **PASS** |
| **Ánh xạ cổng** | `-p 8080:80` | Host: 8080 -> Container: 80 | `0.0.0.0:8080->80/tcp` | **PASS** |
| **Chế độ ngầm** | `-d` | Trả về Container ID, không khóa shell | Trả về hash ID `5e82b7cf183b...` | **PASS** |
| **Phản hồi HTTP** | `curl http://localhost:8080` | Có chuỗi *"Welcome to nginx!"* | HTTP 200 OK, xuất hiện *"Welcome to nginx!"* | **PASS** |
| **Dọn dẹp container** | `docker stop && docker rm` | Dừng và xóa hoàn toàn container | Đã xóa, cổng 8080 giải phóng thành công | **PASS** |
