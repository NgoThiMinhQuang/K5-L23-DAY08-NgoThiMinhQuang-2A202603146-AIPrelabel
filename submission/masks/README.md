# Part 5 — SAM 2.1, điểm do học viên cung cấp

Trạng thái: đã có ba kết quả SAM2.1 từ tọa độ học viên chọn; học viên đã xác nhận hoàn thành thực hành Part5. Phân tích được ghi trong REPORT mục5. Đây là mask thực nghiệm; quyết định Release ngày19/11/2026 chỉ áp dụng cho nhãn detection, không phải chứng nhận sai số biên của segmentation.

Model models/sam2.1_b.pt nạp thành công (161935802 byte). Script sam2_points.py không có giao diện click: nhận tọa độ pixel ảnh gốc qua --pos/--neg.

| Ảnh | File | Positive | Negative | Tổng điểm | Mask px | Polygon |
|---|---|---:|---:|---:|---:|---:|
| img_013.jpg | tuktuk_img013.jpg | 2 | 2 | 4 | 278890 | 1 |
| img_003.jpg | three-wheeler_img003.jpg | 3 | 3 | 6 | 112772 | 2 |
| img_017.jpg | tuktuk_img017.jpg | 2 | 2 | 4 | 45174 | 1 |

## Đầu ra và log

Mỗi lệnh tạo một ảnh JPG overlay cho một vật; mask có thể có nhiều mảnh polygon (img_003 có 2 mảnh), không có yêu cầu mỗi ảnh chỉ đúng 1 polygon. File không phải mask nhị phân và script không lưu JSON/polygon hay tự cập nhật model_log.csv/work/runs.csv. Log chạy thật, lệnh và tọa độ mỗi vật đã được lưu trong logs/*.txt và logs/*.json. Mỗi exit code đều bằng0. Học viên đã xem kết quả và xác nhận hoàn thành thực hành; bài chưa có bộ segmentationGold để đo IoU hoặc sai số biên.

## Yêu cầu bài

GUIDE Part 5: chọn 3 vật khó, tạo ít nhất 3 ảnh masks/*.jpg; ghi SAM, số click và nhận xét vào REPORT mục 5. Rubric Mask SAM 8 điểm: 3 điểm cho >=3 ảnh và phiên bản/số click; 3 điểm phân tích người lái/hàng hóa đối chiếu guideline 4.2; 2 điểm quy tắc/lỗi rút ra. Không cần segmentation 8 ảnh hoặc COCO mask trong phần bắt buộc này. SAM 2.1 đã có dòng r04 trong model_log.csv từ Part 2; script hiện tại không tự tạo run_id nên không thêm log giả cho Part 5.

## Tiêu chí kiểm tra mask

Học viên mở từng JPG, kiểm vùng tô có đủ phần xe nhìn thấy, có ăn vào người/hàng hóa/nền, có gồm bóng đổ hoặc bỏ mất phần xe. Đối chiếu guideline 4.2: phần nhìn thấy, biên, lỗ lớn, bóng đổ, kính và tách vật chạm nhau. Vấn đề chưa có quy tắc rõ ghi thành câu hỏi calibration; không tự chốt thay.

Nếu thiếu vùng thuộc vật, tự chọn thêm positive; nếu tô lan sang vùng không thuộc vật, tự chọn thêm negative. Tránh chọn ngay đường biên hoặc điểm mơ hồ. Tọa độ (x,y) tính trên ảnh gốc, gốc ở góc trên trái, x sang phải và y xuống dưới. Chạy lại với toàn bộ điểm đã chọn và tên _v2 để giữ kết quả so sánh.

## Các file thử nghiệm cũ

cargo_img003.jpg và cyclo_img032.jpg là kết quả trước đó từ điểm do Codex chọn; không tính là minh chứng học viên tự chọn click. tuktuk_img013.jpg cũ đã sao lưu tại work/backups/sam_before_user_clicks/ trước khi thay bằng kết quả điểm học viên. README cũ cũng giữ tại đó. Không nhầm bộ thử nghiệm cũ với ba ảnh trong bảng hiện tại.
