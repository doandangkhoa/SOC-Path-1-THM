---
title: Proxy
updated: 2026-04-26 04:33:01Z
created: 2026-04-26 04:31:32Z
latitude: 18.67958480
longitude: 105.68133330
altitude: 0.0000
---

# Phân loại

### 1\. External Proxy (Proxy Ngoài)

Đây là loại proxy phổ biến nhất, hoạt động như một trung gian giữa thiết bị của bạn và internet.

- **Cách hoạt động:** Khi bạn gửi yêu cầu truy cập web, nó sẽ đi qua máy chủ proxy bên ngoài trước khi đến đích.
- **Mục đích:** Giúp ẩn địa chỉ IP thực của bạn, truy cập nội dung bị chặn theo khu vực địa lý, hoặc tăng cường bảo mật bằng cách che giấu cấu trúc mạng nội bộ.
- **Đặc điểm:** Thường là máy chủ vật lý hoặc ảo đặt tại các vị trí địa lý khác nhau mà bạn có thể chọn.

### 2\. Internal Proxy (Proxy Nội bộ)

Loại proxy này được đặt bên trong mạng nội bộ (LAN) của một tổ chức hoặc doanh nghiệp.

- **Cách hoạt động:** Nó đóng vai trò cổng ra vào cho các máy tính trong mạng nội bộ khi truy cập internet.
- **Mục đích:**
    - **Quản lý:** Kiểm soát và ghi lại lượng truy cập internet của nhân viên.
    - **Bảo mật:** Lọc các trang web độc hại trước khi chúng đến máy tính của người dùng.
    - **Tối ưu hóa:** Lưu trữ (cache) các trang web thường xuyên truy cập để tăng tốc độ tải trang cho toàn mạng.

### 3\. Multi-hop Proxy (Proxy Đa Bậc)

Đây là kỹ thuật nâng cao nhằm tăng tính ẩn danh lên mức tối đa.

- **Cách hoạt động:** Thay vì đi qua một máy chủ proxy duy nhất, lưu lượng truy cập của bạn sẽ được chuyển tiếp qua một chuỗi các máy chủ proxy (ít nhất là 2 hoặc nhiều hơn) trước khi đến đích cuối cùng.
- **Mục đích:** Làm cho việc truy vết nguồn gốc của kết nối trở nên cực kỳ khó khăn. Ngay cả khi một máy chủ trong chuỗi bị xâm phạm, kẻ tấn công vẫn không thể biết được IP thực của bạn hoặc đích đến cuối cùng.
- **Ví dụ tiêu biểu:** Mạng Tor (The Onion Router) là một dạng phổ biến của multi-hop proxy.

### 4\. Domain Fronting

Đây không phải là một loại proxy theo nghĩa truyền thống, mà là một kỹ thuật để ẩn lưu lượng truy cập internet.

- **Cách hoạt động:** Kỹ thuật này lợi dụng cách các mạng phân phối nội dung (CDN) như Google, Amazon, hay Microsoft xử lý các yêu cầu HTTPS. Người dùng sẽ gửi yêu cầu với một tên miền (Host header) rất phổ biến và hợp lệ (ví dụ: `google.com`) để vượt qua các bộ lọc, nhưng trên thực tế lại trỏ đến một nội dung khác (ví dụ: một trang web bị chặn) bên trong cùng một CDN.
- **Mục đích:** Thường được sử dụng để vượt qua kiểm duyệt internet hoặc che giấu đích đến thực sự của lưu lượng dữ liệu. Tuy nhiên, các nhà cung cấp CDN lớn đã hạn chế mạnh mẽ kỹ thuật này do lo ngại về bảo mật và lạm dụng.

&nbsp;

# Bảng so sánh chi tiết các loại proxy

| Loại | Định nghĩa ngắn gọn | Mục đích chính | Mức độ ẩn danh | Ví dụ / Lưu ý |
| --- | --- | --- | --- | --- |
| **External Proxy** | Máy chủ trung gian bên ngoài kết nối bạn với internet. | ẩn IP, truy cập nội dung bị chặn địa lý. | Trung bình | Proxy HTTP/S, Residential Proxy. |
| **Internal Proxy** | Máy chủ nằm trong mạng nội bộ (LAN) của tổ chức. | Quản lý truy cập, lọc nội dung, tăng tốc (cache). | Thấp (trong mạng nội bộ) | Proxy công ty, school firewall. |
| **Multi-hop Proxy** | Chuyển tiếp lưu lượng qua nhiều máy chủ proxy liên tiếp. | Ẩn danh tối đa, khó truy vết nguồn gốc. | Rất cao | Mạng Tor, Chain Proxy. |
| **Domain Fronting** | Kỹ thuật ngụy trang lưu lượng qua tên miền hợp lệ để vượt kiểm duyệt. | Vượt chặn, ẩn đích đến thực sự. | Cao (nhưng đang bị hạn chế) | Lợi dụng CDN (Google, AWS). |