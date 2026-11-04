PIXELFORGE STUDIO — SECURITY ARCHITECTURE REPORT

BÁO CÁO PHÂN TÍCH VÀ ĐÁNH GIÁ CHỌN LỰA BẢO MẬT 2FA
CHO HẠ TẦNG GITHUB ENTERPRISE & CỔNG THANH TOÁN
PHẦN 1: PHÂN TÍCH 2 GIẢI PHÁP XÁC THỰC DANH TÍNH SỐ
1/ Phân tích 3 lớp yếu tố xác thực & Vai trò trong 2FA
Trong an toàn thông tin, xác thực danh tính số dựa trên 3 lớp nền tảng độc lập:
Something You Know : Thông tin bí mật được lưu trữ trong trí nhớ của người dùng. Ví dụ: Mật khẩu, mã PIN, Master Passphrase, câu hỏi bảo mật.
Something You Have: Vật thể vật lý hoặc thiết bị số độc bản mà người dùng sở hữu tại thời điểm xác thực. Ví dụ: Ứng dụng Authenticator, Chìa khóa FIDO2/YubiKey, điện thoại nhận tin nhắn, thẻ thông minh.
Something You Are: Các đặc trưng sinh học hoặc đặc điểm hành vi duy nhất gắn liền với cơ thể con người. Ví dụ: Cảm biến vân tay, quét khuôn mặt 3D, mống mắt.
2/ Nguyên lý hoạt động của Thuật toán 
Thuật toán TOTP sinh ra chuỗi 6 chữ số thay đổi mỗi 30 giây dựa trên hai thành phần cốt lõi:
- Khóa bí mật dùng chung: Chuỗi ký tự ngẫu nhiên được máy chủ tạo ra và gửi tới điện thoại thông qua mã QR khi quét kích hoạt 2FA. Khóa này được lưu an toàn trong bộ nhớ mã hóa của ứng dụng Authenticator.
- Dấu thời gian Unix: Thời gian thực tính bằng giây chia cho chu kỳ 30 giây: T = floor(Current Unix Time / 30).
Công thức tính toán: TOTP = HMAC-SHA1
PHẦN 2: LẬP BẢNG SO SÁNH TRADE-OFF & RA QUYẾT ĐỊNH BAN GIÁM ĐỐC
1/ Bảng so sánh chi tiết giữa SMS OTP và Authenticator TOTP
Tiêu chí đánh giá	Giải pháp 1: SMS OTP	Giải pháp 2: TOTP Authenticator
1. Khả năng chống SIM Swapping & Nghe lén SS7	- Rất kém
- Dễ bị tấn công SIM Swapping qua Social Engineering đối với nhà mạng.
- Mã OTP bị đánh chặn trên mạng di động qua lỗ hổng giao thức SS7.	- Tuyệt đối an toàn
- Khóa Shared Secret lưu an toàn trên chip điện thoại.
- Miễn nhiễm hoàn toàn trước tấn công viễn thông và đánh cắp SIM từ xa.
2. Tính sẵn sàng khi Offline / Nước ngoài	- Bị hạn chế mạnh
- Phụ thuộc hoàn toàn vào sóng di động.
- Khi bay hoặc đi công tác nước ngoài thường mất sóng/roaming đắt đỏ.	- Sắn sàng toàn bộ
- Hoạt động hoàn toàn Offline mọi lúc mọi nơi.
- Sinh mã chính xác trên máy bay, trong hầm tàu điện hoặc khi đi nước ngoài.
3. Độ tiện dụng & Chi phí vận hành	- Tiện dụng nhưng tốn cước
- Quen thuộc với người dùng phổ thông.
- Doanh nghiệp tốn chi phí gửi SMS viễn thông cho mỗi lượt đăng nhập.	- Tối ưu cả tiện dụng lẫn chi phí
- Thao tác quét mã/lấy mã nhanh chóng.
- Miễn phí hoàn toàn vận hành cho PixelForge Studio.
4. Rủi ro khi mất điện thoại	- Rủi ro cao
- Dễ bị kẻ xấu trộm SIM gắn sang máy khác để lấy mã.
- Quy trình cấp lại SIM có rủi ro bị mạo danh.	- Quản lý an toàn qua backup
- Cần bộ Mã Khôi phục hoặc tính năng Cloud Sync mã hóa để lấy lại tài khoản.
2/ Kết luận lựa chọn & Đề xuất trình Ban Giám đốc PixelForge Studio
QUYẾT ĐỊNH BẮT BUỘC: Lựa chọn GIẢI PHÁP 2 
- Triệt tiêu rủi ro SIM Swapping đối với tài khoản trọng yếu: Tài khoản GitHub Enterprise chứa mã nguồn sản phẩm trị giá hàng trăm ngàn USD và kết nối Cổng thanh toán Early Access. Hacker có thể lừa nhà mạng cấp lại SIM của Nhà sáng lập/Lead Dev để cướp SMS OTP. TOTP loại bỏ hoàn toàn nguy cơ này vì Secret Key nằm trên thiết bị
- Đảm bảo tính liên tục vận hành cho kỹ sư đi công tác quốc tế: Đội ngũ kỹ sư thường xuyên tham dự GDC tại Mỹ/Châu Âu hoặc làm việc trên chuyến bay ngắt sóng di động. TOTP giúp kỹ sư truy cập kho mã nguồn 24/7 mà không phụ thuộc vào cước phí chuyển vùng di động hay sóng viễn thông.
- Tối ưu ngân sách doanh nghiệp: Loại bỏ toàn bộ chi phí cước tin nhắn SMS viễn thông biến đổi theo hàng tháng, giúp studio dành toàn bộ ngân sách cho hạ tầng game server.
PHẦN 3: QUẢN LÝ RỦI RO VÀ MÃ KHÔI PHỤC 
Mục đích, Phương pháp lưu trữ an toàn & Sai lầm cần tránh đối với Backup Codes
a) Mục đích của bộ 10 Mã Khôi phục:
Mã Khôi phục là bộ 10 mã số xác thực dùng một lần được cấp khi kích hoạt 2FA. Bộ mã này đóng vai trò là 'chìa khóa dự phòng khẩn cấp' giúp nhân viên khôi phục quyền truy cập vào tài khoản GitHub Enterprise khi bị mất điện thoại, hỏng thiết bị, đổi máy mới chưa kịp chuyển app Authenticator hoặc lỡ xóa ứng dụng.
b) 2 Vị trí lưu trữ an toàn tuyệt đối cho nhân viên:
- Vị trí 1: Trình quản lý mật khẩu an toàn chuẩn Zero-Knowledge : Lưu dưới dạng Secure Note được mã hóa AES-256 bit. Khi truy cập từ thiết bị mới, nhân viên chỉ cần mở Master Passphrase để lấy lại mã.
- Vị trí 2: In bản giấy vật lý cất trong Két sắt / Ngăn khóa cá nhân: Bản in giấy hoàn toàn không kết nối internet, miễn nhiễm 100% trước hacker, mã độc Infostealer hay tấn công mạng từ xa.
PHẦN 4: HƯỚNG DẪN NỘP BÀI NGHỆP VỤ VÀ CẤU TRÚC REPOSITORY GITHUB
Để hoàn tất yêu cầu nộp bài, toàn bộ báo cáo phân tích và file tài liệu sẽ được đưa lên repository GitHub của PixelForge Studio theo cấu trúc chuẩn sau:
pixelforge-2fa-security/
├── README.md                           # Báo cáo phân tích đầy đủ (Markdown format)
├── docs/
│   └── Bao_Cao_Phan_Tich_2FA_PixelForge.docx # File báo cáo Word gửi Ban Giám đốc
└── configs/
    └── 2fa_enforcement_policy.json     # File cấu hình bắt buộc 2FA cho GitHub Org
