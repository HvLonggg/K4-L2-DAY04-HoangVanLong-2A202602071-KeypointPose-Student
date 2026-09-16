# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.14 khớp có v > 0 mỗi người
- Tổng: v=2 325 | v=1 143 | v=0 25

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 7 | 0 | 24% |
| 1 | left_eye | 21 | 8 | 0 | 28% |
| 2 | right_eye | 22 | 7 | 0 | 24% |
| 3 | left_ear | 11 | 18 | 0 | 62% |
| 4 | right_ear | 16 | 13 | 0 | 45% |
| 5 | left_shoulder | 24 | 5 | 0 | 17% |
| 6 | right_shoulder | 25 | 4 | 0 | 14% |
| 7 | left_elbow | 21 | 8 | 0 | 28% |
| 8 | right_elbow | 25 | 4 | 0 | 14% |
| 9 | left_wrist | 20 | 9 | 0 | 31% |
| 10 | right_wrist | 19 | 9 | 1 | 31% |
| 11 | left_hip | 19 | 9 | 1 | 31% |
| 12 | right_hip | 20 | 8 | 1 | 28% |
| 13 | left_knee | 16 | 9 | 4 | 31% |
| 14 | right_knee | 18 | 7 | 4 | 24% |
| 15 | left_ankle | 14 | 8 | 7 | 28% |
| 16 | right_ankle | 12 | 10 | 7 | 34% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
