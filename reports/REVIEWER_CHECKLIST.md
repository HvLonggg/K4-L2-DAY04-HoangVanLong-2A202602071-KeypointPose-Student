# Reviewer checklist - tự đánh giá bài thực hành

Người gán: Hoàng Văn Long  
Người kiểm: Hoàng Văn Long (tự review)  
Ngày: 16/09/2026

Em tự kiểm tra bài dựa trên kết quả chạy công cụ và đối chiếu với gold. Bộ nhãn đủ 29 người, đạt yêu cầu định dạng và có OKS trung bình 0.9417. Trong bảng dưới đây, em ghi rõ những mục đã đạt và những vị trí cần xem thêm.

## Công cụ và kết quả

Lệnh dùng trong PowerShell, mỗi lệnh viết trên một dòng:

```powershell
python tools/check_pose_labels.py --images dataset/images/train --labels dataset/labels/train
python tools/visibility_report.py --labels dataset/labels/train --compare gold/labels/train --out outputs/visibility_report.json --markdown reports/visibility_report.md
```

Kết quả kiểm tra hiện tại: 20/20 file, 29 skeleton, `v=2: 322`, `v=1: 142`, `v=0: 29`; 0 lỗi, 7 cảnh báo, mã trả về 0. Kết quả gold đã lưu: OKS trung bình 0.9417, OKS@0.50 và OKS@0.75 đều 1.0; ghép 29/29 người, không thiếu hoặc thừa người.

| STT | Mục kiểm | Trạng thái | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Đủ 17 vị trí keypoint, không thiếu người so với gold | Đạt theo dữ liệu | 29 skeleton; ghép đủ 29 người với gold. Điểm v=0 vẫn có bộ ba trong nhãn. |
| 2 | Không đảo trái/phải ở vai hoặc hông | Cần xem lại | Gold không phát hiện lỗi; kiểm tra hình học cảnh báo `train_13`, người 3 và `train_16`, người 2. |
| 3 | Không kéo khớp sang người khác | Chưa thấy lỗi qua công cụ | Công cụ không ghi nhận lỗi nhầm người; cần kết hợp xem ảnh để đánh giá đầy đủ. |
| 4 | Khớp bị che dùng v=1 và vẫn có tọa độ | Cần xem lại | Có 142 khớp v=1; `train_04`, người 1 và 2 còn cảnh báo nhiều v=0 trong box. |
| 5 | v=0 chỉ dùng khi khớp ngoài khung | Cần xem lại | Hai mắt cá chân của người 1 trong `train_10` và `train_13` có v=0, phù hợp với phần chân ngoài khung. Chưa xác nhận toàn bộ 29 khớp v=0. |
| 6 | Không dùng Hidden thay visibility | Cần kiểm tra thao tác | Cần kiểm tra trực tiếp trong CVAT vì file export không thể hiện đầy đủ thao tác này. |
| 7 | COCO Keypoints có 51 số mỗi người | Đạt | Đã kiểm tra tất cả annotation trong `person_keypoints_default.json`. |
| 8 | YOLO Pose có 56 số mỗi dòng, kpt_shape [17, 3] | Đạt | Đã kiểm tra các dòng nhãn; cấu hình [17, 3] được xác nhận trong notebook. |
| 9 | Có bảng visibility và đối chiếu hai bộ nhãn | Đã có báo cáo | Có `visibility_report.md` và JSON đối chiếu với gold. |
| 10 | Ghi ca mơ hồ trong guideline | Đã ghi 3 ca | Mắt cá ngoài khung; tai dưới mũ; cổ tay gần tay lái. Chưa có screenshot CVAT riêng cho các ca. |
| 11 | check_pose_labels.py chạy 0 lỗi | Đạt | Còn 7 cảnh báo; không chạy chế độ strict. |

## Các trường hợp em ghi lại khi tự kiểm tra

Số người là thứ tự dòng trong file nhãn của em, không phải annotation ID trong gold.

| Ảnh | Người thứ | Khớp / thành phần | Nội dung tự review | Kết quả / hướng rà soát |
| --- | ---: | --- | --- | --- |
| train_10.jpg | 1 | left_ankle, right_ankle | Mắt cá chân nằm ngoài khung | Nhãn hiện tại có v=0, tọa độ (0, 0). |
| train_10.jpg | 1 | bbox | Kiểm tra phạm vi bbox chuẩn hóa | Đạt yêu cầu định dạng. |
| train_13.jpg | 1 | left_ankle, right_ankle | Mắt cá chân nằm ngoài khung | Nhãn hiện tại có v=0, tọa độ (0, 0). |
| train_13.jpg | 1 | bbox | Kiểm tra phạm vi bbox chuẩn hóa | Đạt yêu cầu định dạng. |
| train_04.jpg | 1, 2 | Các khớp v=0 | Lần lượt 7 và 4 khớp v=0 trong box không sát mép | Cần xem từng khớp; không tự đổi tất cả thành v=1. |
| train_10.jpg | 1 | Các khớp v=0 | Cảnh báo 4 khớp v=0 trong bbox không sát mép | Ảnh cắt phần chân; kiểm tra theo khung ảnh, không chỉ theo box. |
| train_13.jpg | 3 | Vai và hông trái/phải | Chiều trái/phải khác hai mắt | Cần soi lại; chưa xác nhận đảo khớp. |
| train_16.jpg | 2 | Vai và hông trái/phải | Chiều trái/phải khác hai mắt | Cần soi lại; chưa xác nhận đảo khớp. |
| train_01.jpg | 1 | left_wrist | Gold báo lệch nhẹ 34 px | Cần đối chiếu lại điểm cổ tay trên ảnh. |
| train_01.jpg | 2 | right_wrist | Gold báo lệch nhẹ 29 px | Cần đối chiếu lại điểm cổ tay trên ảnh. |

## Kết luận

- Phần khác gold nhiều nhất cần xem lại là visibility, với 56 trường hợp. Tai trái lệch tỷ lệ v=1 nhiều nhất: em 62.07%, gold 3.45%, chênh 58.62 điểm phần trăm. Ngoài ra có 6 trường hợp lệch nhẹ; 72 trường hợp gold không gán khớp không được coi là 72 lỗi vị trí.
- Em đánh giá bài đạt yêu cầu về định dạng và có vị trí khớp khá sát gold. Phần cần làm kỹ hơn là visibility và các cảnh báo ở vai, hông; em sẽ dựa vào ảnh để kiểm tra từng trường hợp.
