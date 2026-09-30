# LAB 4: KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

**Thông tin sinh viên:**
- **Họ và tên:** Trần Phạm Thành Minh
- **MSSV:** 1150080148
- **Lớp:** 11_ĐH_THMT
1. Môi trường thực hành
Hệ điều hành máy thật (Host): Windows 11

Phần mềm ảo hóa: VMware Workstation

Máy Attacker (Kẻ tấn công): Ubuntu 26.04.1 LTS (giao diện dòng lệnh)

IP: 192.168.182.134

Công cụ sử dụng: Nmap phiên bản 7.98

Máy Target (Mục tiêu): Windows Server

IP: 192.168.182.129

2. Cách dựng môi trường
Khởi tạo và chạy 2 máy ảo (Ubuntu và Windows Server) trên VMware Workstation.

Thiết lập Network Adapter của cả 2 máy ảo về chế độ Host-Only để chúng nằm chung một dải mạng nội bộ độc lập (192.168.182.0/24).

Trên Ubuntu, cấp phát IP tĩnh (192.168.182.134) bằng dòng lệnh để đồng bộ dải mạng với Windows Server.

Kiểm tra kết nối hai chiều bằng lệnh ping để đảm bảo hệ thống mạng thông suốt trước khi tiến hành rà quét.

3. Các tình huống thực hiện & Kết quả
Tình huống 1 - Dò tìm Host (-sn): Quét toàn bộ dải mạng, phát hiện thành công máy mục tiêu 192.168.182.129 đang hoạt động. (Kết quả: PASS)

Tình huống 2 - Quét cổng TCP (-sT, -sS): Thực hiện quét kết nối (TCP Connect) và quét tàng hình (TCP SYN), xác định được các cổng đang mở: 135, 139, 445, 5985. (Kết quả: PASS)

Tình huống 3 - Quét cổng UDP (-sU): Kiểm tra các cổng UDP đặc thù, ghi nhận trạng thái open|filtered. (Kết quả: PASS)

Tình huống 4 - Định danh dịch vụ và HĐH (-sV, -O, -A): Trích xuất chi tiết tên và phiên bản dịch vụ (Microsoft Windows RPC, NetBIOS, HTTPAPI). Phân tích được lý do hệ điều hành ẩn danh không trả về kết quả OS chính xác. (Kết quả: PASS)

Tình huống 5 - Kiểm tra lỗ hổng bằng Nmap Scripting Engine (NSE): Chạy kịch bản smb-os-discovery thu thập thông tin SMB và smb-vuln-ms17-010 để đánh giá rủi ro. Kết luận hệ thống không tồn tại lỗ hổng EternalBlue. (Kết quả: PASS)

Tình huống 6 - Trích xuất báo cáo: Lưu output thành công dưới dạng Normal Text (ket_qua.txt) và định dạng Grepable (smb.txt). (Kết quả: PASS)

4. Lỗi gặp phải & Phương án khắc phục
Lỗi 1: Ubuntu không nhận mạng (Network is unreachable)

Nguyên nhân: Xung đột IP hoặc card mạng chưa được cập nhật cấu hình định tuyến (routing table) khi chuyển sang Host-Only.

Khắc phục: Dùng lệnh sudo ip addr flush dev ens33 để làm sạch IP cũ, gán lại IP tĩnh bằng sudo ip addr add 192.168.182.134/24 dev ens33, và kích hoạt lại card mạng.

Lỗi 2: Nmap không tìm thấy cổng mở hoặc báo "Host seems down"

Nguyên nhân: Windows Firewall trên máy Target mặc định chặn các luồng Ping (ICMP) và các luồng quét cổng từ bên ngoài.

Khắc phục: Truy cập PowerShell trên Windows Server, chạy lệnh Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False để tạm thời vô hiệu hóa tường lửa phục vụ bài Lab.

Lỗi 3: Khó khăn khi trích xuất file log từ máy ảo CLI ra Windows Host

Nguyên nhân: Môi trường TTY của Ubuntu không cho phép bôi đen copy/paste chéo ra ngoài, đồng thời các nỗ lực dựng HTTP Server (python3 -m http.server) qua mạng Host-Only bị lỗi Connection Timed Out.

Khắc phục: In nội dung trực tiếp trên Terminal bằng lệnh cat ket_qua.txt / cat smb.txt và tiến hành sao chép, tạo file .txt tương ứng trên máy tính Host.