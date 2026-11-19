# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603146 - Ngo Thi Minh Quang

> Bản bổ sung đang hoàn thiện. Hai ca đã được học viên chốt trong chat ngày 19/11/2026; CAL-02 còn mở. Không thay thế toàn bộ v1.0; không đổi taxonomy.rules. Không tuyên bố đã hoàn tất calibration.

## 1. Câu hỏi mở chưa chốt

**CAL-02 — câu hỏi 3 của v1.0, img_003/img_031:** hàng rời trong giỏ/thùng có thuộc mask xe không? Đề xuất kỹ thuật đang chờ: mask gồm hàng nằm trong giỏ/thùng cố định, loại phần hàng nhô ra và người; box giữ nguyên quy tắc 20% của v1.0. Lý do đề xuất: điểm dương (595,385) do học viên chọn tại img_003 nằm trên vùng hàng. Đây là đề xuất, không tự ghi là quyết định đã chốt. Các câu hỏi mở khác trong v1.0 vẫn giữ trạng thái chưa được xử lý nếu chưa có quyết định cụ thể.

## 2. Ca khó và cách áp dụng đã xác nhận

| Case | Ảnh ví dụ | Quyết định học viên | Loại thay đổi |
|---|---|---|---|
| CAL-01 | img_013, img_004 | Tuk-tuk/ba gác/xích lô là three_wheeler; mái/thùng cố định thuộc cùng xe, người có person riêng. Không dùng mask đẹp để hợp thức hóa nhãn car sai. | Làm rõ ví dụ áp dụng mục 2/4.1/5 v1.0; không đổi định nghĩa lớp. |
| CAL-03 | img_003 | Xe máy hai bánh là motorbike. Kiểm riêng person/xe, rà khung trùng và lớp; conf chỉ dùng ưu tiên kiểm, không miễn kiểm box. | Bổ sung checklist cụ thể, không đổi định nghĩa lớp hoặc tự khẳng định nguyên nhân bỏ lọt. |

Ảnh tham chiếu: [img_003](../../data/images/img_003.jpg), [img_004](../../data/images/img_004.jpg), [img_013](../../data/images/img_013.jpg), [img_031](../../data/images/img_031.jpg). Liên kết tương đối này tính từ submission/guidelines, vì vậy ảnh gốc ở ../../data/images.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Nguồn xác nhận |
|---|---|---|
| v1.0 | Bản giảng viên, giữ nguyên | guideline/guideline_v1.0_label_contract.md |
| v1.1 đang hoàn thiện | Hai ví dụ và checklist CAL-01/CAL-03 đã chốt; CAL-02 còn chờ | Xác nhận trực tiếp của học viên trong chat 19/11/2026; qc/calibration_decisions.csv |
