# Báo cáo phần mở rộng (Bonus Extension) — Cao Thị Thúy Hiền · G06

> Căn cứ theo **GUIDE.md §5 (Lộ trình Mở rộng)** và **SUBMISSION.md**, báo cáo này tổng hợp đầy đủ cả 3 hướng đào sâu chuyên môn:
> 1. Gán nhãn và đối chiếu 4 ảnh mở rộng `S09`–`S10` (`export_extra.zip` và `check_extra.txt`).
> 2. Đề xuất cải tiến Guideline Cabin v1.1 dựa trên bằng chứng thực nghiệm.
> 3. Phân tích định lượng sự mất mát thông tin giữa ảnh màu RGB và ảnh hồng ngoại mô phỏng IR-sim từ `rgb_ir_diff.py`.

---

## 1. Hướng (a): Gán nhãn mở rộng S09–S10 & Đối chiếu nhãn gốc SVIRO

Task phụ `Day12-G06-extra` gồm 4 ảnh cốt lõi mở rộng đã được gán nhãn theo đúng Guideline Cabin v1.0, đóng gói thành `export_extra.zip` và kiểm thử qua `cabin_check.py` đạt **0 lỗi, 0 cảnh báo** (xem log [`check_extra.txt`](check_extra.txt)):

* **Cảnh S09 (`S09_rgb.png`, `S09_ir.png`):**
  * `rear_right` (bên trái ảnh): Người lớn (`person`, `seatbelt: not_worn`) kèm box khuôn mặt (`face`).
  * `rear_middle` (giữa ảnh): Một chiếc balo/túi mềm (`object`, `type: bag`).
  * `rear_left` (bên phải ảnh): Ghế trống hoàn toàn (không vẽ box).
* **Cảnh S10 (`S10_rgb.png`, `S10_ir.png`):**
  * `rear_right` (bên trái ảnh): Nôi em bé xách tay quay về sau (`infant_seat`, `facing: rear`).
  * `rear_middle` (giữa ảnh): Ghế trống hoàn toàn (không vẽ box).
  * `rear_left` (bên phải ảnh): Ghế trẻ em có tựa lưng cao (`child_seat`, `occupied: no`).

### Phân loại các bẫy lỗi tiềm ẩn khi so với nhãn gốc SVIRO:
1. **Lỗi sai ánh xạ (Seat Mapping Flip):** Trong dataset gốc SVIRO, các ghế được đánh số từ trái sang phải theo màn hình hiển thị (`seat_0`, `seat_1`, `seat_2`). Nếu annotator map thẳng `seat_0` thành `rear_left` sẽ bị đảo ngược hoàn toàn so với thực tế xe, vì camera cabin nhìn về sau nên `seat_0` (trái ảnh) thực chất là `rear_right` của xe BMW X5.
2. **Lỗi nhầm lẫn phân loại lớp (`infant_seat` vs `child_seat`):** Ở cảnh S10, hai ghế trẻ em ở hai bên có kiểu dáng hoàn toàn khác nhau. Chiếc bên trái là dạng nôi xách vỏ cong đặt nằm nghiêng (`infant_seat`), trong khi chiếc bên phải là ghế ngồi có tựa lưng đứng và cánh đỡ đầu (`child_seat`). Annotator thiếu kinh nghiệm thường gom chung thành một lớp ghế trẻ em duy nhất.
3. **Lỗi gán ghế trống:** Nhiều học viên theo thói quen gán nhãn COCO thường vẽ thêm box cho ghế trống (`seat: empty`). Guideline v1.0 quy định rõ chỉ vẽ box cho thực thể tồn tại trên ghế, ghế trống không vẽ shape.

---

## 2. Hướng (b): Đề xuất cải tiến Guideline Cabin v1.1 có bằng chứng thực nghiệm

Từ quá trình thực tế gán nhãn 22 ảnh cốt lõi và 4 ảnh mở rộng của bộ `G06`, em đề xuất 3 điểm cải tiến cho Guideline Cabin phiên bản tiếp theo:

### 2.1. Tách biệt tập nhãn `seatbelt` cho dữ liệu tổng hợp SVIRO
* **Bằng chứng:** Trong toàn bộ 10 cảnh của SVIRO BMW X5, 100% mô hình người 3D đều không được dựng dây an toàn vắt qua ngực. Vì vậy thuộc tính `seatbelt` chỉ có thể là `not_worn` hoặc `unknown`.
* **Vấn đề:** Việc đưa thuộc tính `worn_correct` vào schema gán nhãn cho ảnh SVIRO dễ gây lỗi "đoán mò" cho annotator mới và khiến model AI được huấn luyện trên dữ liệu tổng hợp này không bao giờ được học đặc trưng dương tính (positive samples) của việc cài dây đúng cách.
* **Đề xuất v1.1:** Tách thuộc tính dây an toàn theo nguồn dữ liệu: với dataset mô phỏng chưa hỗ trợ dây (SVIRO), mặc định khóa trường này ở giá trị `not_worn`/`not_applicable` để tránh lãng phí thời gian gán nhãn và giảm nhiễu nhãn.

