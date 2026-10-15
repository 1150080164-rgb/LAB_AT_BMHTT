# BÁO CÁO LAB 6 - XÁC THỰC ACTIVE DIRECTORY VÀ CHÍNH SÁCH TRUY CẬP TRÊN PFSENSE

## I. THÔNG TIN SINH VIÊN
*   **Họ và tên:** Nguyễn Quốc Yên
*   **Lớp:** 11CNPM2
*   **MSSV:** 1150080164
*   **Tên lab:** Lab 6 - Xác thực Active Directory và Chính sách truy cập trên pfSense.

## II. PHIÊN BẢN VÀ CÁCH DỰNG MÔI TRƯỜNG
*   **Phiên bản môi trường:** VMware Workstation Pro, pfSense CE 2.7.2, Windows Server 2025, Windows 11.
*   **Cách dựng môi trường:**
    *   Thiết lập pfSense với 4 card mạng riêng biệt: WAN (Bridged/NAT), MGMT (Host-only 192.168.56.x), LAN (Internal/Host-only 10.0.0.x), và DMZ (LAN Segment 172.16.0.x).
    *   Sử dụng máy thật (IP 192.168.56.1) để truy cập WebGUI quản trị qua cổng MGMT (192.168.56.254) để tránh bị chặn khi cấu hình Captive Portal.
    *   Triển khai máy ảo Windows Server đóng 2 vai trò: vửa làm Client duyệt web, vừa đóng vai trò máy chủ IIS để giả lập các trang web đích (JOB_SITE, SOCIAL_SITE, RESTRICTED_SITE) bằng các IP phụ.
    *   Đồng bộ kết nối giữa pfSense và Domain Controller qua giao thức LDAP để lấy thông tin xác thực.

## III. CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN VÀ KẾT QUẢ
*   **Thiết lập nền tảng:** Kết nối thành công LDAP Server tới Domain Controller và bật Captive Portal trên interface LAN (Kết quả: PASS).
*   **Tình huống 1:** Tạo rule Block cấm riêng Client-A truy cập nhóm web việc làm (JOB_SITE - 172.16.0.20), duyệt web báo lỗi Timeout (Kết quả: PASS).
*   **Tình huống 2:** Tạo rule Block cấm toàn bộ nhóm Client truy cập web nội dung cấm (RESTRICTED_SITE - 172.16.0.40) (Kết quả: PASS).
*   **Tình huống 3:** Tạo rule Pass có gắn Schedule để chỉ cho phép Client truy cập mạng xã hội (SOCIAL_SITE - 10.0.0.30) trong giờ nghỉ. Ngoài lịch, tự động bị rule Block bên dưới chặn lại (Kết quả: PASS).
*   **Tình huống 4:** Tạo rule Pass chỉ cho phép dịch vụ web (HTTP 80, HTTPS 443) ra Internet, kết hợp rule Block cấm ping (ICMP) để giới hạn dịch vụ (Kết quả: PASS).
*   **Tình huống 5:** Cô lập vùng DMZ, tạo rule trên tab DMZ cấm mọi luồng dữ liệu khởi tạo từ DMZ (172.16.0.x) đâm ngược vào mạng nội bộ LAN (10.0.0.x). Kiểm tra ping thất bại 100% (Kết quả: PASS).

## IV. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC
*   **Lỗi 1:** Không thể truy cập WebGUI quản trị pfSense ở bước đầu tiên.
    *   **Nguyên nhân:** Máy tính thật chưa được cấu hình IP tĩnh để giao tiếp với cổng MGMT của pfSense.
    *   **Cách khắc phục:** Truy cập Network Connections (ncpa.cpl) trên máy thật, cấu hình IP tĩnh `192.168.56.1` cho card VMnet1. Trên pfSense, gán IP `192.168.56.254` cho cổng LAN tạm thời và ép mở giao thức HTTP qua màn hình Console. Sau đó truy cập thành công giao diện quản trị.
*   **Lỗi 2:** Trình duyệt vẫn vào được trang web dù đã tạo rule cấm (Block).
    *   **Nguyên nhân:** pfSense là tường lửa Stateful. Khi tạo rule mới, nếu máy trạm vẫn đang dùng phiên kết nối cũ chưa hết hạn, Firewall sẽ cho gói tin đi qua mà không kiểm tra lại rule.
    *   **Cách khắc phục:** Sau mỗi lần thay đổi hoặc tạo mới rule chặn, truy cập menu `Diagnostics > States > Reset States` để xóa toàn bộ bộ nhớ đệm kết nối. Sau khi Reset, Firewall áp dụng chính sách mới lập tức.
