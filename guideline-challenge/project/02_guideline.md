# HƯỚNG DẪN GÁN NHÃN BIỂN BÁO HƯỚNG TỚI NGƯỜI ĐI ĐƯỜNG

**Version:** v2

## 1. Mục tiêu và phạm vi

Tạo annotation nhất quán để phát hiện và phân nhóm biển báo dành cho người tham gia giao thông. Gán biển có thể truyền đạt quy tắc, cảnh báo, quyền ưu tiên, chỉ dẫn hoặc thông tin sử dụng đường, không phụ thuộc biển thuộc quốc gia nào. Tập trung vào biển nhỏ/xa, bị che, khó phân nhóm hoặc khó xác định mã.

Không suy đoán biển áp dụng cho làn hoặc xe nào: ontology hiện không có thuộc tính đó. Gán nhãn mặt biển hướng tới người đi đường; bỏ qua quảng cáo, biển cửa hàng, logo, chữ trên xe, road marking và biển chỉ phục vụ cơ sở tư nhân.

## 2. Đơn vị gán nhãn

Mỗi mặt biển giao thông riêng biệt nhìn thấy trong ảnh là một `traffic_sign` instance. Nhiều mặt biển trên cùng cột hoặc giàn vẫn là nhiều instance; không gộp chúng thành một box. Chỉ tính mặt biển nhìn thấy, không tính mặt sau trơn, cột hoặc giá đỡ.

Task hiện tại dùng ảnh tĩnh. Nếu ảnh có ít nhất một biển đã xác nhận, gán trạng thái ảnh `contains_sign`; nếu không có biển trong scope, gán `no_sign`. Nếu còn đối tượng có thể là biển nhưng chưa đủ bằng chứng, gán `uncertain`.

## 3. Quy tắc hình học

Dùng rectangle quanh phần mặt biển nhìn thấy, gồm viền biển nhưng không gồm cột, giá đỡ, vật che hoặc nền. Với biển bị che/cắt mép ảnh, box chỉ bao phần nhìn thấy; không nội suy phần bị khuất. Gán theo hình dạng thực tế trong ảnh, không chỉnh thành hình chính diện.

Ngưỡng QA đề xuất cho ảnh gốc: sai lệch mỗi cạnh không quá `max(5 px, 10% of corresponding face dimension)` so với biên nhìn thấy. Ngưỡng này là quy ước của nhóm cho lab, không phải chuẩn ngành.

Nếu biển quá nhỏ hoặc mờ để đặt box quanh một mặt biển xác định, không vẽ box phỏng đoán. Dùng trạng thái ảnh `uncertain` nếu chưa thể xác nhận sự hiện diện; ghi rõ trường hợp cần review.

## 4. Taxonomy và cách điền attributes

CVAT dùng một class hình chữ nhật `traffic_sign`; thông tin sau lưu trong attributes:

| Attribute | Quy tắc |
|---|---|
| `sign_family` | Chọn đúng một giá trị theo thứ tự ưu tiên bên dưới. |
| `sign_code` | Dùng `Zeichen <mã>` cho mã GTSDB xác định được, ví dụ `Zeichen 205` hoặc `Zeichen 209-10`. Với biển không có mã GTSDB tương ứng hoặc không đủ bằng chứng về mã, nhập đúng literal `unknown`. Không nhập tên dịch, mã phỏng đoán, `_unknown_` hay `__undefined__`. |
| `visibility` | Chọn `full`, `partial`, `low` hoặc `severely_occluded` theo mục 5. |
| `identification` | `known` nếu xác định được loại/nhóm biển từ bằng chứng ảnh; `unknown` nếu chắc chắn là biển giao thông nhưng không xác định được loại. Mã có thể là `unknown` độc lập với identification. |
| `review_status` | `normal` nếu có thể quyết định theo rule; `escalate` nếu cần người review phân xử. Không dùng escalate thay cho unknown. |

### Chọn `sign_family` (một giá trị duy nhất)

Chọn nhóm cụ thể nhất phù hợp, theo thứ tự ưu tiên sau:

1. `temporary` — biển điều tiết/cảnh báo tạm thời.
2. `supplementary` — mặt biển phụ trợ có nội dung riêng; mỗi mặt biển riêng vẫn là instance riêng.
3. `prohibitory` — cấm hoặc hạn chế hành vi/tuyến đi.
4. `mandatory` — yêu cầu bắt buộc một hành vi/hướng đi.
5. `priority` — quy định quyền ưu tiên.
6. `warning` — cảnh báo nguy hiểm hoặc điều kiện phía trước.
7. `direction` — chỉ đường hoặc hướng tuyến.
8. `information` — thông tin dành cho người đi đường, không thuộc nhóm cụ thể hơn.
9. `regulatory` — quy định giao thông không được mô tả cụ thể hơn ở trên.
10. `other` — biển hướng tới người đi đường nhưng không khớp nhóm nào.
11. `unknown` — xác nhận là biển giao thông nhưng không đủ bằng chứng để chọn nhóm.

Không dùng `regulatory` như nhãn chung nếu biển thuộc một nhóm cụ thể hơn.

## 5. Visibility và che khuất

