# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.0 khớp có v > 0 mỗi người
- Tổng: v=2 322 | v=1 142 | v=0 29

So sánh với `gold\labels\train` (29 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 3 | left_ear | 62% | 3% | 59 |
| 4 | right_ear | 45% | 14% | 31 |
| 9 | left_wrist | 31% | 7% | 24 |
| 1 | left_eye | 28% | 3% | 24 |
| 2 | right_eye | 24% | 0% | 24 |
| 0 | nose | 24% | 3% | 21 |
| 7 | left_elbow | 28% | 7% | 21 |
| 16 | right_ankle | 34% | 24% | 10 |
| 5 | left_shoulder | 17% | 7% | 10 |
| 6 | right_shoulder | 14% | 3% | 10 |
| 8 | right_elbow | 14% | 3% | 10 |
| 10 | right_wrist | 31% | 21% | 10 |
| 13 | left_knee | 31% | 21% | 10 |
| 14 | right_knee | 24% | 14% | 10 |
| 12 | right_hip | 28% | 21% | 7 |
| 15 | left_ankle | 24% | 17% | 7 |
| 11 | left_hip | 31% | 28% | 3 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
