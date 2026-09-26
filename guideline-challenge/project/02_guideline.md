# Annotation guideline — Phát hiện và phân loại mặt trước biển báo giao thông

**Version:** v1

## 1. Objective + scope

Tạo dữ liệu cho mô hình ADAS phát hiện và phân loại biển báo trong ảnh đường phố. Label từng **mặt trước của biển báo giao thông** nhìn thấy đủ để xác nhận đó là biển, kể cả biển nhỏ, xa, bị che một phần hoặc nằm sát mép ảnh.

Không xác định biển có áp dụng cho xe chụp ảnh hay không. Không label mặt sau biển, cột, giá đỡ, đèn giao thông, biển quảng cáo, biển tên cửa hàng, biển tên đường hoặc ký hiệu sơn trên mặt đường.

## 2. Annotation unit

- Task gồm các ảnh tĩnh độc lập; dùng **Shape**, không dùng Track.
- Một mặt biển là một instance. Nhiều mặt biển trên cùng cột hoặc trong cùng cụm phải tạo box riêng.
- Biển chính và biển phụ là hai instance nếu chúng có hai mặt biển tách biệt.
- Hai biển giống nhau ở hai vị trí khác nhau là hai instance.
- Hình phản chiếu hoặc hình biển xuất hiện trong quảng cáo, màn hình hay ảnh in không tạo instance mới.

## 3. Geometry rule

- Dùng rectangle axis-aligned, ôm sát **phần nhìn thấy** của mặt biển và viền thuộc mặt biển.
- Không gồm cột, giá đỡ, nền, bóng đổ hoặc biển bên cạnh.
- Biển nghiêng vẫn dùng rectangle nhỏ nhất bao trọn phần mặt biển nhìn thấy.
- Khi biển bị che hoặc bị cắt ở mép ảnh, chỉ box phần nhìn thấy; không suy đoán phần khuất hoặc phần nằm ngoài ảnh.
- Sai lệch chấp nhận cho mỗi cạnh không quá 10% chiều rộng hoặc chiều cao tương ứng của phần mặt biển nhìn thấy.

## 4. Taxonomy

Mỗi box dùng đúng một class:

| Class | Dùng khi |
|---|---|
| `stop` | Biển STOP hình bát giác yêu cầu dừng |
| `no_entry` | Biển cấm đi vào: hình tròn đỏ có vạch trắng ngang |
| `speed_limit` | Biển giới hạn tốc độ có số tốc độ trong vòng tròn |
| `give_way` | Biển nhường đường hình tam giác ngược |
| `warning` | Biển cảnh báo nguy hiểm, thường là tam giác viền đỏ; không gồm `give_way` |
| `other` | Chắc chắn là biển giao thông chính thức nhưng không thuộc năm class trên, ví dụ biển bắt buộc hướng đi, chỉ dẫn, người đi bộ, đỗ xe hoặc biển phụ |
| `unknown` | Chắc chắn là biển giao thông nhưng hình ảnh không đủ để chọn một class khác |

Không tự tạo class mới. `other` nghĩa là nhận ra biển nằm ngoài năm loại cụ thể; `unknown` nghĩa là thiếu bằng chứng để phân loại.

## 5. Inclusion / exclusion

**Bắt buộc label:**

- Mặt trước biển giao thông cố định hoặc tạm thời.
- Mỗi biển riêng trong một cụm biển.
- Biển nhỏ, xa, mờ, bị che hoặc bị cắt mép nếu vẫn chắc chắn đó là biển giao thông.
- Biển chỉ dẫn, biển bắt buộc, biển người đi bộ, biển đỗ xe và biển phụ; dùng `other` nếu không thuộc class cụ thể.

**Không label:**

- Mặt sau hoặc cạnh bên của biển khi không thấy mặt mang thông tin.
- Cột, giá đỡ và khung treo không thuộc mặt biển.
- Đèn giao thông, biển quảng cáo, logo, biển cửa hàng, biển tên đường và bảng thông tin thương mại.
- Ký hiệu sơn trên mặt đường.
- Hình biển trong phản chiếu, áp phích, màn hình hoặc ảnh in.
- Vật quá nhỏ, quá mờ hoặc bị che đến mức không thể xác nhận là biển giao thông.

