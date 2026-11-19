# Báo cáo Day 08 — <<MSHV - Họ và tên>>

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | <<YOLO26m>> | <<theo lớp, từ threshold_sweep>> | <<>> |
| three_wheeler | <<YOLOE-26s, prompt: ...>> | <<>> | <<>> |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): <<>>
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: <<chất lượng mask, tốc độ, RAM, quyền truy cập>>
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? <<>>
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): <<>>
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: <<>>

### 1.1. Kết quả thực nghiệm YOLO26 trên tập pilot

**Cấu hình:** YOLO26, kích thước M, thiết bị CPU; cùng tập pilot gồm **8 ảnh** cho mỗi lần chạy. Nguồn số liệu: kết quả Terminal do người thực hiện cung cấp trong yêu cầu cập nhật; đầu ra tương ứng tại `work/prelabel/t_y26/` và `work/prelabel/t_y26_c05/` (đường dẫn tính từ gốc project). Chưa bổ sung `run_id` vì yêu cầu chưa cung cấp thông tin này.

**Lần chạy 1 — confidence = 0.25:**

```bash
python scripts/prelabel.py --model yolo26 --size m --split pilot --conf 0.25 --out work/prelabel/t_y26
```

**Lần chạy 2 — confidence = 0.50:**

```bash
python scripts/prelabel.py --model yolo26 --size m --split pilot --conf 0.5 --out work/prelabel/t_y26_c05
```

| Tiêu chí | Confidence 0.25 | Confidence 0.50 |
|---|---:|---:|
| Số ảnh xử lý | 8 | 8 |
| Tổng bounding box trên 8 ảnh | 215 | 139 |
| person | 131 | 87 |
| motorbike | 55 | 32 |
| car | 17 | 14 |
| bicycle | 8 | 3 |
| truck | 3 | 2 |
| bus | 1 | 1 |
| Số mask trên 8 ảnh | 0 | 0 |
| Thời gian nạp model (giây) | 0.5 | 0.3 |
| Thời gian chạy 8 ảnh (giây) | 8.1 | 7.5 |
| Trung bình (giây/ảnh, theo Terminal) | 1.01 | 0.94 |

Các dự đoán bị bỏ qua vì lớp không có trong ánh xạ (không tính vào tổng box phía trên):

| Lớp bị bỏ qua | Confidence 0.25 | Confidence 0.50 |
|---|---:|---:|
| umbrella | 10 | 5 |
| handbag | 2 | 2 |
| traffic light | 2 | 1 |
| backpack | 2 | 1 |

**Nhận xét theo số liệu và ghi chú được cung cấp:**

- Khi tăng confidence từ 0.25 lên 0.50, tổng số box giảm **76/215 box**, tương đương khoảng **35.3%**. Tổng số box theo sáu lớp khớp với tổng của từng lần chạy.
- Ngưỡng cao loại bỏ nhiều dự đoán có độ tin cậy thấp; tuy nhiên, có thể bỏ sót các đối tượng nhỏ hoặc ở xa. Đây là khả năng cần kiểm tra, chưa phải kết quả đo recall.
- Cần kiểm tra trực quan bounding box để xác định chất lượng dự đoán. Chỉ từ số lượng box chưa thể xác định ngưỡng nào chính xác hơn.
- YOLO26 chưa có lớp `three_wheeler` trong kết quả ánh xạ hiện tại. Hai lần chạy đều chưa tạo mask.

### 1.2. Kết quả thực nghiệm YOLOE cho lớp three_wheeler

**Cấu hình:** YOLOE; lớp mục tiêu `three_wheeler`; confidence = **0.25**; tập pilot gồm **8 ảnh**. Nguồn số liệu: kết quả Terminal và ghi chú đánh giá trực quan do người thực hiện cung cấp trong yêu cầu cập nhật. Thư mục đầu ra: `work/prelabel/t_yoloe/`. Chưa có thông tin xác nhận kích thước model hoặc thiết bị của lần chạy này.

```bash
python scripts/prelabel.py --model yoloe --classes three_wheeler --split pilot --conf 0.25 --out work/prelabel/t_yoloe
```

| Tiêu chí | Kết quả |
|---|---:|
| Số ảnh xử lý | 8 |
| Tổng bounding box trên 8 ảnh | 8 |
| Số mask trên 8 ảnh | 0 |
| Thời gian nạp model (giây) | 74.0 |
| Thời gian inference cho 8 ảnh (giây) | 3.0 |
| Trung bình (giây/ảnh, theo Terminal) | 0.38 |

**Phân bố box và đánh giá trực quan được cung cấp:** các nhận xét dưới đây chưa phải đối chiếu định lượng với nhãn Gold.

| Ảnh | Số box | Ghi chú trực quan |
|---|---:|---|
| `img_002.jpg` | 4 | Phát hiện xe tuk-tuk màu xanh với confidence 0.93. Các dự đoán confidence 0.40 và 0.37 cần kiểm tra lại; một số box có dấu hiệu nhận diện nhầm. Ghi chú chưa nêu confidence của box còn lại. |
| `img_007.jpg` | 0 | Không phát hiện `three_wheeler`; phù hợp với ghi chú ảnh chủ yếu có xe máy thông thường. |
| `img_011.jpg` | 1 | Một box confidence 0.28 ở khu vực phương tiện bên trái; có khả năng là false positive, cần kiểm tra. |
| `img_015.jpg` | 0 | Không phát hiện `three_wheeler`; đối tượng chính là người đi xe đạp. |
| `img_016.jpg` | 0 | Không phát hiện `three_wheeler`; ảnh chủ yếu có ô tô và xe máy. |
| `img_019.jpg` | 0 | Không phát hiện `three_wheeler`; ảnh chủ yếu có ô tô, xe máy và xe đạp. |
| `img_030.jpg` | 0 | Không phát hiện `three_wheeler`; ảnh chủ yếu có xe buýt và xe máy. |
| `img_032.jpg` | 3 | Phát hiện ba phương tiện dạng xích lô, confidence lần lượt 0.48, 0.92 và 0.27; đây là kết quả đáng chú ý trong ghi chú được cung cấp. |
| **Tổng trên 8 ảnh** | **8** | Tổng số box theo ảnh khớp kết quả Terminal. |

**Nhận xét theo ghi chú được cung cấp:** YOLOE có khả năng tìm lớp `three_wheeler` theo mô tả văn bản, nhận diện được tuk-tuk và xích lô trong một số ảnh. Tuy nhiên, có khả năng xuất hiện false positive ở khu vực giao thông đông đúc; các box confidence thấp cần con người kiểm tra. Kết quả cho thấy tiềm năng hỗ trợ tạo pre-label cho phương tiện ba bánh, chưa đủ cơ sở xác nhận chất lượng nhãn chính thức.

#### 1.2.1. Thực nghiệm SAM 2.1 — Detector → Segmenter

**Cấu hình thực nghiệm:**

| Thành phần | Cấu hình |
|---|---|
| Detector | YOLO26, kích thước M |
| Segmenter | SAM 2.1 Base (`sam2.1_b.pt`) |
| Thiết bị | CPU |
| Confidence | 0.3 |
| Các lớp mục tiêu | person, motorbike, car |
| Dataset | 2 ảnh: `img_004.jpg` và `img_013.jpg` |
| Thư mục kết quả | `work/prelabel/t_sam/` |

