Lý Gia Vinh
1150080041
# LAB 3: IDENTIFYING AND RESPONDING TO INFORMATION SECURITY THREATS

Học phần: An toàn và Bảo mật Thông tin  
Môi trường thực hành: Máy ảo Windows 11 25H2 (Build 26200.9445) trên VMware Workstation Pro  
Chế độ mạng: Host-only (chuyển tạm NAT khi thử nghiệm bắt gói HTTPS)

1. Mục tiêu bài thực hành
Nhận diện, phân tích các mối đe dọa an toàn thông tin phổ biến trên hệ điều hành Windows.
Cấu hình và thu thập nhật ký bảo mật hệ thống (Security Audit Policy, Sysmon, Event Viewer).
Kiểm tra mã độc mẫu (EICAR), phân tích xác thực và xâm nhập tài khoản cục bộ.
Phân tích và phát hiện cơ chế duy trì xâm nhập (Persistence Mechanisms) bằng Sysinternals Autoruns & Process Explorer.
Giám sát, phân tích lưu lượng mạng mã hóa (TLS/HTTPS) và không mã hóa (HTTP) bằng Wireshark.
Mô phỏng và phân tích dấu hiệu tấn công từ chối dịch vụ (DoS/DDoS), Mail Bombing và Social Engineering/Phishing.



2. Cấu trúc thư mục Lab (`C:\LAB3`)

```text
C:\LAB3
├── Assets/ (hoặc lab3_assets/)
│   ├── data/
│   │   ├── ddos_sample.csv           # Dữ liệu phân tích lưu lượng DDoS
│   │   └── mailbomb_sample.csv       # Dữ liệu phân tích log Mail Bombing
│   ├── samples/
│   │   ├── eicar.com.txt             # Chuỗi thử nghiệm mã độc EICAR
│   │   ├── phishing_email.txt        # Mẫu email lừa đảo trích xuất header
│   │   └── social_engineering_cases.csv
│   ├── scripts/
│   │   └── local_load_test.py        # Kịch bản phát tải DoS cục bộ tới port 8080
│   ├── sysmon-lab.xml                # Tệp cấu hình quy tắc Sysmon
│   └── www/                          # Thư mục chứa tài nguyên web phục vụ HTTP Server
├── Downloads/                        # Chứa gói nén LAB3_Threats_Assets.zip
├── Evidence/                         # Chứa toàn bộ tệp nhật ký (.txt) và ảnh chụp minh chứng (.png)
└── Tools/
    ├── ProcessExplorer/              # Bộ công cụ Process Explorer (procexp64.exe)
    ├── SysinternalsSuite/            # Bộ công cụ Sysinternals
    └── Sysmon/                       # Sysmon64.exe
3. Các kịch bản & Tình huống thực hiện
Tình huống 1 (TH1): Thiết lập môi trường & Lấy baseline
Tạo cấu trúc cây thư mục chuẩn tại C:\LAB3.

Ghi nhận mốc thời gian bắt đầu (Evidence\start_time.txt).

Thu thập baseline hệ thống: Thông tin OS, trạng thái Windows Defender, Windows Firewall, danh sách tiến trình và dịch vụ mạng ban đầu.

Tình huống 2 (TH2): Phát hiện mã độc (Malware Detection)
Thử nghiệm chuỗi mẫu EICAR (eicar.com.txt).

Kích hoạt cơ chế phát hiện của Windows Defender / Antivirus.

Thu thập nhật ký xử lý mối đe dọa của Defender.

Tình huống 3 (TH3): Phân tích xác thực & Nhật ký bảo mật (Authentication Logging)
Tạo tài khoản người dùng cục bộ kiểm thử: lab3user.

Bật chính sách kiểm toán đăng nhập:

auditpol /set /category:"Logon/Logoff" /subcategory:"Logon" /success:enable /failure:enable

Mô phỏng hành vi đăng nhập sai thông tin qua runas /user:.\lab3user cmd.exe.

Lọc và trích xuất sự kiện trong Security Log:

Event ID 4624: Đăng nhập thành công.

Event ID 4625: Đăng nhập thất bại (Audit Failure).

Event ID 4648: Đăng nhập bằng thông tin xác thực tường minh.

Đổi mật khẩu tài khoản và kiểm chứng lại quyền truy cập.

Tình huống 4 (TH4): Cài đặt Sysmon & Phát hiện duy trì xâm nhập (Persistence)
Cài đặt dịch vụ giám sát hệ thống Sysmon kèm file cấu hình:

Sysmon64.exe -accepteula -i C:\LAB3\lab3_assets\sysmon-lab.xml

Kiểm tra nhánh ghi nhật ký: Applications and Services Logs > Microsoft > Windows > Sysmon > Operational (Event ID 1: Process Creation).

Thiết lập cơ chế duy trì quyền truy cập (Persistence):

Khóa Registry Run: HKCU\...\CurrentVersion\Run / HKLM\...\CurrentVersion\Run mang tên LAB3_Run_Demo trỏ tới notepad.exe.

Tác vụ lịch trình: Scheduled Task LAB3_Persistence_Demo.

Dùng công cụ Autoruns để phát hiện các mục khởi động bất thường.

Khởi chạy dịch vụ web nội bộ (python -m http.server 8080 --bind 127.0.0.1) và dùng Process Explorer để định danh tiến trình, số PID và kiểm tra tính hợp lệ của dịch vụ.

Tình huống 5 (TH5): Bắt và so sánh lưu lượng HTTP vs HTTPS
Bắt gói tin trên giao diện Loopback (Adapter for loopback traffic capture) bằng Wireshark.

Gửi yêu cầu HTTP không mã hóa chứa tham số nhạy cảm (TRAINING_ONLY) -> Đọc được dữ liệu bản rõ (Plaintext) tại Layer 7.

Chuyển mạng sang NAT, bắt gói trên card Ethernet với giao thức mã hóa HTTPS (https://example.com/).

So sánh tính bảo mật: HTTPS bảo vệ dữ liệu bằng TLS (Application Data mã hóa), chống nghe lén thông tin. Trả máy ảo về lại card mạng Host-only.

Tình huống 6 (TH6): Phân tích DoS, DDoS & Mail Bombing
Chạy script mô phỏng quá trình tạo tải DoS cục bộ: local_load_test.py nhắm vào 127.0.0.1:8080.

Phân tích tệp log tấn công phân tán ddos_sample.csv qua PowerShell: Nhóm tần suất theo IP nguồn (SourceIP) để nhận diện mạng botnet.

Phân tích nhật ký thư rác mailbomb_sample.csv: Thống kê số lượng thư bất thường từ bulk-sender@example.invalid và tổng dung lượng thư gửi đến (SizeBytes).

Tình huống 7 (TH7): Phân tích Social Engineering & Phishing
Đọc nội dung email mẫu phishing_email.txt, phân tích các chỉ số giả mạo (Sender Header, Display Name, URL chuyển hướng, lời kêu gọi khẩn cấp).

Đánh giá bảng kịch bản tấn công xã hội trong social_engineering_cases.csv và đưa ra khuyến nghị phòng ngừa.