## 6. Visibility / occlusion

- Không dùng ngưỡng phần trăm che khuất; annotator không thể đo ổn định bằng mắt.
- Nếu vẫn xác định được class cụ thể, label bằng class đó.
- Nếu chắc chắn là biển nhưng không xác định được class, label `unknown`.
- Nếu không chắc vật thể có phải biển giao thông, không tạo box.
- Với biển bị che hoặc cắt mép, box chỉ phần mặt biển thực sự nhìn thấy.
- Với biển nhỏ/xa, xem ảnh ở kích thước gốc và có thể zoom để quyết định; không đoán nội dung không nhìn thấy.
- Loá, ngược sáng hoặc motion blur dùng cùng quy tắc bằng chứng trên.

## 7. Ambiguity / escalation

Mọi quyết định phải nhìn thấy trong export CVAT:

- **LABEL:** tạo rectangle với một class cụ thể từ `stop` đến `other`.
- **IGNORE:** không tạo object khi vật ngoài scope hoặc không đủ bằng chứng là biển.
- **UNKNOWN:** tạo rectangle class `unknown` khi chắc chắn là biển nhưng không đủ bằng chứng phân loại.
- **ESCALATE:** trong task này dùng chính class `unknown` để chuyển trường hợp sang reviewer; không dùng trao đổi miệng hay ghi chú ẩn.

Reviewer kiểm tra tất cả object `unknown`. Nếu một kiểu mơ hồ lặp lại, Gold & QA owner ghi nó vào `04_edge_cases/edge_case_cards.md` và nhóm sửa rule ở phiên bản sau.

## 8. Temporal rule

Không áp dụng — task dùng ảnh tĩnh độc lập, không dùng video, track hoặc thuộc tính thay đổi theo frame.

## 9. Examples

Các ảnh dưới đây được dành cho split `example`, không dùng trong blind test.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| `GTS18` | Một biển cảnh báo phía trên một biển giới hạn 30 | Hai box riêng: `warning` và `speed_limit` | Mỗi mặt biển là một instance; class cụ thể ưu tiên hơn `other` |
| `GTS20` | Cụm nhiều biển ở giao lộ | Mỗi mặt biển official một box riêng; biển nhường đường là `give_way`, biển còn lại không thuộc class cụ thể là `other` | Không gộp cụm; `other` cho biển official ngoài taxonomy cụ thể |
| `GTS23` | Hai biển STOP ở hai vị trí cùng một cảnh, kèm biển chỉ dẫn | Hai box `stop` riêng; mỗi mặt biển chỉ dẫn nhìn thấy đủ rõ là một box `other` | Hai vị trí là hai instance; không bỏ biển vì hướng đặt khác nhau |
| `GTS26` | Biển cảnh báo nhỏ và xa | Một box `warning` nếu hình tam giác cảnh báo vẫn nhận ra được | Biển nhỏ/xa vẫn label khi đủ bằng chứng |
| `GTS28` | Bảng treo trên mặt tiền cơ sở kinh doanh, không phải biển giao thông | Không tạo object | Loại biển quảng cáo và biển cửa hàng |

## 10. Common mistakes

- Gộp nhiều biển cùng cột vào một box: phải tách mỗi mặt biển.
- Box cả cột hoặc giá đỡ: chỉ box mặt biển và viền thuộc mặt biển.
- Đoán phần bị che để vẽ amodal box: chỉ vẽ phần nhìn thấy.
- Dùng `other` khi không nhìn rõ loại: dùng `unknown`; `other` chỉ dùng khi biết đó là biển official ngoài năm class cụ thể.
- Bỏ qua biển chỉ vì nhỏ hoặc xa: vẫn label nếu đủ bằng chứng là biển.
- Label mặt sau, biển quảng cáo hoặc biển cửa hàng: các vật này ngoài scope.
- Tự suy luận biển có áp dụng cho xe chụp ảnh hay không: relevance nằm ngoài task.
- Tự tạo class mới: chỉ dùng bảy class trong mục 4.
