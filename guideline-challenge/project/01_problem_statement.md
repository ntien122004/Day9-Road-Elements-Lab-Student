# Problem statement + downstream contract

## Bài toán

Gán box và taxonomy cho biển báo hướng tới người tham gia giao thông và có thể truyền đạt quy tắc, cảnh báo, quyền ưu
tiên, chỉ dẫn hoặc thông tin sử dụng đường; tập trung vào biển nhỏ/xa, bị che, khó phân nhóm hoặc khó xác định mã.
Dữ liệu gồm GTSDB và BDD100K.

## Downstream contract

1. **Downstream task / model / user:** bộ dữ liệu thử nghiệm cho nhóm phát triển và QA mô hình phát hiện, phân nhóm
	biển báo dành cho người lái; peer annotator là người dùng guideline trong blind test.
2. **Output cần:** một rectangle cho mỗi mặt biển; `sign_family`, `sign_code`, `visibility`, `identification` và
	`review_status`; thêm tag ảnh `image_status`.
3. **Failure nghiêm trọng nhất:** bỏ sót biển có thật hoặc tạo biển giả làm sai nhãn huấn luyện; phân loại/mã sai
	cũng làm hỏng nhãn taxonomy. Không đoán khi ảnh không đủ bằng chứng.
4. **Escalation:** đặt `review_status=escalate` cho object cần phân xử; nếu chưa xác nhận được ảnh có biển hay không,
	đặt `image_status=uncertain`. Ghi câu hỏi và bằng chứng vào calibration report để nhóm review.

## Scope

- **Trong scope:** từng mặt biển hướng tới người tham gia giao thông, có thể ảnh hưởng đến việc lái xe bằng cách
	truyền đạt quy tắc, cảnh báo, quyền ưu tiên, chỉ dẫn hoặc thông tin đường bộ; gồm cả biển tạm thời và biển phụ trợ
	gắn với biển giao thông. Label khi có thể xác nhận mặt biển, kể cả khi không đọc được mã.
- **Ngoài scope:** quảng cáo, bảng hiệu cửa hàng, chữ trên xe, biển chỉ phục vụ cơ sở tư nhân và không hướng dẫn giao
	thông, mặt sau không có mặt biển nhìn thấy, cột/giá đỡ, và vật thể chỉ giống biển nhưng không đủ bằng chứng. Không
	suy đoán một biển áp dụng cho làn/xe nào: schema hiện không có thuộc tính lane applicability.
- **Geometry tolerance:** rectangle ôm phần mặt biển nhìn thấy, không gồm cột/giá đỡ; ngưỡng QA đề xuất là sai lệch
	mỗi cạnh không quá `max(5 px, 10% of corresponding face dimension)` trên ảnh gốc. Đây là ngưỡng của nhóm cho bài
	lab, không phải chuẩn ngành.

## Output chấm được

Blind export phải cho phép chấm: presence/count (`traffic_sign` boxes), geometry, family/code/visibility/identification,
`review_status`, và ảnh-level `image_status` để phân biệt `contains_sign`, `no_sign`, `uncertain`. `unknown` được
chấm như giá trị tường minh; `escalate` phải thể hiện bằng attribute/tag trong export, không chỉ ghi chú miệng.

## Dữ liệu và giới hạn

Sample pack hiện có 18 ảnh: 11 GTSDB và 7 BDD100K; 5 ảnh blind đều là BDD100K và không trùng sample ID với
example/calibration. Bộ nhỏ; GTSDB chủ yếu có biển Đức, BDD100K cung cấp các cảnh đường bộ đa dạng hơn. Kết quả
không đại diện cho mọi quốc gia, điều kiện đường hoặc hiệu năng triển khai.