**Lệnh chạy:**

```bash
python scripts/prelabel.py --model yolo26 --size m --classes "person,motorbike,car" --images img_004.jpg img_013.jpg --conf 0.3 --segmenter sam --sam sam2.1_b.pt --out work/prelabel/t_sam
```

**Nguồn và kiểm chứng:** kết quả thời gian nạp model, thời gian chạy và ghi chú trực quan do người thực hiện cung cấp. Đã đối chiếu `work/runs.csv` và `work/prelabel/t_sam/run.json` (run_id `r04`): CPU, 2 ảnh, 16 box, 16 mask, 7.21 giây/ảnh. File `work/prelabel/t_sam/pre.coco.json` chứa đúng hai ảnh nêu trên và 16 annotation có trường segmentation không rỗng; kiểm tra này xác nhận đầu ra, chưa đánh giá độ chính xác mask.

| Tiêu chí | Kết quả |
|---|---:|
| Số ảnh xử lý | 2 |
| Tổng bounding box trên 2 ảnh | 16 |
| Tổng mask được tạo trên 2 ảnh | 16 |
| Thời gian nạp model (giây) | 1.2 |
| Thời gian chạy 2 ảnh (giây) | 14.4 |
| Trung bình (giây/ảnh, theo kết quả cung cấp và log) | 7.21 |

Giữ nguyên các giá trị thời gian được cung cấp: 14.4 giây là số tổng đã làm tròn, còn trung bình được ghi với hai chữ số thập phân; không tính lại trung bình từ tổng đã làm tròn.

| Lớp | Số đối tượng dự đoán trên 2 ảnh |
|---|---:|
| person | 9 |
| car | 6 |
| motorbike | 1 |
| **Tổng** | **16** |

Các lớp bị bỏ qua do không nằm trong ánh xạ, theo kết quả được cung cấp và trường `dropped` của log:

| Lớp bị bỏ qua | Số dự đoán |
|---|---:|
| bottle | 8 |
| umbrella | 3 |
| handbag | 1 |
| chair | 1 |

**Đánh giá trực quan do người thực hiện cung cấp:**

| Ảnh | Quan sát |
|---|---|
| `img_004.jpg` | SAM tạo mask cho người và phương tiện. Mask phương tiện nhìn chung bám được hình dạng đối tượng. Một số tuk-tuk bị YOLO26 phân loại thành `car` thay vì `three_wheeler`. |
| `img_013.jpg` | Mask bao được phần lớn thân xe ba bánh; một số vùng thân xe và mái xe được phân đoạn tương đối tốt. Có box `car` kích thước lớn bao phần lớn phương tiện. Cần kiểm tra kỹ ranh giới giữa người ngồi trên xe và thân xe để tránh mask bị chồng lấn. |

**Nhận xét và kết luận do người thực hiện cung cấp:** SAM 2.1 đã tạo được **16 mask từ 16 bounding box** do YOLO26 cung cấp. Phương pháp Detector → Segmenter cho thấy khả năng kết hợp phát hiện đối tượng và phân vùng đối tượng. Tuy nhiên:

- SAM phụ thuộc vào chất lượng bounding box đầu vào.
- SAM không tự sửa nhãn lớp do YOLO26 dự đoán; mask của tuk-tuk vẫn có thể mang nhãn `car` sai từ detector.
- Bounding box quá rộng hoặc chồng lấn có thể làm giảm chất lượng mask.
- Cần kiểm tra và chỉnh sửa mask trước khi sử dụng làm dữ liệu segmentation chính thức.

Thực nghiệm YOLO26 kết hợp SAM 2.1 đã chạy thành công trên 2 ảnh. Cần tiếp tục đánh giá mask và so sánh với nhãn chuẩn; chưa có số đo IoU hoặc chất lượng segmentation để kết luận định lượng. Lần chạy này thuộc **Part 2 — Tour model**, chưa thay thế yêu cầu **Part 5 — Mask SAM cho 3 vật**.

### 1.3. So sánh YOLO26 và YOLOE

| Tiêu chí | YOLO26 | YOLOE |
|---|---|---|
| Cơ chế nhận diện | Closed-set detector: nhận diện các lớp có sẵn | Open-vocabulary detector: có thể dùng prompt văn bản để tìm `three_wheeler` |
| Phạm vi trong thực nghiệm | person, motorbike, car, bicycle, truck, bus | three_wheeler |
| Tập dữ liệu | Pilot, 8 ảnh mỗi lần chạy | Pilot, 8 ảnh |
| Tổng bounding box | 215 ở conf 0.25; 139 ở conf 0.50 | 8 ở conf 0.25 |
| Số mask | 0 trong cả hai lần chạy | 0 |
| Thời gian nạp model | 0.5 giây / 0.3 giây, tương ứng conf 0.25 / 0.50 | 74.0 giây |
| Thời gian xử lý 8 ảnh | 8.1 giây / 7.5 giây, tương ứng conf 0.25 / 0.50 | 3.0 giây inference |
| Khả năng bổ sung trong kết quả được cung cấp | Các đối tượng phổ biến | Tuk-tuk và xích lô |

**Giới hạn so sánh:** phạm vi lớp của hai model khác nhau nên số box không phải thước đo trực tiếp về độ chính xác. Thời gian nạp model được báo cáo riêng với thời gian xử lý; chưa đủ thông tin để kết luận model nào nhanh hơn trong cùng điều kiện phần cứng. Chưa có precision, recall, mAP hoặc đánh giá segmentation để bổ sung.

**Kết luận do người thực hiện cung cấp:** YOLO26 phù hợp với các đối tượng phổ biến như person, motorbike, car. YOLOE bổ sung khả năng tìm kiếm các đối tượng đặc thù như tuk-tuk và xích lô. Cần human review trước khi sử dụng các dự đoán làm nhãn chính thức.

## 2. Quy trình batch review (Part 4)

### 2.1. Nguồn và kiểm tra export

- Người thực hiện xác nhận đã review thủ công đủ 24 ảnh batch trên CVAT. Nguồn nhận: `Downloads/batch_reviewed/annotations/instances_default.json`; bản chép tại `submission/labels/batch_reviewed.json` có cùng SHA256.
- File có cấu trúc COCO detection, đúng 24 tên ảnh trong split batch, không thiếu/thừa ảnh; 586 annotation, 8 lớp đúng taxonomy (gồm ignore_region). Không có ảnh Gold trong export batch. `info` của COCO trống nên bản JSON không tự chứng minh tên task hoặc nhãn lựa chọn trong hộp export; nguồn task batch-review dựa trên quy trình bàn giao và xác nhận của người thực hiện. Không tuyên bố đã kiểm tra lại giao diện CVAT.
- `validate_labels.py --all`: exit 0. Gold 4 ảnh/51 đối tượng OK; batch 24 ảnh/586 đối tượng OK. Có 15 cảnh báo, gồm box cao dưới 20 px; log đầy đủ: `qc/part4_validation.txt`. Không phát hiện lỗi cấu trúc/tham chiếu, tên lớp, thiếu ảnh hoặc box ngoài biên theo bộ kiểm tra của repo. Script không chứng minh lớp đúng thực tế, box ôm sát hoặc không còn vật bỏ sót.
- Checklist yêu cầu: ẩn nhãn rồi quét ảnh; rà bỏ sót, thừa, sai lớp, vị trí/kích thước. Không có log từng ảnh để xác nhận mọi bước thực tế đã được thực hiện; không tự ghi nhận thay học viên.
- Chỉ đối chiếu key sau khi file export tồn tại, khớp bản người thực hiện cung cấp và validation thành công. Không thay đổi Gold, nhãn gốc, pre_traps hoặc key.

