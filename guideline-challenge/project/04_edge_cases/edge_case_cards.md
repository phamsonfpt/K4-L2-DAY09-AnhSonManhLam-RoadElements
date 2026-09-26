# Thẻ Edge Cases (Tình huống khó)

Tài liệu này lưu trữ các quyết định xử lý Edge Case của team, KHÔNG gửi cho nhóm peer.
Nó là cơ sở để ra đề (Gold Decisions) bẫy nhóm peer.

Case ID: EC01
- **Ảnh ví dụ:** `BDD26`
- **Mô tả:** Đèn phát sáng mạnh ban đêm (glare bloom) chìm hoàn toàn phần vỏ nhựa vào bóng tối.
- **Rule chốt (v2):** Chấp nhận vẽ box ôm phần quầng sáng (halo). Bắt buộc gán `pictogram=unknown`.
- **Gold decision:** Gán `state=red`, nhưng `pictogram=unknown`.

Case ID: EC02
- **Ảnh ví dụ:** `BDD13`
- **Mô tả:** Có 5 đầu đèn, không có vạch phân làn hoặc xe đi đè vạch.
- **Rule chốt (v2):** Chống đoán mò cảm tính. `relevance=unknown` và `needs_review=true`.
- **Gold decision:** Đánh sập các peer cố tình đoán bừa `relevant`.

Case ID: EC03
- **Ảnh ví dụ:** `BDD12`
- **Mô tả:** Đèn hình mũi tên rẽ trái màu đỏ rõ ràng, xe nằm ở làn đi thẳng.
- **Rule chốt:** Không điều khiển ego -> `not_relevant`.
- **Gold decision:** Bẫy Severity: Critical nếu gán relevant.

Case ID: EC04
- **Ảnh ví dụ:** `BDD02`
- **Mô tả:** Đèn đường dành cho người đi bộ ở ngã tư.
- **Rule chốt:** Ignore hoặc `not_relevant`.

Case ID: EC05
- **Ảnh ví dụ:** `BDD25`
- **Mô tả:** Đèn bị che hơn 50% bởi xe tải lớn.
- **Rule chốt:** Bỏ qua không vẽ box.

Case ID: EC06
- **Ảnh ví dụ:** `LISA21`
- **Mô tả:** Đèn đang ở trạng thái vàng sắp chuyển đỏ, nhìn hơi lóa.
- **Rule chốt:** `state=yellow`.

Case ID: EC07
- **Ảnh ví dụ:** `LISA20`
- **Mô tả:** Đèn tín hiệu giao thông công trường nhấp nháy.
- **Rule chốt:** `relevance=unknown` nếu không rõ làn.

Case ID: EC08
- **Ảnh ví dụ:** `BDD26`
- **Mô tả:** Hình ảnh đèn phản chiếu trên kính chắn gió.
- **Rule chốt:** IGNORE hoàn toàn.
