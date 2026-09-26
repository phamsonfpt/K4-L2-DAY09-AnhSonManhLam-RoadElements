# QA Plan — Chiến thuật chấm điểm chéo

**Người phụ trách QA:** Vũ Đình Sơn Lâm

## 1. Mục tiêu
Đảm bảo phát hiện toàn bộ các lỗi sai của nhóm Peer dựa trên Sổ quy tắc v2 và bộ bẫy Gold Decisions đã giăng sẵn.

## 2. Chiến lược kiểm tra (Sampling)
- **Kiểm tra 100% (Toàn bộ 5 ảnh Blind).**
- Lọc ưu tiên trên CVAT: Lọc các box có `needs_review=true` hoặc `relevance=unknown` để xem nhóm Peer có áp dụng đúng luật Escalation hay không.

## 3. Các điểm mù cần "soi" kỹ (Dựa trên bẫy Gold):
1. **Ảnh BDD12 (Bẫy relevance):** Kiểm tra xem Peer có bị nhầm lẫn giữa đèn rẽ trái (màu đỏ) thành đèn điều khiển xe đi thẳng (relevant) không.
2. **Ảnh BDD13 (Bẫy ambiguity):** Kiểm tra xem Peer có lạm dụng việc tự đoán làn đường khi vạch kẻ không rõ ràng hay không. Phải bắt buộc dùng `unknown`.
3. **Ảnh BDD26 (Bẫy ban đêm lóa sáng):** Soi kỹ xem Peer có vẽ box ôm phần quầng sáng không, và có bắt buộc gán `pictogram=unknown` không.

## 4. Hành động sau QA
- Ghi nhận mọi lỗi sai vào `clarification_log.csv`.
- Phản hồi cho Peer và thảo luận chốt hạ.
- Nâng cấp Sổ quy tắc lên v3 để lấp lỗ hổng nếu có.
