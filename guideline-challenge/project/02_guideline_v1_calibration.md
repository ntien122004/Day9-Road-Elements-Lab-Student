# HƯỚNG DẪN GÁN NHÃN BIỂN BÁO GIAO THÔNG ĐỨC

**Version:** v1

## 1. Mục tiêu và phạm vi (Objective & Scope)

### Mục tiêu

Tạo một bộ dữ liệu nhất quán để phát hiện và nhận dạng biển báo giao thông Đức trong ảnh/khung hình giao thông.

Annotation cần cho phép hệ thống phía sau xác định:

1. Ảnh có biển báo hay không.
2. Vị trí của từng biển báo.
3. Loại/nhóm biển báo.
4. Biển báo có thể được nhận dạng chắc chắn hay không.
5. Mức độ che khuất hoặc khó quan sát của biển báo.

### Phạm vi

Gán nhãn các biển báo giao thông Đức xuất hiện trong ảnh giao thông đường bộ, bao gồm:

- Biển cảnh báo.
- Biển cấm.
- Biển hiệu lệnh.
- Biển chỉ dẫn.
- Biển ưu tiên.
- Biển hướng/dẫn hướng.
- Biển tạm thời.
- Biển phụ nếu được quy định trong ontology của project.

Không yêu cầu annotator đoán loại biển báo khi hình ảnh không cung cấp đủ thông tin.

---

## 2. Đơn vị gán nhãn (Annotation Unit)

### Đơn vị chính

**Mỗi biển báo vật lý = một annotation instance.**

Ví dụ:

- 3 biển báo riêng biệt trên cùng một cột → tạo 3 annotations.
- 2 biển báo đặt cạnh nhau → tạo 2 annotations.
- Một biển báo xuất hiện ở 2 vị trí khác nhau trong ảnh → tạo 2 instances.

### Ảnh không có biển báo

Nếu ảnh không chứa biển báo hợp lệ:

- Không tạo bounding box `traffic_sign`.
- Ghi `image_status = no_sign`.

**Không tạo bounding box giả.**

### Cụm biển báo

Nếu nhiều biển báo đứng cạnh nhau, không gộp chúng thành một object nếu chúng là các biển báo vật lý riêng biệt.

---

## 3. Quy tắc hình học (Geometry Rule)

### Geometry mặc định

Sử dụng **Bounding Box** cho mỗi biển báo.

Bounding box phải:

- Bao phủ phần biển báo nhìn thấy được.
- Bám sát biển báo.
- Không bao gồm phần nền không cần thiết.
- Không bao gồm cột biển báo nếu có thể tránh.
- Không bao gồm cây, xe, nhà cửa hoặc mặt đường xung quanh.

### Biển báo bị che một phần

Bounding box bao phủ phần biển báo thực sự nhìn thấy.

Không tự mở rộng bounding box vào phần hoàn toàn bị che nếu không có đủ bằng chứng về hình dạng của phần bị che.

### Góc nhìn

Gán nhãn theo **hình dạng thực tế nhìn thấy trong ảnh**, không tưởng tượng biển báo ở góc nhìn chính diện.

### Biển báo rất nhỏ

Nếu vẫn có thể xác định một cách đáng tin cậy rằng đó là biển báo:

- Vẫn tạo annotation.
- `visibility = low`.
- Nếu không xác định được loại cụ thể → `identification = unknown`.

Nếu quá nhỏ đến mức không thể xác định chắc chắn đây có phải biển báo hay không → không gán nhãn; nếu cần review thì dùng `review_status = escalate`.

---

## 4. Phân loại (Taxonomy)

### Class chính trong CVAT

Sử dụng một class chính:

`traffic_sign`

Không tạo một CVAT class riêng cho từng loại biển báo Đức.

Thông tin cụ thể của biển báo được lưu bằng **attributes**.

### Attribute: `sign_family`

Các giá trị cho phép:

- `warning`
- `regulatory`
- `prohibitory`
- `mandatory`
- `priority`
- `information`
- `direction`
- `temporary`
- `supplementary`
- `other`
- `unknown`

