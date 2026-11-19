# Kế hoạch Rework — 19/11/2026

Quyết định Rework được học viên chấp nhận theo đề xuất kỹ thuật. QC đã xác nhận3lỗi trong1/5ảnh=20%, không đủ điều kiệnRelease. Nhãn/mask chưa tự sửa.

## 1. Sửa trực tiếp trên CVAT

Ở img_003: (a) tìm hai khung person quanh cùng người đàn ông sau em bé, giữ khung đúng và xóa khung trùng; (b) kéo lại khung xe ba bánh lớn để bao đủ mái, thân và bánh nhìn thấy; (c) kiểm phương tiện mép phải đang mang three_wheeler và đổi về lớp đúng theo ảnh. ID18/7/3 trên preview QC không bảo đảm trùng IDsidebarCVAT, nên tìm theo vị trí/vật thật. Không xóa hoặc đổi lớp mù theo ID.

## 2. Kiểm mask theo CAL-02B

Giữ giỏ/thùng cố định và hàng nằm trong đó; loại người và phần hàng nhô ra. Xem ba mask user-point img_013/img_003/img_017. Nếu phải sửa, học viên tự chọn điểm mới, ghi lệnh và xuất kết quả thật; không tự sinh lại từ điểmAI hoặc ghi đã đạt khi chưa xem.

## 3. Xuất và kiểm sau sửa

Save trên CVAT, exportCOCO1.0 không kèm ảnh; giữ bằng chứng vòng hiện tại. Theo GUIDE, lưu nhãn sửaQC vào submission/labels/qc_fixed.json đúng phạm vi ảnh sửa, rồi chạy:

```powershell
.\.venv\Scripts\python.exe scripts/build_release.py
.\.venv\Scripts\python.exe scripts/qc_sample.py --n 5 --seed 42
```

Nếu muốn tạo lại phiếuQC, sao lưu phiếu và ảnh hiện tại trước khi dùng --force, vìIDcó thể đổi. Học viên phải thực sự kiểm lại rồi mới chạyqc_report.py. Không dùng5ảnh của vòng trước để chứng minh bộ sửa mới sạch.

## 4. Hồ sơ và giới hạn

Có thể báo cáoRework trung thực theo rubric; không bắt buộc bịaRelease. Học viên cung cấp bổ sung thời lượngGold15phút sau khi hoàn thành; time_log ghi rõ nguồn tự báo cáo và không có log bấm giờ. Không sửaGold để tạo lại lịch sử hoặc giả thời gian. Chưapush/phát hành cuối.
