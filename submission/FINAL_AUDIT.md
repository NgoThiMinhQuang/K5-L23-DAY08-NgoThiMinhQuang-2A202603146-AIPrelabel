# Đối chiếu rubric — cập nhật sau chốt CAL-02 và Rework

Ngày19/11/2026. Đây là kiểm kê bằng chứng, không phải điểm giảng viên chấm. Kết quả bẫy hiện tại12/12=100% ở vòng sửa có hỗ trợ. Gold, nhãn nguồn, mask và log gốc được giữ nguyên.

| Part | Yêu cầu rubric | Trạng thái | Bằng chứng | File đã hoàn thiện | Việc còn cần học viên thực hiện |
|---|---|---|---|---|---|
| 0 | Cài đặt/tái lập/Git (một phần hạng mục8đ) | Kỹ thuật có bằng chứng; Git/nộp chưa chốt | Environment exit0; public repo đã xác minh; L2/CVAT2.74.1 | README.md; STUDENT.md; evidence/check_env_original.txt | Kiểm tên repo và lịch sử commit theo yêu cầu lớp; duyệt trước push |
| 1 | Gold thủ công và contract10đ | Nhãn hợp lệ; học viên xác nhận không đo thời gianGold | 4 ảnh/51annotation; xác nhận học viên gán trước AI; Gold giữ nguyên | labels/gold_manual.json; time_log.csv; REPORT mục2/8 | Không có thời lượng để bổ sung; khai thiếu trung thực, không tự tạo. |
| 2 | Tour >=4runs, >=3model, SAM; recipe20đ | Đủ log/đầu ra; phần nhận xét cần học viên chịu trách nhiệm | 6runs r01–r06 khớp log gốc, gồm SAM2.1 | model_log.csv; evidence/model_runs_original.csv; evidence/runs/; REPORT mục1 | Đọc lại nhận xét/recipe và bổ sung ý kiến cá nhân nếu cần |
| 3 | Ngưỡng theo lớp8đ | Đủ sản phẩm kỹ thuật | threshold_sweep.csv/md/png; motorbike0.10, three_wheeler0.15 | qc/threshold_sweep.*; REPORT mục1 | Xác nhận hiểu giới hạn4ảnhGold và chọn/chấm cùng mẫu |
| 4 | Review, bẫy, AI error profile28đ | Đủ sản phẩm vòng sửa; không biến thành phép đo độc lập | 24ảnh/586annotation;120phút;12/12bẫy vòng sửa có hỗ trợ | labels/batch_reviewed.json; qc/review_stats.*; qc/part4_round*/; REPORT mục2–4 | Không có thao tác kỹ thuật cần chạy lại; giữ nguồn gốc kết quả |
| 5 | >=3ảnhmask; phân tích người/hàng/biên8đ | Đủ3mask từ điểm học viên; thực hành đã xác nhận | SAM2.1CPU; điểm4/6/4; exit0; ảnh đọc được | masks/3JPG hiện tại; masks/logs/; REPORT mục5 | Xác nhận nhận xét mask chi tiết và tính nhất quán với CAL-02 |
| 6a | Calibration>=3quyếtđịnh và guideline10đ | Đủ3quyếtđịnh đã chốt và guidelinev1.1 | CAL-01/CAL-03 xác nhận trực tiếp; CAL-02B chấp nhận đề xuất kỹ thuật | qc/calibration_decisions.csv; calibration_candidates.*; guidelines/guideline_v1.1_calibrated.md | Kiểm mask/nhãn khi áp dụngB, sửa thật nếu cần. |
| 6b | QC, release và báo cáo8đ | Đủ báo cáoQC, quyết địnhRework; chưaRelease | 3/110đối tượnglỗi,1/5ảnhlỗi;scriptREWORK;học viên chấp nhận | qc/qc_review.csv; qc_report_auto.md; release/; REPORT mục7 | SửaCVAT/exportqc_fixed nếu muốn cải thiện chất lượng;chưapush. |

## Kiểm tra cuối

Validation hợp lệ. Calibration đã đủ3dòng cóloi_may/cau_hoi_mo/bay_lot, guidelinev1.1 và taxonomyversionđã đồng bộ; ruleskhông đổi. Releasecandidate24ảnh/586annotation cógeometry/lớp/IDkhôngđổi, manifestv1.1khớp. ScriptQC --guideline-gap0 gợiýREWORK do ảnhlỗi20%>2%; học viên chấp nhận đề xuất, chưaphêduyệtRelease.

check_submission.py còn1lỗi: thiếu thời lượngGold. Học viên xác nhận **không đo**; ôminutesđểtrốngvànhậnghiđúngnguồn. Không bỏqua/chỉnhvalidator hoặc điền0/ước lượng để đạt.

## Việc còn lại

Kế hoạch sửa thật: qc/rework_plan.md. Có thể báo cáo quyết địnhRework trung thực theo rubric; muốnRelease cần sửaQC, kiểmmask theoBvàkiểmlại. Phần nhận xét cá nhân/mask cần học viên đọc và chịu trách nhiệm, không tự tạo thao tác đã thực hiện.

Repo đã được xác minhpublic. TênrepođặtHoTentrướcMSHV, khác mẫuSUBMISSION; cần kiểmquyướclớp trước nộp. Không tự đổi tênremote. Filecấm/trọngsố/tokenkhông đưa vàoGit. Các exporttrùng/demoAIkhôngtínhlàthànhtíchhọcviên, giữmáy nhưngignore. Commitchuẩnbịcục bộđượctạohiệntại,khôngbackdate. Chưapush/chưapháthànhcuối.

## Lệnh sau khi có bản sửa QC thực tế

```powershell
.\.venv\Scripts\python.exe scripts/build_release.py
.\.venv\Scripts\python.exe scripts/qc_sample.py --n 5 --seed 42
```

Sao lưu phiếuQCtrướckhidùng--force. Chỉ khi đã kiểm lại thật mới cập nhậtphiếuQCvàchạyqc_report.py --guideline-gap0. Cuối cùngcheck_submission.py; vẫn cần khai giới hạnGoldkhôngđo.
