# Peer feedback + owner response

**Trạng thái:** bản owner chuẩn bị trước blind handoff. Chưa có peer export, feedback hoặc câu hỏi trong clarification
log; phần peer bên dưới đang chờ nhóm nhận blind pack điền. Các mục ở phần 2 là kết quả calibration nội bộ đã được
đưa vào guideline v2, không phải feedback giả lập từ peer.

- **Nhóm owner:** chưa ghi trong `00_team.md`
- **Nhóm peer:** chờ Lab Coach ghép cặp
- **Người label blind:** chờ peer xác nhận
- **Guideline gửi peer:** v2
- **Peer export nhận được:** chưa nhận

## 1. Peer trả lời — chờ peer điền

Peer trả lời sau khi hoàn thành task blind; ghi sample ID và câu chữ cụ thể. Không dùng các kết quả calibration nội bộ
thay cho phản hồi peer.

1. Rule nào rõ nhất và giúp bạn quyết định nhanh nhất?

	**Trả lời peer:** Chờ phản hồi.

2. Rule nào mơ hồ hoặc khiến bạn phải tự suy diễn? Ghi sample ID và cách bạn đã xử lý.

	**Trả lời peer:** Chờ phản hồi.

3. Sample nào khiến guideline không đủ để quyết định? Điều gì còn thiếu?

	**Trả lời peer:** Chờ phản hồi.

4. Attribute, giá trị mặc định hoặc thao tác CVAT nào dễ gây gán nhãn sai?

	**Trả lời peer:** Chờ phản hồi.

5. Một thay đổi cụ thể nào sẽ giúp annotator mới làm đúng hơn mà ít cần hỏi owner?

	**Trả lời peer:** Chờ phản hồi.

6. Bạn có cần hỏi owner trong lúc làm không? Nếu có, ghi sample ID, câu hỏi nguyên văn và thời điểm để đối chiếu với
	`clarification_log.csv`.

	**Trả lời peer:** Chờ phản hồi. Hiện `clarification_log.csv` chỉ có header, chưa có câu hỏi.

## 2. Owner phân loại — calibration nội bộ đã xử lý

Các hàng sau ghi lại lý do hình thành rule v2. Chúng không khẳng định annotator nào đúng; nếu peer nêu thêm evidence,
owner cần review riêng theo ảnh gốc và export blind.

| Bất đồng calibration | Evidence | Nguyên nhân | Xử lý | Rule đã đưa vào v2 |
|---|---|---|---|---|
| Số `traffic_sign` khác nhau | GTS02: dangvannam=6; dohoangminh=11; nguyenchibang=5; nguyendonquoctuan=0; nguyenviettien=5. `06_calibration_report.csv`, GTS02. | `guideline_gap` | `accept + revise` | Mỗi mặt biển riêng nhìn thấy là một instance; không đếm cột/nền; chỉ tính biển nhỏ/xa khi xác nhận được một mặt biển và đặt được box có ý nghĩa. |
| Số object và visibility khác nhau | GTS15: dangvannam=1; dohoangminh=9; nguyenchibang=1; nguyendonquoctuan=0; nguyenviettien=5. `06_calibration_report.csv`, GTS15. | `guideline_gap` | `accept + revise` | Biển đã xác nhận vẫn được label dù khó đọc; dùng visibility phù hợp. Không biến vật thể chỉ giống biển thành object; candidate chưa xác nhận thì dùng image-level `uncertain`. |
| Số object và family khác nhau | GTS24: counts 3, 5, 1, 0, 4; family values cũng không thống nhất. `06_calibration_report.csv`, GTS24. | `guideline_gap` | `accept + revise` | Tách từng mặt biển; chọn một family cụ thể nhất theo thứ tự trong v2, dùng `unknown` khi bằng chứng thiếu; không dùng `regulatory` thay cho family cụ thể hơn. |

## 3. Owner response and follow-up

- **Feedback peer đã nhận:** chưa có.
- **Feedback peer được chấp nhận / từ chối:** chờ peer feedback và blind export; chưa phân loại.
- **Calibration rule đã sửa:** counting, visible-only geometry, visibility/unknown/escalation, family priority, code format,
  image-status tagging và driver-relevant scope.
- **Guideline hiện tại:** v2.
- **Revision evidence:** `08_revision_log.md`; calibration evidence ở `06_calibration_measure.csv` và
  `06_calibration_report.csv`.
- **Clarification log:** chưa có câu hỏi peer; xem `clarification_log.csv`.
- **Ngày gửi phản hồi lại peer:** chờ hoàn tất blind handoff.
