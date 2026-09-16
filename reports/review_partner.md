# Review partner - Nguyễn Đại Hoàng tự kiểm tra bài

Người kiểm: Nguyễn Đại Hoàng   Bài được kiểm: Nguyễn Đại Hoàng (Tự review)   Ngày: 16/09/2026

> **Lưu ý:** Mục kiểm chéo chờ bạn cùng nhóm cung cấp dữ liệu labels để so sánh.
> Phần dưới đây ghi lại **các lỗi tìm được trong bài của chính mình** trong quá trình tự rework (Chặng 5) — dùng làm tài liệu tham chiếu cho peer review.

---

## Lỗi phát hiện khi tự rework (từ `reports/REPORT.md` và `GUIDELINE_MINI.md`)

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| train_10 | 1 | left_hip, right_hip, left_knee, right_knee | Đặt v=0 (Outside) nhưng người nằm gọn giữa khung ảnh, khớp bị xe che chứ không ra ngoài mép | Đổi sang v=1 (Occluded), đặt chấm ước lượng tại vị trí thắt lưng/đầu gối phía sau xe máy |
| train_16 | 1 | left_shoulder, right_shoulder | Đảo trái/phải — gán theo phản xạ góc camera thay vì person-centric; terminal báo lỗi vai ngược chiều mắt | Đổi tọa độ left_shoulder ↔ right_shoulder theo quy tắc trục xương sống |
| train_16 | 1 | left_hip, right_hip | Đảo trái/phải — hệ quả trực tiếp của lỗi vai, toàn bộ thân trên-dưới bị lệch chiều | Đổi tọa độ left_hip ↔ right_hip đồng bộ với vai |

---

## Bảng so sánh visibility report (bỏ qua do làm bài cá nhân)

> Bỏ qua phần so sánh tỷ lệ %v=1 do làm bài cá nhân, không có bảng visibility của bạn cùng nhóm để so sánh chéo.

*(Bảng trên lấy số liệu từ `outputs/visibility_report.json` — v=2:347 / v=1:109 / v=0:20)*

---

## Checklist (Tự đánh giá lại bài làm của mình)

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm | ✅ | 29 skeleton × 51 keypoints |
| 2 | Bật đường nối: không có xương cắt chéo | ✅ | Đã phát hiện và tự sửa ở `train_16`, `train_13` |
| 3 | Không có xương kéo dài sang cơ thể khác | ✅ | Tốt |
| 4 | Khớp bị che dùng `v = 1` **và có chấm** | ✅ | Tất cả 116 điểm v=1 đều có tọa độ > 0 |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ✅ | 20 điểm v=0 đều có x,y=0 |
| 6 | Không dùng sai tính năng `Hidden` | ✅ | Hợp lý với ảnh giao thông |
| 7 | Export đúng COCO Keypoints 1.0 (51 số/người) | ✅ | File JSON đạt chuẩn |
| 8 | YOLO Pose: 56 cột, kpt_shape [17,3] | ✅ | Đã kiểm tra `data.yaml` và các file txt |
| 9 | Visibility report đã nộp | ✅ | Đã nộp file `outputs/visibility_report.json` |
| 10 | Ca mơ hồ ghi trong GUIDELINE_MINI.md | ✅ | Đã ghi nhận đầy đủ |

---

## Lỗi tìm được

1. **(Đã tự phát hiện và sửa)** train_13: Thiếu hẳn một người ở phía xa
2. **(Đã tự phát hiện và sửa)** train_16: Đảo trái/phải ở hông và vai người thứ nhất
3. **(Cảnh báo OKS)** train_15 và train_11: Điểm OKS thấp (do occluded quá nhiều)

---

## Kết luận

- Lỗi phổ biến nhất phát hiện được: **Đảo trái/phải** (`train_16`) và **dùng v=0 thay v=1** khi khớp bị che nhưng vẫn trong khung (`train_10`).
- Phân loại lỗi: **Lỗi thao tác** — guideline đã có quy định rõ, nhưng dễ làm sai khi xử lý nhanh các ảnh khó góc nhìn.
