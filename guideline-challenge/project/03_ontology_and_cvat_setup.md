# ONTOLOGY VÀ CẤU HÌNH CVAT

**Version:** v1

## 1. Object Classes

| Name | Geometry | Class / Attribute | Giá trị | Default | Mutable? | Lý do |
|---|---|---|---|---|---|---|
| `traffic_sign` | Bounding Box | Class | `traffic_sign` | `traffic_sign` | No | Tất cả traffic sign dùng chung object class |

---

## 2. Attributes

| Name | Geometry | Class / Attribute | Allowed Values | Default | Mutable? | Lý do |
|---|---|---|---|---|---|---|
| `sign_family` | N/A | Attribute | warning, regulatory, prohibitory, mandatory, priority, information, direction, temporary, supplementary, other, unknown | unknown | Yes | Nhóm của biển báo |
| `sign_code` | N/A | Attribute | Mã biển báo Đức hoặc unknown | unknown | Yes | Nhận dạng chính xác mà không tạo hàng trăm class |
| `visibility` | N/A | Attribute | full, partial, low, severely_occluded | full | Yes | Mức độ nhìn thấy có thể thay đổi theo frame |
| `identification` | N/A | Attribute | known, unknown | unknown | Yes | Tách việc phát hiện object khỏi việc nhận dạng |
| `review_status` | N/A | Attribute | normal, escalate | normal | Yes | Ghi nhận trường hợp cần review |

---

## 3. Image-level Attribute

| Name | Allowed Values | Default | Ý nghĩa |
|---|---|---|---|
| `image_status` | contains_sign, no_sign, uncertain | contains_sign | Ghi nhận ảnh có/không có traffic sign |

### Lưu ý

Nếu ảnh không có biển báo:

```text
image_status = no_sign
```

Không tạo bounding box giả.

---

## 4. LABEL / IGNORE / UNKNOWN / ESCALATE

### LABEL

Tạo object:

```text
class = traffic_sign
```

khi object có đủ bằng chứng để xác định là traffic sign và có thể định vị bằng bounding box.

### IGNORE

Không tạo annotation cho:

- quảng cáo;
- logo;
- road marking;
- sticker;
- vật thể không phải traffic sign;
- vật thể không đủ bằng chứng để xác định là traffic sign.

**Việc không tạo annotation chính là cách thể hiện IGNORE trong CVAT.**

### UNKNOWN

Nếu chắc chắn object là traffic sign nhưng không xác định được loại:

```text
sign_family = unknown
sign_code = unknown
identification = unknown
```

### ESCALATE

Nếu cần người review:

```text
review_status = escalate
```

Không được dùng một quyết định chỉ tồn tại trong trao đổi miệng mà không thể thấy trong CVAT/export.

---

## 5. Class hay Attribute?

### Dùng CLASS khi:

1. Object có bản chất khác nhau rõ rệt.
2. Downstream cần phân loại trực tiếp.
3. Geometry hoặc QA rule khác nhau.

### Dùng ATTRIBUTE khi:

1. Nó mô tả thuộc tính của cùng một object.
2. Giá trị có thể thay đổi giữa các frame.
3. Tách thành class sẽ tạo ra quá nhiều tổ hợp.

### Áp dụng cho project

| Thành phần | Loại | Lý do |
|---|---|---|
| `traffic_sign` | Class | Object chính cần detect |
| `sign_family` | Attribute | Thuộc tính của traffic sign |
| `sign_code` | Attribute | Nhận dạng cụ thể của traffic sign |
| `visibility` | Attribute | Có thể thay đổi theo frame |
| `identification` | Attribute | Trạng thái nhận dạng |
| `review_status` | Attribute | Trạng thái QA/review |

Không tạo class kiểu:

```text
small_speed_limit
occluded_speed_limit
unknown_speed_limit
temporary_speed_limit
```

---

## 6. Yêu cầu đối với CVAT Export

Export phải giữ lại được:

- Class.
- Bounding-box coordinates.
- `sign_family`.
- `sign_code`.
- `visibility`.
- `identification`.
- `review_status`.
- `image_status` nếu project dùng image-level attributes.

Nếu một quyết định không thể khôi phục từ annotation/export thì quyết định đó không được dùng làm quy tắc chấm annotation.
