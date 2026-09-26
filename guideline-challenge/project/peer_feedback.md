# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới là xong (gate G5).

- **Nhóm peer:** Soopichanfanclub
- **Người label blind:** Thành viên nhóm Soopichanfanclub

## 1. Peer trả lời (Tóm tắt những gì đã vẽ)

| Ảnh | Số box | Ghi chú |
|---|---|---|
| BDD02 | 8 | 4 đèn xanh hướng về ego (`relevant`, `circle`); 3 vỏ nhìn nghiêng/không thấy mặt đèn và 1 đèn xanh rất xa -> `unknown` + `needs_review`. |
| BDD12 | 1 | Đèn người đi bộ bàn tay đỏ: `state=red`, `pictogram=other`, `relevance=not_relevant`. |
| BDD13 | 0 | Không thấy đầu đèn giao thông nào. |
| BDD25 | 4 | Ban đêm, không thấy vỏ: box ôm quầng sáng, `pictogram=unknown`, `relevance=unknown`, `needs_review=true` theo rule v2. |
| BDD26 | 4 | Ban đêm, lóa sáng, áp dụng vẽ ôm quầng sáng tương tự rule v2. |

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| Peer không vẽ bất kỳ đèn nào ở ảnh BDD13 | Data ambiguity (Ngã tư quá rối, mất vạch kẻ đường, xe đông che khuất) | Accept + add escalation rule: Cấm đoán mò cảm tính khi mất vạch kẻ đường, bắt buộc dùng `unknown`. | Bài làm của peer trống ở BDD13 |
| Peer nhận định đèn đỏ ở BDD12 là đèn người đi bộ | Guideline gap (Sổ quy tắc v2 chưa ghi rõ cách phân loại đèn người đi bộ) | Accept + revise: Bổ sung quy định Đèn người đi bộ luôn là `not_relevant` và `pictogram=other` (hoặc Ignore). | BDD12 peer gán `pictogram=other` |
