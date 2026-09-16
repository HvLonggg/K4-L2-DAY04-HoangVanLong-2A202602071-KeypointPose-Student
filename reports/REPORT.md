# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Hoàng Văn Long  
Ngày viết báo cáo: 16/09/2026

Trong bài này, em thực hiện gán nhãn 17 điểm COCO cho 20 ảnh, tự kiểm tra kết quả với gold và chạy fine-tune YOLO26n-pose. Qua bài thực hành, em hiểu rõ hơn cách xác định vị trí khớp, phân biệt các trạng thái visibility và đánh giá mô hình bằng OKS, mAP. Các số liệu dưới đây lấy từ kết quả chấm nhãn và notebook đã chạy.

## 1. Nhãn của em

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh train đã gán | 20 |
| Số skeleton train | 29 |
| Tổng số vị trí keypoint, gồm cả v=0 | 493 |
| v=2 / v=1 / v=0 | 322 / 142 / 29 |
| Số ảnh test phát sẵn | 10 |
| Số skeleton test | 13 |
| Thời gian trung bình mỗi ảnh | Chưa có nhật ký thời gian để tính |

Ba khớp có tỷ lệ `v=1` cao nhất, tính trên 29 skeleton:

1. `left_ear`: 18/29, tương đương 62.07%.
2. `right_ear`: 13/29, tương đương 44.83%.
3. `right_ankle`: 10/29, tương đương 34.48%.

Em thấy tai là vị trí dễ phải cân nhắc cờ visibility, nhất là ở ảnh có mũ bảo hiểm như `train_15.jpg`. Tuy nhiên, tỷ lệ bị che cao không có nghĩa đây là khớp em đặt lệch nhiều nhất. Kết quả gold còn chỉ ra lỗi lệch nhẹ ở cổ tay, ví dụ `left_wrist` của người 1 trong `train_01.jpg` lệch 34 px.

Kết quả kiểm tra xác nhận đủ 20/20 file nhãn, 29 skeleton và không còn lỗi định dạng. Số lượng visibility thống nhất với output kiểm tra trong notebook. Hiện còn 7 cảnh báo cần rà soát bằng ảnh; các cảnh báo này không chặn bước huấn luyện.

## 2. Chấm với gold

Khi so sánh bộ nhãn sau rà soát với gold, em thu được OKS trung bình 0.9417. Cả 29 cặp người đều đạt ngưỡng OKS 0.75, cho thấy vị trí các khớp được chấm khá sát với gold. Em chỉ lưu một lần đánh giá nên báo cáo trình bày kết quả hiện tại, chưa so sánh trước và sau rà soát.

| Chỉ số | Kết quả hiện tại |
| --- | ---: |
| OKS trung bình | 0.9417 |
| OKS@0.50 | 1.0 |
| OKS@0.75 | 1.0 |
| Lỗi `dao_trai_phai` | 0 |
| Lỗi `nham_nguoi` | 0 |
| Lỗi `xoa_khop_bi_che` | 0 |

Công cụ ghép được 29/29 người, không thiếu và không thừa người. Các kết quả cần chú ý gồm 6 lỗi `lech_nhe`, 56 trường hợp `co_khac_gold` và 72 trường hợp `gold_khong_gan_nhan`. Nhóm cuối là các khớp gold có `v=0`, bị loại khỏi phép tính OKS; không thể coi cả 72 trường hợp là lỗi vị trí của em.

**Nhận xét về chất lượng nhãn**

Điểm tích cực là bộ nhãn bao phủ đủ số người trong tập bài, không có trường hợp thiếu hoặc thừa người khi ghép với bộ tham chiếu. Công cụ đánh giá không ghi nhận các lỗi đảo trái/phải, nhầm người hoặc xóa khớp bị che. Những khác biệt còn lại giúp em xác định rõ phần cần cải thiện tiếp, chủ yếu là độ chính xác của một số cổ tay và cách phân biệt trạng thái visibility.

Qua rà soát các ảnh như `train_10.jpg` và `train_13.jpg`, em chú ý hơn đến việc phân biệt khớp ngoài khung với khớp bị che. Đây là bước quan trọng để bộ nhãn vừa đạt yêu cầu định dạng, vừa thể hiện đúng trạng thái của từng khớp trong ảnh.

Kết quả chấm gold không phát hiện lỗi đảo trái/phải trong 20 ảnh. Tuy vậy, script kiểm tra hình học vẫn cảnh báo vai và hông ở `train_13`, người 3 và `train_16`, người 2. Em hiểu đây là dấu hiệu cần xem lại ảnh, chưa đủ để kết luận đã đảo khớp.

## 3. Tự kiểm tra bài và đối chiếu với gold

Em tự kiểm tra bài của mình, sử dụng bộ nhãn gold trong `gold/labels/train` làm tham chiếu. Nội dung kiểm tra gồm kiểm tra số người, mức tương đồng của vị trí khớp và sự khác biệt về visibility.

