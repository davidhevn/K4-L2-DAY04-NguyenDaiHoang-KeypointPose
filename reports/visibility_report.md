# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.31 khớp có v > 0 mỗi người
- Tổng: v=2 357 | v=1 116 | v=0 20

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 23 | 6 | 0 | 21% |
| 1 | left_eye | 23 | 6 | 0 | 21% |
| 2 | right_eye | 23 | 6 | 0 | 21% |
| 3 | left_ear | 12 | 17 | 0 | 59% |
| 4 | right_ear | 15 | 14 | 0 | 48% |
| 5 | left_shoulder | 27 | 2 | 0 | 7% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 25 | 4 | 0 | 14% |
| 8 | right_elbow | 26 | 3 | 0 | 10% |
| 9 | left_wrist | 19 | 10 | 0 | 34% |
| 10 | right_wrist | 21 | 7 | 1 | 24% |
| 11 | left_hip | 23 | 6 | 0 | 21% |
| 12 | right_hip | 25 | 4 | 0 | 14% |
| 13 | left_knee | 19 | 8 | 2 | 28% |
| 14 | right_knee | 20 | 7 | 2 | 24% |
| 15 | left_ankle | 13 | 8 | 8 | 28% |
| 16 | right_ankle | 15 | 7 | 7 | 24% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
