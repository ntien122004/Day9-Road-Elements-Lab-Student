# EDGE CASE CARDS — CÁC TRƯỜNG HỢP ĐẶC BIỆT

**Version:** v1

Mục đích của file này là ghi lại các trường hợp mà **hai annotator hợp lý có thể đưa ra hai cách gán nhãn khác nhau**.

Mỗi edge case mới phát hiện trong quá trình annotation phải được bổ sung vào file này.

---

## EC-001 — Biển báo bị cây/cành che

### Tình huống

Một phần biển báo bị lá hoặc cành cây che.

### Có thể xảy ra khác biệt

- Annotator A vẽ theo kích thước ước lượng của toàn bộ biển.
- Annotator B chỉ vẽ phần nhìn thấy.

### Quyết định

Chỉ vẽ phần biển báo nhìn thấy được.

```text
visibility = partial
```

Nếu vẫn nhận dạng được:

```text
identification = known
```

Nếu không:

```text
identification = unknown
sign_code = unknown
```

---

## EC-002 — Biển báo bị xe che

### Tình huống

Xe che một phần biển báo.

### Quyết định

- Chỉ bounding box phần biển báo nhìn thấy.
- Không đưa xe vào bounding box.

```text
visibility = partial
```

---

## EC-003 — Biển báo rất nhỏ ở xa

### Tình huống

Một vật thể nhỏ ở xa có hình dạng giống traffic sign.

### Quyết định

Nếu chắc chắn đó là traffic sign:

```text
class = traffic_sign
visibility = low
identification = unknown
sign_code = unknown
```

Nếu không đủ bằng chứng để xác định đó là traffic sign:

**Không annotate.**

Nếu nhóm không thống nhất → `review_status = escalate`.

---

## EC-004 — Quảng cáo có hình giống biển báo

### Tình huống

Quảng cáo có hình tròn/tam giác và màu sắc giống traffic sign.

### Quyết định

Không annotate.

Hình dạng tương tự không đủ để biến object thành traffic sign.

---

## EC-005 — Nhiều biển báo trên cùng một cột

### Tình huống

Có nhiều biển báo xếp dọc trên cùng một cột.

### Quyết định

Mỗi biển báo là một instance riêng.

```text
traffic_sign #1
traffic_sign #2
traffic_sign #3
```

Không gộp thành một bounding box.

---

## EC-006 — Biển phụ

### Tình huống

Có một supplementary sign/plate bên dưới biển chính.

### Quyết định

Thực hiện theo ontology của project.

Không tự ý gộp hoặc tách nếu ontology chưa quy định.

Nếu gặp trường hợp chưa có trong guideline:

```text
review_status = escalate
```

và bổ sung quyết định vào file này sau khi thống nhất.

---

## EC-007 — Biển bị che gần như hoàn toàn

### Tình huống

Chỉ còn một phần rất nhỏ của biển báo nhìn thấy.

### Quyết định

Nếu vẫn xác định được đây là traffic sign:

```text
class = traffic_sign
visibility = severely_occluded
identification = unknown
sign_code = unknown
```

Nếu thậm chí không chắc đây có phải traffic sign:

Không annotate hoặc escalate nếu cần review.

---

## EC-008 — Biển bị cắt bởi mép ảnh

### Tình huống

Một phần biển báo nằm ngoài ảnh.

### Quyết định

Vẽ bounding box theo phần nhìn thấy trong ảnh.

Không kéo bounding box ra ngoài ảnh.

```text
visibility = partial
```

---

## EC-009 — Góc nhìn nghiêng

### Tình huống

Biển báo được chụp từ góc nghiêng mạnh.

### Quyết định

Vẽ theo phần biển báo thực tế nhìn thấy.

Không biến nó thành hình chữ nhật chính diện tưởng tượng.

---

## EC-010 — Không xác định được loại biển

### Tình huống

Chắc chắn là traffic sign nhưng không thể xác định chính xác sign code.

### Quyết định

```text
sign_code = unknown
identification = unknown
```

Không đoán dựa trên context.

---

## EC-011 — Cùng một biển báo qua nhiều frame

### Tình huống

Video có cùng một biển báo xuất hiện trong nhiều frame.

### Quyết định

Nếu task không dùng tracking:

- Annotate từng frame.
- Geometry phải phù hợp với frame hiện tại.
- Cập nhật visibility theo từng frame.

Không tự động copy sign code sang frame mà biển báo đã quá mờ/che khuất.

---

## EC-012 — Biển báo không chắc là của Đức

### Tình huống

Nhìn thấy traffic sign nhưng không xác định được hệ thống biển báo/quốc gia.

### Quyết định

Không gán một German `sign_code` chỉ dựa trên suy đoán.

Nếu scope yêu cầu annotate traffic sign:

```text
class = traffic_sign
sign_code = unknown
identification = unknown
review_status = escalate
```

---

## EC-013 — Hai annotator chọn hai sign code khác nhau

### Tình huống

Cả hai annotator đều xác định đúng là traffic sign nhưng không thống nhất sign code.

### Quyết định

Không chọn theo cảm tính hoặc majority nếu chưa có quy tắc.

Đưa vào:

```text
review_status = escalate
```

Sau khi reviewer quyết định, cập nhật guideline/edge case nếu đây là tình huống có thể lặp lại.

---

## EC-014 — Vật thể bị mờ do chuyển động

### Tình huống

Biển báo bị motion blur nhưng vẫn có thể xác định là traffic sign.

### Quyết định

Nếu geometry còn đủ rõ:

```text
class = traffic_sign
visibility = low
```

Nếu sign code không thể xác định:

```text
sign_code = unknown
identification = unknown
```

---

## EC-015 — Không chắc có nên annotate hay không

### Tình huống

Annotator không thể quyết định object có đủ bằng chứng để được coi là traffic sign.

### Quyết định

Không tự đoán.

Nếu object không thể được xác định đáng tin cậy → không annotate.

Nếu cần quyết định thống nhất cho dataset →:

```text
review_status = escalate
```

và thêm kết luận cuối cùng vào edge-case cards.
