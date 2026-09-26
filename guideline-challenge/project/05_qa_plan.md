# QA plan + quality gates

## Flow

Guideline → Calibration → Annotation → Self-QC → Review → Rework → Quality Gate.

- **Ai review, review bao nhiêu:** Lê Hữu Sơn là QA owner. Reviewer kiểm 100% object thuộc class `stop`, `no_entry`, `speed_limit`, `warning`, 100% object `unknown`, 100% năm ảnh blind và ít nhất 30% ảnh còn lại nếu mở rộng production. Người tạo annotation không tự duyệt phần của mình khi còn người khác trong nhóm.
- **Chọn sample theo rule nào:** Ưu tiên toàn bộ sample có tag `critical`, `edge`, `ambiguity`, `small_far`; sau đó review ngẫu nhiên tối thiểu 30% sample normal. Nếu một annotator mới có lỗi major/critical, tăng lên review 100% phần còn lại của annotator đó.
- **Issue được ghi ở đâu, đóng thế nào:** Bất đồng calibration ghi trong `06_calibration_report.csv`; case lặp lại ghi trong `04_edge_cases/edge_case_cards.md`; lỗi blind ghi ở `07_blind_handoff/transfer_score.csv` và `peer_feedback.md`. Issue chỉ đóng khi annotation đã sửa, reviewer kiểm lại trên CVAT/export và note có bằng chứng sample_id + rule áp dụng.
- **Khi phát hiện guideline gap thì update và version ra sao:** Dừng chấm case chịu ảnh hưởng, thêm rule/example/escalation vào `02_guideline.md`, ghi lý do trong `08_revision_log.md`, rồi tăng version. Sau calibration dùng `v2`; sau blind handoff dùng `v3`. Không sửa gold hoặc sample split sau freeze.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Bỏ sót, gán sai hoặc tạo false positive cho `stop`, `no_entry`, `speed_limit`, `warning` rõ ràng; lỗi có thể làm ADAS phản ứng sai về dừng, cấm, tốc độ hoặc nguy hiểm | Bỏ một biển `no_entry`; gán biển quảng cáo thành `speed_limit` | Dừng gate, sửa 100% ảnh cùng rule, reviewer kiểm lại; nếu do guideline gap thì cập nhật guideline trước khi tiếp tục |
| Major | Sai quyết định LABEL/IGNORE/UNKNOWN, sai class `give_way`/`other`, gộp hoặc lặp object, hoặc geometry lệch trên 20%/dính object bên cạnh | Gán tam giác ngược thành `warning`; vẽ hai box trùng một mặt biển; bỏ panel official | Rework object và review 100% sample cùng loại; mở rộng kiểm tra annotator nếu lặp lại |
| Minor | Geometry lệch 10–20% nhưng vẫn đúng một mặt biển và không ảnh hưởng class/count; lỗi trình bày không đổi quyết định downstream | Box thừa một dải nền nhỏ, không dính cột hay biển khác | Sửa trong self-QC hoặc batch rework; reviewer kiểm mẫu |
| Question | Bằng chứng ảnh không đủ hoặc guideline chưa cho một quyết định duy nhất | Đốm nhỏ có thể là biển; panel official nhưng loại không đọc được | Dùng `unknown` chỉ khi chắc chắn là biển; nếu chưa chắc là biển thì IGNORE; ghi edge case nếu lặp lại |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Decision accuracy (D) | Số gold decision không phải geometry có `correct=1` / tổng decision không phải geometry | Đo trực tiếp inclusion, count và class mà ADAS cần |
| Critical decision correctness (C) | Số decision severity `critical` đúng / tổng decision critical | Tách riêng lỗi an toàn cao, không để điểm trung bình che mất |
| Geometry compliance (G) | Số geometry decision đạt tight visible box và tolerance 10% / tổng geometry decision | Kiểm box đủ sát để huấn luyện detector |
| Duplicate-object rate | Số mặt biển bị vẽ nhiều hơn một box / tổng mặt biển được review | Calibration cho thấy box lặp làm sai count dù class đúng |
| Unknown review completion | Số object `unknown` đã được reviewer chốt / tổng object `unknown` | Bảo đảm escalation được xử lý, không trở thành nhãn bỏ quên |

Metric high-risk tách riêng: **critical defect escape rate = số critical decision sai sau review / tổng critical decision**. Mục tiêu bắt buộc là `0%`.

## Quality gate

```text
PASS if:
  critical defect escape rate = 0%;
  Decision accuracy >= 90%;
  Geometry compliance >= 90%;
  duplicate-object rate = 0% trên phần đã review;
  100% object unknown đã được reviewer xử lý.
REWORK if:
  không có critical escape nhưng D hoặc G từ 70% đến dưới 90%,
  hoặc còn duplicate/unknown chưa review.
REJECT / ESCALATE if:
  có ít nhất 1 critical escape,
  hoặc D/G dưới 70%,
  hoặc cùng một guideline gap xuất hiện từ 2 sample trở lên.
```

Trade-off: review 100% mọi object sẽ tốn thời gian không cần thiết cho bài nhỏ. Nhóm tập trung 100% vào class critical, `unknown` và ảnh rủi ro; sample normal được review 30%. Ngưỡng D/G 90% giữ chi phí rework vừa phải, còn critical escape vẫn tuyệt đối bằng 0 vì hậu quả downstream cao.
