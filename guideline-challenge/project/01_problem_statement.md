# Problem statement + downstream contract

## Bài toán

Xác định trạng thái (`state`) và mức liên quan tới ego (`relevance`) của từng đầu đèn giao thông tại ngã tư có nhiều đầu đèn — nơi mà gán sai đèn nào điều khiển xe ego sẽ dẫn đến quyết định dừng/đi sai cho hệ thống lái tự động.

## Downstream contract

1. **Downstream task / model / user là ai?** Hệ thống ADAS / Self-driving — module quyết định dừng/đi tại ngã tư. Model cần biết chính xác đèn nào đang điều khiển làn của xe ego để ra lệnh phanh hoặc tiếp tục.
2. **Output annotation nào thực sự cần?** Bounding box (rectangle) cho từng đầu đèn + 3 attribute: `state` (red/yellow/green/off/unknown), `relevance` (relevant/not_relevant/unknown), `pictogram` (circle/arrow_left/arrow_straight/arrow_right/other/unknown). Với video: track liên tục, state theo frame.
3. **Failure nào gây hậu quả lớn nhất?** Gán `relevance=not_relevant` cho đèn đỏ đang điều khiển ego → xe chạy khi cần dừng (CRITICAL). Thứ hai: gán `state` sai ở frame chuyển → model học sai thời điểm chuyển tín hiệu.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Khi không đủ bằng chứng để xác định làn/hướng ego → đặt `relevance=unknown` + `needs_review=true`. QA reviewer sẽ quyết định dựa trên context rộng hơn.

## Scope

- **Trong scope (bắt buộc label):** Mọi đầu đèn giao thông mà nhìn thấy ≥50% vỏ đèn, kể cả đèn nhỏ/xa.
- **Ngoài scope (ignore):** Phản chiếu đèn trên kính xe/mặt đường, đèn trang trí, đèn LED quảng cáo, đèn đường (streetlight), đèn bên trong xe.
- **Geometry tolerance:** Box ôm phần vỏ đèn nhìn thấy, lệch ≤ 2 px mỗi cạnh là đạt. Không lấy cột hay giá treo.

## Output chấm được

LABEL = có box trong export. IGNORE = không có box. UNKNOWN = attribute `state="unknown"` hoặc `relevance="unknown"`. ESCALATE = `relevance="unknown"` + `needs_review="true"`. Geometry = toạ độ box trong XML (xtl, ytl, xbr, ybr).

## Dữ liệu và giới hạn

- LISA: 30 frame liên tiếp từ dayClip5 (frame 1606→1635), xe tiến tới ngã tư ban ngày. Chỉ 1 clip.
- BDD100K: 26 ảnh dashcam, ~12 ảnh có đèn giao thông, đa dạng thời tiết và giờ.
- Blind test dùng 100% ảnh BDD (cảnh hoàn toàn mới cho peer). LISA chỉ dùng cho example và calibration.
