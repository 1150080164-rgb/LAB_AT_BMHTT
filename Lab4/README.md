BÁO CÁO LAB 4 - KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

I. THÔNG TIN SINH VIÊN

Họ và tên: Nguyễn Quốc Yên

Lớp: 11CNPM2

MSSV: 1150080164

Tên lab: Lab 4 - Khảo sát và đánh giá bề mặt mạng bằng Nmap.

II. PHIÊN BẢN VÀ CÁCH DỰNG MÔI TRƯỜNG

Phiên bản môi trường: VMware, Máy ảo Ubuntu 64-bit (đóng vai trò máy quét), Máy ảo Metasploitable 2 (đóng vai trò máy đích chứa lỗ hổng).

Cách dựng môi trường:

Thiết lập hai máy ảo chạy song song trên VMware.

Cấu hình Network Adapter của cả hai máy về chung một dải mạng (Host-only/NAT) để đảm bảo giao tiếp nội bộ an toàn, tuân thủ nguyên tắc không Bridge máy Metasploitable 2 ra mạng public.

Cập nhật hệ thống và cài đặt công cụ quét mạng Nmap trên máy ảo Ubuntu thông qua lệnh sudo apt install nmap.

Cấp phát và đồng bộ địa chỉ IP cho máy đích, đồng thời sử dụng lệnh ping từ máy quét để đảm bảo kết nối mạng thông suốt trước khi tiến hành rà soát.

III. CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN VÀ KẾT QUẢ

Thu thập IP: Xác định chính xác địa chỉ IP của máy quét (Ubuntu) qua lệnh ip -br addr và máy đích (Metasploitable 2) qua lệnh ifconfig (Ảnh 1 & 2) (Kết quả: PASS).

Tình huống 1: Host Discovery - Phát hiện các host đang hoạt động trong mạng nội bộ thông qua lệnh ping scan -sn (Ảnh 3) (Kết quả: PASS).

Tình huống 2: TCP Scan - Khảo sát và đánh giá trạng thái các cổng (open/closed/filtered) bằng kỹ thuật quét tàng hình SYN Scan -sS (Ảnh 4) (Kết quả: PASS).

Tình huống 3: Version Detection - Nhận diện chính xác tên và phiên bản của các dịch vụ phần mềm đang chạy trên các cổng mở thông qua cờ -sV (Ảnh 5) (Kết quả: PASS).

Tình huống 4: OS Fingerprinting - Phân tích các gói tin phản hồi để dự đoán hệ điều hành của máy mục tiêu bằng cờ -O (Ảnh 6) (Kết quả: PASS).

Tình huống 5: Nmap Scripting Engine (NSE) - Khai thác script smb-os-discovery trên cổng 445 để thu thập thông tin chuyên sâu về hệ điều hành và giao thức SMB (Ảnh 7) (Kết quả: PASS).

Tình huống 6: Báo cáo - Xuất toàn bộ kết quả quét ra tệp văn bản chuẩn .txt bằng cờ -oN để lập hồ sơ bằng chứng (Ảnh 8) (Kết quả: PASS).

IV. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

Lỗi 1: Máy quét không tìm thấy máy đích (Lỗi "Network is unreachable" / "Host seems down").

Nguyên nhân: Máy ảo Metasploitable 2 bị cấu hình thừa một Network Adapter gây xung đột DHCP (No DHCPOFFERS received), dẫn đến việc máy không nhận được IP. Sau khi sửa, hai máy lại rơi vào tình trạng lệch dải mạng (Ubuntu nhận dải 192.168.116.x trong khi Metasploitable 2 nằm ở dải 192.168.156.x).

Cách khắc phục: Đã tiến hành ngắt kết nối Network Adapter thừa trong VMware Settings. Sử dụng câu lệnh ép IP tĩnh trực tiếp trên Metasploitable 2: sudo ifconfig eth0 192.168.116.100 netmask 255.255.255.0 up. Sau đó dùng lệnh ping từ máy Ubuntu, kết quả trả về 0% packet loss, đảm bảo hai máy đã thông mạng hoàn toàn.

Lỗi 2: Lỗi cú pháp (Typo) khi thực thi lệnh Linux Terminal.

Nguyên nhân: Do thao tác bàn phím, gõ sai một số ký tự quan trọng (Ví dụ: gõ metmask thay vì netmask, gõ nm thay vì nmap, và đặc biệt là nhầm lẫn giữa chữ O in hoa với số 0 trong lệnh quét hệ điều hành -O).

Cách khắc phục: Đọc kỹ các luồng cảnh báo của Linux (như "unrecognized option '-0'" hoặc "command not found"). Tiến hành rà soát lại tham số, phân biệt rõ chữ hoa/chữ thường theo đúng tài liệu Lab và gõ lại lệnh thành công.