### 2.2. Lệnh đã chạy

```powershell
python scripts/validate_labels.py --all
python scripts/review_stats.py --pre submission/prelabel/pre_traps.coco.json --final submission/labels/batch_reviewed.json --gold submission/labels/gold_manual.json --key submission/prelabel/traps_key.json
```

Thực thi bằng Python trong `.venv`, cờ `-B -X utf8`; các đối số dữ liệu giữ đúng GUIDE. Đầu ra thống kê là `qc/review_stats.json` và `.md`; bản vòng đầu được lưu riêng tại `qc/part4_round1/` cùng export để truy vết sau rework.

### 2.3. Thời gian thực tế

Người thực hiện xác nhận đã dành **2 tiếng = 120 phút** review 24 ảnh; ghi vào `time_log.csv`, dòng batch_review. Trung bình **120/24 = 5 phút/ảnh**. Giờ bắt đầu 10:00 được ghi nhận riêng; không dùng để suy ra thời lượng. Thời gian Gold chưa được cung cấp nên chưa tính được tốc độ tăng so với gán tay.

## 3. AI error profile (Part 4)

Nguồn: `qc/review_stats.json` và `.md`. Profile dùng 24 ảnh batch; script loại 27 đối tượng quanh các vùng bẫy khỏi so sánh để không gán lỗi nhân tạo cho model. Mẫu số còn 621 box máy và 571 đối tượng sau duyệt; đây không phải 586 annotation thô của export.

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---|---|
| accepted | 495 | 495/621 = 79.7% | box máy |
| edited | 38 | 38/621 = 6.1% | box máy |
| deleted | 85 | 85/621 = 13.7% | box máy |
| added | 38 | 38/571 = 6.7% | đối tượng sau duyệt |

Accepted nghĩa là cùng lớp và IoU >= 0.9 giữa nhãn máy và nhãn cuối, không phải chứng nhận đối tượng đúng bằng mắt. Edited là cùng lớp, IoU từ 0.5 đến dưới 0.9. Đổi lớp có thể tính thành deleted + added: script ghi 8 trường hợp đổi lớp, nên ước lượng sót thật theo script là 38 - 8 = 30, không phải cả 38 đều bỏ sót. Tổng accepted + edited + deleted = 618, ít hơn 621 vì nhánh xử lý ignore_region không tính các box chưa ghép bị phủ bởi ignore là deleted; không sửa mẫu số hoặc ép tỉ lệ cộng thành 100%.

| Lớp | accepted | edited | deleted | added |
|---|---:|---:|---:|---:|
| person | 258 | 4 | 8 | 14 |
| bicycle | 6 | 0 | 2 | 3 |
| motorbike | 167 | 24 | 44 | 13 |
| car | 46 | 1 | 6 | 4 |
| bus | 2 | 0 | 1 | 1 |
| truck | 3 | 0 | 3 | 2 |
| three_wheeler | 13 | 9 | 21 | 1 |

Motorbike có nhiều box bị xóa/sửa nhất theo số đếm (44 deleted, 24 edited). Three_wheeler có 21 deleted và 9 edited so với 13 accepted, cần chú ý dự đoán trùng/sai lớp/box lỏng. Các số đếm gợi ý nơi cần xem lại, không tự xác định nguyên nhân từng box hoặc kết luận lớp nào có độ chính xác thấp nhất; added/deleted còn có đổi lớp và ảnh hưởng ignore.

### 3.1. Máy so với Gold

- 4 ảnh, 51 đối tượng Gold; TP = 42, FP = 50, FN = 9.
- Precision = **42/(42+50) = 42/92 = 45.7%**, KTC 95% khoảng 36–56%.
- Recall = **42/(42+9) = 42/51 = 82.4%**, KTC 95% khoảng 70–90%.
- Đây là số đo của máy so với Gold, không phải precision/recall của bộ batch sau review. Ngưỡng được chọn trên chính 4 ảnh Gold nên kết quả lạc quan; mẫu nhỏ, không có kiểm định độc lập. Không tính residual/new error vì export batch không chứa Gold.

## 4. Khả năng phát hiện bẫy và hiệu quả review (Part 4)

### 4.1. Kết quả vòng đầu

**9/12 = 75.0%**, KTC 95% khoảng 47–91%, dưới yêu cầu >= 80% (ít nhất 10/12). Giữ điểm vòng đầu, không sửa số liệu để vượt ngưỡng.

| Loại bẫy | Bắt được | Còn lọt |
|---|---|---|
| loose (box lỏng) | 3/3 | 0 |
| extra (box thừa) | 2/3 | img_018.jpg |
| wrong_class (sai lớp) | 2/3 | img_003.jpg |
| missing (bỏ sót) | 2/3 | img_021.jpg |

### 4.2. Vị trí cần người thực hiện sửa trong CVAT

Ảnh minh họa: `qc/part4_missed_traps.png`. Tọa độ dưới đây là pixel ảnh gốc: img_003/img_018 có kích thước 1024x682, img_021 là 1024x683; dạng (x1,y1)-(x2,y2). ID là annotation trong JSON export, không bảo đảm trùng ID trên sidebar CVAT.

| Ảnh | Bằng chứng còn lọt | Hành động cần đối chiếu và làm trên CVAT |
|---|---|---|
| img_003.jpg | Annotation JSON #4 là bicycle tại (812.24,238.75)-(1019.04,430.34), đúng vùng xe máy bên phải do người áo xanh điều khiển. Key ghi lớp trước bẫy là motorbike. | Chọn khung xe máy đó, đổi bicycle thành motorbike. Giữ person của người lái riêng; rà lại khung sát phần xe nhìn thấy. |
| img_018.jpg | Annotation JSON #171 là person tại (619.45,48.82)-(731.93,185.63); vùng này là bầu trời trên mái nhà, không có người. | Xóa khung person trên trời. Không xóa các person thật phía dưới. |
| img_021.jpg | Key thiếu person tại (727.05,333.09)-(806.45,475.43). Export chưa có person cùng lớp với IoU >= 0.5 ở vị trí này. Đây là người mặc áo khoác xám có mũ trùm, ngồi trên xe phía trước, bên phải giữa ảnh. | Thêm một khung person quanh phần nhìn thấy của người áo xám. Đối chiếu người sát bên để không gộp hai người; giữ motorbike của xe riêng. Tọa độ key chỉ là vùng tham chiếu, cần tự ôm sát vật thật. |

Các lỗi tương ứng ba lượt kiểm: nhãn lớp, box thừa, đối tượng bỏ sót. Chưa có bằng chứng để khẳng định học viên đã bỏ bước nào hoặc nguyên nhân tâm lý cụ thể. Accepted 79.7% không chứng minh review tốt: vẫn lọt ba bẫy và có thể còn lỗi cả máy lẫn người cùng bỏ qua.