### 2.2. Bổ sung nhãn quan hệ không gian khi vật thể nằm trong ghế trẻ em
* **Bằng chứng:** Tại các cảnh có đồ vật đặt trong lòng ghế trẻ em, Guideline yêu cầu vẽ 2 box độc lập (`child_seat` và `object`) cùng chung thuộc tính `seat`.
* **Vấn đề:** Khi hai bounding box có độ giao thoa IoU quá cao, thuật toán hậu xử lý NMS (Non-Maximum Suppression) của model Object Detection thường vô tình triệt tiêu box nhỏ hơn, hoặc nhầm tưởng ghế trẻ em đang có trẻ em ngồi (`occupied = yes`).
* **Đề xuất v1.1:** Bổ sung thuộc tính quan hệ lồng ghép (ví dụ `is_inside_child_seat: yes/no` cho nhãn `object`) để hỗ trợ mô hình phân tầng (hierarchical reasoning) phân định chính xác.

### 2.3. Cải tiến thuật toán kiểm tra tính hợp lệ trục tọa độ của `cabin_check.py`
* **Bằng chứng:** Trên ảnh cận hồng ngoại Drive&Act (D01–D06), camera bị xoay 90 độ khiến trục nối hai mắt của người lái chạy dọc theo trục Y thay vì trục X. Lệnh `cabin_check.py` hiện tại so sánh `left_eye_outer[0] < right_eye_outer[0]` theo trục X nên luôn kích hoạt cảnh báo sai dù annotator gán chuẩn giải phẫu.
* **Đề xuất v1.1:** Script kiểm tra tự động nên đọc trường metadata hoặc nhận diện góc quay của ảnh để chuyển sang so sánh tọa độ Y (`left_eye_outer[1] > right_eye_outer[1]`), giúp giảm cảnh báo giả và tự động hóa quy trình kiểm thử hoàn toàn.

---

## 3. Hướng (c): Phân tích định lượng sự mất mát thông tin qua `rgb_ir_diff.py`

Em đã chạy script phân tích quang phổ:
```bash
python scripts/rgb_ir_diff.py G06/oms --out ir_pairs
```
Bảng số liệu trích xuất từ file `ir_pairs/ir_pairs.csv` cho 2 cảnh RGB tối nhất trong bộ G06:

| Cảnh | Kênh | Vùng | Độ sáng trung bình (Mean) | Độ tương phản (Std) | Tỷ lệ điểm ảnh tối (% Dark < 16) | Tỷ lệ bão hòa (% Sat > 250) |
|---|---|---|---|---|---|---|
| **S06** | RGB | Toàn ảnh | **20.76** | 39.57 | **67.44%** | 0.00% |
| | IR-sim | Toàn ảnh | **58.66** | 54.59 | **17.47%** | 1.89% |
| | RGB | Ghế sau bên trái (`third_left`) | **7.39** | 14.92 | **87.53%** | 0.00% |
| | IR-sim | Ghế sau bên trái (`third_left`) | **26.29** | 18.36 | **32.19%** | 0.00% |
| **S07** | RGB | Toàn ảnh | **32.12** | 46.16 | **58.33%** | 0.59% |
| | IR-sim | Toàn ảnh | **58.49** | 44.36 | **10.30%** | 0.65% |
| | RGB | Ghế sau ở giữa (`third_mid`) | **23.21** | 39.00 | **72.59%** | 0.27% |
| | IR-sim | Ghế sau ở giữa (`third_mid`) | **61.55** | 42.92 | **12.15%** | 0.30% |

### Nhận xét chuyên môn:
1. **Những trường thông tin ảnh hồng ngoại (IR-sim) giữ được tốt hơn ảnh màu RGB:**
   * Ở cảnh `S06` vào ban đêm, ảnh RGB có tới **67.44%** diện tích chìm hoàn toàn trong bóng tối (điểm ảnh < 16), khu vực ghế trái ảnh (`third_left`) gần như tối đen (87.53% dark), mắt người không thể nhận biết có vật thể hay không.
   * Ngược lại, ảnh IR-sim kéo tỷ lệ tối xuống chỉ còn **17.47%**, nâng độ sáng trung bình lên 58.66. Nhờ đó, ranh giới đệm ghế, viền tựa đầu và hình khối của hành khách ở ghế `rear_left` hiện lên sắc nét, cho phép model nhận diện sự hiện diện của người ngồi và vẽ bounding box chính xác.
2. **Những trường thông tin bị mất mát trên ảnh hồng ngoại:**
   * **Màu sắc trang phục và vật thể:** Toàn bộ thông tin màu sắc (RGB channels) bị triệt tiêu thành các mức thang xám đơn sắc. Không thể phân biệt được màu áo, màu balo hay màu da.
   * **Độ tương phản bề mặt vật liệu:** Trên ảnh RGB, dải dây an toàn thường có độ bóng bề mặt phản xạ ánh sáng khác biệt so với chất liệu vải cotton của áo. Trên ảnh IR-sim, cả áo sẫm màu và dây an toàn đều hấp thụ tia hồng ngoại tương đương nhau, làm mép dải dây bị chìm hoàn toàn vào thân áo, buộc annotator phải gán `seatbelt = unknown`.
