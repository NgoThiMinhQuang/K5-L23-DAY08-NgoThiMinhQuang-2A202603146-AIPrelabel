# Báo cáo Day 08 — 2A202603146 - Ngo Thi Minh Quang

> Bản chuẩn bị nộp ngày 19/11/2026. Học viên lớp L2; Windows, chạy model trên CPU; CVAT Docker 2.74.1 (xác minh qua /api/server/about). Phần số liệu đã đối chiếu log; đã chốt ba ca calibration và Rework theo xác nhận/ủy quyền học viên. Học viên xác nhận không đo thời gian Gold, nên không đáp ứng được yêu cầu thời lượng thật của rubric. Không coi đây là Release đã phê duyệt.

## 1. Model và recipe (Part 2–3)

### Recipe đã thực thi

| Lớp | Model | Ngưỡng dùng | Bằng chứng/lý do kỹ thuật |
|---|---|---:|---|
| person | YOLO26 M | 0.30 | r01/r02/r05; không ngưỡng <=0.50 đạt recall mục tiêu trên 21 vật Gold, giữ mặc định |
| bicycle | YOLO26 M | 0.30 | Chỉ 4 vật Gold, không đủ mẫu chọn riêng |
| motorbike | YOLO26 M | 0.10 | threshold_sweep: TP 10, FP 21, FN 1; recall 10/11=90.9%, precision 10/31=32.3% |
| car | YOLO26 M | 0.30 | Không ngưỡng <=0.50 đạt recall mục tiêu; giữ mặc định |
| bus / truck | YOLO26 M | 0.30 | Không có vật Gold hai lớp này, không suy ra chất lượng riêng |
| three_wheeler | YOLOE S | 0.15 | r03/r06; prompt tuk-tuk, cyclo, cargo tricycle; TP7/FP2/FN0 trên Gold tại ngưỡng này |