### 4.3. Lượt sửa sau khi biết đáp án

Học viên cần tự sửa và lưu trên CVAT, export COCO 1.0 không kèm ảnh. Giữ nguyên nhãn vòng đầu và review_stats trong `qc/part4_round1/`. Vòng sau dùng file riêng; nếu áp dụng sửa QC, dùng `qc_fixed.json` theo hướng dẫn Part 6. Không ghi đè Gold hoặc pre_traps/key.

Nếu xác nhận sửa đúng thêm 1 bẫy và không làm mất các bẫy đã bắt thì số đạt sẽ là 10/12 = 83.3%; sửa đúng cả ba sẽ là 12/12 = 100%. Đây chỉ là phép tính điều kiện, chưa phải kết quả đo vòng hai. Vì đã xem đáp án, vòng hai phải ghi rõ có hỗ trợ; không dùng để thay thành tích review độc lập vòng đầu 9/12.

### 4.4. Hiệu quả, hạn chế và việc còn chờ

- Tốc độ đã biết: 120 phút/24 ảnh = 5 phút/ảnh. Chưa có thời gian Gold nên chưa đủ bằng chứng kết luận pre-label giúp nhanh hơn gán tay bao nhiêu.
- Review đã làm thay đổi nhãn, nhưng các sai khác không tự chứng minh mọi sửa đổi đều đúng. Validation chỉ kiểm cấu trúc, số ảnh, lớp, hình học cơ bản; chưa kiểm chất lượng đầy đủ bằng mắt.
- Cần tự rà lại lô theo GUIDE vì bắt bẫy dưới 80%, đặc biệt img_003/img_018/img_021. Với cụm vật nhỏ dưới 20 px, áp dụng ignore_region theo guideline khi đủ điều kiện; không xóa mù theo cảnh báo.
- Quy trình khuyến nghị cho lượt tiếp: quét ảnh khi ẩn nhãn, rà thừa/sai lớp rồi kiểm từng người–xe; ghi ca không chắc quy tắc bằng can_review và nhật ký. Chưa tự nhận học viên đã thực hiện những bước này.
- CP4: đã có export đúng tập, validation hợp lệ, thống kê và time log; **chưa đạt ngưỡng chất lượng bắt bẫy**. Chờ học viên thao tác CVAT/xác nhận các lỗi và export vòng sửa trước khi đánh dấu đạt toàn bộ.

### 4.5. Kết quả vòng sửa có hỗ trợ — 19/11/2026

Người thực hiện đã sửa trên CVAT và cung cấp batch_reviewed_round2. Đã validate và thay nguyên bản export vào labels/batch_reviewed.json: đúng 24 ảnh batch, 586 annotation, 8 lớp đúng taxonomy; validation exit 0, còn 15 cảnh báo như trước. Không tạo nhãn thay học viên.

- Bắt bẫy vòng hai: **12/12 = 100.0%**, vượt ngưỡng 80%; loose/extra/wrong_class/missing đều 3/3, không còn bẫy lọt theo script.
- Vòng đầu **9/12 = 75.0%** vẫn được lưu tại qc/part4_round1/. Vòng hai tại qc/part4_round2/ là **có hỗ trợ đối chiếu đáp án**, không phải phép đo review độc lập mới.
- Profile không đổi: accepted 495/621 = 79.7%; edited 38/621 = 6.1%; deleted 85/621 = 13.7%; added 38/571 = 6.7%. Các vùng bẫy được loại khỏi profile nên sửa ba bẫy không làm đổi các con số này. Precision/recall máy trên Gold vẫn 42/92 = 45.7% và 42/51 = 82.4%; Gold không bị sửa.
- Sửa đủ 12 bẫy không chứng minh mọi annotation đều đúng; còn cảnh báo và QC Part 6. Mẫu 12 bẫy nhỏ, KTC 95% vòng hai khoảng 76–100%, không có ý nghĩa kiểm định độc lập sau khi xem đáp án.
- CP4 có đủ export, validation, thống kê và batch_review=120 phút; vòng sửa đạt ngưỡng bẫy theo script. Kết quả ban đầu và việc được hỗ trợ phải giữ trong báo cáo. Các ghi chú chờ sửa ở mục 4.1–4.4 mô tả vòng đầu.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| img_013.jpg | SAM 2.1 Base, CPU | 2 dương + 2 âm = 4 điểm học viên gửi | <<học viên kiểm tra>> | <<học viên ghi nhận xét>> | tuktuk_img013.jpg |
| img_003.jpg | SAM 2.1 Base, CPU | 3 dương + 3 âm = 6 điểm học viên gửi | <<học viên kiểm tra>> | <<học viên ghi nhận xét>> | three-wheeler_img003.jpg |
| img_017.jpg | SAM 2.1 Base, CPU | 2 dương + 2 âm = 4 điểm học viên gửi | <<học viên kiểm tra>> | <<học viên ghi nhận xét>> | tuktuk_img017.jpg |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? <<>>
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: <<>>

### 5.1. Công việc tiếp theo và trạng thái thực hiện

| Công việc | Trạng thái |
|---|---|
| Thử nghiệm SAM 2.1 để tạo segmentation mask | Đang thực hiện |
| So sánh kết quả detection và segmentation | Đang thực hiện |
| Tổng hợp kết quả các model | Đang thực hiện |
| Hoàn thiện đánh giá và báo cáo | Đang thực hiện |

Trạng thái trên được ghi theo yêu cầu của người thực hiện. Chưa có kết quả SAM 2.1 hoặc số đo segmentation được cung cấp để điền vào bảng mask và đánh giá so sánh.

### 5.2. Cập nhật trạng thái sau thực nghiệm SAM 2.1

Bảng này cập nhật trạng thái hiện tại, thay cho mô tả tiến độ ở mục 5.1 vốn được giữ nguyên để bảo toàn nội dung cũ. Các nhận định trước đó rằng chưa có kết quả SAM phản ánh thời điểm trước lần bổ sung này.

| Hạng mục | Trạng thái hiện tại | Phạm vi bằng chứng |
|---|---|---|
| YOLO26 | Hoàn thành thực nghiệm đã báo cáo | Hai lần chạy pilot, run_id `r01` và `r02`; chưa đồng nghĩa hoàn thành toàn bộ checkpoint |
| YOLOE | Hoàn thành thực nghiệm đã báo cáo | Lần chạy pilot, run_id `r03`; chưa đồng nghĩa hoàn thành toàn bộ checkpoint |
| SAM 2.1 Detector → Segmenter | Đã chạy thành công, cần đánh giá chất lượng | Run_id `r04`, 2 ảnh, 16 box và 16 mask |
| Các bước segmentation tiếp theo | Chưa hoàn thành | Chưa có sản phẩm mask tương tác Part 5 và đánh giá chất lượng đầy đủ |

### 5.3. Đối chiếu các checkpoint còn chưa hoàn thành hoặc chưa đủ bằng chứng

**Căn cứ:** `CHECKPOINTS.md`, `GUIDE.md` (Part 0–6), `SUBMISSION.md`; đối chiếu với nội dung REPORT và các file hiện có tại thời điểm cập nhật. Không tự đánh dấu hoàn thành từ việc có thư mục, file mẫu hoặc chỉ chạy model thành công. Không chạy model lên Gold, không mở nội dung nhãn Gold hay đáp án bẫy trong lần đối chiếu này.

