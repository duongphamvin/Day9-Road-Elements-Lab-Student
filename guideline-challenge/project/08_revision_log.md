# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v2 | Phân biệt trực quan `give_way`, `warning` và biển bắt buộc hướng đi `other`; cấm box trùng | Ba annotator phân loại và đếm khác nhau trong cùng ảnh | `GTS02`, `06_calibration_report.csv` |
| v2 | Làm rõ `unknown` chỉ dùng khi đã xác nhận đó là mặt biển official | Vật rất nhỏ/mờ bị xử lý thành `unknown` hoặc IGNORE khác nhau | `GTS07`, `06_calibration_report.csv` |
| v2 | Mỗi panel phụ official có biên riêng là một object `other` | Annotator bỏ, gán sai hoặc dùng `unknown` cho panel phụ | `GTS12`, `GTS22`, `06_calibration_report.csv` |
| v2 | Bổ sung ví dụ calibration và nhắc tight box phải bao hết phần mặt biển nhìn thấy | Có box lặp và box cắt mất mép biển | `GTS01`, `GTS27`, `06_calibration_report.csv` |