```powershell
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "motorbike=0.1,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

YOLOE đứng trước trong merge để ưu tiên lớp xe ba bánh khi trùng dự đoán YOLO26. Đây là recipe đã dùng, không khẳng định tối ưu trên dữ liệu ngoài mẫu.

### Sáu lần chạy đã có log

| Run | Model | Tập/số ảnh | Box | Mask | Giây/ảnh |
|---|---|---|---:|---:|---:|
| r01 | YOLO26 M, conf0.25 | pilot/8 | 215 | 0 | 1.01 |
| r02 | YOLO26 M, conf0.50 | pilot/8 | 139 | 0 | 0.94 |
| r03 | YOLOE S, conf0.25 | pilot/8 | 8 | 0 | 0.38 |
| r04 | YOLO26 M + SAM 2.1 Base, conf0.30 | custom/2 | 16 | 16 | 7.21 |
| r05 | YOLO26 M, conf0.05 | all/36 | 1669 | 0 | 0.88 |
| r06 | YOLOE S, conf0.05 | all/36 | 125 | 0 | 0.45 |

Nguồn: model_log.csv, evidence/model_runs_original.csv và evidence/runs/r01–r06.json. Đã đối chiếu run_id, model, params, split, n_boxes và sec_per_img; không tạo số thời gian mới. Hai lần YOLO26 pilot khác ngưỡng giảm 76/215=35.3% box, chưa chứng minh model tốt hơn vì chưa phân biệt box bị loại đúng/sai. Các ghi chú trực quan trong model_log có nguồn học viên đã cung cấp; giữ nguyên lịch sử quyết định tại thời điểm chạy.

SAM chính: **SAM 2.1 Base, sam2.1_b.pt, CPU**. Checkpoint có trên máy và đã dùng thật trong r04 và ba thí nghiệm điểm ở Part5; không cần SAM3/gated cho yêu cầu bắt buộc. Lý do lựa chọn được hỗ trợ về kỹ thuật là chạy được trên cấu hình hiện tại và phù hợp prompt điểm; chưa có benchmark hai phiên bản nên không tuyên bố SAM2.1 tốt hơn SAM1/3.

Lỗi lớp tuk-tuk thành car trong r04 không sửa được bằng chỉ tăng/giảm confidence; CAL-01 đã chốt đối chiếu lớp three_wheeler và sửa nhãn. SAM tạo ranh giới không tự sửa tên lớp. Ví dụ điểm thấp cần kiểm từ ghi chú học viên: img_011, YOLOE conf0.28 nghi false positive; không coi điểm confidence là xác suất đúng.

Ngưỡng được chọn và chấm trên cùng 4 ảnh Gold nên số đo lạc quan, không phải kiểm định độc lập. Lớp ít vật không đủ để chọn ngưỡng riêng. Sản phẩm: qc/threshold_sweep.csv, .md, .png; không chạy lại các thí nghiệm trong lần chuẩn bị nộp này.

## 2. Quy trình và bằng chứng batch review (Part 4)

Học viên xác nhận đã gán Gold trước khi xem AI, review 24 ảnh batch và trực tiếp sửa nhãn trên CVAT. Hai bản export được cung cấp từ máy học viên; bản hiện tại là batch_reviewed_round2. labels/batch_reviewed.json có đúng 24 tên ảnh trong split batch, 586 annotation, 8 lớp taxonomy, không lẫn Gold. COCO info không ghi tên task nên nguồn task dựa trên bàn giao/xác nhận, không giả vờ có provenance task trong JSON.

Đã chạy validate_labels.py --all trong các lần kiểm trước, và kiểm lại khi chuẩn bị nộp: cấu trúc Gold 4 ảnh/51 annotation và batch 24 ảnh/586 annotation hợp lệ; các cảnh báo hình học lưu trong qc/validation_all.txt. Bộ kiểm tra không xác nhận mọi vật có lớp đúng hoặc box ôm sát.

Lệnh thống kê đã dùng, không rerun Part4 trong lần hoàn thiện này:

```powershell
python scripts/review_stats.py --pre submission/prelabel/pre_traps.coco.json --final submission/labels/batch_reviewed.json --gold submission/labels/gold_manual.json --key submission/prelabel/traps_key.json
```

Chỉ mở key sau khi có export đã xác nhận. Bản export, review_stats và nguồn gốc từng vòng giữ nguyên tại qc/part4_round1/ và qc/part4_round2/. Kết quả hiện tại **12/12=100% ở vòng sửa có hỗ trợ đối chiếu đáp án**; không mô tả đây là phép đo review độc lập mới.

Thời gian do học viên xác nhận: batch_review **120 phút/24 ảnh=5 phút/ảnh**. Học viên xác nhận không đo thời gian Gold; để trống minutes và ghi rõ không đo; không suy ra từ 10:00, timestamp, hoặc thời gian chạy model. Chưa đủ số liệu để tính phần trăm tiết kiệm so với gán tay.

## 3. AI error profile và so với Gold

Nguồn: qc/review_stats.json/md. Profile dùng 24 ảnh, loại 27 đối tượng quanh vùng bẫy khỏi so sánh để tách lỗi nhân tạo khỏi chất lượng model. Mẫu số 621 box máy và 571 đối tượng sau duyệt là số sau lọc, khác 586 annotation thô.

| Trạng thái | Số/tổng | Tỉ lệ |
|---|---|---:|
| accepted | 495/621 box máy | 79.7% |
| edited | 38/621 box máy | 6.1% |
| deleted | 85/621 box máy | 13.7% |
| added | 38/571 đối tượng sau duyệt | 6.7% |

Accepted = cùng lớp, IoU>=0.9; edited = cùng lớp, 0.5<=IoU<0.9. Đổi lớp có thể thành deleted+added: script ghi 8 trường hợp, nên added không đồng nghĩa 38 vật sót thật; ước lượng theo script là 38-8=30. Tổng accepted+edited+deleted=618 thay vì621 vì các box chưa ghép trong ignore_region không bị đếm deleted; không tự sửa đầu ra để các tỷ lệ cộng100%.

| Lớp | accepted | edited | deleted | added |
|---|---:|---:|---:|---:|
| person | 258 | 4 | 8 | 14 |
| bicycle | 6 | 0 | 2 | 3 |
| motorbike | 167 | 24 | 44 | 13 |
| car | 46 | 1 | 6 | 4 |
| bus | 2 | 0 | 1 | 1 |
| truck | 3 | 0 | 3 | 2 |
| three_wheeler | 13 | 9 | 21 | 1 |

Motorbike có nhiều box sửa/xóa nhất theo số đếm; three_wheeler cần chú ý nhãn sai/khung trùng/box lỏng. Bảng không tự xác định nguyên nhân từng box hay xếp hạng precision theo lớp.

Gold: **4 ảnh/51 đối tượng**, TP42, FP50, FN9. Precision máy=42/92=**45.7%**, recall=42/51=**82.4%**; KTC95% tương ứng khoảng36–56% và70–90%. Đây là chất lượng pre-label so với Gold, không phải precision/recall của batch sau review. Chỉ 4 ảnh, cùng tập chọn ngưỡng nên kết quả lạc quan. Không tính residual/new error vì batch export không chứa Gold.

## 4. Bẫy, hiệu quả review và giới hạn

Kết quả sau sửa: **12/12=100%**, loose/extra/wrong_class/missing đều3/3. Đây là vòng có hỗ trợ; log vòng đầu được giữ trong evidence riêng, không viết lại lịch sử.

Ba ca đã được đối chiếu sau export: img_003 xe máy bên phải bị gán bicycle; img_018 person trên trời là khung thừa; img_021 thiếu người áo khoác xám trên xe. Học viên sửa trên CVAT rồi cung cấp export mới. Script đo đạt bẫy không đảm bảo mọi nhãn đều đúng: QC sau đó vẫn phát hiện lỗi ở img_003.

Chưa có nguồn để kết luận cá nhân đã bỏ bước nào do mệt/neo nhãn, nên không viết hộ nguyên nhân tâm lý. Confidence cao hoặc accepted cao không chứng minh review sạch; vẫn phải tìm vật mà cả máy và người cùng bỏ sót.

Các thao tác phòng lỗi đã chốt qua CAL-03, áp dụng cho lượt tiếp: (1) kiểm lớp từng xe máy thay vì tin nhãn bicycle; (2) kiểm mỗi người và xe thành hai đối tượng riêng; (3) rà khung trùng cùng cá thể, không miễn kiểm do conf cao. Không tự ghi rằng các thao tác này đã được thực hiện đầy đủ trên mọi ảnh của vòng trước.

Tốc độ hiện có5 phút/ảnh; Gold không được đo thời gian nên chưa kết luận được tiết kiệm thời gian. Mẫu12 bẫy nhỏ; KTC95% của kết quả hiện tại khoảng76–100%, và việc đã biết đáp án làm kết quả vòng sửa không độc lập.

## 5. Mask SAM 2.1 (Part 5)

Học viên tự chọn các điểm và gửi lệnh; Codex chỉ thực thi, thêm --device cpu. Học viên đã xác nhận hoàn thành Part5. Không chạy lại hoặc chọn thêm điểm trong lần chuẩn bị nộp.

| Ảnh | Positive / negative | Tổng điểm | Mask px | Polygon | File |
|---|---|---:|---:|---:|---|
| img_013 | 2/2 | 4 | 278890 | 1 | masks/tuktuk_img013.jpg |
| img_003 | 3/3 | 6 | 112772 | 2 | masks/three-wheeler_img003.jpg |
| img_017 | 2/2 | 4 | 45174 | 1 | masks/tuktuk_img017.jpg |

Đúng **3 ảnh kết quả của ba vật** dùng điểm học viên. Các demo cũ do AI chọn điểm giữ tại máy nhưng loại khỏi commit; không tính thêm vào thành tích học viên. Log lệnh/điểm/stdout ở masks/logs/*.txt và .json. SAM2.1 Base CPU, tất cả exit0. Script chỉ lưu JPG overlay, không tự lưu nhị phân/polygon JSON hoặc tạo model_log; số polygon là mảnh ranh giới, không phải số vật khác nhau.

Đối chiếu guideline4.2: mask theo phần nhìn thấy; không gồm bóng đổ; lỗ lớn cần loại; vật chạm nhau tách vùng; kính thuộc xe theo v1.0. Mask không cứu nhãn lớp sai. Quan sát kỹ thuật có thể hỗ trợ học viên: img_013 cần kiểm ranh giới người lái/tay; img_003 có vùng người và hàng được tô nên liên quan CAL-02, hai polygon không tự là lỗi; img_017 cần kiểm chỗ xe bị xe trước che. Đây là ghi chú hỗ trợ, không giả lập nhận xét cá nhân học viên hoặc khẳng định mask đã đạt sai số biên3px.

Nội dung học viên còn cần xác nhận cụ thể: mask từng ảnh có gồm người/hàng không, điểm nào cải thiện vùng nào và lỗi còn lại; đặc biệt khi CAL-02 được chốt, phải kiểm tính nhất quán của mask hiện tại. Không tự sinh mask mới để hợp thức hóa guideline.

## 6. Calibration (Part 6)

| Ca | Nguồn | Trạng thái | Nội dung |
|---|---|---|---|
| CAL-01 | loi_may | Học viên đã chốt | Tuk-tuk/ba gác/xích lô là three_wheeler, một xe gồm mái/thùng cố định; người riêng. Không đổi định nghĩa để hợp thức hóa car. |
| CAL-02 | cau_hoi_mo | Chấp nhận B theo đề xuất kỹ thuật | Mask gồm hàng nằm trong giỏ/thùng cố định, loại phần hàng nhô ra và người; box giữ v1.0. Nguồn: học viên chấp nhận làm theo đề xuất trong chat. |
| CAL-03 | bay_lot | Học viên đã chốt | Motorbike cho xe máy hai bánh; kiểm lớp, người/xe, khung trùng theo checklist; conf không thay việc kiểm. |

calibration_decisions.csv hiện có **3 quyết định đã chốt**, gồm loi_may, cau_hoi_mo và bay_lot. CAL-02 được học viên chấp nhận theo đề xuất kỹ thuật, không mô tả là quyết định độc lập không có AI. Quy tắc B đã vào guideline_v1.1_calibrated.md; taxonomy.version cập nhật v1.1, taxonomy.rules giữ nguyên. Việc kiểm lại nhãn/mask theo quy tắc mới thuộc kế hoạch Rework, chưa tự ghi đã sửa xong.

CAL-01/CAL-03 chủ yếu là lỗi áp dụng quy tắc đã rõ, không đổi lớp/taxonomy; CAL-02 mới là ranh giới guideline cần làm rõ. Không đổi taxonomy.rules. Hình minh họa dẫn img_003/img_004/img_013/img_031. Nếu quy tắc mới đổi nhãn, học viên sửa trên CVAT và export qc_fixed.json; không sửa Gold hoặc bộ bẫy.

## 7. QC, release candidate và quyết định

### Kết quả đã được học viên xác nhận

Mẫu seed42 gồm **5/24 ảnh**, 0 ca can_review+5 ngẫu nhiên: img_003, img_006, img_014, img_031, img_035; **110 đối tượng**. Học viên xác nhận đã kiểm đủ: img_003 có3 lỗi dưới đây, bốn ảnh còn lại sạch. Ghi trong qc_review.csv và human_confirmations.json; bản ảnh cóID ở qc/previews/.

| Ảnh | ID trong release/QC | Lỗi xác nhận | Trạng thái sửa |
|---|---|---|---|
| img_003 | 18 (trùng1) | thua: hai person cho cùng người | Chưa có export QC sửa |
| img_003 | 7 | sai_vi_tri_kich_thuoc: khung xe cắt mái | Chưa có export QC sửa |
| img_003 | 3 | sai_lop: three_wheeler ở mép phải | Chưa có export QC sửa |

ID trên là ID ảnh preview/release hiện tại, không bảo đảm trùng ID sidebar CVAT.

**Lỗi theo đối tượng:3/110=2.7%. Lỗi theo ảnh:1/5=20.0%.** Mỗi loại lỗi có1 trường hợp; không đủ căn cứ gọi là lỗi hệ thống. Báo cáo tự động: qc/qc_report_auto.md, chạy với --guideline-gap0 sau khi CAL-02 được chốt. Gợi ý script hiện tại: **REWORK**, do tỷ lệ ảnh lỗi20% vượt ngưỡng2%. Học viên đồng ý làm theo đề xuất kỹ thuật; quyết định báo cáo là **Rework**. Không coi đã sửa lỗi hoặc đã Release.

Người QC cũng là người review, không độc lập. Mẫu5 ảnh nhỏ; cận trên95% Wilson cho tỷ lệ ảnh lỗi thật khoảng**62%**. Không khẳng định đạt2% chỉ từ5 ảnh; bẫy100% không thay QC.

### Release candidate

Có release/final_det.coco.json, release_summary.csv, release_manifest.json: đúng24 ảnh batch, **586 annotation=585 vật+1 ignore_region**. Đếm lớp: person282, motorbike209, car53, three_wheeler24, bicycle9, truck5, bus3, ignore_region1. Manifest, summary và nhãn nhất quán với export vònghai; chưa có qc_fixed.json. Release candidate đã đồng bộ taxonomy versionv1.1 sau khi chốt guideline. Đã kiểm annotations và ID không đổi sau build lại; không tự thay geometry/lớp của dữ liệu nguồn. Việc đáp ứng chất lượng guideline vẫn cần Rework.

Validation hợp lệ về cấu trúc;15 cảnh báo hình học cần đối chiếu guideline. Tồn tại thư mục release chỉ chứng minh đã xây bộ ứng viên, không chứng minh đã Release. Không thay nhãn nguồn để xóa ba lỗi đã xác nhận.

**Quyết định: Rework, ngày19/11/2026**, theo đề xuất kỹ thuật được học viên chấp nhận. Lý do:3lỗi đã xác nhận ở1/5ảnh=20%, vượt2%; còn phải kiểm tính nhất quán mask với CAL-02. Kế hoạch cụ thể ở qc/rework_plan.md. Nếu sửa QC, học viên thực hiện trên CVAT, export qc_fixed.json rồi build lại. Chưa push, chưa phê duyệt Release cuối.

## 8. Ghi chú cá nhân, tái lập và trung thực

- Học viên xác nhận tự gán Gold trước khi xem AI; không sửa Gold trong các lần trợ giúp. Đây là xác nhận học viên, không phải suy luận từ validation.
- Học viên xác nhận thời gian batch120 phút; học viên xác nhận không đo thời gian Gold, không ước lượng hoặc điền0. Chưa tính được so sánh tốc độ gán tay/review.
- Model CPU, Python3.12.10; phiên bản thư viện trong evidence/check_env_original.txt. CVAT2.74.1 thực tế khác bản2.81 hướng dẫn; ghi rõ để tái lập.
- Có log r01–r06 và đầu ra thực nghiệm; không dùng cache để thay các lần chạy này. Lần chuẩn bị nộp chỉ đọc log gốc, kiểm cấu trúc, chạy QC/report; không rerun model/threshold/review_stats/SAM.
- **AI hỗ trợ:** Codex đọc tài liệu, kiểm schema/đường dẫn/manifest/log, thực thi lệnh với tọa độ học viên, đề xuất nghi vấn QC và ca calibration, trình bày báo cáo theo bằng chứng. Học viên xác nhận3 lỗiQC vàCAL-01/CAL-03. CAL-02 và quyết định Rework sau đó được học viên chấp nhận theo đề xuất kỹ thuật. Nhận xét mask chi tiết vẫn cần học viên đọc lại; không tự ghi thao tác sửa chưa thực hiện.
- Các mask demo do Codex tự chọn điểm trước đó không tính vào3mask của học viên. Kết quả bẫy hiện tại là vòng sửa có hỗ trợ; log các vòng được giữ nguyên, không sửa điểm máy.
- Không sửa thuật toán để đạt điểm; không phát hiện lỗi thực thi bắt buộc sửa code. Các cảnh báo/thành phần chưa đủ vẫn được báo trung thực.
- Bản báo cáo dài trước khi chuẩn hóa giữ ở evidence/REPORT_history_before_submission_cleanup.md. Những số đo lịch sử là bằng chứng, không phải trạng thái hiện tại.
- Bài học/nhận xét cá nhân cuối cùng cần học viên tự bổ sung. Không tự viết thay trải nghiệm hoặc nguyên nhân bỏ lọt.
