# Reviewer checklist - điền khi kiểm bài người khác

Người gán: Nguyễn Đại Hoàng   Người kiểm: Nguyễn Đại Hoàng (Tự review)   Ngày: 16/09/2026

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ✅ | 29 skeleton × 51 keypoints (17×3) — xác nhận từ `annotations/coco_keypoints/person_keypoints_default.json` |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ✅ | Đã phát hiện và sửa `train_16`, `train_13` (vai và hông bị đảo trái/phải gây cắt chéo), hiện đã đúng |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ✅ | `train_16` có 2 người — skeleton tách biệt, kiểm qua visualize_pose |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ✅ | 116 điểm v=1, tất cả có tọa độ x,y > 0 — xác nhận từ script kiểm label |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ✅ | 20 điểm v=0, toàn bộ tọa độ x,y = 0 — chủ yếu ở `left_ankle`/`right_ankle` (v0=8/7) và `left_knee`/`right_knee` (v0=2/2) |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ✅ | Không phát hiện điểm v=2 bất thường; 26/29 skeleton có ít nhất 1 điểm v=1 (phù hợp với tập ảnh xe máy) |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ✅ | 29/29 annotation có đúng 51 số — xác nhận từ `annotations/coco_keypoints/person_keypoints_default.json` |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ✅ | Tất cả 29 dòng đều có 56 cột; `data.yaml` có `kpt_shape: [17, 3]` đúng định dạng |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ✅ | `outputs/visibility_report.json` + `reports/visibility_report.md` đều có — phần so bảng bỏ qua do làm cá nhân |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ✅ | 3 ca mơ hồ được ghi đầy đủ (train_10/hông, train_16/vai, train_20/mắt cá), kèm ảnh CVAT minh hoạ |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ✅ | Đã chạy và xác nhận "ĐẠT định dạng" |

## Lỗi tìm được

Chép sang `reports/review_partner.md`. Mỗi dòng một lỗi, đủ bốn cột - người sửa phải
mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| train_10 | 1 | left_hip, right_hip, left_knee, right_knee | v=0 (Outside) nhưng khớp vẫn nằm trong khung, bị xe che — sai rule | Đổi sang v=1, đặt chấm ước lượng tại vị trí thắt lưng/đầu gối phía sau xe |
| train_16 | 1 | left_shoulder, right_shoulder | Đảo trái/phải — gán theo góc camera thay vì person-centric | Đổi tọa độ left_shoulder ↔ right_shoulder theo trục xương sống |
| train_16 | 1 | left_hip, right_hip | Đảo trái/phải — hệ quả của lỗi vai ở trên | Đổi tọa độ left_hip ↔ right_hip đồng bộ với vai |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: **Đảo trái/phải** — xảy ra ở `train_16` khi người đứng chéo/quay lưng, dễ nhầm vì phản xạ nhìn theo góc camera thay vì góc cơ thể người.
- Nó là lỗi **thao tác** — guideline đã nói rõ "trái/phải tính theo cơ thể người", nhưng khi làm nhanh ở ảnh có góc người bất thường thì vẫn bị phản xạ thị giác dẫn sai.
