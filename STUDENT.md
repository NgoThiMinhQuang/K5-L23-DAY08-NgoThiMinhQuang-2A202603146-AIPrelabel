# Thông tin học viên

- Họ và tên: Ngo Thi Minh Quang
- MSHV: 2A202603146
- Lớp: L2
- Tài khoản GitHub: NgoThiMinhQuang
- Repo bài nộp: https://github.com/NgoThiMinhQuang/K5-L23-DAY08-NgoThiMinhQuang-2A202603146-AIPrelabel
- Commit bài nộp: theo commit mới nhất trên nhánh main của repo; kết quả và quyết định trình bày trong submission/REPORT.md.
- Máy: Windows, các model chạy CPU.
- CVAT Docker: 2.74.1, kiểm qua API /api/server/about ngày19/11/2026.

## Kết quả tóm tắt

- Máy: accepted495/621=79.7%; edited38/621=6.1%; deleted85/621=13.7%; added38/571=6.7%.
- Gold: precision máy42/92=45.7%; recall42/51=82.4%.
- Bắt bẫy hiện tại: **12/12=100%, vòng sửa có hỗ trợ**; log lịch sử giữ ở submission/qc/part4_round1 và part4_round2.
- Thời gian: batch_review120 phút/24 ảnh=5 phút/ảnh. Gold:15phút/4ảnh=3,75phút/ảnh (thời lượng tôi cung cấp bổ sung sau khi hoàn thành; không có log bấm giờ). Review5phút/ảnh cao hơn33,3%; hai tập khác độ phức tạp.
- Mask: đúng3 vật từ điểm học viên chọn, SAM2.1 BaseCPU, số điểm4/6/4.
- Kết quả QC sau sửa: **0/5 ảnh lỗi=0.0%; 0/108 đối tượng lỗi=0.0%**. Img_003 được kiểm lại sau sửa; bốn ảnh khác giữ xác nhận vì nhãn không đổi. Vòng trước lưu riêng.
- Calibration:3 quyết định đã chốt, gồm CAL-02 phương ánB đã được chọn và ghi trong guideline v1.1.
- Quyết định: **Release bộ nhãn detection**, ngày20/11/2026, sau xác nhận ảnh sửa sạch và các kiểm tra đạt. Bộ cuối24 ảnh/584 annotation; không bao gồm chứng nhận chất lượng segmentation.
