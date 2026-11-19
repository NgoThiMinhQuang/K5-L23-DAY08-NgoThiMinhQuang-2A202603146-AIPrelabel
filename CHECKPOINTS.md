# Checkpoints — Day 08 (240 phút trên lớp)

Mốc tính từ lúc bắt đầu buổi, là điểm **tự kiểm tra**, không phải hạn nộp. Cài đặt (Part 0) làm **trước** buổi học. Deadline do giảng viên/coach thông báo.

| Mốc | Mục tiêu | Tự kiểm tra |
|---|---|---|
| Trước lớp | **CP0** Cài đặt | `check_env.py` SẴN SÀNG (hoặc Colab chạy được); Project CVAT có 8 nhãn; đã đọc label contract |
| 0:25 | **CP1** Gold | `gold_manual.json` qua `validate_labels` (4 ảnh); chưa hề xem pre-label của 4 ảnh gold; có `time_log` |
| 0:55 | **CP2** Tour model | `model_log.csv` ≥ 4 dòng có nhận xét (YOLO26, YOLOE, **SAM**); đã chạy `--split all --conf 0.05` |
| 1:05 | **CP3** Ngưỡng | `threshold_sweep.md` có ngưỡng theo lớp |
| 1:15 | Nghỉ 10' | |
| 2:50 | **CP4** Review | `batch_reviewed.json` (24 ảnh); `review_stats.md` có bắt bẫy % |
| 3:05 | **CP5** Mask | ≥ 3 ảnh trong `submission/masks/` |
| 3:50 | **CP6** Calibration + QC | `calibration_decisions.csv` ≥ 3, `guideline_v1.1`, `REPORT.md` có quyết định |
| 4:00 | Nộp | `check_submission.py` sạch; đã nộp link |

## Cần hiểu ở mỗi mốc
- **CP1:** vì sao gold phải gán **trước** khi thấy máy? Neo nhãn làm sai số đo nào?
- **CP2:** vì sao YOLO26 gọi tuk-tuk là `car`? Open-vocabulary khác closed-set ở đâu?
- **CP3:** vì sao chọn ngưỡng **theo lớp**? "Điểm 0.9" có phải "90% đúng" không? Chọn và chấm trên cùng gold gây thiên lệch gì?
- **CP4:** `accepted` cao nhưng bắt bẫy thấp nghĩa là gì? Lỗi nào của máy **không hiện** trên điểm số?
- **CP5:** "một click, ba mask": ai quyết mask gồm gì? Mask đúng có cứu được nhãn lớp sai không?
- **CP6:** guideline mơ hồ khác "tôi làm sai" ở đâu? Vì sao 0 lỗi trong vài ảnh chưa chứng minh "dưới 2%"?

Chi tiết lệnh: [GUIDE.md](GUIDE.md). Bài nâng cao: [docs/ADVANCED.md](docs/ADVANCED.md).

## Trạng thái CP4 — kiểm tra ngày 19/11/2026

- Export người thực hiện cung cấp: đúng 24 ảnh batch, 586 annotation; khớp tên ảnh split batch, không có Gold.
- Validation exit 0: Gold 4 ảnh/51 đối tượng OK; batch 24 ảnh/586 đối tượng OK, có 15 cảnh báo cần xem theo guideline.
- Đã chạy review_stats đúng lệnh GUIDE: accepted 495/621, edited 38/621, deleted 85/621; added 38/571. Bắt bẫy 9/12 = 75%, dưới 80%.
- Người thực hiện xác nhận review 2 tiếng: time_log ghi 120 phút/24 ảnh = 5 phút/ảnh. Không tự điền thời gian Gold.
- Đã có sản phẩm kỹ thuật CP4; **chưa đạt toàn bộ về chất lượng**, chờ học viên rà lại CVAT và export vòng sửa. Ba bẫy lọt: img_003 sai lớp, img_018 thừa, img_021 thiếu.
- Chi tiết/bằng chứng: submission/REPORT.md mục 2–4; submission/qc/part4_validation.txt; submission/qc/review_stats.md; submission/qc/part4_missed_traps.png. Vòng đầu được lưu riêng trong submission/qc/part4_round1/; vòng sửa sau khi biết đáp án phải ghi có hỗ trợ.