Kết quả ở mục 2 cho thấy bộ nhãn đủ số người và có độ tương đồng tốt về vị trí. Khi xem kỹ hơn bảng visibility, em nhận thấy cách chọn cờ ở tai, mắt và cổ tay còn khác gold khá nhiều. Đây là phần em cần chú ý thêm dù điểm OKS đã cao.

### Các khớp lệch tỷ lệ v=1 nhiều nhất

Em đếm trực tiếp hai bộ nhãn, mỗi bộ có 29 skeleton. Tỷ lệ bằng số khớp `v=1` chia cho 29; chênh lệch là tỷ lệ của em trừ tỷ lệ gold, tính theo điểm phần trăm và làm tròn sau khi tính.

| Khớp | Em: số v=1 / tỷ lệ | Gold: số v=1 / tỷ lệ | Chênh (điểm %) |
| --- | ---: | ---: | ---: |
| left_ear | 18 / 62.07% | 1 / 3.45% | +58.62 |
| right_ear | 13 / 44.83% | 4 / 13.79% | +31.03 |
| left_eye | 8 / 27.59% | 1 / 3.45% | +24.14 |
| right_eye | 7 / 24.14% | 0 / 0.00% | +24.14 |
| left_wrist | 9 / 31.03% | 2 / 6.90% | +24.14 |

Tai trái có chênh lệch lớn nhất: em có 18 khớp v=1, còn gold có 1. Ba khớp left_eye, right_eye và left_wrist cùng chênh 24.14 điểm phần trăm. Qua bảng này, em thấy mình dùng v=1 nhiều hơn gold ở một số vị trí. Em cần xem lại từng khớp trên ảnh để phân biệt khớp nhìn rõ, bị che hoặc không được gold gán nhãn; chỉ nhìn tỷ lệ thì chưa xác định được nguyên nhân.

Quy tắc em rút ra khi tự kiểm tra là: khớp thấy rõ thì chọn `v=2`; khớp bị che nhưng còn trong khung và có căn cứ ước lượng vị trí thì giữ chấm và chọn `v=1`; khớp ngoài mép ảnh thì chọn `v=0`. Với tai bị mũ che, cần xem phần đầu và tai còn lộ để quyết định, không chỉ dựa vào việc người đó có đội mũ. Quy tắc này được ghi trong `GUIDELINE_MINI.md` để em áp dụng nhất quán khi tự review.

## 4. Model

Em chạy notebook trên Colab bằng GPU Tesla T4. Log ghi Ultralytics 8.4.153, Python 3.13.15 và PyTorch 2.11.0+cu128.

| Thông số | Giá trị đã chạy |
| --- | --- |
| Model khởi tạo | yolo26n-pose.pt |
| Train / test | 20 ảnh, 29 người / 10 ảnh, 13 người |
| kpt_shape | [17, 3] |
| epochs đặt tối đa | 80 |
| Epoch thực tế hoàn thành | 39 |
| Epoch có checkpoint tốt nhất | 9 |
| imgsz / batch / workers | 640 / 8 / 2 |
| patience | 30 |
| seed | 20260915 |
| fliplr | 0.5 |
| Thời gian train theo log | 0.022 giờ, khoảng 79.2 giây |
| Checkpoint sử dụng | outputs/runs/pose_finetune/weights/best.pt |

Train dừng sớm vì 30 epoch liên tiếp không cải thiện tiêu chí chọn checkpoint. Em dùng kết quả đánh giá riêng ở mục 4 của notebook, sau khi nạp `best.pt`, để lập bảng dưới đây; không lấy số validation được in ngay cuối quá trình train.

| Chỉ số | YOLO26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50 | 0.9785 | 0.9600 | -0.0185 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

Số liệu lấy từ bảng đánh giá trong notebook `day4_pose_finetune_yolo26.ipynb`, sau khi nạp checkpoint tốt nhất. Đây là kết quả của lần chạy đã lưu trong notebook.

### Trả lời năm câu hỏi cuối notebook

**1. pose_mAP50-95 thay đổi bao nhiêu?**

Chỉ số tăng từ 0.6853 lên 0.6908, tức tăng 0.0055 hay 0.55 điểm phần trăm. Đây là tín hiệu tích cực trong phạm vi bài thực hành: chất lượng pose cải thiện nhẹ, pose_precision tăng thêm 0.0058, trong khi pose_mAP50 và pose_recall được duy trì. Tuy nhiên, box_mAP50-95 giảm 0.0078, nên kết quả cho thấy sự cải thiện ở pose chứ chưa phải ở tất cả chỉ số. Tập 10 ảnh test còn được dùng làm validation để chọn checkpoint, nên đây là kết quả của bài thực hành, chưa phải đánh giá trên một tập test độc lập hoàn toàn.

**2. box_mAP và pose_mAP chênh nhau bao nhiêu?**

