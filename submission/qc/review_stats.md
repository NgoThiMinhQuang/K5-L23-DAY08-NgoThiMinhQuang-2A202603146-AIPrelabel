# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 621; đối tượng sau duyệt: 571; ngưỡng accepted: IoU >= 0.9
- Đã **loại 27 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 495 | 495/621 = 79.7% | 621 box máy |
| edited (sửa) | 38 | 38/621 = 6.1% | 621 box máy |
| deleted (xoá) | 85 | 85/621 = 13.7% | 621 box máy |
| added (vẽ thêm) | 38 | 38/571 = 6.7% | 571 đối tượng sau duyệt |

Trong số deleted + added, **8 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 30.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 258 | 4 | 8 | 14 |
| bicycle | 6 | 0 | 2 | 3 |
| motorbike | 167 | 24 | 44 | 13 |
| car | 46 | 1 | 6 | 4 |
| bus | 2 | 0 | 1 | 1 |
| truck | 3 | 0 | 3 | 2 |
| three_wheeler | 13 | 9 | 21 | 1 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 51 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 42 | 50 | 9 | 45.7% (KTC 95%: 36–56%) | 82.4% (KTC 95%: 70–90%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 12/12 = 100.0% (KTC 95%: 76–100%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 3/3
  - extra: 3/3
  - wrong_class: 3/3
  - missing: 3/3

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
