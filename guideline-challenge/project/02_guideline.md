# Annotation guideline — Phát hiện và phân loại mặt trước biển báo giao thông

**Version:** v2

## 1. Objective + scope

Tạo dữ liệu cho mô hình ADAS phát hiện và phân loại biển báo trong ảnh đường phố. Label từng **mặt trước của biển báo giao thông** nhìn thấy đủ để xác nhận đó là biển, kể cả biển nhỏ, xa, bị che một phần hoặc nằm sát mép ảnh.

Không xác định biển có áp dụng cho xe chụp ảnh hay không. Không label mặt sau biển, cột, giá đỡ, đèn giao thông, biển quảng cáo, biển tên cửa hàng, biển tên đường hoặc ký hiệu sơn trên mặt đường.

## 2. Annotation unit

- Task gồm các ảnh tĩnh độc lập; dùng **Shape**, không dùng Track.
- Một mặt biển là một instance và chỉ có **một box**. Box lặp trên cùng mặt biển là lỗi.
- Nhiều mặt biển trên cùng cột hoặc trong cùng cụm phải tạo box riêng.
- Biển chính và mỗi panel phụ official là các instance riêng nếu chúng có biên mặt biển tách biệt.
- Hai biển giống nhau ở hai vị trí khác nhau là hai instance.
- Hình phản chiếu hoặc hình biển xuất hiện trong quảng cáo, màn hình hay ảnh in không tạo instance mới.

## 3. Geometry rule

- Dùng rectangle axis-aligned, ôm sát **phần nhìn thấy** của mặt biển và viền thuộc mặt biển.
- Không gồm cột, giá đỡ, nền, bóng đổ hoặc biển bên cạnh.
- Biển nghiêng vẫn dùng rectangle nhỏ nhất bao trọn phần mặt biển nhìn thấy.
- Khi biển bị che hoặc bị cắt ở mép ảnh, chỉ box phần nhìn thấy; không suy đoán phần khuất hoặc phần nằm ngoài ảnh.
- Box phải bao hết phần mặt biển nhìn thấy, không cắt mất một cạnh; đồng thời không mở rộng sang cột hoặc nền.
- Sai lệch chấp nhận cho mỗi cạnh không quá 10% chiều rộng hoặc chiều cao tương ứng của phần mặt biển nhìn thấy.

## 4. Taxonomy

Mỗi box dùng đúng một class:

| Class | Dùng khi |
|---|---|
| `stop` | Biển STOP hình bát giác yêu cầu dừng |
| `no_entry` | Biển cấm đi vào: hình tròn đỏ có vạch trắng ngang |
| `speed_limit` | Biển giới hạn tốc độ có số tốc độ trong vòng tròn |
| `give_way` | Biển nhường đường: tam giác **ngược**, đỉnh hướng xuống, viền đỏ và tâm sáng |
| `warning` | Biển cảnh báo nguy hiểm: thường là tam giác **đứng**, đỉnh hướng lên, viền đỏ; không gồm `give_way` |
| `other` | Chắc chắn là biển giao thông official nhưng không thuộc năm class trên: biển tròn xanh bắt buộc hướng đi, biển cấm vượt, biển chỉ dẫn/thông tin, biển người đi bộ, đỗ xe, chevron hoặc panel phụ |
| `unknown` | Chắc chắn là một mặt biển giao thông official nhưng hình ảnh không đủ để chọn một class khác |

Quy tắc ưu tiên:

1. Tam giác ngược là `give_way`, không phải `warning`.
2. Tam giác đứng cảnh báo nguy hiểm là `warning`.
3. Biển tròn xanh có mũi tên/hướng đi là `other`, không phải `give_way`, `warning` hay `stop`.
4. Panel thông tin, panel phụ và chevron official có mặt biển riêng dùng `other`, kể cả khi không đọc được chữ.
5. `other` nghĩa là nhận ra loại biển nằm ngoài năm class cụ thể; `unknown` chỉ dùng khi không đủ bằng chứng chọn class.

Không tự tạo class mới.

## 5. Inclusion / exclusion

**Bắt buộc label:**

- Mặt trước biển giao thông cố định hoặc tạm thời.
- Mỗi biển riêng trong một cụm biển.
- Biển nhỏ, xa, mờ, bị che hoặc bị cắt mép nếu vẫn chắc chắn đó là biển giao thông.
- Biển chỉ dẫn, biển bắt buộc, biển người đi bộ, biển đỗ xe, chevron và mỗi panel phụ official có biên riêng; dùng `other` nếu không thuộc class cụ thể.

**Không label:**

- Mặt sau hoặc cạnh bên của biển khi không thấy mặt mang thông tin.
- Cột, giá đỡ và khung treo không thuộc mặt biển.
- Đèn giao thông, biển quảng cáo, logo, biển cửa hàng, biển tên đường và bảng thông tin thương mại.
- Ký hiệu sơn trên mặt đường.
- Hình biển trong phản chiếu, áp phích, màn hình hoặc ảnh in.
- Vật quá nhỏ, quá mờ hoặc bị che đến mức không thể xác nhận là một mặt biển giao thông official.

## 6. Visibility / occlusion

