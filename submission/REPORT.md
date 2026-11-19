# Báo cáo Day 08 — 2A202603146 - Ngo Thi Minh Quang

> Tôi thực hiện bài lab trên Windows, sử dụng CPU và CVAT Docker 2.74.1. Báo cáo tổng hợp quá trình chạy model, review nhãn, tạo mask và kiểm tra chất lượng. Sau QC, tôi chọn Rework để sửa các lỗi còn lại trước khi phát hành dữ liệu.

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

Tôi đặt YOLOE trước YOLO26 khi gộp kết quả để ưu tiên nhãn xe ba bánh ở các vùng dự đoán trùng nhau. Cấu hình này được sử dụng cho bộ dữ liệu của bài lab; hiệu quả trên dữ liệu khác cần được đánh giá thêm.

### Sáu lần chạy đã có log

| Run | Model | Tập/số ảnh | Box | Mask | Giây/ảnh |
|---|---|---|---:|---:|---:|
| r01 | YOLO26 M, conf0.25 | pilot/8 | 215 | 0 | 1.01 |
| r02 | YOLO26 M, conf0.50 | pilot/8 | 139 | 0 | 0.94 |
| r03 | YOLOE S, conf0.25 | pilot/8 | 8 | 0 | 0.38 |
| r04 | YOLO26 M + SAM 2.1 Base, conf0.30 | custom/2 | 16 | 16 | 7.21 |
| r05 | YOLO26 M, conf0.05 | all/36 | 1669 | 0 | 0.88 |
| r06 | YOLOE S, conf0.05 | all/36 | 125 | 0 | 0.45 |

Tôi tổng hợp số liệu từ model_log.csv, evidence/model_runs_original.csv và evidence/runs/r01–r06.json. Khi tăng ngưỡng YOLO26 từ 0.25 lên 0.50, số box giảm 76/215, tương đương 35.3%. Ngưỡng cao giúp giảm số dự đoán cần duyệt, nhưng cũng có nguy cơ bỏ sót vật nhỏ hoặc bị che. Vì vậy, tôi sử dụng kết quả quét ngưỡng theo lớp thay vì chọn một ngưỡng chung chỉ dựa trên số box.

Tôi chọn **SAM 2.1 Base (sam2.1_b.pt)** vì model chạy được trên CPU của máy và hỗ trợ tạo mask từ điểm positive/negative. Tôi sử dụng phiên bản này trong r04 và ba đối tượng ở Part 5. Bài thực nghiệm chưa so sánh trực tiếp với SAM 1 hoặc SAM 3.

Trong r04, tuk-tuk bị YOLO26 gán thành car. Thay đổi confidence không giải quyết được sự khác biệt về danh mục lớp này, nên tôi đối chiếu với định nghĩa three_wheeler khi review. SAM chỉ tạo ranh giới và không tự sửa nhãn lớp. Ở img_011, dự đoán YOLOE có confidence 0.28 là một trường hợp tôi ghi nhận cần kiểm tra false positive.

Tôi chọn ngưỡng trên cùng 4 ảnh Gold dùng để đánh giá, nên precision/recall có thể lạc quan hơn khi áp dụng sang tập mới. Những lớp có ít vật không đủ cơ sở để chọn ngưỡng riêng. Kết quả quét ngưỡng được lưu trong qc/threshold_sweep.csv, .md và .png.

## 2. Quy trình và bằng chứng batch review (Part 4)

Tôi gán tay tập Gold trước khi xem kết quả AI, sau đó review 24 ảnh batch và sửa nhãn trực tiếp trên CVAT. Bản export hiện tại là batch_reviewed_round2, được lưu thành labels/batch_reviewed.json. File có đúng 24 ảnh batch, 586 annotation và 8 lớp theo taxonomy, không chứa ảnh Gold.

Kết quả validate_labels.py --all cho thấy Gold gồm 4 ảnh/51 annotation và batch gồm 24 ảnh/586 annotation đều hợp lệ về cấu trúc. Các cảnh báo hình học được lưu trong qc/validation_all.txt. Tôi tiếp tục dùng QC bằng mắt để đánh giá nhãn lớp, độ ôm sát của box và vật bị bỏ sót.

Lệnh dùng để thống kê kết quả review:

```powershell
python scripts/review_stats.py --pre submission/prelabel/pre_traps.coco.json --final submission/labels/batch_reviewed.json --gold submission/labels/gold_manual.json --key submission/prelabel/traps_key.json
```

