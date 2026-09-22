# Báo cáo Lab 1: Bắt gói tin Telnet - SSH

## 1. Thông tin sinh viên
- **Họ và tên:** Nguyễn Quốc Yên
- **Mã số sinh viên:** 1150080164

## 2. Tên bài Lab
- Lab 1: Bắt gói tin Telnet - SSH

## 3. Nội dung đã thực hiện
- Cài đặt dịch vụ `inetutils-telnetd` trên máy ảo Ubuntu Server 26.04.1 LTS và sử dụng PuTTY từ Windows (Client) để kết nối cổng TCP/23.
- Sử dụng Wireshark bắt gói tin Telnet, phân tích phiên truyền dữ liệu plaintext và kiểm tra với mật khẩu phức tạp.
- Cài đặt và cấu hình dịch vụ `openssh-server` trên Ubuntu Server, kết nối bảo mật qua cổng TCP/22 bằng PuTTY.
- Bắt và phân tích gói tin SSH trên Wireshark, so sánh tính bảo mật với Telnet.
- Hoàn thành trả lời 11 câu hỏi lý thuyết và đối sánh an toàn thông tin giữa hai giao thức.
- Quay video minh chứng toàn bộ quá trình thực hành và đăng tải lên YouTube.

## 4. Kết quả thực hiện
- Kết nối thành công tới Server qua cả hai giao thức Telnet và SSH.
- **Telnet:** Wireshark bắt trọn 100% nội dung (username, password, command) ở dạng rõ (plaintext).
- **SSH:** Toàn bộ payload trao đổi được mã hóa an toàn, Wireshark chỉ quan sát được metadata (IP, Port, Packet Size).
- **Video thực hành:**
  - [Video minh chứng 1](https://youtu.be/nW_Sxt8pc64?si=t8LHsbRktgY3pFIa)
  - [Video minh chứng 2](https://youtu.be/BrhPMFcS4EM?si=-NrrrTckSCNjBEjd)

## 5. Lưu ý khi kiểm tra bài làm
- Môi trường thực hành: Ubuntu Server 26.04.1 LTS (Server) và Windows (Client/Attacker).
- Chi tiết các bước và hình ảnh TCP Stream được lưu trong file `Lab1.docx` cùng thư mục.
