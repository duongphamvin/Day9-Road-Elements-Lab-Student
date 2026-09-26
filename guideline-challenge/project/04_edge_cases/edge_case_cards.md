# Edge-case library

Kho nội bộ của nhóm; không gửi cho peer. Quyết định blind tương ứng được khóa trong `gold_decisions.csv`.

---

CASE ID: EC01
Sample: GTS01
Scene: Hai cụm biển giống nhau, mỗi cụm xếp ba mặt nhỏ.
Observation: Annotator dễ bỏ sót hoặc vẽ box lặp khi phóng to và quay lại cùng cụm.
Decision: LABEL
Expected: Sáu object: `warning` x2, `speed_limit` x2, `other` x2; mỗi mặt đúng một tight box.
Rationale: Count sai làm detector học thiếu hoặc học trùng object.
Common mistake: Vẽ chín box do lặp ba mặt ở cụm phải; gán biển cấm vượt thành `no_entry`.
Diversity: small_far / conflict / duplicate

---

CASE ID: EC02
Sample: GTS02
Scene: Hai tam giác ngược, nhiều biển tròn xanh chỉ hướng và một biển nhỏ giữa ảnh.
Observation: Tam giác ngược dễ bị nhầm với tam giác cảnh báo; biển tròn xanh dễ bị gán class không liên quan.
Decision: LABEL
Expected: Hai `give_way` và ba `other`, mỗi mặt đúng một box.
Rationale: Nhầm `give_way` với warning thay đổi semantics downstream.
Common mistake: Gán tam giác ngược thành `warning`, biển tròn xanh thành `stop`, hoặc vẽ box trùng.
Diversity: ambiguity / conflict / critical

---

CASE ID: EC03
Sample: GTS07
Scene: Đường dưới cầu có một đốm tròn rất nhỏ và mờ.
Observation: Không đủ bằng chứng xác nhận đốm đó là mặt biển official.
Decision: IGNORE
Expected: Không tạo object.
Rationale: `unknown` chỉ dành cho object đã xác nhận là biển; dùng nó cho mọi đốm mờ làm tăng false positive.
Common mistake: Vẽ `unknown` chỉ vì hình gần giống vòng tròn.
Diversity: small_far / ambiguity / negative

---

CASE ID: EC04
Sample: GTS12
Scene: Biển cảnh báo công trường với các panel official bên dưới.
Observation: Nội dung panel khó đọc nhưng biên mặt panel và quan hệ với biển chính rõ.
Decision: LABEL
Expected: Một `warning`; mỗi panel official có biên riêng là một `other`.
Rationale: Mỗi mặt biển là một instance dù chữ không đọc được.
Common mistake: Bỏ panel, gán panel thành `stop`, hoặc dùng `unknown` chỉ vì không đọc được chữ.
Diversity: ambiguity / stacked-panels

---

CASE ID: EC05
Sample: GTS22
Scene: Cụm gồm panel thông tin trên, tam giác ngược giữa và chevron dưới.
Observation: Ba mặt có hình dạng, chức năng và biên riêng.
Decision: LABEL
Expected: `other` x2 và `give_way` x1; ba tight box riêng.
Rationale: Tách đúng instance và class giúp detector không gộp cụm.
Common mistake: Gán tam giác ngược thành `warning`, bỏ panel trên hoặc chevron dưới.
Diversity: conflict / stacked-panels / critical

---

CASE ID: EC06
Sample: GTS27
Scene: Biển tam giác cảnh báo bông tuyết trong ảnh ngược sáng; một vật tam giác rất nhỏ ở xa bên trái.
Observation: Biển bông tuyết gần, có viền và biểu tượng rõ; vật xa không đủ bằng chứng.
Decision: LABEL / IGNORE
Expected: Một `warning` cho biển bông tuyết; không box vật xa chưa xác nhận.
Rationale: Phân biệt class rõ với vật mơ hồ, tránh lạm dụng `unknown`.
Common mistake: Gán biển rõ thành `unknown`, cắt mất cạnh phải hoặc lấy cả cột.
Diversity: low_visibility / small_far / geometry

