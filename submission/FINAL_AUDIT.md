# Đối chiếu rubric và trạng thái chuẩn bị nộp — 19/11/2026

Đây là kiểm kê bằng chứng, không phải điểm giảng viên chấm. Dữ liệu, Gold, mask, log gốc được giữ nguyên. Kết quả bẫy hiện tại **12/12=100% ở vòng sửa có hỗ trợ**; lịch sử giữ riêng, không sửa số liệu.

| Part | Yêu cầu rubric | Trạng thái | Bằng chứng | File đã hoàn thiện/tổng hợp | Việc còn cần học viên xác nhận |
|---|---|---|---|---|---|
| 0 | Cài đặt/tái lập/Git (một phần hạng mục8đ) | Kỹ thuật có bằng chứng; Git/nộp chưa chốt | Environment exit0; public repo đã xác minh; L2/CVAT2.74.1 | README.md; STUDENT.md; evidence/check_env_original.txt | Kiểm tên repo và lịch sử commit theo yêu cầu lớp; duyệt trước push |
| 1 | Gold thủ công và contract10đ | Dữ liệu đạt kiểm tra; thiếu thời gian thật | 4 ảnh/51annotation; xác nhận học viên gán trước AI; Gold giữ nguyên | labels/gold_manual.json; time_log.csv; REPORT mục2/8 | Cung cấp phút Gold đã đo; nếu không đo thì khai thật, không tự tạo |
| 2 | Tour >=4runs, >=3model, SAM; recipe20đ | Đủ log/đầu ra; phần nhận xét cần học viên chịu trách nhiệm | 6runs r01–r06 khớp log gốc, gồm SAM2.1 | model_log.csv; evidence/model_runs_original.csv; evidence/runs/; REPORT mục1 | Đọc lại nhận xét/recipe và bổ sung ý kiến cá nhân nếu cần |
| 3 | Ngưỡng theo lớp8đ | Đủ sản phẩm kỹ thuật | threshold_sweep.csv/md/png; motorbike0.10, three_wheeler0.15 | qc/threshold_sweep.*; REPORT mục1 | Xác nhận hiểu giới hạn4ảnhGold và chọn/chấm cùng mẫu |
| 4 | Review, bẫy, AI error profile28đ | Đủ sản phẩm vòng sửa; không biến thành phép đo độc lập | 24ảnh/586annotation;120phút;12/12bẫy vòng sửa có hỗ trợ | labels/batch_reviewed.json; qc/review_stats.*; qc/part4_round*/; REPORT mục2–4 | Không có thao tác kỹ thuật cần chạy lại; giữ nguồn gốc kết quả |
| 5 | >=3ảnhmask; phân tích người/hàng/biên8đ | Đủ3mask từ điểm học viên; thực hành đã xác nhận | SAM2.1CPU; điểm4/6/4; exit0; ảnh đọc được | masks/3JPG hiện tại; masks/logs/; REPORT mục5 | Xác nhận nhận xét mask chi tiết và tính nhất quán với CAL-02 |
| 6a | Calibration>=3quyếtđịnh và guideline10đ | Chưa đủ quyết định | CAL-01/CAL-03 học viên chốt; CAL-02cau_hoi_mo còn mở | qc/calibration_decisions.csv; calibration_candidates.*; guidelines/guideline_v1.1_calibrated.md | Chốt/sửa đề xuấtCAL-02; không tự coi đề xuấtAI là quyết định |
| 6b | QC, release và báo cáo8đ | QC có xác nhận; chưa phê duyệt cuối | 5ảnh/110vật;3/110lỗi,1/5ảnhlỗi; scriptHOLD; candidate24ảnh/586annotation | qc/qc_review.csv; qc_report_auto.md; release/; REPORT mục7 | ChọnHold/Rework/Release theo số liệu; nếu sửa, thao tácCVAT/exportqc_fixed.json |

## Kiểm tra đã chạy

