# Annotation guideline — Traffic light state + ego relevance at multi-light intersections

**Version:** v1

## 1. Objective + scope

**Mục tiêu:** Label từng đầu đèn giao thông trong ảnh/video dashcam, xác định trạng thái (`state`) và đèn nào đang điều khiển xe ego (`relevance`), phục vụ module dừng/đi của hệ thống ADAS.

**Trong scope:**
- Mọi đầu đèn giao thông (tín hiệu điều khiển giao thông) mà nhìn thấy >= 50% vỏ đèn
- Đèn tròn, đèn mũi tên, đèn người đi bộ (phân loại qua `pictogram`)
- Đèn nhỏ/xa nếu nhìn thấy vỏ đèn

**Ngoài scope (IGNORE — không vẽ box):**
- Phản chiếu đèn trên kính xe, mặt đường, tòa nhà
- Đèn đường (streetlight) — không phải tín hiệu giao thông
- Đèn LED quảng cáo, đèn trang trí
- Đèn bên trong cabin xe

## 2. Annotation unit

- **Ảnh tĩnh (BDD):** Mỗi đầu đèn = 1 Rectangle (Shape)
- **Video (LISA):** Mỗi đầu đèn vật lý = 1 Rectangle Track xuyên suốt các frame
- **Khi nào là instance mới:** Mỗi mặt đèn riêng biệt (có vỏ riêng) là 1 instance. Cụm 2 đèn trên cùng trụ nhưng mặt khác nhau (1 tròn, 1 mũi tên) = 2 instance.

## 3. Geometry rule

- **Tool:** Rectangle
- **Tight box:** Box sát mặt vỏ đèn nhìn thấy được
- **Không lấy cột/giá treo:** Cạnh box không bao gồm cột đỡ, dây cáp, giá treo
- **Đèn bị cắt mép ảnh:** Box đi tới mép ảnh
- **Tolerance:** <= 2 pixel mỗi cạnh so với vỏ đèn
- **Đèn chồng lấn:** Mỗi đầu đèn 1 box riêng, box được phép overlap

## 4. Taxonomy

Chỉ có 1 label duy nhất: `traffic_light` (rectangle). Mỗi box có 4 attribute:

| Attribute | Mutable? | Allowed values | Default | Khi nào dùng unknown |
|---|---|---|---|---|
| `state` | Yes | red, yellow, green, off, unknown | `__undefined__` | Bị che, lóa, quá nhỏ để đọc màu |
| `relevance` | No | relevant, not_relevant, unknown | `__undefined__` | Không xác định được đèn thuộc nhóm làn nào |
| `pictogram` | No | circle, arrow_left, arrow_straight, arrow_right, other, unknown | `__undefined__` | Quá nhỏ/mờ, không nhìn rõ hình trên mặt đèn |
| `needs_review` | Yes | true/false | false | Bật khi relevance=unknown hoặc lúc nào không chắc chắn |

Mọi box phải được gán giá trị cụ thể. Export mà còn `__undefined__` = lỗi annotator.

Bảng đầy đủ ở `03_ontology_and_cvat_setup.md` — hai nơi phải khớp nhau.

## 5. Inclusion / exclusion

**Bắt buộc label:**
- Đèn giao thông nhìn thấy >= 50% vỏ đèn
- Đèn đang tắt (`state=off`) nhưng thấy rõ vỏ đèn
- Đèn mũi tên — box riêng, `pictogram` tương ứng
- Đèn nhỏ ở ngã tư phía xa — nếu nhìn thấy vỏ đèn, vẫn label

**Không label (IGNORE):**
- Phản chiếu đèn trên kính, mặt đường, tòa nhà — không phải đèn thật
- Đèn đường, đèn LED quảng cáo, đèn trang trí
- Đèn thấy < 50% vỏ (bị che gần hết)

## 6. Visibility / occlusion

