# Khai báo dùng AI — Cao Thị Thúy Hiền

Nhắc lại: không đưa ảnh cabin hay ảnh tài xế (kể cả ảnh chụp màn hình) cho bất kỳ công cụ AI nào.

| # | Công cụ | Bạn hỏi gì (tóm tắt) | Bạn dùng kết quả thế nào | Bạn có kiểm lại không? Bằng gì? |
|---|---|---|---|---|
| 1 | Antigravity AI (Google DeepMind) | Hỏi về Guideline Cabin v1.0, cách xác định hệ quy chiếu ghế sau xe OMS và trục toạ độ mắt/tay trên ảnh Drive&Act xoay 90 độ | Áp dụng để phân loại nhãn, xác định toạ độ mắt trái có Y lớn hơn mắt phải và tránh các nhãn không hợp lệ (như worn_correct trên SVIRO) | Có, kiểm tra lại bằng checklist mục 4.9 trong GUIDE.md và script tự động `cabin_check.py` |
| 2 | Antigravity AI (Google DeepMind) | Nhờ hỗ trợ phân tích ý nghĩa các cảnh báo WARN của script `cabin_check.py` và cách viết báo cáo danh sách lớp v0 cho model DMS/OMS | Viết phần giải thích chi tiết cảnh báo trong `check_final.txt` và hoàn thiện bảng 6 lớp DMS + 6 lớp OMS trong `class_list_v0.md` | Có, đối chiếu trực tiếp với slide bài giảng 54 và chạy lại `cabin_check.py check` xác nhận 0 lỗi |