- validate_labels.py --all: exit0; Gold4ảnh/51annotation và batch24ảnh/586annotation hợp lệ;15cảnh báo cần đối chiếu guideline. Log qc/validation_all.txt.
- qc_report.py --guideline-gap1: exit0, dựa trên phiếu học viên đã xác nhận. Lỗi đối tượng3/110=2.7%; ảnh1/5=20.0%; Wilson95% cận trên62%; gợi ýHOLD vì CAL-02còn mở. Không tự phê duyệtRelease.
- check_submission.py: còn3lỗi do dữ kiện con người: minutesGold trống; calibration mới2quyếtđịnh; chưa có cau_hoi_mo đã chốt. Log qc/submission_check.txt. Không sửa validator để vượt các lỗi này.
- JSON/CSV trong submission đọc được, số cột CSV nhất quán; manifest/summary/lớp/box release khớp nguồn,3ảnhmask học viên đọc được. Không rerunmodel/threshold/review_stats/SAM.

## Git, file dư và thông tin cá nhân

Repo được GitHub API xác minh public. Tên hiện tại đặt HoTen trước MSHV, khác mẫu SUBMISSION.md là K5-L23-DAY08-[MSHV]-[HoTen]-AIPrelabel; cần học viên kiểm quy ước riêng lớp trước khi đổi tên trên GitHub. Không tự rename remote.

Đã kiểm các file tracked và sẽ thêm: không có work/, .venv/, models/, trọng số hoặc mẫu token/mật khẩu bị phát hiện. Không in giá trị nghi nhạy cảm trong báo cáo. Giữ data/ATTRIBUTION.csv và ảnh nguồn. Các bản export GoldZIP/thư mục lặp và hai demoSAM doAI chọn điểm trước kia được giữ tại máy nhưng thêm quy tắc ignore, không đưa vào bài như mask của học viên.

Tác giả Git đang cấu hình là tài khoản học viên. Có thể đóng gói thành3commit kỹ thuật hiện tại theo nhóm: Gold/log model/ngưỡng; batch review vàSAM; hồ sơQC/calibration/báo cáo. Không backdate hoặc mô tả các commit đóng gói hôm nay là lịch sử commit tại thời điểm học từngPart. Chưa push, chưa tag hoặc phát hành cuối.

## Danh sách xác nhận tối thiểu

1. CAL-02: chốt hoặc sửa đề xuất mask gồm hàng nằm trong giỏ/thùng cố định, loại hàng nhô ra và người; box giữ v1.0. Nếu quy tắc mới khác mask/nhãn hiện tại, học viên tự sửa và export kết quả thật.
2. Số phútGold đã đo. Nếu không đo, xác nhận khôngđo để báo cáo trung thực; không có giải pháp kỹ thuật hợp lệ để bịa thời gian cho validator.
3. Quyết định cuối: với guideline còn mở, scriptgợiýHOLD; sau khi chốt, ảnh lỗi20% vẫn vượt2%, cần xử lýRework hoặc báo cáoRework/Hold có lý do. Không tự chọnRelease.
4. Đọc nhận xétmask/recipe và bổ sung trải nghiệm thật; kiểm tênrepo theo lớp trước khi nộp.

## Lệnh tiếp theo

Sau khi có xác nhậnCAL-02 và kế hoạch xử lý nhãn, cập nhật guideline/CSV bằng quyết định thật. Nếu cóqc_fixed.json mới:

```powershell
.\.venv\Scripts\python.exe scripts/build_release.py
.\.venv\Scripts\python.exe scripts/qc_sample.py --n 5 --seed 42
```

Sao lưu phiếuQC đã điền trước khi dùng --force; không dùng kết quảQC cũ cho IDnhãn mới. Khi không còn câu hỏi guideline mới chạy qc_report.py với --guideline-gap0; nếu còn, dùng sốthật. Cuối cùng chạy check_submission.py. Khôngpush trước khi học viên đồng ý.