| Tình huống | Hành động |
|---|---|
| Đèn rõ ràng | Label bình thường |
| Đèn bị che 1 phần (>= 50% visible) | Vẫn label, box ôm phần thấy, `needs_review=true` nếu không chắc |
| Đèn bị che gần hết (< 50% visible) | IGNORE — không vẽ |
| Đèn bị lóa (glare) | Vẫn label (thấy vỏ đèn), `state=unknown` nếu không đọc chắc được màu |
| Đèn quá nhỏ/xa (< 15px) | Vẫn label nếu nhận ra là đèn giao thông, `state=unknown`, `pictogram=unknown`, `needs_review=true` |
| Ban đêm — đèn sáng nhưng vỏ không rõ | Label nếu nhận ra vỏ đèn từ hình dạng ánh sáng |

## 7. Ambiguity / escalation

**Quyết định relevance — quy trình:**
1. Xác định làn/hướng đi của ego (dựa vào vạch kẻ, vị trí xe, bối cảnh)
2. Xác định đèn thuộc nhóm nào (vị trí trụ, giá treo, mũi tên)
3. So khớp: đèn điều khiển làn ego hay làn khác?

| Bằng chứng | Decision |
|---|---|
| Đèn gắn giá treo phía trước, cùng hướng ego, pictogram phù hợp | `relevance=relevant` |
| Đèn rõ ràng cho làn rẽ trái nhưng ego đi thẳng | `relevance=not_relevant` |
| Đèn cho người đi bộ | `relevance=not_relevant` |
| Không xác định được đèn cho làn nào | `relevance=unknown`, `needs_review=true` → ESCALATE |

**LABEL / IGNORE / UNKNOWN / ESCALATE trong CVAT:**
- LABEL = có box
- IGNORE = không vẽ box
- UNKNOWN = attribute `state="unknown"` hoặc `relevance="unknown"`
- ESCALATE = `relevance="unknown"` + `needs_review="true"`

**Quyết định state khi mơ hồ:**
- Thấy màu → gán state tương ứng
- Không thấy được màu → `state=unknown`
- TUYỆT ĐỐI KHÔNG BỊA state cho đèn không rõ.

## 8. Temporal rule

**Đối với bài thi Blind (ảnh BDD):** Không áp dụng — task ảnh tĩnh, dùng Rectangle Shape.

**Đối với Calibration nội bộ (video LISA):**
- Track bắt đầu ở frame đầu tiên nhìn thấy >= 50% vỏ đèn.
- State đổi → đặt keyframe mới ở đúng frame chuyển. CVAT giữ giá trị cũ cho đến khi gặp keyframe mới.
- Đèn ra khỏi khung hoặc bị che hẳn → BẮT BUỘC đặt `outside` ở frame đó. Thiếu outside = box "ma" kéo dài.
- Khi đổi state, CVAT tự tạo keyframe attribute nhưng KHÔNG tự kéo box. Phải kiểm tra và căn lại box.
- State nhảy vô lý (red→green→red trong 2 frame) là lỗi — đèn thật không chuyển vậy.
- `pictogram` và `relevance` cố định cho cả track — không đổi theo frame.

## 9. Examples

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| LISA05 | 2 đầu đèn rõ ràng ban ngày | Box 2 đèn. Gán state theo màu thấy, relevance theo vị trí đèn so với làn ego | Mục 4 + 7 |
| BDD18 | Ảnh ban đêm, đèn sáng lóa | Box đèn. Nếu lóa quá → `state=unknown` | Mục 6 |
| LISA20 | Đèn nhỏ ở ngã tư phía xa | Box nhỏ, `state=unknown`, `pictogram=unknown`, `relevance=unknown`, `needs_review=true` | Mục 6 + 7 |

## 10. Common mistakes

- **Gộp 2 đầu đèn vào 1 box:** Mỗi mặt đèn (có vỏ riêng) = 1 box riêng.
- **Box lấy cả cột:** Box sát vỏ đèn, tolerance <= 2px.
- **Để `__undefined__`:** Quét tất cả box trước export, đảm bảo mọi attribute đã được gán giá trị cụ thể.
- **Gán relevance cảm tính:** Phải có bằng chứng (vạch kẻ, vị trí trụ, giá treo, mũi tên). Không có bằng chứng → `unknown`.
- **Quên outside (video):** Track kết thúc trước frame cuối phải có `outside`.
- **Bịa state:** Frame nào không đọc được màu → `state=unknown`. Không được dùng frame trước/sau để suy diễn.
