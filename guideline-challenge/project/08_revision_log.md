# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v2 | Bổ sung quy tắc đếm mỗi mặt biển riêng, box phần nhìn thấy, ngưỡng geometry QA, scope biển hướng tới người đi đường, thứ tự chọn sign_family, chuẩn sign_code, thao tác image_status tag và đường escalation; thêm ví dụ GTS02/GTS15/GTS24. | Calibration cho thấy bất đồng về số object, visibility, family và code; scope được làm rõ theo downstream task. Quy định evidence/unknown/escalate thay vì ép consensus hoặc mặc định lỗi annotator. | `06_calibration_report.csv`: GTS02, GTS15, GTS24; `06_calibration_measure.csv`; `01_problem_statement.md`; `03_cvat_labels.json` |