Sau khi export nhãn đã review, tôi đối chiếu đáp án bẫy và sửa lại trên CVAT. Kết quả hiện tại đạt **12/12 = 100% ở vòng sửa có hỗ trợ đối chiếu đáp án**. Tôi lưu riêng export và thống kê từng vòng tại qc/part4_round1/ và qc/part4_round2/ để theo dõi thay đổi.

Tôi dành **15 phút để gán 4 ảnh Gold**, trung bình **3,75 phút/ảnh**, và **120 phút để review 24 ảnh batch**, trung bình **5 phút/ảnh**. Thời lượng Gold được tôi cung cấp bổ sung sau khi hoàn thành, không có log bấm giờ để đối chiếu; nguồn này được ghi trong time_log.csv.

## 3. AI error profile và so với Gold

Nguồn: qc/review_stats.json/md. Profile dùng 24 ảnh, loại 27 đối tượng quanh vùng bẫy khỏi so sánh để tách lỗi nhân tạo khỏi chất lượng model. Mẫu số 621 box máy và 571 đối tượng sau duyệt là số sau lọc, khác 586 annotation thô.

| Trạng thái | Số/tổng | Tỉ lệ |
|---|---|---:|
| accepted | 495/621 box máy | 79.7% |
| edited | 38/621 box máy | 6.1% |
| deleted | 85/621 box máy | 13.7% |
| added | 38/571 đối tượng sau duyệt | 6.7% |

Accepted là các cặp box cùng lớp có IoU ≥ 0.9; edited là các cặp cùng lớp có 0.5 ≤ IoU < 0.9. Script ghi nhận 8 trường hợp đổi lớp dưới dạng deleted + added, nên số vật sót thật được ước lượng là 38 − 8 = 30. Tổng accepted + edited + deleted bằng 618, thấp hơn 621 vì các box chưa ghép nằm trong ignore_region không được tính vào deleted.

| Lớp | accepted | edited | deleted | added |
|---|---:|---:|---:|---:|
| person | 258 | 4 | 8 | 14 |
| bicycle | 6 | 0 | 2 | 3 |
| motorbike | 167 | 24 | 44 | 13 |
| car | 46 | 1 | 6 | 4 |
| bus | 2 | 0 | 1 | 1 |
| truck | 3 | 0 | 3 | 2 |
| three_wheeler | 13 | 9 | 21 | 1 |

Motorbike có số box bị sửa và xóa nhiều nhất, lần lượt là 24 và 44. Với three_wheeler, số box bị xóa là 21, cho thấy nhóm xe ba bánh cần được kiểm kỹ về nhãn lớp, khung trùng và phạm vi box. Tôi sử dụng bảng này để xác định nhóm cần ưu tiên kiểm tra, thay vì xem số box bị xóa là precision của từng lớp.

Gold: **4 ảnh/51 đối tượng**, TP42, FP50, FN9. Precision máy=42/92=**45.7%**, recall=42/51=**82.4%**; KTC95% tương ứng khoảng36–56% và70–90%. Đây là chất lượng pre-label so với Gold, không phải precision/recall của batch sau review. Chỉ 4 ảnh, cùng tập chọn ngưỡng nên kết quả lạc quan. Không tính residual/new error vì batch export không chứa Gold.

## 4. Bẫy, hiệu quả review và giới hạn

Sau vòng sửa có hỗ trợ đối chiếu đáp án, tôi xử lý được **12/12 bẫy = 100%**. Cả bốn nhóm loose, extra, wrong_class và missing đều đạt 3/3.

Ba trường hợp tôi sửa sau khi đối chiếu là: xe máy bên phải img_003 bị gán bicycle; khung person thừa trên vùng trời ở img_018; và người áo khoác xám bị bỏ sót ở img_021. Tôi lưu thay đổi trên CVAT rồi export lại. Tuy đã xử lý đủ các bẫy, QC mẫu vẫn phát hiện lỗi ở img_003.

Qua kết quả này, tôi nhận thấy cần đánh giá riêng khả năng phát hiện bẫy và chất lượng nhãn sau review. Tỷ lệ accepted cao chưa đủ để kết luận bộ nhãn đã sạch; tôi vẫn cần rà lại nhãn sai, khung trùng và các đối tượng bị bỏ sót.

Trong lượt review tiếp theo, tôi áp dụng ba quy tắc từ CAL-03: (1) đối chiếu lớp của từng xe máy với ảnh gốc; (2) kiểm người và phương tiện thành các đối tượng riêng; (3) rà khung trùng trên cùng cá thể, kể cả khi dự đoán có confidence cao.

