Lab 7: Xây dựng chính sách Web Protection trên mô hình pfSense + Web Proxy

Thông tin sinh viên:

Sinh viên: Nguyễn Quốc Yên (MSSV: 1150080164)

Lớp: 11-CNPM2

Video Demo: https://youtu.be/to1NTGV_j88


    Mục tiêu

 Xây dựng hệ thống bảo vệ và lọc nội dung web (Web Protection) bằng cách kết hợp Tường lửa pfSense và Máy chủ Web Proxy. Hệ thống đảm bảo mọi luồng truy cập từ người dùng (Client) phải đi qua Proxy để kiểm duyệt, đồng thời ngăn chặn tuyệt đối các hành vi vượt rào (bypass) ra Internet trực tiếp.

  Mô hình & Công nghệ

 pfSense Firewall: Đóng vai trò Gateway định tuyến, ép luồng dữ liệu mạng qua Proxy (Port 8080) và chặn truy cập web trực tiếp (Port 80, 443).

Web Proxy Server (Ubuntu Server 24.04):

Squid: Xử lý lưu lượng Proxy.

E2Guardian: Lọc nội dung, phân loại Category, kiểm soát theo URL/từ khóa.

ClamAV: Tích hợp quét virus tự động trên luồng tải về.

Môi trường phụ trợ: Domain Controller (Windows Server), DMZ Web Server (chứa web giả lập), Windows 11 (Client test).

 Kết quả cấu hình & Kiểm thử

Hệ thống đã triển khai và kiểm chứng thành công 6 kịch bản chính:

Truy cập hợp lệ (Baseline): Các trang giáo dục hoặc nằm trong danh sách trắng (Allow-list) truy cập bình thường.
 
Khóa cứng (Hard Block): Chặn tuyệt đối (Access Denied) các trang thuộc danh mục cấm (Geo-IP, Job Search, Game).

Cảnh báo & Vượt rào tạm thời (Temporary Bypass): Cảnh báo rủi ro với các trang nhạy cảm (Vũ khí, Tôn giáo) nhưng cho phép người dùng tự bấm xác nhận để truy cập tạm thời (5 phút).

Kiểm soát theo thời gian (Schedule): Cho phép truy cập nội dung Giải trí (Entertainment) đúng vào khung giờ quy định, ngoài khung giờ sẽ bị chặn.

Bảo vệ mã độc (Antivirus Scan): ClamAV phát hiện và chặn lập tức kết nối khi Client cố tải file chứa mã độc (Test bằng mẫu EICAR).

Chống Bypass (Anti-bypass): Khi người dùng tự ý tắt Proxy trên máy tính để đi trực tiếp, pfSense lập tức "Drop" gói tin (ERR_CONNECTION_TIMED_OUT), bảo vệ tính toàn vẹn của hệ thống lọc.
