# QA plan + quality gates

Các ngưỡng dưới đây là ngưỡng nội bộ cho bài lab và bộ ảnh nhỏ, không phải chuẩn ngành.

## Flow and review coverage

- **Người review:** owner và một thành viên thứ hai không tạo gold cho decision đang review. Cả hai cùng xem ảnh gốc
  và peer export; bất đồng giữa reviewer được ghi thành câu hỏi/escalation, không tự chọn theo đa số.
- **Coverage:** review 100% cả 5 ảnh blind, tất cả `traffic_sign` instance, trạng thái ảnh, family/code/visibility,
  review status và toàn bộ geometry decision. Không lấy mẫu ngẫu nhiên phụ: với chỉ 5 ảnh, bỏ sót một lỗi sẽ làm metric
  mất ý nghĩa.
- **Ưu tiên:** kiểm tra trước ảnh `critical` và `ambiguity`, sau đó `edge`, rồi xác nhận cả `normal` case. Quét toàn
  ảnh ở kích thước gốc, kể cả object nhỏ/xa và các vùng ảnh không có box.
- **Issue log:** decision outcome và `correct`/`note` nằm trong `07_blind_handoff/transfer_score.csv`; câu hỏi peer
  nguyên văn nằm trong `07_blind_handoff/clarification_log.csv`. Calibration nội bộ vẫn ở `06_calibration_report.csv`.
- **Đóng issue:** ghi bằng chứng (sample ID, object/attribute, ảnh gốc hoặc export), nguyên nhân, action và người review.
  Chỉ đóng khi rule được áp dụng nhất quán hoặc đã ghi rõ `unknown`/`escalate`; sau đó cập nhật score/note.
- **Guideline gap:** ghi rule thay đổi và evidence trong `08_revision_log.md`; tăng version v3 sau blind handoff. Không
  sửa gold sau freeze; nếu phát hiện gold sai, ghi rõ `gold sai:` trong score note để debrief.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Lỗi làm bỏ sót hoặc tạo giả một biển có ảnh hưởng đến quy tắc/cảnh báo/chỉ dẫn lái xe, hoặc khiến trạng thái có biển/không biển sai. | Bỏ sót biển cấm/ưu tiên đã được gold xác nhận; ảnh `no_sign` khi có biển thuộc scope. | Reject kết quả liên quan; rà soát lại toàn bộ 5 ảnh và escalate ngay. |
| Major | Instance có thật nhưng count, box vượt tolerance, family, identification hoặc visibility sai theo gold, có khả năng làm hỏng detection/taxonomy. | Gộp hai mặt biển thành một box; family sai; không đánh dấu biển đã xác nhận cần review. | Rework annotation bị ảnh hưởng; kiểm tra lại cùng loại quyết định trên cả 5 ảnh. |
| Minor | Lỗi hình học nhỏ hoặc metadata phụ không đổi việc xác định instance/nhóm biển, nhưng vượt ngưỡng đã thống nhất. | Một cạnh box lệch quá `max(5 px, 10% of corresponding face dimension)` nhưng biển vẫn định vị đúng. | Sửa box/attribute và ghi lại; không reject toàn bộ nếu không có lỗi khác. |
| Question | Rule hoặc pixel evidence không cho phép một quyết định ổn định; chưa thể gán lỗi cho annotator. | Hai cách hợp lý để phân nhóm biển bị che, hoặc không rõ candidate có phải biển thuộc scope hay không. | Ghi clarification/escalation; bổ sung rule hoặc ví dụ trước lần handoff tiếp theo. |

## Metrics

| Metric | Cách tính | Vì sao phù hợp |
|---|---|---|
| Image-status accuracy | Ảnh có `image_status` khớp gold / tổng 5 ảnh. | Bắt lỗi bỏ sót toàn ảnh hoặc báo có biển khi không có. |
| Exact sign-count accuracy | Ảnh có đúng số instance gold / tổng 5 ảnh. | Bắt trực tiếp lỗi đếm thiếu, đếm thừa hoặc gộp mặt biển. |
| Non-geometry decision accuracy | Decision đúng / tổng decision không phải geometry. | Đo family, code, visibility, identification và review status theo gold. |
| Geometry pass rate | Box đạt ngưỡng từng cạnh / tổng geometry decision. | Đo vị trí box theo tiêu chí đã công bố, không dùng cảm giác chung. |
| Critical escape rate | Critical decision sai / tổng critical decision. | Tách riêng lỗi rủi ro cao; mọi critical miss cần xử lý dù tỷ lệ tổng tốt. |

## Quality gate

```text
PASS if:
  critical escape rate = 0;
  image_status và exact sign count đúng trên cả 5 ảnh;
  non-geometry decision accuracy >= 90%;
  geometry pass rate = 100%;
  không còn câu hỏi domain chưa được trả lời bằng rule hoặc escalation.
REWORK if:
  không có critical error nhưng một metric PASS không đạt, hoặc có lỗi major/minor còn sửa được.
REJECT / ESCALATE if:
  có bất kỳ critical error nào; export thiếu/không đọc được; reviewer không thống nhất được gold/evidence;
  hoặc không thể xác định đúng trạng thái ảnh hay số instance của một ảnh blind.
```

**Trade-off:** Blind set chỉ có 5 ảnh nên review toàn bộ tốn ít hơn rủi ro bỏ sót một case quan trọng. Ngưỡng 100% cho
critical, count và geometry bảo vệ các quyết định chính; 90% cho decision còn lại cho phép một lỗi metadata đơn lẻ
không nghiêm trọng kích hoạt rework thay vì reject. Kết quả không đại diện cho chất lượng trên dữ liệu triển khai.
