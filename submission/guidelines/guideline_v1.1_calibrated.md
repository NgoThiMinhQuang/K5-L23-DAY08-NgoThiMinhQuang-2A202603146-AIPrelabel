# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603146 - Ngo Thi Minh Quang

> Ba ca đã được chốt trong chat ngày 19/11/2026. CAL-02 là phương án B được học viên chấp nhận theo đề xuất kỹ thuật. Chỉ bổ sung v1.0, không đổi taxonomy.rules; dữ liệu còn trong trạng thái Rework, chưa Release.

## 1. Câu hỏi về hàng hóa đã chốt — CAL-02

Ảnh ví dụ: img_003, img_031. **Mask xe gồm khung, mái, giỏ/thùng cố định và hàng nằm trong giỏ/thùng; loại người và phần hàng nhô ra ngoài giỏ/thùng.** Box giữ nguyên v1.0: hàng nhô quá khoảng20% chiều dài xe không tính vào box. Người luôn là đối tượng riêng. Quy tắc mask và box có phạm vi khác nhau, không đồng nhất mặt nạ với toàn hình chữ nhật.

Nguồn xác nhận: học viên chấp nhận làm theo đề xuất kỹ thuật trong chat19/11/2026. Điểm dương(595,385) học viên chọn ở img_003 nằm trên vùng hàng, phù hợp định hướng B; đây không thay kiểm tra kết quả thực tế. Cần rà mask và nhãn hiện tại trong lượt Rework; chưa khẳng định đã đạt quy tắc mới. Các câu hỏi mở khác của v1.0 ngoài ba ca này chưa được đánh giá, không tự tuyên bố tất cả đã được giải quyết.

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
| v1.1 | CAL-01/CAL-03: ví dụ và checklist; CAL-02: ranh giới mask hàng hóa theo B | Xác nhận/ủy quyền học viên trong chat19/11/2026; qc/calibration_decisions.csv |
