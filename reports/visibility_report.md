# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.1 khớp có v > 0 mỗi người
- Tổng: v=2 328 | v=1 139 | v=0 26

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 23 | 6 | 0 | 21% |
| 1 | left_eye | 22 | 7 | 0 | 24% |
| 2 | right_eye | 22 | 7 | 0 | 24% |
| 3 | left_ear | 14 | 15 | 0 | 52% |
| 4 | right_ear | 18 | 11 | 0 | 38% |
| 5 | left_shoulder | 27 | 2 | 0 | 7% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 20 | 9 | 0 | 31% |
| 8 | right_elbow | 21 | 8 | 0 | 28% |
| 9 | left_wrist | 17 | 12 | 0 | 41% |
| 10 | right_wrist | 19 | 9 | 1 | 31% |
| 11 | left_hip | 17 | 12 | 0 | 41% |
| 12 | right_hip | 21 | 7 | 1 | 24% |
| 13 | left_knee | 13 | 12 | 4 | 41% |
| 14 | right_knee | 16 | 9 | 4 | 31% |
| 15 | left_ankle | 16 | 5 | 8 | 17% |
| 16 | right_ankle | 14 | 7 | 8 | 24% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
