# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| traffic_light | rectangle | class (label) | — | — | — | Mỗi đầu đèn vật lý = 1 instance. Chỉ cần 1 class duy nhất vì tất cả đều là đèn giao thông, phân biệt bằng attribute |
| state | — | attribute of traffic_light | red, yellow, green, off, unknown | `__undefined__` | Yes | Trạng thái đèn thay đổi theo thời gian (frame). off = đèn tắt, unknown = không đọc được |
| relevance | — | attribute of traffic_light | relevant, not_relevant, unknown | `__undefined__` | No | Một đèn vật lý cố định cho 1 nhóm làn — không đổi theo frame. unknown kèm needs_review = ESCALATE |
| pictogram | — | attribute of traffic_light | circle, arrow_left, arrow_straight, arrow_right, other, unknown | `__undefined__` | No | Hình in trên mặt kính đèn — cố định vật lý. Là bằng chứng để xác định relevance |
| needs_review | — | attribute of traffic_light | true, false | false | Yes | Flag cho QA reviewer kiểm tra. Bật khi relevance=unknown hoặc bất kỳ khi nào không chắc chắn |

## Class hay attribute

- `traffic_light` là **class** vì đây là object type duy nhất cần detect. Không cần class khác.
- `state`, `relevance`, `pictogram` là **attribute** vì chúng là thuộc tính của cùng 1 object. Nếu tách thành class riêng sẽ nổ tổ hợp (5 state x 3 relevance x 6 pictogram = 90 class — vô lý).
- Default `__undefined__` có thể gây bias: annotator quên đổi → export có giá trị không hợp lệ. Self-QC phải kiểm tất cả box không còn `__undefined__`.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.74.1
- **Tên task calibration** (có version guideline): schoolmini-calib-v1
- **Guide của task đã dán `02_guideline.md`?** Đã dán
- **Nhóm dùng Track hay Shape, vì sao:** Calibration (LISA video) dùng Track vì cần theo dõi đèn qua 30 frame liên tiếp. Blind test (BDD ảnh tĩnh) dùng Shape vì mỗi ảnh độc lập.

## Setup test

Đinh Hoàng Lịch đã mở task CVAT thành công, upload bộ ảnh calibration và dán Sổ quy tắc (guideline) vào mô tả task. Các thành viên đã truy cập và thao tác gán nhãn bình thường. Đảm bảo CVAT hoạt động tốt.