### CP4 — cập nhật sau export vòng 2

- Đã thay export vòng 2 do người thực hiện cung cấp: đúng 24 ảnh batch, 586 annotation, validation exit 0; còn 15 cảnh báo.
- Bắt bẫy: **12/12 = 100%**, mỗi loại 3/3; đạt ngưỡng 80% ở lượt sửa có hỗ trợ.
- Vòng đầu độc lập: **9/12 = 75%**, giữ nguyên bằng chứng trong submission/qc/part4_round1/. Vòng hai trong submission/qc/part4_round2/; không thay điểm vòng đầu.
- CP4 đã đủ sản phẩm kỹ thuật/thời gian (120 phút) và kết quả lượt sửa; không đồng nghĩa toàn bộ nhãn đã qua QC hoặc bài được Release.

## Trạng thái CP5 — điểm học viên cung cấp

- Đã kiểm tra sam2_points.py, checkpoint SAM 2.1 Base nạp được; chạy ba lệnh CPU theo tọa độ học viên cho img_013/img_003/img_017, exit 0.
- Đã có ba JPG thực nghiệm với 4/6/4 điểm; log và nguồn tọa độ tại submission/masks/README.md và logs/.
- **Chưa hoàn thành CP5**: học viên cần tự xem và xác nhận mask, ghi người lái/hàng hóa/lỗi và đối chiếu guideline 4.2 trong REPORT mục 5. Không lấy ba ảnh demo do Codex tự chọn điểm trước đó làm bằng chứng click của học viên.

## Part 6 — kiểm kê và chờ quyết định người thực hiện

- Người thực hiện xác nhận Part 4/5 đã hoàn thành; không chạy lại. Phần nhận xét còn trống trong báo cáo phải được học viên bổ sung bằng nội dung thật.
- Đã có release candidate 24 ảnh/586 annotation, manifest/summary khớp nguồn; validation release exit 0.
- Đã có mẫu QC 5 ảnh/110 đối tượng; phiếu 0/5 ảnh đã kiểm. Chưa tính tỷ lệ lỗi, chưa chạy qc_report.py.
- Đã chuẩn bị 3 ca calibration riêng ở qc/calibration_candidates.csv/md; số quyết định đã chốt vẫn 0, guideline v1.1 chưa xác nhận.
- **CP6 chưa hoàn thành**: chờ học viên quyết định calibration, kiểm QC và chọn Hold/Rework/Release. Chi tiết tại submission/qc/part6_preflight.md.

## Kiểm kê cuối — 19/11/2026

Bảng đối chiếu rubric mới nhất: submission/FINAL_AUDIT.md. Các ghi chép trước đó là lịch sử, không phải trạng thái hiện tại.

- CP0: môi trường có log exit0; L2 vàCVAT2.74.1 đã xác minh; Git/nộp chưa chốt.
- CP1: Gold4ảnh/51annotation hợp lệ, học viên xác nhận gán trướcAI; thiếu thời gianGold thật.
- CP2–CP3: đủ6log và sản phẩm model/ngưỡng, khôngrerun; phần nhận xét học viên cần đọc lại.
- CP4: export24ảnh/586annotation,120phút; hiện tại12/12=100% ở vòng sửa có hỗ trợ, lịch sử giữ riêng.
- CP5: có3mask từ điểm học viên, thực hành được học viên xác nhận; nhận xét chi tiết/correspondenceCAL-02còn cần xem.
- CP6: đã ghiQC5ảnh/110vật theo xác nhận học viên,3lỗi;2quyếtđịnhcalibration đã chốt,CAL-02chờ;scriptgợiýHOLD. Chưa hoàn thành/toànbộRelease.
- check_submission hiện còn3lỗi dữ kiện người học; không sửa scriptđểbỏqua.
