# Tự đánh giá — Cao Thị Thúy Hiền · G06

## 1. Tự rà soát 4 cảnh

Nghỉ tay 1 phút, mở lại bài của mình, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ.

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| `S01_ir.png` | `infant_seat` | `seat_flip` | Ban đầu định gán theo hướng mắt nhìn trên ảnh là `rear_left`, sau đó nhớ lại camera nhìn về sau nên trái ảnh là `rear_right` | Đã sửa |
| `S02_rgb.png` | `rear_middle` | `extra` | Ghế giữa trống, ban đầu định vẽ box gán empty, sau đó đọc lại guideline ghế trống không vẽ box | Đã sửa |
| `D02.png` | `hand` | `lr_flip` | Ảnh bị xoay 90 độ khiến ban đầu nhầm tay trái (ở phía dưới màn hình có đeo vòng) thành tay phải, đã đổi lại đúng `side: left` | Đã sửa |
| `D03.png` | `face13` | `flag` | Vùng mắt phải bị đốm phản xạ đèn LED NIR che khuất mí trên, đã bật cờ `occluded: 1` cho các điểm mắt bị che | Đã sửa |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: Bị nhầm lẫn hệ quy chiếu Trái / Phải trên ảnh camera DMS (Drive&Act) do ảnh bị xoay ~90 độ.

Vì sao mình mắc: Thói quen trực quan nhìn theo màn hình máy tính (phía trên / phía dưới hoặc trái / phải của ảnh) thay vì đặt mình vào vị trí và tư thế thực tế của tài xế ngồi trong cabin.

Lần sau tránh bằng cách: Luôn ghi nhớ quy tắc vàng: "Ghế gọi theo xe, trái/phải gọi theo người". Với ảnh DMS xoay 90 độ, luôn nhớ mắt trái và tay trái của tài xế có toạ độ Y lớn hơn (nằm thấp hơn trên màn hình).

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai. Mình học được gì từ dòng đó:
Ở khung hình `D02.png`, bạn gán `eye_right = unknown` do nghi ngờ vùng mắt bị mờ bởi đốm phản xạ xiên của đèn LED NIR trên kính, trong khi mình ban đầu gán `open`. Khi thảo luận và zoom kỹ 200% trên CVAT, mình nhận ra bạn đã tuân thủ rất chặt chẽ quy tắc "Không thấy thì unknown" — bài học là khi khe mí bị che một phần hoặc không hoàn toàn rõ ràng, tuyệt đối không được suy đoán theo trực giác mà phải gán unknown để đảm bảo dữ liệu huấn luyện an toàn cho model.