| Checkpoint | Trạng thái theo bằng chứng hiện có | Phần còn thiếu để xác nhận hoàn thành |
|---|---|---|
| **CP0 — Cài đặt** | **Chưa đủ bằng chứng xác nhận**. Các log `r01`–`r04` chứng minh đã chạy được model trên CPU, nhưng không chứng minh toàn bộ CP0. | Kết quả `check_env.py` SẴN SÀNG (hoặc Colab chạy được); bằng chứng Project CVAT có 8 nhãn và đã đọc label contract. Theo GUIDE, còn cần điền thông tin học viên và tiêu đề README. |
| **CP1 — Gold** | **Chưa đủ bằng chứng xác nhận**. Có `submission/labels/gold_manual.json`, nhưng chưa xác nhận nội dung hoặc kết quả validate; `submission/time_log.csv` chỉ có header. | Xác nhận nhãn Gold gán tay đủ 4 ảnh đã qua `validate_labels`; thời gian thật cho `gold_manual`; xác nhận Gold được hoàn thành trước khi xem pre-label. REPORT mục 8 vẫn để trống thông tin Gold có bị nhiễm hay không. |
| **CP2 — Tour model** | **Chưa hoàn thành**. Đã có 4 lượt chạy trong `work/runs.csv`: YOLO26 (`r01`, `r02`), YOLOE (`r03`), YOLO26 + SAM 2.1 (`r04`); REPORT đã có kết quả và ghi chú. | `submission/model_log.csv` hiện chỉ có header, chưa có ≥ 4 dòng kèm nhận xét và dòng SAM ghi phiên bản. Chưa có log hoặc đầu ra `y26_all` và `yoloe_all` cho hai lệnh `--split all --conf 0.05` theo GUIDE. |
| **CP3 — Ngưỡng** | **Chưa hoàn thành**. Hai ngưỡng 0.25/0.50 trong REPORT mới là thử nghiệm pilot. | Chưa có `submission/qc/threshold_sweep.csv`, `.md`, `.png`; chưa có ngưỡng theo lớp và lý do trong recipe. Cần ghi giới hạn chọn/chấm trên cùng 4 ảnh Gold; lớp có dưới 5 vật giữ mặc định 0.3 theo GUIDE. |
| **CP4 — Review** | **Chưa hoàn thành**. REPORT mục 2–4 còn là mẫu; `time_log.csv` chưa có dữ liệu. | Chưa có pre-label gộp theo ngưỡng, `pre_traps_import.coco.json`, `pre_traps.coco.json`, `traps_key.json`, `labels/batch_reviewed.json` đủ 24 ảnh, `qc/review_stats.json` và `.md`; chưa có thời gian `batch_review`, tỷ lệ bắt bẫy trên 12 bẫy, AI error profile, so sánh thời gian và 3 quy tắc giảm thiên kiến. Chỉ mở đáp án bẫy sau khi export nhãn đã review theo GUIDE. |
| **CP5 — Mask** | **Chưa hoàn thành**. Đã có 16 mask tự động trên 2 ảnh ở `work/prelabel/t_sam/`, nhưng chưa có thư mục `submission/masks/`; bảng Part 5 trong REPORT còn trống. | Cần dùng SAM với click cho 3 vật khó, lưu ≥ 3 ảnh kết quả vào `submission/masks/`; ghi phiên bản SAM, số click, người lái/hàng hoá có nằm trong mask, lỗi và đối chiếu guideline mục 4.2; bổ sung dòng SAM trong `model_log.csv`. Nếu áp dụng phương án rút gọn của GUIDE, làm 2 vật và thêm 1 ảnh đổi phiên bản SAM, vẫn phải đủ ≥ 3 ảnh kết quả. Interactor SAM trong CVAT là tuỳ chọn. |
| **CP6 — Calibration + QC** | **Chưa hoàn thành**. `calibration_decisions.csv` chỉ có header; guideline v1.1 vẫn là mẫu; `submission/release/` chỉ có `.gitkeep`; REPORT mục 6–7 còn trống. | Cần ≥ 3 quyết định calibration (≥ 1 `loi_may`, ≥ 1 `cau_hoi_mo`) và bổ sung guideline v1.1 có ảnh ví dụ; tạo `release/final_det.coco.json`, `release_summary.csv`, `release_manifest.json`; lấy mẫu QC, điền `qc_sample.csv`, `qc_review.csv`, sinh `qc_report_auto.md`; hoàn thiện quyết định Release/Hold/Rework, tỷ lệ kèm mẫu số và giới hạn mẫu nhỏ trong REPORT. |
| **Mốc Nộp** | **Chưa hoàn thành theo các sản phẩm kiểm tra được; một số điều kiện chưa có bằng chứng**. | Cần `check_submission.py` không còn LỖI, `validate_labels.py --all` sạch; REPORT đủ 8 mục; README có tiêu đề cá nhân; STUDENT điền đủ thông tin và kết quả tóm tắt. Cần xác nhận repo public/tên đúng/mở khi chưa đăng nhập, ≥ 4 commit bằng tài khoản học viên theo từng Part, không chứa token hoặc dữ liệu ngoài phạm vi, đã push và nộp link đúng hạn. Chưa có bằng chứng đủ cho các điều kiện này; số commit sẵn có trong lịch sử repo không tự chứng minh là commit bài làm của học viên. |

**Đối chiếu tiến độ segmentation:** 16 mask xác nhận bước Detector → Segmenter đã tạo đầu ra; chưa xác nhận mask đúng ranh giới, đúng quy tắc người lái/hàng hoá hoặc đúng nhãn lớp. Cần hoàn thiện CP5 và phần đánh giá tương ứng; không dùng kết quả này để tự đánh dấu hoàn thành CP5, CP6 hoặc quyết định Release.

### 5.4. Kiểm chứng kỹ thuật CP0–CP2 ngày 19/11/2026

Mục này cập nhật bằng chứng mới nhất, thay cho các trạng thái thiếu bằng chứng trước đó ở mục 5.3. Các nội dung cũ được giữ nguyên như lịch sử cập nhật.

**Sao lưu và bảo toàn dữ liệu:** trước khi sửa, đã sao lưu REPORT, model log, time log, taxonomy, Gold JSON/ZIP/thư mục export, calibration, guideline, README, STUDENT và runs.csv vào `work/backups/technical_20261119_101526/`. Manifest `protected_sha256.csv` ghi SHA256 của các file Gold, toàn bộ ảnh gốc, split, attribution, taxonomy và time log. Thư mục sao lưu nằm trong `work/`, không đưa vào commit.

SHA256 của `submission/labels/gold_manual.json`:

```text
AEF1399A186E9728F6C09AD58D6A42C5C74450811DE7696020CF6D568DE0FBC1
```

