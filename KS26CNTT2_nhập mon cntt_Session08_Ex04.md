Họ và tên: Trần Nguyễn Kha  
**Bài 4 : Xây dựng quy trình lưu nhật ký hệ thống**  
Đề bài / Yêu cầu  
**1. Mục tiêu**  
Phân tích sự đánh đổi (Trade-off) giữa việc ghi đè (>) và ghi nối tiếp (>>) khi redirect output trong PowerShell.  
Thiết kế quy trình lưu trữ nhật ký hệ thống bền vững bằng PowerShell.  
**2. Bối cảnh & Vấn đề**  
An đang viết kịch bản để lưu báo cáo lỗi hàng ngày. An phân vân giữa hai câu lệnh PowerShell:  
Get-Content server.log | Select-String "ERROR" > daily_report.txt  
Get-Content server.log | Select-String "ERROR" >> daily_report.txt  
**3. Ràng buộc & Bẫy dữ liệu**  
Bẫy: Điều gì xảy ra nếu An gõ nhầm > thay vì >> vào cuối tháng khi báo cáo đã tích lũy dữ liệu của 30 ngày trước đó? (Liên hệ trực tiếp kiến thức Lesson 03.)  
Kiến thức mở rộng: Trong PowerShell, > thực chất là bí danh (alias) của lệnh Out-File, còn >> tương đương Out-File -Append. Một điểm khác biệt cần lưu ý: Out-File mặc định ghi tệp theo encoding UTF-16, trong khi CMD (echo >) thường ghi theo ANSI. Nếu mở nhầm công cụ đọc file không hỗ trợ UTF-16, nội dung có thể hiển thị sai ký tự. Ngoài ra, PowerShell còn có lệnh Add-Content, công dụng tương tự >> (ghi nối vào cuối tệp) nhưng cho phép chỉ định encoding rõ ràng, ví dụ Add-Content -Path daily_report.txt -Value $ket_qua -Encoding utf8.  
**4. Yêu cầu đầu ra**  
**P** **hần ** **1: So sánh**  
**Lập bảng so sánh hai toán tử về:**  
**Cơ chế hoạt động trong PowerShell.**  
**Rủi ro mất dữ liệu.**  
**Ngữ cảnh sử dụng thực tế (Ca sáng vs Theo dõi 24/7).**  
**Encoding file đầu ra (dựa trên phần "Kiến thức mở rộng" ở mục 3).**  
| | | |  
|-|-|-|  
| **Tiêu chí** | **Toán tử ghi đè (>)** | **Toán tử ghi nối tiếp (>>)** |   
| Cơ chế hoạt động | Tạo tệp mới hoặc ghi đè hoàn toàn nội dung cũ | Nối tiếp kết quả mới vào cuối tệp hiện có |   
| Rủi ro mất dữ liệu | Rất cao | Thấp |   
| Ngữ cảnh sử dụng | Báo cáo theo ca độc lập | Theo dõi, tích lũy log liên tục 24/7 |   
| Encoding file đầu ra | Mặc định UTF-16 (qua *Out-File*) | Mặc định UTF-16(qua *Out-File -Append*) |   
   
**Phần 2: Lựa chọn**  
**Chốt giải pháp tối ưu cho việc ghi nhật ký lỗi phát sinh liên tục trong ngày và lập luận lý do. Có thể đề xuất thêm cách dùng Add-Content hoặc Out-File -Append -Encoding utf8 nếu phù hợp.**  
-Xử lý bẫy: Gõ nhầm *>* vào cuối tháng sẽ làm mất toàn bộ lịch sử log của 30 ngày trước đó, chỉ giữ lại lần chạy cuối.  
-Giải pháp tối ưu: Sử dụng lệnh *Add-Content* để chủ động kiểm soát bảng mã UTF-8, tránh lỗi hiển thị font chữ:  
*PowerShell*  
*Get-Content server.log | Select-String "ERROR" | Add-Content -Path daily_report.txt -Encoding utf8*  
