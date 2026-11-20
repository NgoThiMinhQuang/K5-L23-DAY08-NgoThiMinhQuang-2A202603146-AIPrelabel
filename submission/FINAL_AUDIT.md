# Đối chiếu rubric — cập nhật20/11/2026

Bảng kiểm kê bằng chứng, không phải điểm giảng viên chấm. Kết quả bẫy Part4 là12/12=100% ở vòng sửa có đối chiếu đáp án. Nhãn trướcQC và lịch sử được giữ nguyên.

| Part | Yêu cầu rubric | Trạng thái | Bằng chứng | File đã hoàn thiện | Việc còn cần thực hiện/lưu ý |
|---|---|---|---|---|---|
| 0 | Cài đặt/tái lập/Git (một phần hạng mục8đ) | Môi trường đạt; hồ sơ và các commit đã được đẩy main | check_env exit0; public repo; L2; CVAT2.74.1; remote main đã được đối chiếu | README.md; STUDENT.md; evidence/check_env_original.txt | Kiểm quy ước tên repo của lớp; chưa tự đổi tên remote. |
| 1 | Gold thủ công và contract10đ | Nhãn hợp lệ; có thời lượng tự báo cáo15phút | 4ảnh/51annotation; time_log15phút do học viên cung cấp sau khi hoàn thành, không có log bấm giờ | labels/gold_manual.json; time_log.csv; REPORT mục2/8 | Giữ đúng nguồn tự báo cáo; không coi là bằng chứng đã bấm giờ tại thời điểm làm. |
| 2 | Tour >=4runs, >=3model, SAM; recipe20đ | Đủ log, đầu ra và phần recipe/nhận xét trong REPORT | 6runs r01–r06 khớp log gốc, gồm SAM2.1 | model_log.csv; evidence/model_runs_original.csv; evidence/runs/; REPORT mục1 | Đọc lại nhận xét/recipe và bổ sung ý kiến cá nhân nếu cần |
| 3 | Ngưỡng theo lớp8đ | Đủ sản phẩm kỹ thuật | threshold_sweep.csv/md/png; motorbike0.10, three_wheeler0.15 | qc/threshold_sweep.*; REPORT mục1 | Xác nhận hiểu giới hạn4ảnhGold và chọn/chấm cùng mẫu |
| 4 | Review, bẫy, AI error profile28đ | Đủ sản phẩm vòng sửa; không biến thành phép đo độc lập | 24ảnh/586annotation;120phút;12/12bẫy vòng sửa có hỗ trợ | labels/batch_reviewed.json; qc/review_stats.*; qc/part4_round*/; REPORT mục2–4 | Không có thao tác kỹ thuật cần chạy lại; giữ nguồn gốc kết quả |
| 5 | >=3ảnhmask; phân tích người/hàng/biên8đ | Đủ3mask từ điểm học viên, có phân tích trong REPORT | SAM2.1CPU; điểm4/6/4; exit0; ảnh đọc được | masks/3JPG hiện tại; masks/logs/; REPORT mục5 | Segmentation chưa có Gold đo IoU; Release hiện tại chỉ áp dụng detection. |
| 6a | Calibration>=3quyếtđịnh và guideline10đ | Đủ3quyếtđịnh đã chốt và guidelinev1.1 | CAL-01/CAL-03 xác nhận trực tiếp; CAL-02B chấp nhận đề xuất kỹ thuật | qc/calibration_decisions.csv; calibration_candidates.*; guidelines/guideline_v1.1_calibrated.md | Kiểm mask/nhãn khi áp dụngB, sửa thật nếu cần. |
| 6b | QC, release và báo cáo8đ | Đã sửaQC, kiểm ảnh mới và chốtRelease detection | 24ảnh/584annotation; mẫu5ảnh/108vật,0lỗi; học viên xác nhận img003sạch,4ảnhkhácnhãnkhôngđổi | labels/qc_fixed.json; qc/qc_fixed_round2/; qc/qc_report_auto.md; release/; REPORT mục7 | Đã đồng ý Release có điều kiện, các kiểm tra đã đạt; vẫn lưu giới hạn mẫu nhỏ. |

## Kết quả sau sửaQC

Chỉimg_003 thay đổi trong export mới. Đã tách1ảnh/22annotation nguyên bản thànhqc_fixed.json, không tạo nhãn. Release24ảnh/584annotation khớp export đủ24ảnh theo ảnh/lớp/box; manifestghiđúngimg_003đượcthay. Gold/nhãnbatchtrướcsửa/bẫy/mask/loggiữnguyên.

Học viên xem ảnhQCmới và xác nhậnimg_003sạch, đồngýRelease detection nếukiểmtrađạt. BốnảnhkhácgiữQCtrướcvìgeometry/lớpkhôngđổi. QC0/108đốitượnglỗi và0/5ảnhlỗi;scriptgợiýRELEASE. Đây không phảiQCđộclậpmới, mẫu5ảnhnhỏ, Wilson95%cậntrên43%; không khẳngđịnhtoànbộlôlỗithậtdưới2%.

Validation vàcheck_submission.py:0lỗi/0cảnhbáo ởkiểmtracấutrúcbàinộp; validate_labels còn15cảnhbáohìnhhọcđãlưulog. Gold15phútdohọcviêncungcấpsaukhihoànthành,khôngcólogbấmgiờ,khôngtựcoilàbằngchứngđođươngthời.

## Phạm vi quyết định

**Release nhãn detection, ngày20/11/2026.** Không tựtạorelease/tagGitHub,khôngchứngnhậnmasksegmentationhoànhảo. BằngchứngvòngQCcũởqc/qc_before_fixed_20261120;vòngmớiởqc/qc_fixed_round2;quyếtđịnhvàkiểmtraởqc/release_verification.json.

Repođãpublic. CầnkiểmquyướctênrepocủalớpvìtênhiệntạiđặtHoTentrướcMSHV,khácmẫumặcđịnhSUBMISSION. Cáccommitchuẩnbịkhôngbackdate;khôngtuyênbốlịchsửđượctạotrongquátrìnhlàmtừngPart. Giữnguồn/thôngtincá nhânđúngthựctế.

## Rà soát toàn thư mục ngày20/11/2026

Đã quét218file được Git quản lý tại thời điểm rà:24filePython,37JSON/notebook,17CSV,78ảnh và38Markdown. Không thấy lỗi cú pháp, JSON/CSV, tham chiếuCOCO, ảnh hỏng hoặc liên kết nội bộ bị gãy. 12/12test hồi quy qua; môi trường sẵn sàng và CVATHTTP200. Đã sửa trạng thái cũ ở tài liệu mask/calibration và đồng bộ bảng đếm lớp preflight. Báo cáo chi tiết: qc/folder_audit_20261120.json. Các thư viện trong .venv, trọng sốmodels và bản sao tạmwork là dữ liệu cục bộ, không nằm trong phạm vi sửa mã nguồn hoặc commit.
