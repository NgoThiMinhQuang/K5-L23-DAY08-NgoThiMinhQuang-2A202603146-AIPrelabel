# Calibration — ba ca đã chốt

Trạng thái hiện tại lấy từ calibration_decisions.csv và guideline_v1.1_calibrated.md. Các đề xuất ban đầu là lịch sử, không phải ca còn chờ.

## CAL-01 — loi_may — img_013|img_004

**Trạng thái: đã chốt.**

Vấn đề: YOLO26 gọi tuk-tuk là car trong thực nghiệm Detector -> Segmenter; SAM không tự sửa lớp.

Bằng chứng: REPORT mục 1; work/prelabel/t_sam/pre.coco.json; model_log r04.

Quyết định: Học viên chốt: three_wheeler cho tuk-tuk/ba gác/xích lô; một khung cho cả xe và mái/thùng cố định; người có person riêng.

## CAL-02 — cau_hoi_mo — img_003|img_031

**Trạng thái: đã chốt.**

Vấn đề: Hàng rời, giỏ và thùng cố định cần phân biệt khi xác định box/mask xe.

Bằng chứng: guideline v1.0 câu hỏi mở số 3 và mục 4.1.5; masks/three-wheeler_img003.jpg; work/qc/review/img_031.jpg.

Quyết định: Phương án B được chấp nhận theo đề xuất kỹ thuật: mask xe gồm hàng nằm trong giỏ/thùng cố định; loại người và phần hàng nhô ra ngoài giỏ/thùng. Box giữ quy tắc v1.0: loại hàng nhô quá 20% chiều dài xe.

## CAL-03 — bay_lot — img_003

**Trạng thái: đã chốt.**

Vấn đề: Bẫy bicycle trên xe máy bên phải lọt vòng đầu, đã sửa ở vòng hai có hỗ trợ.

Bằng chứng: qc/part4_round1/review_stats.json; qc/part4_round2/review_stats.json; qc/part4_missed_traps.png.

Quyết định: Học viên chốt: xe máy hai bánh là motorbike; tách person và xe; rà lớp và box trùng theo checklist, không bỏ qua vì conf cao.
