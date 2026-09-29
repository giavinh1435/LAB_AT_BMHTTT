Lý Gia Vinh
1150080041
 LAB 4: KHẢO SÁT CỔNG VÀ ĐÁNH GIÁ AN TOÀN HỆ THỐNG (HARDENING)

Môi trường thực hành:** Windows 11 VM (VMware Workstation Pro)
Công cụ chính:** Nmap, Npcap, Windows Defender Firewall, PowerShell (Admin)
Mục tiêu:** Rà soát cổng dịch vụ (TCP/UDP), phân tích phản ứng giao thức mạng và đánh giá hiệu quả phòng thủ trước/sau khi Hardening.



1. Các nội dung đã thực hiện

Mục 5 & 6: Khảo sát cổng TCP & UDP (Localhost / Loopback)
TCP Connect (`-sT`):** Hoàn tất bắt tay 3 bước, xác định các cổng mở (`135`, `445`, `8080`).
TCP SYN Scan (`-sS`):** Quét Half-open (gửi SYN, nhận SYN/ACK, hủy bằng RST). Yêu cầu quyền Admin để gửi raw packet.
Inverse Scan (FIN `-sF` / Xmas `-sX`):** Kiểm chứng phản ứng theo RFC 793. Trên Windows, các cổng đều phản hồi `RST` $\rightarrow$ Nmap báo `closed`.
UDP Scan (`-sU`):** Quét cổng UDP phổ biến, ghi nhận cơ chế không hướng kết nối và trạng thái `open|filtered`.

Mục 8 & 9: Nhận diện Dịch vụ, Hệ điều hành & NSE Script
Service Version (`-sV`):** Nhận diện chính xác dịch vụ và phiên bản phần mềm (Python SimpleHTTP cổng `8080`, RPC/SMB cổng `135`/`445`).
OS Fingerprinting (`-O` / `-A`):** Phân tích TCP/IP stack để suy đoán nhân hệ điều hành Windows.
NSE SMB Discovery:** Dùng script `smb-os-discovery` và `smb2-security-mode` kiểm tra thông tin máy và chính sách ký gói tin SMB cổng `445`.

Mục 11: Thực nghiệm Phòng thủ (Before & After Hardening)
Before:** Quét cổng `8080` (HTTP Server) $\rightarrow$ trạng thái `open`.
Hành động Hardening:** Tạo Inbound Rule trên Windows Defender Firewall chặn cổng `8080`.
After:** Quét lại cổng `8080` $\rightarrow$ trạng thái chuyển từ `open` sang `filtered`.
Kết luận:** Chứng minh việc siết chặt chính sách tường lửa đã loại bỏ bề mặt tấn công của dịch vụ không cần thiết.



2. Danh mục tệp minh chứng (Evidence)

| Tên tệp | Mô tả nội dung |
| :--- | :--- |
| `H4_TCP_SYN_Scan.png` | Kết quả quét TCP SYN (`-sS`) hiển thị danh sách cổng mở |
| `H5_Service_Version.png` | Kết quả nhận diện phiên bản dịch vụ (`-sV`) |
| `H6_OS_Detection.png` | Kết quả suy đoán hệ điều hành Windows (`-O`) |
| `H7_NSE_SMB_Script.png` | Output chạy script NSE thu thập thông tin SMB |
| `H8_Hardening_Before.png` | Trạng thái cổng 8080 mở (`open`) trước khi cấu hình firewall |
| `H8_Hardening_After.png` | Trạng thái cổng 8080 bị lọc (`filtered`) sau khi áp rule chặn |
| `scan_result.txt` / `.xml` | Tệp xuất log toàn bộ phiên quét bằng Nmap |
| `evidence_sha256.csv` | Bảng mã băm SHA-256 bảo đảm tính toàn vẹn của minh chứng |