### Attribute: `sign_code`

Ghi mã biển báo Đức khi có thể xác định chắc chắn.

Ví dụ:

- `Zeichen 205`
- `Zeichen 206`
- `Zeichen 274`
- `Zeichen 301`
- `Zeichen 306`

Nếu không thể xác định chính xác:

`unknown`

**Không được đoán mã.**

### Attribute: `visibility`

Các giá trị:

- `full`
- `partial`
- `low`
- `severely_occluded`

### Attribute: `identification`

Các giá trị:

- `known`
- `unknown`

### Attribute: `review_status`

Các giá trị:

- `normal`
- `escalate`

---

## 5. Quy tắc Bao gồm / Loại trừ (Inclusion / Exclusion)

### Bao gồm (LABEL)

Gán nhãn:

- Biển báo giao thông Đức nhìn thấy rõ.
- Biển báo bị che một phần nhưng vẫn xác định được là biển báo.
- Biển báo ở xa nhưng vị trí có thể xác định đáng tin cậy.
- Biển báo tạm thời.
- Biển báo gắn trên cột.
- Biển báo gắn trên giàn/khung.
- Biển báo nhìn thấy giữa các xe hoặc qua khoảng trống của cây cối.

### Loại trừ (IGNORE)

Không gán nhãn:

- Quảng cáo.
- Logo công ty.
- Biển hiệu cửa hàng.
- Road marking/vạch kẻ đường.
- Sticker trên xe.
- Biển trang trí.
- Vật thể có hình tròn/tam giác giống biển báo nhưng không đủ bằng chứng là biển báo giao thông.

### Biển báo không xác định được là của Đức

Nếu chắc chắn đó là traffic sign nhưng không xác định được hệ thống biển báo:

- Vẫn có thể tạo `traffic_sign` nếu scope của dataset yêu cầu.
- `sign_code = unknown`.
- `identification = unknown`.
- Chỉ chọn `sign_family` nếu có thể xác định chắc chắn.

**Không tự gán mã biển báo Đức.**

---

## 6. Khả năng nhìn thấy / Che khuất (Visibility / Occlusion)

### `full`

Toàn bộ phần quan trọng của biển báo nhìn thấy được.

```text
visibility = full
```

### `partial`

Một phần biển báo bị che, bị cắt bởi mép ảnh hoặc không nhìn thấy hoàn toàn, nhưng vẫn đủ bằng chứng để xác định object.

```text
visibility = partial
```

### `low`

Biển báo nhìn thấy nhưng:

- quá nhỏ,
- bị mờ,
- tương phản thấp,
- ở quá xa,
- hoặc khó quan sát.

```text
visibility = low
```

### `severely_occluded`

Chỉ nhìn thấy một phần rất nhỏ nhưng vẫn đủ bằng chứng để xác định đây là traffic sign.

```text
visibility = severely_occluded
```

Trong trường hợp này, thường dùng:

```text
identification = unknown
```

trừ khi loại biển báo vẫn thực sự rõ ràng.

### Vật thể che khuất

Không đưa vật thể che khuất vào bounding box.

Ví dụ:

- Cành cây → không thuộc biển báo.
- Xe → không thuộc biển báo.
- Cột → không thuộc phần biển báo.
- Lá/cây che biển → chỉ annotate phần biển báo nhìn thấy.

---

## 7. Mơ hồ và Escalation (Ambiguity / Escalation)

### Nguyên tắc quan trọng

**Không được đoán.**

Nếu có từ hai cách hiểu hợp lý:

- Nếu chắc chắn là traffic sign → vẫn tạo annotation.
- Nếu không chắc mã biển → `sign_code = unknown`.
- Nếu không chắc loại → `identification = unknown`.
- Nếu cần người khác quyết định → `review_status = escalate`.

### Khi nào cần ESCALATE?

Escalate khi:

1. Không chắc object có phải traffic sign hay không.
2. Có từ hai sign code đều có khả năng đúng.
3. Không xác định được hệ thống biển báo/quốc gia.
4. Hình học không đủ rõ để tạo bounding box đáng tin cậy.
5. Biển báo có thiết kế bất thường hoặc không thuộc các trường hợp đã quy định.

**Mọi quyết định escalation phải được thể hiện bằng attribute trong CVAT. Không sử dụng quy tắc chỉ truyền miệng.**

---

## 8. Quy tắc theo thời gian (Temporal Rule)

Áp dụng khi ảnh đến từ video hoặc chuỗi frame.

### Cùng một biển báo qua nhiều frame

Nếu CVAT task không sử dụng tracking:

- Annotate biển báo độc lập ở từng frame.
- Geometry phải phản ánh vị trí thực tế trong frame đó.

### Visibility thay đổi

Nếu biển báo rõ ở frame trước nhưng bị che ở frame sau:

- Cập nhật `visibility`.
- Cập nhật `identification` nếu frame mới không còn đủ thông tin.

Không copy một `sign_code` từ frame trước sang frame sau nếu frame sau không còn đủ bằng chứng.

### Biển báo đi vào/ra khỏi frame

Chỉ tạo annotation khi biển báo đủ rõ để xác định là traffic sign.

Khi không còn nhìn thấy biển báo, không tạo annotation cho phần không tồn tại trong frame.

---

## 9. Ví dụ (Examples)

### Ví dụ 1 — Biển báo rõ ràng

```text
class = traffic_sign
sign_family = prohibitory
sign_code = [mã xác định được]
visibility = full
identification = known
review_status = normal
```

### Ví dụ 2 — Có biển báo nhưng quá xa

```text
class = traffic_sign
sign_family = unknown
sign_code = unknown
visibility = low
identification = unknown
review_status = normal
```

### Ví dụ 3 — Bị xe che một phần

```text
class = traffic_sign
visibility = partial
```

Nếu vẫn nhận dạng được:

```text
identification = known
sign_code = [mã xác định được]
```

Nếu không:

```text
identification = unknown
sign_code = unknown
```

### Ví dụ 4 — Không có biển báo

```text
image_status = no_sign
```

Không tạo `traffic_sign` bounding box.

### Ví dụ 5 — Quảng cáo giống biển báo

Không annotate.

Lý do: không phải traffic sign.

### Ví dụ 6 — Nhiều biển báo trên cùng cột

Tạo:

```text
traffic_sign #1
traffic_sign #2
traffic_sign #3
```

Không tạo một bounding box bao quanh cả cụm.

---

## 10. Các lỗi thường gặp (Common Mistakes)

### Lỗi 1 — Đoán mã biển báo

Sai:

> "Nhìn giống biển giới hạn tốc độ nên chọn mã gần đúng."

Đúng:

> Nếu không đủ bằng chứng → `sign_code = unknown`.

### Lỗi 2 — Bao gồm cột biển báo

Cột không thuộc geometry của traffic sign.

### Lỗi 3 — Gộp nhiều biển báo

Mỗi biển báo vật lý riêng biệt phải có annotation riêng.

### Lỗi 4 — Gán nhãn quảng cáo

Không phải vật thể hình tròn/tam giác nào cũng là traffic sign.

### Lỗi 5 — Bỏ qua biển báo nhỏ

Nếu vẫn xác định được đó là traffic sign → annotate và dùng:

`visibility = low`

### Lỗi 6 — Tưởng tượng phần bị che

Không tự vẽ/đoán phần hoàn toàn không nhìn thấy.

### Lỗi 7 — Tạo quá nhiều class

Không tạo các class như:

```text
small_speed_limit
occluded_speed_limit
unknown_speed_limit
temporary_speed_limit
```

Những thông tin này phải là attribute.

### Lỗi 8 — Không ghi nhận sự không chắc chắn

Khi không chắc chắn, phải thể hiện bằng:

```text
identification = unknown
sign_code = unknown
review_status = escalate
```

khi phù hợp.
