# Mini guideline - Keypoint & Pose

Người gán: Hoàng Văn Long  
Hình thức review: Tự review  
Ngày cập nhật: 16/09/2026

Em ghi lại các quy tắc dùng khi gán nhãn và tự kiểm tra bài. Những trường hợp em chú ý nhất là tai bị mũ che, cổ tay gần tay lái và người bị cắt ở mép ảnh. Khi đối chiếu với gold, em dùng các quy tắc này để xem lại những khác biệt về visibility.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Quy tắc em áp dụng

| Tình huống | Quy tắc | Căn cứ và ảnh mẫu |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Nếu vị trí khớp bị quần áo che, dùng v=1 và ước lượng từ thân, đùi, tư thế; không đặt theo mép áo một cách máy móc. | `train_15`, người 2 mặc áo đen: vùng hông gần thân xe. Xem ảnh A. |
| Tai bị tóc hoặc mũ che một phần | Vị trí tai thấy rõ thì v=2; bị che nhưng còn trong khung thì v=1 và giữ tọa độ; không dùng một cờ chung cho mọi người đội mũ. | `train_15`, người 1 bên phải ảnh có mũ bảo hiểm che vùng tai. Xem ảnh A. |
| Người bị cắt ở mép ảnh | Xét từng khớp theo khung ảnh: khớp ngoài mép là v=0, xuất YOLO thành (0, 0, 0). Không suy ra khớp trong ảnh chỉ vì bbox nằm trong ảnh. | `train_10`, người 1: phần chân ngoài khung. Xem ảnh B. |
| Cổ tay sau tay lái / thân người | Dựa vào hướng cẳng tay và phần bàn tay còn thấy để đặt khớp; nếu chính vị trí cổ tay bị che thì v=1, không đặt chấm vào tay lái. | `train_15`, người 2 bên trái ảnh, right_wrist đang có v=1. Xem ảnh A. |
| Hai người che khuất nhau | Lần theo vai–khuỷu–cổ tay và hông–gối–mắt cá của cùng người trước khi nối; giữ trái/phải theo cơ thể. | `train_13`, người 2 phía sau người 1: cần theo đúng cánh tay bị che. Xem ảnh C. |
| Người rất nhỏ hoặc mờ | Bài hiện chưa có ngưỡng kích thước để tự bỏ người. Giữ các người thuộc bộ bài, phóng to và ghi ca khó; không tự dùng v=0 chỉ vì người nhỏ. | `train_13`, người 3 phía trái xa ảnh vẫn có skeleton. Xem ảnh C. |

Em dùng các ảnh gốc dưới đây để minh họa tình huống. Phần screenshot các điểm trong CVAT sẽ bổ sung sau.

### Ảnh A - mũ bảo hiểm, hông và cổ tay gần xe

![train_15: người 1 bên phải, người 2 bên trái](dataset/images/train/train_15.jpg)

### Ảnh B - người bị cắt phần chân

![train_10: mắt cá chân nằm ngoài khung ảnh](dataset/images/train/train_10.jpg)

### Ảnh C - che khuất giữa người và người nhỏ ở xa

![train_13: người 1 phía trước, người 2 phía sau, người 3 bên trái xa ảnh](dataset/images/train/train_13.jpg)

## 3. Ba ca mơ hồ được ghi lại khi rà soát

Số người dưới đây tính theo thứ tự dòng trong nhãn của em.

### Ca 1 - train_10.jpg, người 1, left_ankle và right_ankle

- Mơ hồ: chân không xuất hiện; cần phân biệt bị xe che với nằm ngoài ảnh.
- Trạng thái phù hợp: cả hai khớp có v=0 và tọa độ (0, 0).
- Căn cứ: ảnh cao 375 px và chỉ cho thấy phần trên cơ thể người đang nghiêng trên xe; vùng mắt cá chân nằm ngoài khung.
- Nếu chọn ngược: dùng v=1 hoặc v=2 cho khớp ngoài khung sẽ mô tả sai trạng thái của khớp và có thể tạo tọa độ không hợp lệ.