Sau fine-tune, ở ngưỡng 50–95, chênh lệch là `0.8041 - 0.6908 = 0.1133`, tương đương 11.33 điểm phần trăm. Ở ngưỡng 50, chênh lệch là `0.9600 - 0.8450 = 0.1150`. Trong lần chạy này, kết quả phát hiện người cao hơn kết quả định vị bộ khớp. Em thấy điều này phù hợp với việc model có thể bao được người nhưng vẫn đặt sai tay hoặc chân; tuy nhiên box dùng IoU còn pose dùng OKS nên hai phép chấm không hoàn toàn giống nhau.

**3. Một ảnh test model đoán sai và loại lỗi**

Ở `test_07.jpg`, ảnh dự đoán trong mục 5 cho thấy một nhánh tay bên phải ảnh bị kéo sang vùng thức ăn và bàn, trong khi tay người đang hướng về vùng ngực/cổ. Em xếp trường hợp này vào dấu hiệu “trượt hẳn” khi xem ảnh, cần đối chiếu từng keypoint để xác nhận chính xác bên trái/phải theo cơ thể. Đây là nhận xét thị giác từ ảnh dự đoán, không phải nhãn lỗi do công cụ chấm test xuất ra.

Ảnh dự đoán được trích từ output notebook: [10 ảnh test kèm pose](../outputs/notebook_test_predictions.png).

**4. Ảnh có OKS thấp nhất giữa nhãn của em và model; ai đúng?**

Trong bảng mục 6, cặp ghép thấp nhất thuộc `train_15`, OKS 0.609. Ảnh này có hai người đội mũ bảo hiểm, tay gần tay lái và thân xe che một phần cơ thể. Gold chấm hai người trong nhãn của em là 0.8685 và 0.9374, nên nhãn của em có mức đồng thuận với gold cao hơn mức đồng thuận thấp nhất với model. Tuy nhiên, output model không ghi chỉ số người của từng cặp ghép; em chưa thể xác định cặp 0.609 là người nào hoặc khẳng định mọi khớp của em đều đúng. Hai ca khác cần xem lại là `train_06` với OKS 0.640 và `train_13` với một cặp OKS 0.669.

**5. Ảnh em gán tệ nhất có trùng ảnh model bất đồng nhiều nhất không?**

Có, nếu xét skeleton có OKS thấp nhất: nhãn người 2 của em trong `train_15` đạt 0.8685 với gold, thấp nhất trong các cặp được lưu. Đồng thời `train_15` có cặp model–nhãn thấp nhất là 0.609. Nếu lấy trung bình các skeleton theo ảnh, `train_01` mới thấp nhất, bằng `(0.8753 + 0.9239) / 2 = 0.8996`; vì vậy cần nói rõ cách chọn “tệ nhất”. Việc trùng `train_15` ở mức cặp thấp nhất gợi ý ảnh có che khuất gây khó cho cả hai bên, nhưng chưa chứng minh model sai nhất so với gold vì notebook không chấm trực tiếp model với gold trên từng ảnh train.

## 5. Một trường hợp xác định visibility dựa trên ảnh

Ở `train_10.jpg`, người 1, hai khớp `left_ankle` và `right_ankle` có trạng thái `v=0`. Ảnh chỉ cho thấy phần trên cơ thể người đang nghiêng trên xe, còn vùng mắt cá chân nằm ngoài khung. Căn cứ này giúp em phân biệt khớp ngoài ảnh với khớp vẫn trong ảnh nhưng bị vật che. Với trường hợp thứ hai, cần giữ vị trí ước lượng và dùng `v=1` thay vì loại bỏ khớp.

## 6. Đánh giá chung và hướng hoàn thiện

Bài thực hành đạt được các kết quả chính: hoàn thành 20 ảnh với 29 skeleton, vượt qua kiểm tra định dạng và đạt OKS trung bình 0.9417 trên bộ nhãn hiện tại. Kết quả ghép đủ 29/29 người và OKS@0.75 đạt 1.0 cho thấy mức tương đồng tốt với bộ tham chiếu trong phạm vi dữ liệu được chấm.

Ở phần mô hình, YOLO26n-pose sau fine-tune đạt pose_mAP50-95 là 0.6908 và pose_precision là 0.9792, lần lượt tăng 0.0055 và 0.0058 so với baseline. Qua bài này, em hiểu rõ hơn vai trò của visibility, cách đọc kết quả OKS và sự khác nhau giữa phát hiện người với định vị khớp. Em cũng rút ra rằng cần đánh giá đồng thời số liệu và ảnh dự đoán để nhận xét đúng chất lượng của nhãn và model.

Hướng hoàn thiện tiếp theo là rà soát các cảnh báo còn lại, làm rõ những trường hợp visibility khác bộ tham chiếu và bổ sung ảnh minh chứng cho phần tự review. Thời gian gán nhãn và kết quả trước rà soát chưa được lưu đầy đủ nên chưa đưa vào so sánh định lượng. Các kết quả trong báo cáo được trình bày theo đúng phạm vi của lần đánh giá đã lưu.