---

CASE ID: EC07
Sample: GTS17
Scene: Hai biển cấm đi vào ở hai phía lối vào; gần đó có biển quảng cáo cửa hàng.
Observation: Hai mặt `no_entry` thuộc scope nhưng bảng quảng cáo không thuộc scope.
Decision: LABEL / IGNORE
Expected: Hai `no_entry`; không label bảng quảng cáo.
Rationale: Bỏ sót `no_entry` là lỗi critical; false positive quảng cáo làm nhiễu model.
Common mistake: Chỉ vẽ một biển hoặc gán bảng quảng cáo thành `other`.
Diversity: critical / conflict / negative-object

---

CASE ID: EC08
Sample: GTS24
Scene: Một biển tròn xanh đi thẳng rõ và một biển `no_entry` rất nhỏ ở xa.
Observation: Hai biển khác kích thước và nằm xa nhau trong ảnh.
Decision: LABEL
Expected: Một `other` cho biển đi thẳng và một `no_entry` cho biển nhỏ; hai box riêng.
Rationale: Không bỏ sót class critical chỉ vì kích thước nhỏ.
Common mistake: Chỉ vẽ biển lớn, hoặc gán biển đi thẳng thành `warning`.
Diversity: small_far / critical / ambiguity

---

CASE ID: EC09
Sample: GTS15
Scene: Biển tròn gạch chéo thể hiện hết một hạn chế cụ thể.
Observation: Đây là biển official nhưng không thuộc năm class cụ thể của taxonomy.
Decision: LABEL
Expected: Một `other`, không dùng `unknown`.
Rationale: `other` dùng khi biết object là loại biển ngoài taxonomy; `unknown` dùng khi thiếu bằng chứng phân loại.
Common mistake: Bỏ qua vì lệnh đã hết hiệu lực hoặc dùng `unknown` vì không biết mã biển Đức.
Diversity: ambiguity / semantics / escalation-boundary

---

CASE ID: EC10
Sample: GTS04
Scene: Hai cặp biển nhỏ đối xứng trên cao tốc; mỗi cặp có giới hạn 120 và cấm vượt xe tải.
Observation: Bốn mặt nhỏ nhưng vẫn nhận ra hình dạng và bố cục.
Decision: LABEL
Expected: `speed_limit` x2 và `other` x2; bốn tight box riêng.
Rationale: `speed_limit` là class critical; biển cấm vượt nằm trong `other` chứ không phải `no_entry`.
Common mistake: Bỏ một phía, gộp hai mặt cùng cột, hoặc gọi biển cấm vượt là `no_entry`.
Diversity: small_far / critical / stacked-panels

---

CASE ID: EC11
Sample: GTS28
Scene: Bảng treo có hình và màu giống một sign trên mặt tiền cơ sở kinh doanh.
Observation: Vị trí và nội dung cho thấy đây là bảng cửa hàng, không phải traffic control device.
Decision: IGNORE
Expected: Không tạo object.
Rationale: Scope loại quảng cáo/cửa hàng để tránh false positive.
Common mistake: Gán `other` vì bảng có viền rõ.
Diversity: negative / ambiguity

---

CASE ID: EC12
Sample: GTS26
Scene: Biển tam giác cảnh báo nhỏ và xa nhưng viền tam giác vẫn nhận ra.
Observation: Class vẫn xác định được dù chi tiết biểu tượng khó đọc.
Decision: LABEL
Expected: Một `warning`; tight box phần mặt biển nhìn thấy.
Rationale: Không cần đọc biểu tượng con khi hình thức đủ xác định class warning.
Common mistake: Bỏ qua hoặc dùng `unknown` chỉ vì không đọc được biểu tượng.
Diversity: small_far / escalation-boundary
