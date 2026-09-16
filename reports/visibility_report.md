# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 16.29 khớp có v > 0 mỗi người
- Tổng: v=2 347 | v=1 109 | v=0 20

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 23 | 5 | 0 | 18% |
| 1 | left_eye | 23 | 5 | 0 | 18% |
| 2 | right_eye | 23 | 5 | 0 | 18% |
| 3 | left_ear | 12 | 16 | 0 | 57% |
| 4 | right_ear | 15 | 13 | 0 | 46% |
| 5 | left_shoulder | 26 | 2 | 0 | 7% |
| 6 | right_shoulder | 27 | 1 | 0 | 4% |
| 7 | left_elbow | 25 | 3 | 0 | 11% |
| 8 | right_elbow | 25 | 3 | 0 | 11% |
| 9 | left_wrist | 19 | 9 | 0 | 32% |
| 10 | right_wrist | 20 | 7 | 1 | 25% |
| 11 | left_hip | 22 | 6 | 0 | 21% |
| 12 | right_hip | 24 | 4 | 0 | 14% |
| 13 | left_knee | 18 | 8 | 2 | 29% |
| 14 | right_knee | 19 | 7 | 2 | 25% |
| 15 | left_ankle | 12 | 8 | 8 | 29% |
| 16 | right_ankle | 14 | 7 | 7 | 25% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