Theo thời lượng tôi cung cấp, review batch mất 5 phút/ảnh, cao hơn mức 3,75 phút/ảnh của Gold khoảng 33,3%. Kết quả này chưa cho thấy tiết kiệm thời gian trong bài thực nghiệm. Hai tập có số ảnh và độ phức tạp khác nhau, thời gian Gold được cung cấp sau khi hoàn thành, nên đây chỉ là so sánh mô tả, chưa tách được ảnh hưởng của pre-label. Bộ kiểm tra chỉ có 12 bẫy, với khoảng tin cậy 95% của kết quả hiện tại khoảng 76–100%; kết quả vòng sửa cũng chịu ảnh hưởng của việc đã đối chiếu đáp án.

## 5. Mask SAM 2.1 (Part 5)

Tôi tự chọn các điểm positive và negative cho ba đối tượng. Các lệnh SAM 2.1 được chạy trên CPU theo những tọa độ này.

| Ảnh | Positive / negative | Tổng điểm | Mask px | Polygon | File |
|---|---|---:|---:|---:|---|
| img_013 | 2/2 | 4 | 278890 | 1 | masks/tuktuk_img013.jpg |
| img_003 | 3/3 | 6 | 112772 | 2 | masks/three-wheeler_img003.jpg |
| img_017 | 2/2 | 4 | 45174 | 1 | masks/tuktuk_img017.jpg |

