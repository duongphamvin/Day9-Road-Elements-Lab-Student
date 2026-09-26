# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** sudo-lite
- **Người label blind:** sudo-lite annotator

## 1. Peer trả lời

1. Rule nào rõ nhất / giúp quyết định nhanh nhất?
   Rule phân biệt `speed_limit` (hình tròn có số) và `no_entry` (hình tròn đỏ có vạch trắng ngang) cùng `give_way` (tam giác ngược) rất rõ, nhận diện và gán nhãn tức thì không cần phân vân.

2. Rule nào mơ hồ hoặc phải tự suy diễn?
   Biển cấm vượt (hình tròn viền đỏ bên trong có hai xe) dễ nhầm sang `warning` vì có tính chất cảnh báo rủi ro vượt xe; chưa có mô tả nhấn mạnh rằng biển cấm có viền tròn đỏ ngoài `speed_limit`/`no_entry` phải rơi vào `other`. Ngoài ra biển quá nhỏ ở nhánh rẽ đối diện khó xác định có thuộc scope hay không.

3. Sample nào khiến guideline "vỡ"?
   GTS04 (hai biển xếp tầng trên cao tốc: biển cấm vượt xe tải bị phân vân giữa warning và other) và GTS24 (biển cấm vào rất nhỏ ở góc đường xa dễ bị bỏ qua do chú ý vào biển đi thẳng lớn ở tiền cảnh).

4. Attribute / default nào trong CVAT dễ gây thao tác sai?
   Do schema tối giản chỉ dùng 7 class dạng Rectangle và không có attribute nên thao tác vẽ và chọn class rất mượt, không bị lỗi quên chọn attribute.

5. Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?
   Bổ sung thêm hình ảnh/mô tả quy tắc: "Mọi biển cấm hình tròn viền đỏ (ngoài cấm vào và tốc độ) đều là `other`, không được gán `warning`" và lưu ý kiểm tra các nhánh đường phụ ở xa khi tiếp cận giao lộ.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| GTS04: Gán 2 biển cấm vượt xe tải thành `warning` thay vì `other` | guideline_gap | accept + revise (làm rõ trong mục 4 và mục 10: biển cấm viền tròn đỏ ngoài `no_entry`/`speed_limit` thuộc `other`, `warning` chỉ dành cho tam giác đứng) | GTS04 d2 trong `transfer_score.csv` |
| GTS24: Bỏ sót biển `no_entry` nhỏ ở lối rẽ đối diện ngã tư | execution_error | coaching (nhắc nhở annotator phóng to quét toàn cảnh ngã rẽ và các nhánh rẽ đối diện để không bỏ sót biển cấm critical) | GTS24 d2 trong `transfer_score.csv` |
| GTS21: Gold ban đầu chỉ ghi 1 biển chính, peer gắn thêm 4 biển phụ | guideline_gap | accept + revise (ghi nhận peer làm đúng theo mục 5 về biển chỉ hướng/người đi bộ; ghi nhận `gold sai:` và bổ sung ví dụ GTS21 vào guideline v3) | GTS21 d1 trong `transfer_score.csv` |
