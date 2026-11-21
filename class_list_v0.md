# Danh sách lớp v0 — Cao Thị Thúy Hiền · G06

Viết tiếp từ slide 54 bài giảng. Mỗi lớp trả lời: **model cần nhận ra cái này để làm gì?**

## DMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `eye_state` | open / closed / unknown | Phát hiện trạng thái ngủ gật (drowsiness) hoặc ngủ lơ mơ (microsleep khi mắt nhắm liên tục > 1.5s) để kích hoạt chuông cảnh báo và rung vô lăng. | Có, trừ trường hợp tròng kính bị đốm phản xạ LED NIR che khuất. |
| 2 | `gaze_zone` | road / mirror_l / mirror_r / mirror_c / cluster / center / lap / other | Phát hiện mất tập trung hướng nhìn (distraction: nhìn màn hình trung tâm hoặc nhìn xuống đùi quá 2s khi xe đang chạy). | Có, dựa trên sự kết hợp giữa hướng quay đầu và hướng con ngươi. |
| 3 | `face13_landmarks` | 13 điểm (mắt, mũi, miệng) | Ước lượng tư thế đầu 3D (Head Pose: yaw, pitch, roll) và tính chỉ số EAR (Eye Aspect Ratio), MAR (Mouth Aspect Ratio) để phát hiện ngáp và gật gù. | Có, cần gắn cờ `occluded` khi bị gọng kính hoặc bàn tay che khuất một phần. |
| 4 | `hand_on_wheel` | yes / no / unknown | Giám sát tài xế giữ tay trên vô lăng (Hands-on Detection) khi xe chạy chế độ tự lái cấp độ 2 (ADAS L2 / Autopilot) theo luật an toàn. | Có, nhận diện rõ qua hình dáng ngón tay và khớp cổ tay tiếp xúc vành vô lăng. |
| 5 | `phone_interaction` | held / mounted / lap / none | Phát hiện hành vi sử dụng điện thoại khi lái xe (nghe gọi áp tai hoặc nhắn tin) để đưa ra nhắc nhở an toàn giao thông. | Có thấy hình dáng cạnh viền/màn hình, nhưng mất màu sắc vỏ ốp lưng. |
| 6 | `seatbelt_driver` | worn_correct / worn_incorrect / not_worn / unknown | Cảnh báo thắt dây an toàn trước khi khởi hành và điều chỉnh lực siết dây, thời điểm kích nổ túi khí khi xảy ra va chạm. | Có, nhưng cần độ tương phản giữa dải dây và màu áo của tài xế. |

## OMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `seat_occupancy` | empty / person (theo vị trí ghế) | Xác định ghế có người ngồi để kích hoạt hệ thống nhắc nhở thắt dây an toàn và tối ưu hóa điều hòa vùng độc lập. | Có, dựa vào hình khối cơ thể người ngồi trên đệm ghế. |
| 2 | `infant_seat` | rear / front / none (theo ghế) | Tự động vô hiệu hóa (tắt) túi khí ghế phụ nếu đặt nôi quay về sau; phục vụ tính năng cảnh báo bỏ quên trẻ sơ sinh (Child Presence Detection - CPD). | Có, nhận biết tốt nhờ khung quai xách và hình dạng vỏ nôi cong đặc trưng. |
| 3 | `child_seat` | occupied (no / unknown) | Hỗ trợ chuẩn an toàn Euro NCAP phát hiện trẻ nhỏ ngồi ghế trẻ em; quản lý trạng thái khóa chốt an toàn ISOFIX. | Có, nhận biết qua cấu trúc tựa lưng cao và đệm cánh ôm hai bên. |
| 4 | `object_classification` | bag / box / cup / other | Phân biệt hành lý/đồ vật với cơ thể người để tránh báo động giả cảnh báo dây an toàn; nhắc nhở để quên đồ khi tắt máy rời xe. | Có, phân loại dựa trên hình khối học (hộp vuông, balo mềm, chai cốc trụ). |
| 5 | `passenger_face` | rectangle | Nhận diện hành khách quen thuộc để cá nhân hóa hồ sơ tiện nghi (User Profile: chỉnh độ ngả lưng ghế, hướng gió, vị trí màn hình). | Có, phát hiện khung khuôn mặt tốt trong mọi điều kiện ánh sáng. |
| 6 | `seatbelt_passenger` | worn_correct / worn_incorrect / not_worn / unknown | Nhắc nhở người ngồi hàng ghế sau cài dây an toàn đúng quy cách, đảm bảo an toàn tối đa khi phanh gấp hoặc va chạm. | Có, quan sát tốt đường chéo vắt từ cột B hoặc lưng ghế qua ngực hành khách. |

## 2 câu hỏi leo thang

Câu hỏi bạn muốn hỏi người viết guideline (hoặc khách hàng) vì guideline v1.0 chưa trả lời. Mỗi câu kèm **tên khung** làm ví dụ.

1. **Câu hỏi:** Khi dải dây an toàn màu tối chìm hoàn toàn vào trang phục màu đen của hành khách dưới ánh sáng hồng ngoại (IR-sim hoặc NIR), hệ thống OMS có cho phép kết hợp tín hiệu cảm biến chốt khóa dây (buckle switch sensor) từ phần cứng xe hay chỉ dựa 100% vào thị giác máy tính?
   * **Khung ví dụ:** `S03_ir.png` (hành khách ghế sau `rear_left`).
   * **Bạn tạm gán thế nào và vì sao:** Tạm gán `seatbelt = unknown` theo đúng Quy tắc chung số 1 của Guideline ("Không thấy thì unknown"), tuyệt đối không suy đoán từ ảnh màu RGB cùng cảnh hoặc suy từ giả định người lớn ngồi trên xe.

2. **Câu hỏi:** Trong trường hợp đồ vật (ví dụ balo, túi xách) được đặt trực tiếp vào bên trong lòng ghế trẻ em (`child_seat`), guideline yêu cầu vẽ 2 box riêng. Việc này có gây xung đột nhãn cho model khi cả 2 box cùng chia sẻ chung một vùng không gian và cùng thuộc tính `seat` không? Có nên bổ sung cờ quan hệ (ví dụ: `in_child_seat = yes`) không?
   * **Khung ví dụ:** `S02_rgb.png` / `S02_ir.png` (tình huống đặt vật thể vào lòng ghế trẻ em).
   * **Bạn tạm gán thế nào và vì sao:** Tạm gán 2 box độc lập: 1 box `child_seat` (thuộc tính `occupied = no` vì không có trẻ em ngồi) và 1 box `object` (thuộc tính `seat` theo vị trí ghế đó) theo đúng mục 4.8 của Guideline v1.0.
