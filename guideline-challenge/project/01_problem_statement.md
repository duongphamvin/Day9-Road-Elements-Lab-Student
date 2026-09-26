# Problem statement + downstream contract

Tối đa nửa trang, viết **trước khi mở CVAT**. Đây là bằng chứng của gate G1 (topic lock). Thay mọi placeholder
mới là xong.

## Bài toán

Tạo dữ liệu cho mô hình ADAS phát hiện và phân loại từng mặt trước biển báo giao thông nhìn thấy trong ảnh, gồm trường hợp biển nhỏ, xa hoặc bị che một phần.

## Downstream contract

1. **Downstream task / model / user là ai?** Mô hình phát hiện và phân loại biển báo cho hệ thống hỗ trợ lái ADAS, dùng kết quả để nhận biết các biển quan trọng trong cảnh đường phố.
2. **Output annotation nào thực sự cần?** Mỗi mặt trước biển thuộc scope có một bounding box ôm sát phần nhìn thấy của mặt biển, không gồm cột hoặc giá đỡ, và một class trong `stop`, `no_entry`, `speed_limit`, `give_way`, `warning`, `other`, `unknown`.
3. **Failure nào gây hậu quả lớn nhất?** Bỏ sót hoặc phân loại sai biển `stop`, `no_entry`, `speed_limit` hay `warning` nhìn thấy rõ; hoặc gắn nhầm vật không phải biển thành một trong các class quan trọng này.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Nếu chắc chắn là biển giao thông nhưng không đủ thông tin để phân loại, vẫn vẽ box và chọn `unknown`. Nếu không đủ bằng chứng vật thể là biển giao thông, không tạo object. Các trường hợp lặp lại hoặc gây tranh luận được ghi vào `04_edge_cases/edge_case_cards.md` để Gold & QA owner rà soát.

## Scope

- **Trong scope (bắt buộc label):** Mặt trước của biển báo giao thông cố định hoặc tạm thời; biển nhỏ, xa hoặc bị che một phần nếu vẫn đủ bằng chứng là biển; mỗi mặt biển trong một cụm là một object riêng.
- **Ngoài scope (ignore):** Mặt sau biển; cột và giá đỡ; biển quảng cáo, biển tên cửa hàng, đèn giao thông, ký hiệu sơn trên mặt đường; vật quá nhỏ hoặc quá mờ khiến không thể xác nhận là biển giao thông. Không suy luận biển có áp dụng cho xe chụp ảnh hay không.
- **Geometry tolerance:** Box ôm sát phần nhìn thấy của mặt biển và viền thuộc mặt biển, không gồm cột, giá đỡ, nền hoặc biển bên cạnh. Không suy đoán phần bị che. Sai lệch mỗi cạnh không quá 10% chiều rộng hoặc chiều cao tương ứng của phần mặt biển nhìn thấy.

## Output chấm được

Blind test chấm các quyết định `LABEL`, `IGNORE`, `UNKNOWN`, class và geometry. `LABEL` được thể hiện bằng một bounding box cùng class cụ thể; `IGNORE` bằng việc không tạo object cho vật ngoài scope; `UNKNOWN` bằng bounding box có class `unknown`; geometry được so với quy tắc và tolerance ở trên. Trường hợp cần rà soát được ghi thành edge-case card, không dùng một quyết định ẩn chỉ trao đổi bằng miệng.

## Dữ liệu và giới hạn

Chỉ dùng ảnh trong `data/gtsdb` và `data/bdd100k`; dự kiến chọn 3–5 ảnh `example`, 5–8 ảnh `calibration` và 4–5 ảnh `blind`. Hai nguồn dùng hệ ký hiệu biển khác nhau, nên taxonomy dựa trên ý nghĩa phổ biến thay vì mã biển riêng của từng quốc gia. Ảnh tĩnh không đủ để kết luận chắc chắn biển áp dụng cho làn hoặc hướng di chuyển nào, nên ego relevance nằm ngoài scope.
