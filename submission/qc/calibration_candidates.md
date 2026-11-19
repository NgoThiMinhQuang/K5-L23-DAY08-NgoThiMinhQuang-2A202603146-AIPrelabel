# Calibration — quyết định đã xác nhận và một ca còn chờ

## CAL-01 — loi_may — img_013|img_004

**Trạng thái:** hoc_vien_da_xac_nhan

Vấn đề: YOLO26 gọi tuk-tuk là car trong thực nghiệm Detector -> Segmenter; SAM không tự sửa lớp.

Bằng chứng: REPORT mục 1.2.1; work/prelabel/t_sam/pre.coco.json; model_log r04.

Đề xuất: Gán tuk-tuk/ba gác/xích lô theo three_wheeler, một khung cho cả xe và mái/thùng gắn liền; người là person riêng. Ca này là lỗi model theo định nghĩa đã có, không đổi taxonomy để hợp thức hóa car.

Quyết định học viên: Học viên chốt: three_wheeler cho tuk-tuk/ba gác/xích lô; một khung cho cả xe và mái/thùng cố định; người có person riêng.

## CAL-02 — cau_hoi_mo — img_003|img_031

**Trạng thái:** de_xuat_B_cho_hoc_vien_xac_nhan

Vấn đề: Hàng rời, giỏ và thùng cố định cần phân biệt khi xác định box/mask xe.

Bằng chứng: guideline v1.0 câu hỏi mở số 3 và mục 4.1.5; masks/three-wheeler_img003.jpg; work/qc/review/img_031.jpg.

Đề xuất: Đề xuất B để nhất quán với điểm dương (595,385) học viên chọn trên vùng hàng ở img_003: mask xe gồm hàng nằm trong giỏ/thùng cố định; loại người và phần hàng nhô ra ngoài giỏ/thùng. Box giữ v1.0: loại hàng nhô quá 20% chiều dài xe. Chưa phải quyết định học viên đã chốt.

Quyết định học viên: Chưa xác nhận.

## CAL-03 — bay_lot — img_003

**Trạng thái:** hoc_vien_da_xac_nhan

Vấn đề: Bẫy bicycle trên xe máy bên phải lọt vòng đầu, đã sửa ở vòng hai có hỗ trợ.

Bằng chứng: qc/part4_round1/review_stats.json; qc/part4_round2/review_stats.json; qc/part4_missed_traps.png.

Đề xuất: Giữ motorbike cho xe máy hai bánh; mỗi người và mỗi xe có khung riêng. Rà toàn bộ xe theo checklist trước khi chấp nhận, không bỏ qua vì conf cao; đây là lỗi áp dụng/checklist, không đổi định nghĩa lớp.

Quyết định học viên: Học viên chốt: xe máy hai bánh là motorbike; tách person và xe; rà lớp và box trùng theo checklist, không bỏ qua vì conf cao.

## Cập nhật quyết định

Học viên trả lời “cái này mình theo ý bạn” sau đề xuất chốt B và Rework. CAL-02 được ghi là chấp nhận đề xuất kỹ thuật; không mô tả là quyết định độc lập không có AI. Quy tắc đã vào guideline v1.1; kiểm tính nhất quán mask/nhãn là công việc Rework, không tự khẳng định đã sửa xong.
