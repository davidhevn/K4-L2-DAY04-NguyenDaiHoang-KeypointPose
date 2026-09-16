# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Đại Hoàng   Nhóm: Cá nhân   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 357 / 116 / 20 |
| Thời gian trung bình mỗi ảnh | 5 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. left_ear (59%)
2. right_ear (48%)
3. left_wrist (34%)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.
Đúng. Tai thường xuyên bị che khuất một phần hoặc toàn phần bởi mũ bảo hiểm (do tập ảnh giao thông) hoặc góc quay ngang đầu. Cổ tay (wrist) thường bị tay lái xe hoặc chính thân hình người (khi xoay góc chéo) che khuất, đòi hỏi phải ước lượng vị trí liên tục.

## 2. Chấm với gold

> **Lưu ý:** Bộ gold Day 4 chưa được phát hành — bảng OKS sẽ được cập nhật ngay khi nhận được file gold và chạy `tools/evaluate_pose_annotations.py`.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.928 | 0.928 |
| OKS@0.50 | 0.966 | 1.000 |
| OKS@0.75 | 0.966 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy**:

- `train_10` + người 1 + hông/chân: Đổi từ `v=0` (outside) sang `v=1` (occluded) vì khớp vẫn nằm giữa viền khung ảnh, chỉ bị xe che lấp.
- `train_13` + người 1: Bổ sung thêm 1 người bị thiếu (skeleton thứ 29) để giải quyết lỗi "Thiếu hẳn một người" với OKS = 0.
- `train_16` + người 1 + vai (`left_shoulder/right_shoulder`): Đảo lại vị trí trái/phải do gán ngược chiều (person-centric).
- `train_16` + người 1 + hông (`left_hip/right_hip`): Tương tự, đảo lại vị trí trái/phải để đồng bộ cấu trúc thân người.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?
Xảy ra ở `train_16` và `train_13`. Đây là các ca khó do góc chụp chéo phía sau / đối diện, dễ gây nhầm lẫn nếu gán theo phản xạ thị giác (góc nhìn của người gán) thay vì áp dụng mường tượng góc nhìn từ cơ thể đối tượng trong ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: Làm bài cá nhân (Tự review)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| N/A | — | — | — | Do làm bài cá nhân nên chưa có dữ liệu bạn nhóm để so sánh. |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Bỏ qua do làm cá nhân, tự thống nhất với guideline gốc.

## 4. Model

*(Số liệu lấy từ `outputs/eval_model.json` — notebook Colab fine-tune 80 epoch)*

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50 | 0.9785 | 0.9600 | -0.0185 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**
`pose_mAP50-95` tăng nhẹ `+0.0055` (cùng với `precision` tăng `+0.0058`). Tập dữ liệu 20 ảnh giúp model tinh chỉnh chính xác hơn các góc khớp xương đặc thù (ngồi xe). Tuy nhiên, `box_mAP50-95` lại giảm `-0.0078`, cho thấy việc fine-tune quá ít dữ liệu khiến task nhận diện Box bắt đầu có dấu hiệu bị overfit/nhiễu.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?**
`box_mAP50` (`0.9600`) cao hơn rất nhiều so với `pose_mAP50` (`0.8450`). Model tìm **người** dễ hơn rất nhiều. Phát hiện box chỉ cần bắt được vùng đặc trưng tổng thể; trong khi tìm khớp (pose) yêu cầu toạ độ vi mô chính xác của 17 điểm, chịu ảnh hưởng rất lớn từ việc bị che khuất và sai số đánh giá OKS khắt khe.

3. **Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43:**
Trên các ảnh test (ví dụ test_02 hoặc test_06), model thỉnh thoảng gặp lỗi **đảo trái/phải** ở các góc người bị xoay chéo/quay lưng, và lỗi **lệch nhẹ** tại các khớp cổ tay/cổ chân ngoại vi khi bị lấp bởi xe.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**
Ảnh `train_15` (OKS: 0.598) và `train_11` (OKS: 0.607). Ngoài ra còn lệch hẳn số người ở `train_10`, `train_13`, `train_03`. Người gán nhãn đúng vì bám sát nguyên tắc không gán những bóng người quá mờ ở xa; trong khi model bắt cả những chủ thể mờ ở hậu cảnh và dự đoán sai toạ độ khớp bị che.

5. **Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**
Có sự trùng khớp. Những ảnh có chỉ số OKS thấp nhất đều nằm ở các ca có độ che khuất (occlusion) rất cao hoặc góc camera dị. Điều này chứng minh độ khó nội tại của bức ảnh (inherent ambiguity) là có thật, khiến cả người gán nhãn lẫn model đều khó chốt được điểm cực trị tuyệt đối.

## 5. Một rule evidence bạn đã dùng

Ở ảnh `train_20`, người 1, khớp `right_ankle`. Bàn chân mặc đồ đen của người điều khiển xe tệp hoàn toàn vào bóng râm mặt đường nên mất chi tiết viền. Tuy nhiên, căn cứ vào đầu gối bên phải (`right_knee`) và gác chân của xe máy ở ngay dưới, tôi vẫn xác định được trục cẳng chân. Do đó, tôi áp dụng rule "Bị che nhưng có cơ sở giải phẫu/không gian" -> Chọn `v=1` và ước lượng vị trí tại gác chân xe máy, thay vì đẩy nó thành `v=0`.