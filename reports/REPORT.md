# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Đức Thọ   Nhóm: VinAI-Team04   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 328 / 139 / 26 |
| Thời gian trung bình mỗi ảnh | 4.2 phút / ảnh |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear`: 52% (15/29 người)
2. `left_wrist`: 41% (12/29 người) (đồng tỉ lệ với `left_hip` 41% và `left_knee` 41%)
3. `right_ear`: 38% (11/29 người)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Những khớp này chủ yếu là những khớp **hay bị che khuất vật lý nhất** (tóc/mũ che tai, đồ vật hoặc thân mình che cổ tay), chứ không hẳn là những khớp **khó xác định vị trí giải phẫu nhất**. Với tai và cổ tay, dù bị che nhưng chúng ta vẫn có căn cứ trực quan rất mạnh (tai ngang hàng đuôi mắt, cổ tay dóng theo trục cẳng tay). Ngược lại, khớp khó gán nhất trên thực tế là khớp hông (`left_hip`, `right_hip`), bởi vì người trong ảnh mặc trang phục dày hoặc áo thụng trùm qua xương chậu, hoàn toàn không có đường viền cơ thể trực tiếp mà người gán phải tự tái tạo khung xương giải phẫu để đặt điểm mấu chuyển lớn xương đùi.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.908 | 0.953 |
| OKS@0.50 | 0.931 | 1.000 |
| OKS@0.75 | 0.931 | 1.000 |
| Lỗi `dao_trai_phai` | 2 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

- `train_06.jpg`, người thứ 1, toàn bộ 17 khớp: Đảo ngược lại tất cả các cặp khớp đối xứng trái/phải (mắt, tai, vai, khuỷu, cổ tay, hông, đầu gối, cổ chân) để quy về đúng hệ quy chiếu cơ thể nhân vật khi họ đang quay lưng, thay vì nhìn theo góc nhìn màn hình.
- `train_09.jpg`, người thứ 1, các cặp khớp vai, khuỷu, cổ tay và chân: Đảo lại toàn bộ các cặp khớp trái/phải bị gán nhầm, đồng thời căn chỉnh lại vị trí mắt cá chân trái/phải để triệt tiêu hoàn toàn cả lỗi `dao_trai_phai` và lỗi `truot_han` ở cổ chân.
- `train_17.jpg`, người thứ 1, khớp `right_ankle`: Sửa điểm mắt cá chân phải bị trượt hẳn 128 px ra ngoài lề đường (từ toạ độ pixel [354.92, 562.15] về đúng vị trí tâm mắt cá chân [235.0, 516.0]) và thu hẹp bounding box cho ôm sát thân người.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Lỗi đảo trái/phải xảy ra ở hai ảnh: `train_06.jpg` và `train_09.jpg`. Đây là những bức ảnh có độ rõ nét trung bình - khá, không phải ảnh quá khó về chất lượng quang học. Tuy nhiên, sai sót xảy ra vì góc chụp nhân vật không đứng trực diện mà quay lưng hoặc đứng nghiêng chếch. Khi thao tác với tốc độ nhanh, người gán nhãn theo quán tính thị giác sẽ gán phần cơ thể nằm ở nửa bên trái màn hình là "left", nửa bên phải là "right", vô tình quên mất nguyên tắc cốt lõi: bên trái hay bên phải phải tính theo hệ quy chiếu cơ thể giải phẫu của chính nhân vật trong ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: Nguyễn Văn A (VinAI-Team04)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 52% | 35% | 17% | Guideline chưa nêu rõ: bạn cùng nhóm giữ `v=2` khi nhìn thấy một phần vành tai, còn tôi chuyển `v=1` ngay khi có tóc che |
| `left_hip` | 41% | 24% | 17% | Guideline chưa nêu rõ: bạn cùng nhóm gắn `v=2` theo nếp gấp áo quần ngoài, còn tôi ước lượng `v=1` tại mấu chuyển xương đùi |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

- **Luật về tai:** Bất kể nhìn thấy một phần vành tai hay không, nếu có tóc dài hoặc mũ bảo hiểm trùm lên trên 30% diện tích tai thì bắt buộc gán `v = 1` và chấm tại tâm lỗ tai ngoài ước lượng ngang hàng đuôi mắt; chỉ giữ `v = 2` khi toàn bộ vành tai và lỗ tai lộ rõ hoàn toàn.
- **Luật về hông:** Đối với người mặc trang phục dài/thụng che khuất đường rãnh háng, 100% các trường hợp khớp hông phải được đánh dấu `v = 1` tại mấu chuyển lớn xương đùi giải phẫu ước lượng, nghiêm cấm đặt chấm `v = 2` theo nếp gấp mép quần áo.

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6853 | 0.0000 |
| pose_precision | 0.9734 | 0.9746 | +0.0012 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8054 | -0.0065 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?
   *Trả lời:* `pose_mAP50-95` hầu như không đổi (chênh lệch 0.0000), trong khi `pose_precision` tăng nhẹ (+0.0012) và `box_mAP50-95` giảm nhẹ (-0.0065). 20 ảnh là số lượng dữ liệu rất nhỏ so với hàng chục nghìn ảnh mà model COCO đã được huấn luyện ban đầu. 20 ảnh này giúp model học được tính nhất quán trong việc đánh dấu các điểm bị che (`v = 1`), giúp nâng nhẹ độ chuẩn xác cục bộ (precision), nhưng vì kích thước mẫu quá ít nên chưa đủ để mở rộng độ bao phủ (recall) và gây nhiễu nhẹ cho detector bounding box ở tập test.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?
   *Trả lời:* `box_mAP50-95` (0.8054 - 0.8119) cao hơn đáng kể so với `pose_mAP50-95` (0.6853), chênh nhau khoảng 0.12 - 0.13. Model tìm *người* dễ hơn tìm *khớp* rất nhiều. Lý do là bounding box của người là một vùng đặc trưng lớn, có cấu trúc tổng thể rõ ràng (đầu, thân, chân, độ tương phản nền) nên mạng CNN/Attention rất dễ bắt dính. Trong khi đó, keypoint là các điểm đơn lẻ kích thước cực nhỏ (vài pixel), dễ bị dịch chuyển do góc chụp, xoay cơ thể và đặc biệt là bị che khuất (occlusion).

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):
   *Trả lời:* Trong ảnh `test_03.jpg`, model gặp lỗi **lệch nhẹ** ở khớp khuỷu tay và cổ tay phải (~10-15px) do nhân vật cầm điện thoại ép sát ngực; ở ảnh `test_09.jpg`, model mắc lỗi **trượt hẳn** ở vùng mắt cá chân do góc chụp dốc từ trên cao xuống làm cẳng chân bị che khuất một phần (foreshortening).

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?
   *Trả lời:* Ảnh `test_06.jpg` (nhân vật cúi người bê đồ) có OKS tương đối thấp nhất giữa nhãn và model. Trong trường hợp này, **nhãn người gán đúng hơn**. Người gán có tri thức tiên nghiệm về giải phẫu sinh học (vị trí xương sống, độ gập của đầu gối dưới lớp quần), trong khi model bị đánh lừa bởi bề mặt nếp gấp áo và bóng đổ nên dự đoán khớp hông bị kéo cao lệch hẳn lên lưng.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?
   *Trả lời:* Có, các ảnh có tư thế phức tạp như `train_04.jpg` (chân bị che khuất bởi bàn ghế) trong tập train và `test_09.jpg` trong tập test đều là những ảnh cả người gán lẫn model đều gặp khó khăn nhất. Điều này chứng minh rằng: những bức ảnh có mức độ che khuất cao (heavy occlusion), góc chụp không quy chuẩn (non-canonical viewpoints) và mật độ đồ vật gây nhiễu lớn là thách thức cố hữu của thị giác máy tính, đòi hỏi guideline phải cực kỳ chi tiết và mô hình cần dữ liệu huấn luyện đa dạng hơn.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->

Tại ảnh `train_07.jpg`, người thứ 1, tôi phải đưa ra quyết định cho khớp cổ tay phải (`right_wrist`). Nhân vật đang dùng hai tay cầm một vật thể lớn che khuất hoàn toàn bề mặt da và chu vi khớp cổ tay phải, tuy nhiên phần cánh tay áo và trục xương cẳng tay phải vẫn lộ rõ hoàn toàn và hướng thẳng về phía vật thể. Do khớp nằm trọn vẹn bên trong khung ảnh và hướng xương cẳng tay tạo cơ sở hình học vững chắc để nội suy vị trí cổ tay với sai số dưới 5 pixel, tôi quyết định chọn trạng thái `v = 1` (Occluded) và đặt chấm ước lượng tại điểm tiếp giáp với bàn tay, thay vì đánh `v = 0` (Outside).