| Hạng mục | Kết quả kiểm chứng | Trạng thái còn thiếu |
|---|---|---|
| CP0 — môi trường | Chạy bằng `.venv/Scripts/python.exe -B -X utf8 scripts/check_env.py --cvat http://localhost:8080`: exit 0, SẴN SÀNG; Python 3.12.10; đủ thư viện; CPU; 36/36 ảnh; có trọng số; CVAT HTTP 200. Ảnh chụp người thực hiện cung cấp cho thấy Project `day08_2A202603146` có 8 nhãn. | CPU là cảnh báo hiệu năng; chưa đăng nhập HuggingFace chỉ ảnh hưởng SAM 3 tuỳ chọn. Chưa xác nhận đã đọc label contract; README/STUDENT và điều kiện Git/nộp bài còn thiếu. Chưa đánh dấu toàn bộ CP0 hoàn thành. |
| CP1 — cấu trúc và validation Gold | `validate_labels.py --all`: exit 0, 4 ảnh, 51 đối tượng: OK, không cảnh báo. Kiểm tra JSON có đúng img_001/img_012/img_020/img_036; ID ảnh/annotation không trùng; tham chiếu image/category hợp lệ. Ảnh chụp CVAT cho thấy task gold-manual có annotation trên bốn ảnh. | Chưa xác nhận lịch sử gán Gold hoàn tất trước khi xem AI; chưa có thời gian gold_manual thật. Validation không kiểm tra được box ôm sát hay lớp đúng thực tế. CP1 chưa đủ bằng chứng để hoàn thành toàn bộ. |
| CP2 — bốn thực nghiệm và logging | Đã đối chiếu r01–r04 trong work/runs.csv, run.json và pre.coco.json; model_log.csv có 4 dòng theo đúng 10 cột ban đầu, part=2; số box lần lượt 215/139/8/16, số mask 0/0/0/16. Không chạy lại model. Các nhận xét trực quan được chuyển từ ghi chú người thực hiện trong REPORT và ghi rõ nguồn; không tự nhận đã human review. | Chưa chạy YOLO26/YOLOE toàn bộ ảnh ở conf 0.05, chờ xác nhận trình tự Gold. Các nhận xét chưa phải đánh giá định lượng chất lượng nhãn. CP2 chưa hoàn thành toàn bộ. |
| CP3 — threshold sweep | Đã đọc scripts/threshold_sweep.py và docs/THRESHOLD.md; script sinh CSV/MD/PNG, chỉ gợi ý ngưỡng riêng khi đủ mẫu và đạt recall mục tiêu trong trần 0.5. | Chưa chạy vì chưa có hai đầu ra toàn bộ ảnh; không tự đưa ra ngưỡng tối ưu. |
| CP4 — review | Chưa có batch_reviewed.json, bộ pre-label có bẫy hoặc review_stats; ảnh chụp Gold không phải bằng chứng review batch. | Chưa chuẩn bị pre-label/bẫy do phụ thuộc CP2–CP3; chưa review 24 ảnh; chưa có batch_review trong time log. Không mở đáp án bẫy, không tạo nhãn đã review giả. CP4 chưa hoàn thành. |

**Thời gian thủ công:** `submission/time_log.csv` được giữ nguyên, chỉ có header. Không dùng thời gian inference, ngày sửa file hoặc thời gian hiển thị trong ảnh CVAT để suy ra số phút Gold/review. Chưa tính được chênh lệch thời gian gán tay và review.

**Bằng chứng máy:** `check_env.txt`, `validate_labels.txt`, `checks.json`, `gold_structure.json` trong thư mục sao lưu. Model log đọc lại đủ 4 dòng và khớp nguyên giá trị model/params/split/n_boxes/sec_per_img của runs.csv. Log xác nhận YOLOE S chạy CPU, bổ sung thông tin trước đây còn thiếu ở mục 1.2; run_id của các lần YOLO26 là r01/r02 và YOLOE là r03.

**Giới hạn và điểm cần người thực hiện xác nhận:** Gold có hoàn tất gán tay và export trước khi xem bất kỳ pre-label hoặc kết quả AI nào trên 4 ảnh Gold hay không? Đây là thông tin lịch sử không thể suy ra từ validation hoặc ảnh chụp. Sau khi xác nhận, mới chạy dự đoán toàn bộ ảnh và threshold sweep. Không chỉnh nhãn Gold, không sửa dữ liệu gốc, không commit hoặc push trong giai đoạn này.

### 5.5. Hoàn tất phần kỹ thuật CP2–CP3 và bàn giao review CP4

**Xác nhận của người thực hiện:** trong cuộc trao đổi ngày 19/11/2026, người thực hiện xác nhận đã gán tay và export đủ 4 ảnh Gold trước khi xem mọi kết quả AI trên các ảnh Gold. Đây là xác nhận lịch sử do người thực hiện cung cấp, không phải suy luận từ ảnh chụp hoặc validation. Xác nhận này cập nhật điểm còn chờ ở mục 5.4. Thời gian Gold vẫn chưa có số phút thật; time_log.csv giữ nguyên header.

**CP2 — hai lần chạy bổ sung, không chạy lại r01–r04:**

| Run | Model / cấu hình | Ảnh | Box | Mask | Nạp (giây) | Chạy (giây) | Giây/ảnh |
|---|---|---:|---:|---:|---:|---:|---:|
| r05 | YOLO26 M, CPU, all, conf 0.05, no-viz | 36 | 1669 | 0 | 0.4 | 31.6 | 0.88 |
| r06 | YOLOE S, CPU, all, conf 0.05, three_wheeler, no-viz | 36 | 125 | 0 | 2.4 | 16.2 | 0.45 |

Nguồn: thông báo Terminal của lần chạy và `work/prelabel/y26_all/run.json`, `work/prelabel/yoloe_all/run.json`; thời gian nạp/tổng chạy lấy từ Terminal. YOLOE dùng ba prompt mặc định trong taxonomy: tuk-tuk, cyclo, cargo tricycle. Hai file pre.coco.json đều có đúng 36 ảnh. model_log.csv hiện có 6 dòng dữ liệu, giữ nguyên header 10 cột và khớp runs.csv. Các trường quyết định chỉ ghi trạng thái sử dụng đầu ra, không tự thay quyết định chuyên môn của người thực hiện.

```bash
python scripts/prelabel.py --model yolo26 --size m --split all --conf 0.05 --out work/prelabel/y26_all --no-viz
python scripts/prelabel.py --model yoloe --classes three_wheeler --split all --conf 0.05 --out work/prelabel/yoloe_all --no-viz
python scripts/threshold_sweep.py --pred work/prelabel/y26_all/pre.coco.json work/prelabel/yoloe_all/pre.coco.json --gold submission/labels/gold_manual.json
```

**CP3 — đã tạo threshold_sweep.csv, threshold_sweep.md, threshold_sweep.png** trong `submission/qc/`. Biểu đồ đã được kiểm tra hiển thị đủ bảy lớp. Mục tiêu recall 0.90; IoU ghép đúng lớp 0.50; chỉ gợi ý ngưỡng riêng khi có ≥ 5 vật Gold và có ngưỡng ≤ 0.50 đạt mục tiêu. Bảng dưới trích trực tiếp CSV tại ngưỡng dùng cho merge; precision/recall là tỷ lệ 0–1 trên Gold, chưa phải đánh giá độc lập.

