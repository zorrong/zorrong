# Hướng Dẫn Xử Lý Lỗi Coolify + Cloudflare Tunnel (Step-by-Step)

Tài liệu này tổng hợp các bước khắc phục các lỗi phổ biến khi chạy Coolify phía sau Cloudflare Tunnel, bao gồm lỗi SSH Connection Refused, lỗi Redirect Loop, và lỗi Terminal WebSocket.

---

## 1. Lỗi SSH: "Connection refused 127.0.0.1:22"

**Triệu chứng:**
Khi coolify cố gắng kết nối SSH vào chính server (localhost) để deploy nhưng báo lỗi `connect to host 127.0.0.1 port 22: Connection refused`.

**Nguyên nhân:**
Coolify chạy trong Docker Container. Trong môi trường Container, `127.0.0.1` (localhost) là chính cái container đó, KHÔNG PHẢI là máy chủ (Host) của bạn.

**Cách khắc phục:**
1.  Truy cập giao diện Coolify.
2.  Vào mục **Servers** -> Chọn server **Localhost**.
3.  Tìm ô **IP Address**.
4.  Thay đổi từ `127.0.0.1` hoặc `localhost` thành:
    ```
    10.0.0.1
    ```
    *(Đây thường là địa chỉ Gateway mặc định của Docker Bridge, giúp container nhìn thấy máy chủ)*.
5.  Bấm **Validate Connection**.

---

## 2. Lỗi Redirect Loop (ERR_TOO_MANY_REDIRECTS)

**Triệu chứng:**
Khi truy cập vào tên miền Coolify (ví dụ `cp.pnftrading.com`), trình duyệt báo lỗi trang web chuyển hướng quá nhiều lần.

**Nguyên nhân:**
- **Coolify/Traefik:** Ép buộc sử dụng HTTPS.
- **Cloudflare Tunnel:** Gửi tín hiệu HTTP.
- -> Traefik thấy HTTP -> Yêu cầu chuyển sang HTTPS -> Gửi lại Cloudflare -> Cloudflare vẫn gửi HTTP -> Lặp vô tận.

**Cách khắc phục:**
Cấu hình Cloudflare Tunnel để gửi HTTPS ngay từ đầu.

1.  Vào **Cloudflare Zero Trust Dashboard** -> **Access** -> **Tunnels**.
2.  Chọn Tunnel đang chạy -> **Configure** -> **Public Hostname**.
3.  Edit Hostname của Coolify (ví dụ: `cp.pnftrading.com`).
4.  Cấu hình như sau:
    *   **Service Type:** `HTTPS`
    *   **URL:** `10.0.0.1:443` *(Dùng IP Docker Gateway : Port HTTPS)*
5.  Vào mục **TLS** (hoặc TLS Settings) ngay bên dưới.
6.  **No TLS Verify:** Chuyển sang **Enable** (Bật).
7.  **Save**.

---

## 3. Lỗi Terminal WebSocket (WebSocket error occurred)

**Triệu chứng:**
Vào được web Coolify nhưng khi mở Terminal của Container thì báo lỗi kết nối hoặc màn hình đen.

**Nguyên nhân:**
- Do chưa cấu hình đúng domain trong Coolify (khiến Traefik không định tuyến được WebSocket).
- Hoặc do Cloudflare Tunnel trỏ không đúng cổng (ví dụ trỏ vào 8000 thay vì qua Traefik 80/443).
- Hoặc do Cloudflare SSL chưa để mode Full.

**Cách khắc phục:**

**Bước 1: Cấu hình đúng Domain trong Coolify** (Rất quan trọng)
1.  Vào Coolify -> **Settings** -> **General**.
2.  Ô **Domain**: Điền chính xác domain có `https://` (ví dụ: `https://cp.pnftrading.com`).
3.  Bấm **Save**. *(Bước này kích hoạt Traefik định tuyến đúng cho cả Web và WebSocket)*.

**Bước 2: Kiểm tra Cloudflare SSL**
1.  Vào Dashboard quản lý domain chính (cloudflare.com).
2.  Vào **SSL/TLS**.
3.  Chọn chế độ **Full** (Không chọn Flexible, không chọn Off).

**Bước 3: Đảm bảo đã làm đúng phần 2 (Lỗi Redirect Loop)**
Nếu bạn đã chỉnh Tunnel trỏ về `HTTPS + 10.0.0.1:443` như mục 2, thì WebSocket cũng sẽ tự động hoạt động ngon lành.

---

## Tóm tắt cấu hình chuẩn cho Cloudflare Tunnel
| Thông số | Giá trị khuyên dùng |
| :--- | :--- |
| **Service Type** | `HTTPS` |
| **URL** | `10.0.0.1:443` |
| **No TLS Verify** | `Enabled` |
| **Coolify Instance Domain** | `https://your-domain.com` |
| **Cloudflare SSL Mode** | `Full` |

Lưu lại tài liệu này để lần sau setup server mới đỡ quên anh nhé! ^^
