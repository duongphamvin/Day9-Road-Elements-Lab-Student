# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `stop` | rectangle | class | không áp dụng | không áp dụng | không | Biển STOP là output an toàn riêng mà mô hình downstream cần phân loại trực tiếp. |
| `no_entry` | rectangle | class | không áp dụng | không áp dụng | không | Biển cấm đi vào là output an toàn riêng mà mô hình downstream cần phân loại trực tiếp. |
| `speed_limit` | rectangle | class | không áp dụng | không áp dụng | không | Biển giới hạn tốc độ là output an toàn riêng; bài này không tách theo từng con số tốc độ. |
| `give_way` | rectangle | class | không áp dụng | không áp dụng | không | Biển nhường đường là output an toàn riêng mà mô hình downstream cần phân loại trực tiếp. |
| `warning` | rectangle | class | không áp dụng | không áp dụng | không | Gom các biển cảnh báo nguy hiểm không thuộc class cụ thể khác trong bài. |
| `other` | rectangle | class | không áp dụng | không áp dụng | không | Biển giao thông official nhận diện được nhưng nằm ngoài năm class cụ thể. |
| `unknown` | rectangle | class | không áp dụng | không áp dụng | không | Cơ chế escalation nhìn thấy trong export: chắc chắn là biển nhưng thiếu bằng chứng để phân loại. |

## Class hay attribute

Bảy giá trị đều là class vì chúng là quyết định phân loại trực tiếp của mô hình downstream và mỗi object chỉ nhận đúng một giá trị. Không dùng attribute: task ảnh tĩnh không có trạng thái thay đổi theo frame, và thêm thuộc tính visibility/certainty sẽ lặp lại quyết định đã được biểu diễn bằng `unknown`.

- `LABEL`: vẽ rectangle bằng một trong sáu class xác định từ `stop` đến `other`.
- `UNKNOWN` / `ESCALATE`: vẽ rectangle class `unknown`; reviewer rà soát mọi object này.
- `IGNORE`: không tạo object cho vật ngoài scope hoặc vật không đủ bằng chứng là biển giao thông.

Không dùng `unknown` cho ảnh negative hoặc vật ngoài scope.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): `2.74.1`, chạy tại `http://localhost:8080`.
- **Task calibration:** Ba thành viên dùng task calibration riêng cho guideline `v1`. Các export `duong.zip`, `loc.zip`, `son.zip` đều chứa đúng 7 sample calibration; định dạng export không lưu tên hoặc ID task nên repo không có ID để ghi lại.
- **Guide của task đã dán `02_guideline.md`?** Có — cả ba lượt calibration dùng cùng guideline `v1`. Sau khi phân tích bất đồng, bản trong repo đã được nâng lên `v2`.
- **Nhóm dùng Track hay Shape, vì sao:** Dùng Shape rectangle vì mỗi ảnh độc lập; không dùng Track.

## Setup test

Tạ Quang Lộc và Lê Hữu Sơn mở task độc lập với người chuẩn bị spec, import cùng `03_cvat_labels.json`, xem đủ 7 class rectangle và hoàn thành đủ 7 ảnh. Ba export được `lab9.py calib` đọc thành công với cùng bộ sample `GTS01`, `GTS02`, `GTS07`, `GTS12`, `GTS19`, `GTS22`, `GTS27`; vì vậy setup ảnh, schema và export format hoạt động.

Chỗ người dùng vấp không nằm ở thao tác CVAT mà ở semantics: `give_way` bị nhầm với `warning`, biển bắt buộc hướng đi bị nhầm class, panel phụ bị bỏ hoặc gán sai, và `unknown` bị dùng cho vật chưa chắc là biển. Các điểm này đã được ghi trong `06_calibration_report.csv` và sửa trong guideline `v2`.