| Lớp | Ngưỡng dùng | Vật Gold | TP | FP | FN | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|---:|
| person | 0.30 | 21 | 15 | 20 | 6 | 0.429 | 0.714 |
| bicycle | 0.30 | 4 | 3 | 2 | 1 | 0.6 | 0.75 |
| motorbike | 0.10 | 11 | 10 | 21 | 1 | 0.323 | 0.909 |
| car | 0.30 | 8 | 7 | 8 | 1 | 0.467 | 0.875 |
| bus | 0.30 | 0 | 0 | 1 | 0 | 0.0 | 0.0 |
| truck | 0.30 | 0 | 0 | 2 | 0 | 0.0 | 0.0 |
| three_wheeler | 0.15 | 7 | 7 | 2 | 0 | 0.778 | 1.0 |

Motorbike=0.10 và three_wheeler=0.15 là gợi ý của script, không khẳng định tối ưu tổng quát. Person/car không có ngưỡng ≤ 0.50 đạt recall 90%; bicycle chỉ có 4 vật; bus/truck có 0 vật nên dùng mặc định 0.30. Giá trị 0 cho lớp không có vật không chứng minh model kém hoặc tốt. Chọn và chấm trên cùng 4 ảnh Gold khiến số đo lạc quan; chưa có tập kiểm định riêng. Số đo này tính trước bước loại trùng giữa model nên không thay thế số đo của pre-label đã gộp.

**Recipe kỹ thuật đang dùng:** sáu lớp person/bicycle/motorbike/car/bus/truck dùng YOLO26 M; three_wheeler dùng YOLOE S với prompt trong taxonomy để bổ sung lớp không có trong ánh xạ COCO. SAM 2.1 Base đã được thử detector → segmenter ở r04; lý do lựa chọn cuối cùng và đánh giá mask bằng click vẫn cần người thực hiện. Chưa coi recipe là kết luận chất lượng cuối cùng.

**CP4 — đã chuẩn bị pre-label và bộ bẫy:**

```bash
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "motorbike=0.1,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
python scripts/inject_traps.py --pre work/prelabel/merged_review/pre.coco.json --student "2A202603146"
```

YOLOE đứng trước để ưu tiên lớp three_wheeler khi box chồng lên dự đoán YOLO26 ở IoU ≥ 0.60. MSHV `2A202603146` lấy từ tên Project CVAT và ngữ cảnh bài làm. Script thông báo đã tạo đủ 12 bẫy trên 12 ảnh, chia đều bốn loại; không mở hoặc phân tích nội dung traps_key.json.

| Sản phẩm | Kiểm chứng |
|---|---|
| work/prelabel/merged_review/pre.coco.json | 28 ảnh batch+gold, 726 box trước cài bẫy |
| submission/prelabel/pre_traps_import.coco.json | Đúng 24 ảnh batch, 634 box; không có ảnh Gold; frame đầu img_003.jpg có 27 box |
| submission/prelabel/pre_traps.coco.json | Bộ đầy đủ dùng cho review_stats sau export; không sửa tay |
| submission/prelabel/traps_key.json | Script đã sinh; chưa mở đáp án; chỉ dùng sau khi review và export |
| work/review_import/pre_traps_import.zip | Gói COCO annotations/instances_default.json sao chép nguyên file import, SHA256 bản sao khớp nguồn |
| work/review_handoff.md | Hướng dẫn import/review/export và checklist 24 ảnh; mọi ô thao tác thủ công để trống |

Validation file import bằng `validate_labels.py ... --expect batch` trả exit 0, 24 ảnh/634 đối tượng OK; có 16 cảnh báo box cao dưới 20 px, cần người thực hiện đánh giá theo guideline trong review. Không tự sửa các box này hoặc bộ bẫy. Log tại `work/backups/technical_20261119_101526/validate_review_import.txt`; tổng kiểm tra tại review_import_checks.json.

**Trạng thái mới nhất:** CP0 đạt phần môi trường và có bằng chứng ảnh chụp 8 nhãn, còn xác nhận label contract/thông tin bài nộp. CP1 đạt cấu trúc/validation và có xác nhận trình tự Gold, còn thiếu thời gian thật và đánh giá thủ công theo guideline. CP2 đạt phần chạy bắt buộc và ghi log; người thực hiện còn cần xác nhận nhận xét/recipe cuối cùng. CP3 đạt phần sinh sản phẩm và ghi ngưỡng theo quy tắc script; giữ nguyên các giới hạn mẫu nhỏ. CP4 mới đạt chuẩn bị kỹ thuật, **chưa hoàn thành review**. CP5–CP6 chưa hoàn thành. Không tự công bố Release.

**Dừng để người thực hiện review:** tạo task batch-review từ 24 ảnh trong work/tasks/review; import đúng file batch (ZIP nếu CVAT yêu cầu); kiểm tra frame đầu 27 box; bấm giờ thật, quét ảnh khi ẩn nhãn rồi kiểm bỏ sót/thừa/lớp/box; lưu và export COCO sau đủ 24 ảnh. Cung cấp ZIP/JSON đã export cùng số phút thật để tiếp tục validation và thống kê. Không mở đáp án bẫy trước export. Không commit hoặc push trong lần thực hiện này.

### 5.6. Cập nhật kỹ thuật sau export review — 19/11/2026

> Trạng thái Part 4 mới nhất nằm ở mục 2–4; các ghi chép checkpoint trước đó là lịch sử tại thời điểm thực hiện.

- Người thực hiện đã cung cấp export CVAT trong Downloads/batch_reviewed/annotations/instances_default.json. Đã chép nguyên vẹn vào submission/labels/batch_reviewed.json và đối chiếu SHA256.
- Validation: Gold 4 ảnh/51 đối tượng; batch 24 ảnh/586 đối tượng, exit 0. Batch có 15 cảnh báo, gồm box dưới 20 px; chưa tự sửa nhãn từ cảnh báo.
- Đã chạy review_stats.py với đúng pre_traps, final, Gold và key sau khi export. Các con số trong mục 2–3 lấy từ đầu ra script, không sửa đầu ra tự động.
- Đã chạy SAM 2.1 Base bằng sam2_points.py trên CPU cho 3 vật, mỗi vật 2 điểm dương + 1 điểm âm do Codex chọn; không phải thao tác click thủ công của học viên. Lệnh và đầu ra được lưu trong masks/README.md. Học viên cần tự xem mask và viết nhận xét mục 5; đây là kết quả thực nghiệm, chưa xác nhận mask đạt chất lượng.
- Đã tạo release candidate: 24 ảnh/586 đối tượng. Đây là bộ ứng viên phục vụ QC, chưa phải quyết định Release.
- Đã lấy mẫu QC seed 42: 5 ảnh/110 đối tượng, không có ảnh tick can_review. Phiếu qc_review.csv chưa được học viên kiểm, không tự điền khong để tạo kết quả sạch.
- Cập nhật xác nhận mới nhất: người thực hiện cho biết bắt đầu làm lúc 10:00 sáng 19/11/2026 (Asia/Bangkok), sau đó xác nhận dành 2 tiếng review 24 ảnh. time_log.csv đã ghi batch_review = 120 phút; Gold vẫn chưa có thời lượng thật.
- Calibration và các kết luận chuyên môn còn chờ học viên tự chốt theo RULES.md; chưa đánh dấu CP4–CP6 hoàn thành toàn bộ.

### 5.7. Chạy theo điểm do học viên cung cấp