### Ca 2 - train_15.jpg, người 1 bên phải ảnh, left_ear

- Mơ hồ: có thể nhìn được một phần mặt nhưng vùng tai nằm dưới mũ bảo hiểm.
- Quyết định trong nhãn hiện tại: v=1, tọa độ chuẩn hóa (0.778029, 0.157062). Vị trí này được ước lượng do tai bị mũ che.
- Căn cứ: đầu nằm trọn trong ảnh, mũ bao vùng tai; vì vậy không dùng lý do “không thấy tai” để tự chuyển thành ngoài khung.
- Nếu chọn ngược: dùng v=0 sẽ bỏ giám sát vị trí tai; dùng v=2 sẽ ghi trạng thái nhìn thấy rõ trong khi vùng cần xác định bị che.

### Ca 3 - train_15.jpg, người 2 bên trái ảnh, right_wrist

- Mơ hồ: cổ tay ở gần tay lái, gương xe và vùng áo tối; dễ đặt nhầm lên bộ phận xe.
- Quyết định trong nhãn hiện tại: v=1, tọa độ chuẩn hóa (0.324687, 0.394203), tương đương khoảng (135.07, 252.29) px trên ảnh 416 × 640.
- Căn cứ: khớp vẫn trong ảnh; dùng hướng cẳng tay và vị trí bàn tay để ước lượng vùng nối, không lấy tâm tay lái làm khớp. Cần phóng to để rà lại độ chính xác của tọa độ.
- Nếu chọn ngược: dùng v=0 sẽ bỏ khớp bị che còn trong ảnh; đặt lên tay lái sẽ dạy model vị trí cổ tay lệch khỏi cơ thể.

## 4. Sau khi đối chiếu visibility với gold

Em tự đối chiếu nhãn của mình với gold để xem khớp nào khác nhiều về visibility. Hai bộ đều có 29 skeleton; bảng đã được lưu trong `reports/visibility_report.md` và `outputs/visibility_report.json`.

| Khớp | Em: %v=1 | Gold: %v=1 | Chênh (điểm %) |
| --- | ---: | ---: | ---: |
| left_ear | 62.07% | 3.45% | +58.62 |
| right_ear | 44.83% | 13.79% | +31.03 |
| left_eye | 27.59% | 3.45% | +24.14 |
| right_eye | 24.14% | 0.00% | +24.14 |
| left_wrist | 31.03% | 6.90% | +24.14 |

Tai trái lệch nhiều nhất: em có 18 khớp v=1, gold có 1. Vị trí thứ ba có ba khớp đồng hạng. Bảng công cụ làm tròn đến số nguyên; bảng trên tính từ số đếm và làm tròn hai chữ số sau dấu thập phân.

Qua đối chiếu, em thấy mình dùng v=1 ở tai nhiều hơn gold. Khác biệt này cần được xem trên từng ảnh vì có khớp gold ghi v=2, có khớp gold không gán. Em rút ra ba câu hỏi trước khi chọn cờ: khớp có trong khung không, có nhìn rõ vị trí không và nếu bị che thì có căn cứ nào để ước lượng. Cách kiểm tra này giúp em dùng visibility nhất quán hơn.

## 5. Việc còn cần rà soát

- `train_04`, người 1 và 2: kiểm tra các khớp v=0 đang bị cảnh báo; ảnh có phần cơ thể bị xe che và bị cắt mép nên phải xét từng khớp.
- `train_13`, người 3 và `train_16`, người 2: kiểm tra vai/hông theo cơ thể; chưa tự đảo vì cảnh báo từ hướng hai mắt.
- Bổ sung screenshot CVAT cho các ca minh họa để phần tự review có bằng chứng trực quan rõ hơn.
