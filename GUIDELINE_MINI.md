# Mini guideline - nhóm: VinAI-Team04  |  người gán: Trần Đức Thọ  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Đặt tại mấu chuyển lớn xương đùi (ước lượng khoảng 1/2 chiều rộng hông hoặc ngang đáy xương chậu, ngay dưới cạp quần), gán `v = 1` nếu quần áo che khuất đường cong giải phẫu | Quần áo dài/rộng che mất bề mặt cơ thể thật; việc cố định tại mấu chuyển lớn xương đùi theo tỉ lệ giải phẫu giúp model học đúng khớp quay của chân thay vì viền áo |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu nhìn thấy vành tai hoặc gốc tai: đặt tại lỗ tai ngoài, gán `v = 2`. Nếu bị che khuất nhưng xác định được vùng thái dương - đuôi mắt: ước lượng vị trí lỗ tai ngang hàng đuôi mắt và gán `v = 1` | Vị trí tai liên kết chặt chẽ với mắt và sống mũi theo mặt phẳng Frankfurt; gán `v = 1` giúp giữ góc nghiêng đầu cho pose model |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Toàn bộ các khớp chân nằm ngoài khung ảnh (đầu gối, cổ chân) đặt toạ độ (0, 0) và gán `v = 0` (Outside - phím `o`). Không đoán ra ngoài mép ảnh | Khớp ngoài khung không có cơ sở pixel nào; nếu đoán bừa toạ độ ngoài biên sẽ làm model phạt gradient sai lầm và gây nhiễu phân bố toạ độ |
| Cổ tay nằm sau tay lái / sau thân mình | Dóng theo trục xương cẳng tay từ khuỷu tay kéo dài đến khớp cổ tay ước lượng, đặt điểm và gán `v = 1` (Occluded - phím `q`) | Cẳng tay là đoạn xương cứng thẳng; góc và chiều dài cẳng tay cho phép nội suy vị trí cổ tay với độ lệch < 5px ngay cả khi bị vật che |
| Hai người chồng lên nhau | Hoàn thành trọn vẹn từng người một. Khớp của người phía sau bị người phía trước che thì gán `v = 1` tại vị trí giải phẫu của người phía sau | Tránh tuyệt đối lỗi `nham_nguoi` (bắt nhầm sang tay/chân người phía trước). Quy tắc một người một lần đảm bảo tính toàn vẹn topology |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả mọi người có chiều cao bounding box >= 40 pixel hoặc nhìn rõ ít nhất phần đầu và thân trên. Người quá mờ nhạt/nền xa (< 30px không rõ hình người) bỏ qua | Bộ 20 ảnh core đều đủ rõ để gán; ngưỡng 40px đảm bảo tỉ lệ phân giải khớp không bị nhiễu do kích thước stride của backbone YOLO |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_knee, right_knee, left_ankle, right_ankle`

- Mơ hồ ở chỗ nào: Người ngồi trong tư thế bị che khuất phần chân bởi đồ vật phía trước nhưng toàn bộ cơ thể vẫn nằm gọn trong khung hình. Ban đầu phân vân liệu chân bị che hết thì để `v = 0` (Outside) hay `v = 1` (Occluded).
- Bạn quyết thế nào: Giữ nguyên trong khung hình, dóng theo trục đùi từ hông để ước lượng vị trí đầu gối và cổ chân, gán `v = 1` thay vì `v = 0`.
- Vì sao: Theo luật cốt lõi của lớp: Khớp còn nằm trong khung hình và có cơ sở giải phẫu thì bắt buộc phải là `v = 1` và có chấm ước lượng. `v = 0` chỉ dùng khi khớp thật sự bị cắt khỏi khung ảnh.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model sẽ học rằng một người ngồi giữa ảnh không có chân (`v = 0`), dẫn tới triệt tiêu khả năng suy đoán tư thế khi bị vật cản che (occlusion reasoning).

### Ca 2 - ảnh `train_06.jpg`, người thứ `1`, khớp `toàn bộ các cặp khớp trái / phải`

- Mơ hồ ở chỗ nào: Người đứng ở góc chụp nghiêng chếch từ phía sau (quay lưng), dẫn đến dễ bị nhầm lẫn giữa bên trái và bên phải theo trực giác nhìn từ màn hình.
- Bạn quyết thế nào: Đứng vào vị trí của người đó và hướng mặt cùng chiều với họ: tay trái của họ ở phía bên trái của cơ thể họ (tương ứng bên trái bức ảnh), tay phải ở bên phải bức ảnh.
- Vì sao: Định nghĩa COCO keypoints quy định left/right tính theo cơ thể người giải phẫu, không tính theo góc nhìn của người quan sát.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu đảo trái/phải, khi áp dụng data augmentation lật ảnh ngang (`fliplr = 0.5`), lỗi đảo này bị nhân đôi khiến model học sai hoàn toàn mối quan hệ không gian giữa các khớp.

### Ca 3 - ảnh `train_07.jpg`, người thứ `1`, khớp `right_wrist`

- Mơ hồ ở chỗ nào: Bàn tay cầm một vật thể lớn che khuất hoàn toàn khớp cổ tay phải.
- Bạn quyết thế nào: Dựa vào trục cẳng tay phải đang hướng về vật cầm, ước lượng điểm cổ tay tại vị trí tiếp giáp giữa cẳng tay và bàn tay cầm vật, đánh cờ `v = 1` (Occluded).
- Vì sao: Bề mặt khớp bị che khuất nhưng hướng xương cẳng tay cung cấp bằng chứng thị giác trực tiếp và đáng tin cậy.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu người khác đánh `v = 2`, model sẽ phạt nhầm vùng bề mặt vật thể thành cổ tay; nếu đánh `v = 0`, model sẽ bỏ qua khả năng dự đoán khớp tay khi người thao tác cầm nắm đồ vật.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `52%` / họ `35%`) và `left_hip` (bạn `41%` / họ `24%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline ban đầu chưa nêu rõ: khi tóc hoặc mũ che một phần tai thì nên để `v=2` hay `v=1`, và khi mặc áo thụng thì hông tính là `v=1` hay người kia cố tình gán `v=2` vào viền áo.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: 
  * "Đối với tai: Chỉ cần có bất kỳ phần tóc hoặc mũ nào trùm lên vành tai thì lập tức chuyển sang `v = 1` và chấm tại tâm ống tai ngoài ước lượng."
  * "Đối với hông: Khi trang phục là áo phông rộng/áo khoác thụng phủ qua xương chậu, 100% các trường hợp hông phải được đánh dấu `v = 1` tại mấu chuyển lớn xương đùi ước lượng, không được gắn `v = 2` vào nếp gấp quần áo."