- Không dùng ngưỡng pixel hoặc phần trăm che khuất; annotator không thể áp dụng ổn định bằng mắt.
- Xem ảnh ở kích thước gốc và có thể zoom trước khi quyết định.
- Nếu xác nhận được class cụ thể, label bằng class đó.
- Nếu xác nhận được một mặt biển official nhưng không xác định được class, label `unknown`.
- Nếu chỉ thấy một đốm/hình mờ và không thể xác nhận đó là mặt biển official, không tạo box. Không dùng `unknown` để đánh dấu vật chưa chắc là biển.
- Không đọc được chữ trên panel phụ không tự động thành `unknown`: nếu hình thức và vị trí cho thấy rõ đó là panel official ngoài năm class cụ thể, dùng `other`.
- Với biển bị che hoặc cắt mép, box chỉ phần mặt biển thực sự nhìn thấy.
- Loá, ngược sáng hoặc motion blur dùng cùng quy tắc bằng chứng trên.

## 7. Ambiguity / escalation

Mọi quyết định phải nhìn thấy trong export CVAT:

- **LABEL:** tạo rectangle với một class cụ thể từ `stop` đến `other`.
- **IGNORE:** không tạo object khi vật ngoài scope hoặc không đủ bằng chứng là biển.
- **UNKNOWN:** tạo rectangle class `unknown` khi chắc chắn là biển official nhưng không đủ bằng chứng phân loại.
- **ESCALATE:** trong task này dùng chính class `unknown` để chuyển trường hợp sang reviewer; không dùng trao đổi miệng hay ghi chú ẩn.

Reviewer kiểm tra tất cả object `unknown`. Nếu một kiểu mơ hồ lặp lại, Gold & QA owner ghi nó vào `04_edge_cases/edge_case_cards.md` và nhóm sửa rule ở phiên bản sau.

## 8. Temporal rule

Không áp dụng — task dùng ảnh tĩnh độc lập, không dùng video, track hoặc thuộc tính thay đổi theo frame.

## 9. Examples

Các ảnh dưới đây thuộc split `example` hoặc `calibration`, không dùng trong blind test.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| `GTS18` | Một biển cảnh báo phía trên một biển giới hạn 30 | Hai box riêng: `warning` và `speed_limit` | Mỗi mặt biển là một instance; class cụ thể ưu tiên hơn `other` |
| `GTS20` | Cụm nhiều biển ở giao lộ | Mỗi mặt biển official một box riêng; biển nhường đường là `give_way`, biển còn lại không thuộc class cụ thể là `other` | Không gộp cụm |
| `GTS23` | Hai biển STOP ở hai vị trí cùng một cảnh, kèm biển chỉ dẫn | Hai box `stop` riêng; mỗi mặt biển chỉ dẫn nhìn thấy đủ rõ là một box `other` | Hai vị trí là hai instance |
| `GTS26` | Biển cảnh báo nhỏ và xa | Một box `warning` nếu hình tam giác cảnh báo vẫn nhận ra được | Biển nhỏ/xa vẫn label khi đủ bằng chứng |
| `GTS28` | Bảng treo trên mặt tiền cơ sở kinh doanh, không phải biển giao thông | Không tạo object | Loại biển quảng cáo và biển cửa hàng |
| `GTS01` | Hai cụm giống nhau, mỗi cụm có cảnh báo, giới hạn 50 và biển cấm vượt | Sáu box: mỗi bên một `warning`, một `speed_limit`, một `other`; không box lặp | Mỗi mặt một box; biển cấm vượt nằm trong `other` |
| `GTS02` | Hai biển nhường đường, hai biển tròn xanh chỉ hướng và một biển tròn xanh nhỏ ở giữa | Năm box: hai `give_way` và ba `other`; không box lặp | Tam giác ngược khác warning; biển bắt buộc hướng đi là other |
| `GTS07` | Một đốm tròn rất nhỏ/mờ dưới cầu, không đủ xác nhận là mặt biển official | Không tạo object | Không dùng unknown cho vật chưa chắc là biển |
| `GTS12` | Biển cảnh báo công trường cùng các panel phụ official bên dưới | Một `warning`; mỗi panel phụ có biên riêng là một box `other` | Panel phụ official là instance riêng, không cần đọc được chữ |
| `GTS22` | Panel thông tin, biển nhường đường và chevron trong cùng cụm | Ba box: `other`, `give_way`, `other` | Mỗi mặt official một box; tam giác ngược là give_way |
| `GTS27` | Biển tam giác đứng có biểu tượng bông tuyết | Một box `warning` bao hết phần tam giác nhìn thấy, không gồm cột | Warning rõ không dùng unknown; box không cắt mép biển |

## 10. Common mistakes

- Gộp nhiều biển cùng cột vào một box hoặc vẽ box lặp trên cùng mặt biển: phải có đúng một box cho mỗi mặt.
- Box cả cột/giá đỡ hoặc cắt mất mép mặt biển: box đủ mặt biển nhìn thấy, không lấy nền.
- Đoán phần bị che để vẽ amodal box: chỉ vẽ phần nhìn thấy.
- Gọi tam giác ngược là `warning`: phải dùng `give_way`.
- Gọi biển tròn xanh chỉ hướng là `stop`, `warning` hoặc `give_way`: dùng `other`.
- Dùng `other` khi không rõ class: dùng `unknown` nếu chắc chắn là biển; `other` chỉ dùng khi biết biển nằm ngoài năm class cụ thể.
- Dùng `unknown` cho một đốm chưa chắc là biển: trường hợp này không tạo object.
- Bỏ panel phụ, chevron hoặc biển chỉ vì nhỏ/xa: vẫn label nếu đủ bằng chứng là mặt biển official.
- Label mặt sau, biển quảng cáo hoặc biển cửa hàng: các vật này ngoài scope.
- Tự suy luận biển có áp dụng cho xe chụp ảnh hay không: relevance nằm ngoài task.
- Tự tạo class mới: chỉ dùng bảy class trong mục 4.