Đã chạy ba lệnh học viên gửi cho img_013, img_003 và img_017 bằng SAM 2.1 Base trên CPU. Số điểm lần lượt 2 dương/2 âm, 3 dương/3 âm, 2 dương/2 âm. Không tự chọn thêm hoặc đổi điểm. Codex thực thi lệnh, học viên là nguồn tọa độ; chưa có xác nhận học viên đã kiểm kết quả bằng mắt. Chi tiết lệnh và stdout tại masks/logs/; hướng dẫn và số liệu tại masks/README.md. Ba ảnh hiện tại thay bộ ví dụ do Codex chọn điểm ở mục 5.6 trong vai trò minh chứng Part 5. Chưa đánh dấu CP5 hoàn thành và chưa viết thay nhận xét mask.


### 5.8. Xác nhận của người thực hiện

Người thực hiện đã xác nhận Part 4 và Part 5 hoàn thành và yêu cầu không chạy lại. Ba mask dùng tọa độ học viên gửi được giữ nguyên, không sinh lại. Xác nhận này ghi nhận việc thực hành và xem kết quả; không tự tạo các nhận xét chi tiết còn trống trong mục 5. Tài liệu/log cũ vẫn giữ nguyên để truy vết.

## 6. Calibration (Part 6)

- Đã kiểm tra calibration_decisions.csv: hiện chỉ có header, **0 quyết định đã chốt**. Guideline v1.1 hiện vẫn là khuôn, chưa có quy tắc đã được học viên phê duyệt.
- Đã chuẩn bị **3 ca chờ quyết định**, tách riêng trong qc/calibration_candidates.csv và .md: CAL-01 (loi_may: YOLO26 gọi tuk-tuk là car), CAL-02 (cau_hoi_mo: ranh giới hàng rời/giỏ/thùng với xe trong box và mask), CAL-03 (bay_lot: motorbike bị gán bicycle trong vòng đầu).
- Mỗi ca có nguồn, ảnh ví dụ và câu hỏi cần học viên chốt. **Chưa tự ghi các ca chờ này vào calibration_decisions.csv như quyết định thật**; chưa tự sửa guideline v1.1 hoặc taxonomy.rules.
- Cần ít nhất 3 quyết định đã xác nhận, gồm >=1 loi_may và >=1 cau_hoi_mo. Phân biệt lỗi model/người áp dụng sai với guideline mơ hồ; chỉ quy tắc bổ sung thực sự mới vào v1.1. Mỗi ca có ảnh ví dụ, liên kết case_id và lịch sử thay đổi.
- Khi quyết định làm đổi nhãn, học viên cần sửa CVAT và export qc_fixed.json; sau đó build_release.py áp dụng nhãn đã sửa. Không sửa Gold hoặc annotation gốc để thay đổi kết quả vòng trước.

## 7. QC và quyết định (Part 6)

### 7.1. Release candidate và kiểm tra tự động

- Nguồn: labels/batch_reviewed.json vòng hai; chưa có qc_fixed.json. Đầu ra build_release.py: release/final_det.coco.json, release_summary.csv, release_manifest.json.
- **24 ảnh batch, 586 annotation**: person 282; motorbike 209; car 53; three_wheeler 24; bicycle 9; truck 5; bus 3; ignore_region 1. Đây là 585 đối tượng cộng 1 vùng ignore, không gọi cả 586 là vật QC.
- Manifest và summary khớp số ảnh, tổng annotation và số đếm theo lớp; box/lớp của release khớp export hiện tại. Validation release exit 0; còn cảnh báo hình học cần đối chiếu guideline trong qc/part6_release_validation.txt.
- Đã kiểm ba JPG mask từ điểm học viên: file đọc được, kích thước ảnh đúng, log exit 0, SAM 2.1 Base trên CPU. Học viên đã xác nhận Part 5 hoàn thành; nhận xét cụ thể về vùng người lái/hàng hóa/biên mask trong mục 5 vẫn cần viết. Không chạy lại mask.
- Kết quả kiểm kê đọc từ sản phẩm cũ Part 0–5 ở qc/part6_preflight.json và .md; không rerun model/threshold/review_stats/SAM.

### 7.2. Mẫu QC và trạng thái phiếu

- Mẫu seed 42: **5/24 ảnh**, gồm **0 ca can_review + 5 ảnh ngẫu nhiên**, **110 đối tượng** (không đếm ignore_region): img_003, img_006, img_014, img_031, img_035.
- Ảnh có ID hiện tại: work/qc/review/. Phiếu qc/qc_review.csv còn trống cả 5 ảnh: **0/5 ảnh đã kiểm**, không thể tính tỷ lệ lỗi; không coi ô trống là khong.
- Ba nghi vấn hỗ trợ trong qc/qc_observations_draft.csv liên quan person trùng, mái xe bị cắt và nhãn phương tiện mép phải ở img_003. Đây là ghi chú do Codex quan sát, **chưa phải lỗi đã được học viên xác nhận**, chưa đưa vào thống kê QC.
- Sau khi học viên kiểm từng ảnh, mỗi lỗi một dòng; ảnh sạch ghi khong. Người kiểm QC là học viên, đồng thời là người review nên đánh giá không độc lập.

### 7.3. Số đo và giới hạn

- Lỗi theo đối tượng: **chưa tính**, mẫu số sẽ là 110 đối tượng; lỗi theo ảnh: **chưa tính**, mẫu số sẽ là 5 ảnh. Không ghi 0/110 hoặc 0/5 khi chưa kiểm.
- Chưa chạy qc_report.py vì chưa đủ phiếu và chưa có số câu hỏi guideline còn mở do học viên xác nhận. Chưa có qc_report_auto.md hợp lệ để dùng kết luận.
- Ngay cả nếu sau kiểm có 0/5 ảnh lỗi, mẫu nhỏ vẫn không chứng minh tỷ lệ ảnh lỗi thật <=2%; cần đọc cận trên Wilson trong báo cáo script sau khi điền phiếu. Bẫy 12/12 vòng hai có hỗ trợ không thay phép đo vòng đầu độc lập 9/12.
- Script kiểm cấu trúc/ảnh/lớp/box và tổng đếm, không xác nhận vật thật, ôm sát, bỏ sót chung hoặc checklist được thực hiện đầy đủ.

### 7.4. Quyết định cuối cùng

**Chưa có quyết định Hold/Rework/Release.** Dừng chờ học viên chốt calibration và kiểm 5 ảnh QC. Không coi tồn tại thư mục release là đã Release. Sau đó chạy qc_report.py với số guideline-gap/data-problem thực tế; học viên tự chọn quyết định theo Hold -> Rework -> Release và viết lý do khớp số liệu.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): <<>>
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? <<không / có, như sau: ...>>
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: <<>>
- Dùng cache `data/cache/` thay vì tự chạy? <<không / có, vì ...>>
- Lỗi / bug script đã gặp (nếu có): <<>>
- Một điều bạn làm khác lần sau: <<>>

- **Bổ sung về việc dùng AI trong lần cập nhật này:** Codex hỗ trợ trình bày các kết quả YOLO26/YOLOE và nhận xét do người thực hiện cung cấp thành bảng, kiểm tra phép cộng số box và tỷ lệ giảm. Các ghi chú trực quan không phải kết quả Codex tự quan sát ảnh. Người thực hiện cần kiểm tra lại nội dung với đầu ra thực nghiệm trước khi nộp; các mục Gold, QC và quyết định Release/Hold/Rework chưa được điền thay.