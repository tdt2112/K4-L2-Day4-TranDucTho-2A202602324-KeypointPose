# Reviewer checklist - Biên bản đánh giá chéo bài bạn cùng nhóm

Người gán: Bạn cùng nhóm (Partner)   |   Người kiểm: Trần Đức Thọ   |   Ngày: 16/09/2026

## 1. Công cụ đã chạy kiểm tra
```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels ../partner/dataset/labels/train
python3 tools/visualize_pose.py --images dataset/images/train --labels ../partner/dataset/labels/train --out outputs/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare ../partner/dataset/labels/train --markdown reports/visibility_compare.md
```

## 2. Bảng kiểm tra chất lượng (Checklist)

| # | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
|---|---|:---:|---|
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ Đạt | Đủ 29 skeleton trên 20 ảnh |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☒ Chưa đạt | `train_06.jpg` và `train_09.jpg` có xương cắt chéo ở thân |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ Đạt | Không phát hiện lỗi nhầm người (`nham_nguoi`) |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☒ Cần lưu ý | `train_04.jpg` và `train_10.jpg` để `v = 0` cho khớp chân nằm giữa ảnh |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☑ Đạt | Đa số khớp `v = 0` nằm ở rìa cắt ảnh |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ Đạt | Không dùng phím `h` |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ Đạt | Kiểm tra file JSON có đúng 51 số |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ Đạt | `kpt_shape` đúng [17, 3] |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☑ Đạt | Đã so sánh chéo |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☑ Đạt | Đã cập nhật quy định hông, tai, cổ tay |
| 11 | `check_pose_labels.py` chạy 0 lỗi định dạng | ☑ Đạt | 0 lỗi chặn nộp |

## 3. Danh sách lỗi tìm được (Findings)

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_06.jpg` | 1 | Toàn bộ 17 khớp | Đảo trái/phải (`dao_trai_phai`) do nhầm góc nhìn của người chụp khi nhân vật quay lưng | Đảo lại toàn bộ các cặp khớp trái/phải theo hệ quy chiếu giải phẫu cơ thể người |
| `train_09.jpg` | 1 | Vai, khuỷu, cổ tay, chân | Đảo trái/phải (`dao_trai_phai`) và trượt khớp cổ chân | Hoán đổi lại các cặp điểm đối xứng và kéo điểm cổ chân về đúng tâm mắt cá chân |
| `train_17.jpg` | 1 | `right_ankle` | Trượt hẳn (`truot_han`) lệch 128 px ra ngoài lề đường | Kéo chấm về vị trí tâm mắt cá chân phải (gần x ≈ 0.552, y ≈ 0.806) |
| `train_04.jpg` | 1 | `left_knee`, `right_knee` | Xoá khớp bị che / nhầm `v = 0` thay vì `v = 1` | Đổi cờ sang `v = 1` (Occluded - phím `q`) và đặt chấm ước lượng theo trục xương đùi |

## 4. Hai câu kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** Lỗi đảo trái/phải (`dao_trai_phai`) khi đối tượng quay lưng hoặc đứng nghiêng so với ống kính máy ảnh, và lỗi lạm dụng cờ `v = 0` (Outside) cho các khớp nằm giữa ảnh bị che khuất thay vì dùng `v = 1` (Occluded).
- **Nó là lỗi thao tác hay lỗi guideline chưa rõ?** Lỗi đảo trái/phải chủ yếu là do **thao tác vội vàng** khi gặp các tư thế nhìn ngược hướng; còn lỗi nhầm giữa `v = 0` và `v = 1` là do **guideline ban đầu chưa nhấn mạnh sự khác biệt** giữa "khớp ra khỏi khung hình" (`v = 0`) và "khớp bị che khuất nhưng còn trong khung hình" (`v = 1`).
