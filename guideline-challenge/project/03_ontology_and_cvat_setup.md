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

- **Phiên bản CVAT** (`make cvat-status`): Chưa thực hiện kiểm tra trên máy dựng task.
- **Tên task calibration** (có version guideline): Dự kiến `NPC-calib-v1`; chưa tạo task.
- **Guide của task đã dán `02_guideline.md`?** Chưa thực hiện; phải dán nguyên guideline `v1` khi tạo task.
- **Nhóm dùng Track hay Shape, vì sao:** Dùng Shape rectangle vì mỗi ảnh độc lập; không dùng Track.

## Setup test

Chưa thực hiện. Sau khi tạo task, một thành viên không tham gia setup phải mở task và xác nhận: vẽ rectangle cho từng mặt biển; chọn đúng một trong bảy class; dùng `unknown` khi chắc chắn là biển nhưng không phân loại được; không tạo object cho vật ngoài scope. Ghi tên người test và chỗ vấp vào đây trước calibration.
