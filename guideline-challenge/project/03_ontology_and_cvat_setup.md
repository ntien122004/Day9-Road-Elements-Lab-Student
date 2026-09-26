# ONTOLOGY VÀ CẤU HÌNH CVAT

**Version:** v2

Ontology này khớp với `03_cvat_labels.json`. Task dùng ảnh tĩnh GTSDB/BDD100K, Shape mode và không dùng Track. Mọi attribute đều không mutable.

## 1. Classes và attributes

| Name | Geometry | Type | Allowed values | Default trong CVAT | Mutable? | Lý do |
|---|---|---|---|---|---|---|
| `traffic_sign` | rectangle | class | — | — | No | Mỗi mặt biển hướng tới người đi đường là một instance. |
| `sign_family` | — | attribute của `traffic_sign` | warning, regulatory, prohibitory, mandatory, priority, information, direction, temporary, supplementary, other, unknown | `__undefined__` | No | Phân nhóm biển; chọn `unknown` khi đã xác nhận biển nhưng thiếu bằng chứng phân loại. |
| `sign_code` | — | attribute của `traffic_sign` | text; mã GTSDB theo `Zeichen <mã>` hoặc `unknown` | `__undefined__` | No | Ghi mã biển khi có bằng chứng; không đoán. Biển không có mã tương ứng dùng `unknown`. |
| `visibility` | — | attribute của `traffic_sign` | full, partial, low, severely_occluded | `__undefined__` | No | Mức độ nhìn thấy mặt biển trong ảnh tĩnh. |
| `identification` | — | attribute của `traffic_sign` | known, unknown | `__undefined__` | No | Tách việc xác nhận object là biển khỏi việc nhận dạng loại/mã. |
| `review_status` | — | attribute của `traffic_sign` | normal, escalate | `__undefined__` | No | Ghi nhận object cần người review; không thay thế giá trị unknown. |
| `image_status` | tag | attribute `status` cấp ảnh | contains_sign, no_sign, uncertain | `__undefined__` | No | Trạng thái cả ảnh; dùng tag thay vì box giả khi ảnh không có biển hoặc candidate chưa xác nhận. |

`__undefined__` là trạng thái chưa chọn, không phải đáp án. Annotator phải chọn giá trị có chủ đích cho các attribute cần thiết và không để `__undefined__` trong export hoàn tất.

## 2. LABEL / IGNORE / UNKNOWN / ESCALATE

- **LABEL:** tạo rectangle `traffic_sign` cho từng mặt biển trong scope đủ bằng chứng để xác nhận và định vị.
- **IGNORE:** không tạo box cho quảng cáo, logo, road marking, sticker, biển cửa hàng hoặc vật thể không đủ bằng chứng là biển trong scope.
- **UNKNOWN:** với biển đã xác nhận nhưng không nhận dạng được, dùng `sign_family=unknown`, `identification=unknown` và/hoặc `sign_code=unknown` tùy thông tin còn thiếu.
- **ESCALATE:** object đã xác nhận cần phân xử thì đặt `review_status=escalate`; candidate cấp ảnh chưa thể xác nhận thì gắn tag `image_status`, chọn `status=uncertain`.

## 3. CVAT đã xác minh

- **CVAT local:** 2.75.1 tại `http://localhost:8080`.
- **Project:** `Day9 - GTS traffic sign`, project ID 6.
- **Task GTSDB cũ:** `gtsdb-v1`, task ID 29; job annotation ID 30; owner `bancie`; 28 ảnh GTSDB; Shape mode. Đây là task cũ, không phải task calibration mới của sample pack.
- **Guide cũ:** project Guide ID 2 từng dùng guideline v1. Task GTSDB cũ dùng schema trước sample pack; không dùng task cũ cho blind handoff.
- **Task calibration:** exports đã có từ năm annotator nhưng task names/IDs riêng không được lưu trong ZIP metadata đã kiểm tra.
- **Task blind/final:** chưa tạo; cần gold freeze và handoff trước.

## 4. Kiểm tra export

Export CVAT cần giữ label, tọa độ rectangle, attributes `sign_family`, `sign_code`, `visibility`, `identification`, `review_status` và tag ảnh `image_status.status`. Nếu quyết định không thể đọc lại từ export thì không dùng làm tiêu chí chấm.