- `full`: mặt biển nhìn rõ, không bị che đáng kể.
- `partial`: một phần mặt biển bị che hoặc cắt mép ảnh nhưng vẫn nhận diện được.
- `low`: phần lớn mặt biển nhìn thấy nhưng nhỏ, xa, mờ hoặc tương phản thấp.
- `severely_occluded`: phần lớn mặt biển bị che; vẫn đủ bằng chứng xác nhận là biển nhưng hình dáng/chi tiết hạn chế.

Khi có cả che khuất và chất lượng ảnh thấp, chọn mức che khuất nặng nhất nếu che khuất là yếu tố chính; nếu mặt biển hầu như lộ rõ nhưng chỉ khó do kích thước/độ mờ, chọn `low`. Không đưa xe, cành cây, lá hoặc cột vào box.

## 6. Include, ignore và trạng thái ảnh

**Include:** biển hướng tới người tham gia giao thông, gồm biển cảnh báo, cấm/hạn chế, bắt buộc, ưu tiên, chỉ dẫn, thông tin đường bộ, biển tạm thời và mặt biển phụ trợ. Bao gồm biển nhỏ/xa hoặc bị che khi vẫn xác nhận được mặt biển và có thể vẽ box theo phần nhìn thấy.

**Ignore:** quảng cáo, logo, biển cửa hàng, road marking, sticker, biển trang trí, biển chỉ phục vụ cơ sở tư nhân, cột/giàn đỡ và vật thể chỉ giống biển nhưng không đủ bằng chứng.

Trong CVAT, tạo label dạng tag `image_status`, rồi chọn attribute `status`:

- `contains_sign` — có ít nhất một biển trong scope đã xác nhận.
- `no_sign` — đã kiểm tra ảnh và không có biển trong scope.
- `uncertain` — có dấu hiệu có thể là biển trong scope nhưng không đủ bằng chứng xác nhận. Nếu có biển đã xác nhận khác trong cùng ảnh, vẫn gán box cho các biển đó và dùng `uncertain` khi còn candidate chưa phân xử.

Gán đúng một image-status tag cho mỗi ảnh. Không tạo box giả cho candidate chưa xác nhận.

## 7. UNKNOWN và escalation

- Có bằng chứng xác nhận biển, nhưng chưa rõ family/type: tạo box, đặt `sign_family=unknown`, `identification=unknown`; nhập `sign_code=unknown` nếu chưa xác định được mã.
- Biết family nhưng không biết mã: giữ family/identification có bằng chứng và chỉ đặt `sign_code=unknown`.
- Chưa biết candidate có phải biển trong scope hay không: không tạo box phỏng đoán; đặt `image_status.status=uncertain`.
- Cần người review để quyết định family, mã hoặc box của một biển đã xác nhận: đặt `review_status=escalate` trên object.

Ghi nội dung cần phân xử vào calibration/QA report. Không tự ép consensus bằng lời và không mặc định bất đồng là lỗi annotator; xác định đó là thiếu rule, thiếu bằng chứng hay lỗi thao tác rồi cập nhật rule/example/escalation.

## 8. Quy tắc thời gian

Không áp dụng cho sample pack hiện tại: mỗi task item là ảnh tĩnh, không dùng track. Nếu project sau này chuyển sang video, cần viết temporal rule riêng trước khi tạo task đó.

## 9. Ví dụ calibration

Đây là ảnh calibration để áp dụng rule, không phải gold answer về số lượng hoặc phân nhóm. Dùng export và ảnh gốc để thảo luận bằng chứng; không dùng ảnh blind làm ví dụ.

| sample_id | Điểm cần kiểm tra | Expected annotation principle |
|---|---|---|
| GTS02 | Bất đồng lớn về số object | Đếm từng mặt biển hướng tới người đi đường có bằng chứng; không đếm cột/nền; family/code chỉ theo bằng chứng. |
| GTS15 | Bất đồng về số object và visibility | Biển xác nhận được vẫn được tạo box dù khó đọc; không biến vật thể chưa xác nhận thành biển. |
| GTS24 | Bất đồng về count/family | Tách từng mặt biển; chọn một family cụ thể nhất hoặc `unknown`, không chọn `regulatory` thay cho nhóm cụ thể hơn. |

## 10. Lỗi thường gặp

- Chỉ gán nhãn biển Đức hoặc bỏ biển giao thông của quốc gia khác dù nó hướng tới người đi đường.
- Cố suy luận biển áp dụng cho làn/xe nào; thuộc tính đó không có trong schema.
- Gộp nhiều mặt biển trên một cột thành một object hoặc gộp cột vào box.
- Bỏ sót biển xa có thể xác nhận, hoặc tạo box cho vật thể nền chỉ giống biển.
- Đoán phần bị che, family hoặc mã; dùng `unknown` khi đủ bằng chứng về biển nhưng thiếu bằng chứng về loại/mã.
- Dùng mã không thống nhất (`V205`, `__vorschriftzeichen_205__`, `Zeichen 205`); chuẩn hóa theo `Zeichen <mã>`.
- Để `__undefined__` hoặc giá trị rỗng trong export hoàn tất.
- Chỉ ghi escalation trong trao đổi miệng thay vì `review_status=escalate` hoặc `image_status.status=uncertain`.
