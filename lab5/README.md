Bài Tập Thực Hành Lab 5: Thiết Lập Tường Lửa pfSense

**Thông tin sinh viên:**
- **Họ và tên:** Nguyễn Quốc Yên
- **Lớp:** 11-CNPM2
- **MSSV:** 1150080164
- **Môn học:** Chuyên đề Mạng / Quản trị Mạng


 Giới thiệu
Mục tiêu của bài Lab này là triển khai, cấu hình và quản trị tường lửa **pfSense** trên môi trường ảo hóa. Bài thực hành đi sâu vào việc thiết lập các vùng mạng (LAN, WAN, DMZ), cấu hình chính sách truy cập (Firewall Rules) và biên dịch địa chỉ (NAT) nhằm đảm bảo an ninh cho hệ thống mạng doanh nghiệp mô phỏng.

Môi trường & Công cụ
- **Nền tảng ảo hóa:** VMware Workstation Pro
- **Hệ điều hành Firewall:** pfSense CE 2.7.2 (FreeBSD 64-bit)
- **Hệ điều hành Client/Server:** Windows Server 2025, Ubuntu Server

Sơ đồ Mạng & Phân bổ IP (Topology)

| Tên Máy Ảo (VM) | Chức Năng | Thông số phần cứng | Card Mạng (Network Adapter) | Địa chỉ IP |
| :--- | :--- | :--- | :--- | :--- |
| **pfSense** | Firewall / Router | 2GB RAM, 2 vCPU, 20GB HDD | `WAN:` Bridged <br> `LAN:` Host-only <br> `DMZ:` LAN Segment | `LAN:` 10.0.0.1/8 <br> `DMZ:` 172.16.0.1/16 |
| **Domain Controller** | Server mạng nội bộ (LAN) | 2GB RAM | Host-only | 10.0.0.2/8 |
| **LAN-Test** | Client mạng nội bộ (LAN) | 1GB RAM | Host-only | 10.0.0.3/8 |
| **DMZ-Web** | Web Server vùng DMZ | 2GB RAM | LAN Segment (dmz-net) | 172.16.0.2/16 |

 Các Kịch Bản Thực Hành (Scenarios)

 1. Khởi tạo & Cấu hình nền tảng
- Gán IP tĩnh cho card mạng LAN thông qua giao diện Console (CLI).
- Đăng nhập vào WebGUI, vô hiệu hóa luật mặc định (`Default allow LAN to any`).
- Cấu hình Outbound NAT sang chế độ **Hybrid** để tự động điều phối lưu lượng.

 2. Tình huống 1: Chặn ICMP (Ping), cho phép Web/DNS
- **Mục tiêu:** Client trong LAN không thể Ping ra Internet nhưng vẫn lướt web bình thường.
- **Giải pháp:** Sử dụng cơ chế Top-Down của Firewall Rule. Tạo 3 rule theo thứ tự:
  1. `Block` ICMP (từ LAN ra Any).
  2. `Pass` TCP/UDP Port 53 (để truy vấn DNS).
  3. `Pass` TCP Port 80, 443 (để truy cập HTTP/HTTPS).

 3. Tình huống 2: Giới hạn truy cập theo Host cụ thể
- **Mục tiêu:** Chỉ cấp quyền ra Internet cho 1 máy duy nhất.
- **Giải pháp:** 
  - Tạo Rule `Pass` cho địa chỉ nguồn `10.0.0.2` (Domain Controller) đặt ở trên cùng.
  - Tạo Rule `Block` toàn bộ dải mạng `LAN net` đặt ở ngay bên dưới.

 4. Tình huống 3: Cô lập vùng DMZ khỏi LAN nội bộ
- **Mục tiêu:** Máy chủ Web đặt tại DMZ có thể ra Internet, nhưng không thể kết nối ngược vào LAN nhằm tránh rủi ro xâm nhập khi bị tấn công.
- **Giải pháp:** Tạo rule `Block` lưu lượng từ `DMZ net` đi đến đích là `LAN net`, đặt trên rule `Pass DMZ to Any`.

 5. Tình huống 4: Port Forwarding (NAT từ ngoài vào trong)
- **Mục tiêu:** Publish dịch vụ Web nội bộ cho người dùng trên Internet.
- **Giải pháp:** Tạo luật Port Forwarding (NAT). Trỏ lưu lượng đến từ IP WAN ở Port `8080` chuyển hướng vào Port `80` của máy chủ DMZ-Web (`172.16.0.2`).


Lưu ý kỹ thuật (Troubleshooting & Hardening)
1. **Clear States:** pfSense lưu cache các kết nối hiện tại. Vì vậy, **bắt buộc** phải thực hiện *Reset States* (trong menu Diagnostics > States) sau mỗi lần thêm/sửa Rule để luật mới có hiệu lực ngay lập tức.
2. **Thứ tự Rule (Top-Down):** pfSense đọc rule từ trên xuống dưới. Nếu gói tin khớp với rule ở