Tôi lưu **3 ảnh kết quả cho ba đối tượng** tại submission/masks/. Cả ba lệnh SAM 2.1 Base trên CPU đều hoàn tất thành công. Log lệnh, tọa độ và kết quả được lưu trong masks/logs/*.txt và .json. Đầu ra của script là JPG overlay; số polygon biểu thị các mảnh ranh giới của mask, không phải số đối tượng.

Theo guideline 4.2, mask cần bám phần nhìn thấy của đối tượng, loại bóng đổ và tách vùng giữa các vật chạm nhau. Kính thuộc xe, còn lỗ lớn cần được loại khỏi mask. Với img_013, vùng cần chú ý là ranh giới giữa người lái và xe. Ở img_003, mask còn tô vào vùng người và hàng hóa; phần này cần được rà lại theo CAL-02. Hai polygon ở ảnh này chưa đủ để kết luận mask sai, vì một đối tượng bị che có thể tạo nhiều vùng rời nhau. Với img_017, tôi cần kiểm kỹ ranh giới tại chỗ xe bị phương tiện phía trước che khuất.

Trong kế hoạch Rework, tôi sẽ rà lại phần người và hàng hóa trong từng mask theo CAL-02. Tôi chưa đo sai số đường biên hoặc IoU của mask so với một bộ segmentation Gold, nên phần đánh giá hiện tại chủ yếu dựa trên kiểm tra trực quan.

## 6. Calibration (Part 6)

| Ca | Nguồn | Trạng thái | Nội dung |
|---|---|---|---|
| CAL-01 | loi_may | Đã chốt | Tuk-tuk/ba gác/xích lô là three_wheeler, một xe gồm mái/thùng cố định; người riêng. Không đổi định nghĩa để hợp thức hóa car. |
| CAL-02 | cau_hoi_mo | Chấp nhận B theo đề xuất kỹ thuật | Mask gồm hàng nằm trong giỏ/thùng cố định, loại phần hàng nhô ra và người; box giữ v1.0. Tôi chọn phương án B sau khi xem đề xuất kỹ thuật. |
| CAL-03 | bay_lot | Đã chốt | Motorbike cho xe máy hai bánh; kiểm lớp, người/xe, khung trùng theo checklist; conf không thay việc kiểm. |

Tôi chốt **3 quyết định calibration**, gồm một ca loi_may, một ca cau_hoi_mo và một ca bay_lot. Sau khi xem đề xuất kỹ thuật, tôi chọn phương án B cho CAL-02. Các quyết định được lưu trong calibration_decisions.csv và guideline_v1.1_calibrated.md; taxonomy được cập nhật lên v1.1, giữ nguyên các ngưỡng trong rules. Tôi sẽ kiểm lại tính nhất quán của nhãn và mask theo guideline mới trong lượt Rework.

CAL-01 và CAL-03 xử lý các lỗi áp dụng quy tắc đã có, còn CAL-02 bổ sung phạm vi hàng hóa trong mask xe. Tôi sử dụng img_003, img_004, img_013 và img_031 làm ví dụ. Những thay đổi nhãn sau QC sẽ được sửa trên CVAT và xuất thành qc_fixed.json để giữ riêng với dữ liệu review trước đó.

## 7. QC, release candidate và quyết định

### Kết quả kiểm tra mẫu

Tôi kiểm mẫu **5/24 ảnh** với seed 42, gồm 0 ca can_review và 5 ảnh ngẫu nhiên: img_003, img_006, img_014, img_031, img_035. Mẫu có **110 đối tượng**. Tôi xác nhận img_003 có ba lỗi dưới đây; bốn ảnh còn lại không ghi nhận lỗi. Phiếu kiểm được lưu tại qc_review.csv, ảnh có ID tại qc/previews/.

| Ảnh | ID trong release/QC | Lỗi xác nhận | Trạng thái sửa |
|---|---|---|---|
| img_003 | 18 (trùng1) | thua: hai person cho cùng người | Chưa có export QC sửa |
| img_003 | 7 | sai_vi_tri_kich_thuoc: khung xe cắt mái | Chưa có export QC sửa |
| img_003 | 3 | sai_lop: three_wheeler ở mép phải | Chưa có export QC sửa |

ID trên là ID ảnh preview/release hiện tại, không bảo đảm trùng ID sidebar CVAT.

**Tỷ lệ lỗi theo đối tượng là 3/110 = 2.7%; tỷ lệ ảnh có lỗi là 1/5 = 20.0%.** Mỗi loại lỗi xuất hiện một lần trong mẫu. Sau khi chốt CAL-02, tôi sử dụng qc_report.py với --guideline-gap 0. Báo cáo tự động đề xuất **REWORK** do tỷ lệ ảnh có lỗi vượt ngưỡng 2%. Tôi chọn Rework để xử lý các lỗi này trước khi phát hành.

Tôi vừa là người review vừa là người QC nên kết quả kiểm chưa độc lập. Với mẫu chỉ có 5 ảnh, cận trên 95% theo Wilson của tỷ lệ ảnh lỗi thật khoảng **62%**. Vì vậy, kết quả mẫu này chưa chứng minh bộ nhãn đạt ngưỡng lỗi 2%, dù tỷ lệ xử lý bẫy ở vòng sửa đạt 100%.

### Release candidate

Tôi xây dựng bộ ứng viên gồm release/final_det.coco.json, release_summary.csv và release_manifest.json. Bộ này có **24 ảnh batch, 586 annotation**, trong đó có 585 đối tượng và 1 ignore_region. Số lượng theo lớp: person 282, motorbike 209, car 53, three_wheeler 24, bicycle 9, truck 5, bus 3 và ignore_region 1. Manifest và bảng tổng hợp khớp với nhãn vòng hai. Sau khi chốt guideline, phiên bản taxonomy trong bộ ứng viên được đồng bộ thành v1.1. Các lỗi QC chưa được sửa nên tôi chưa phát hành bộ dữ liệu này.

Bộ dữ liệu hợp lệ về cấu trúc và còn 15 cảnh báo hình học cần rà theo guideline. Tôi giữ bộ release ở trạng thái ứng viên và lưu riêng các lỗi QC để theo dõi việc sửa.

**Tôi chọn Rework, ngày 19/11/2026.** Mẫu QC có 3 lỗi trên 1/5 ảnh, tương đương 20%, vượt ngưỡng 2%. Tôi cần sửa các khung ở img_003 và rà mask theo CAL-02. Kế hoạch được lưu tại qc/rework_plan.md; sau khi sửa trên CVAT, tôi sẽ export qc_fixed.json và xây lại bộ ứng viên.

## 8. Ghi chú cá nhân và môi trường thực nghiệm

Tôi gán nhãn Gold thủ công trước khi chạy model, trực tiếp review batch trên CVAT và tự chọn các điểm positive/negative cho SAM. Tôi bổ sung thời lượng Gold là 15 phút sau khi hoàn thành bài. Do không có bản ghi bấm giờ tại thời điểm làm, tôi ghi rõ đây là thời lượng tự báo cáo và xem đó là một hạn chế của phần đánh giá thời gian.

Tôi thực hiện các thí nghiệm trên CPU với Python 3.12.10. Phiên bản thư viện được lưu trong evidence/check_env_original.txt. CVAT trên máy là 2.74.1, khác phiên bản 2.81 được dùng trong tài liệu hướng dẫn.

Log r01–r06, kết quả quét ngưỡng, export từng vòng và lệnh SAM được giữ để đối chiếu. Bản báo cáo trước khi chỉnh sửa được lưu trong evidence/REPORT_history_before_submission_cleanup.md.

Qua bài lab, tôi thấy pre-label giúp tạo bộ nhãn ban đầu nhưng vẫn cần kiểm từng đối tượng và QC sau review. Trong lượt Rework tiếp theo, tôi sẽ ưu tiên các khung trùng, nhãn lớp của phương tiện và ranh giới người–xe trong mask.
