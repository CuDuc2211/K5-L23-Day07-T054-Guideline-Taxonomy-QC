# Guideline gán nhãn — mã 112

- **Phiên bản** v0.1, bản đề xuất cá nhân Pha 1
- **Ngày** 2026-11-16
- **Người viết** Nguyễn Hàn Trường Giang
- **Loại nhãn** Bounding box 2D trên ảnh đường phố
- **Công cụ** CVAT, xuất COCO 1.0

## 1. Mục tiêu

Bộ nhãn dùng để huấn luyện và đánh giá mô hình phát hiện người và phương tiện trong ảnh đường phố. Bỏ sót người đi bộ hoặc người đi xe máy có thể làm hệ thống cảnh báo giao thông phản ứng muộn. Gán nhãn cho hình in, tượng hoặc ảnh phản chiếu có thể gây cảnh báo giả. Box quá rộng hoặc gộp người với xe làm mô hình học sai vị trí và kích thước của từng đối tượng.

## 2. Lớp và thuộc tính

### Lớp

| Lớp | Định nghĩa | Ví dụ | Phản ví dụ hoặc ca dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con có thân xe chủ yếu thiết kế để chở người. | Sedan, hatchback, SUV, taxi dùng thân sedan. | Xe bán tải có thùng hàng riêng là `truck`. Xe thân hộp có khoang sau liền cabin, kiểu xe van, là `van`. |
| `truck` | Xe tải có kết cấu chuyên chở hàng, nhận biết qua thùng hàng riêng sau cabin hoặc khung gầm và đầu kéo kiểu xe tải. | Xe bán tải, xe tải thùng kín, đầu kéo kéo rơ-moóc. | Xe giao hàng thân liền khối kiểu van là `van`, kể cả khi không có cửa kính ở khoang sau. |
| `van` | Xe thân hộp cỡ nhỏ có khoang sau liền thân với cabin, dùng để chở người hoặc hàng. | Van giao hàng, minivan thân hộp. | Xe tải có thùng kín tách khỏi cabin là `truck`. SUV thân xe con là `car`. |
| `motorcycle` | Xe máy hoặc mô tô có kết cấu xe hai bánh, kể cả mô tô gắn thùng bên nếu nhận ra được kết cấu đó. | Xe số, xe tay ga, mô tô gắn sidecar. | Xe đạp và xích lô đạp nằm ngoài taxonomy. Xe cơ giới ba bánh có kết cấu riêng như tuk-tuk là ca cần nhóm chốt. |
| `person` | Người thật xuất hiện trực tiếp trong cảnh và có phần cơ thể nhìn thấy đủ để nhận diện. | Người đi bộ, người ngồi ven đường, người đi xe máy hoặc ngồi sau xe. | Ma-nơ-canh, tượng, người trên poster và hình người trong gương nằm ngoài lớp này. |
| `ignore_region` | Vùng chứa đối tượng thuộc phạm vi gán nhưng quá nhỏ hoặc dày đặc đến mức không thể gán từng box đáng tin cậy. | Cụm người ở xa không tách được từng người, cụm xe máy bị che chồng nhiều lớp. | Người hoặc xe nhìn rõ và tách được thành từng cá thể phải có box riêng. |

Mỗi đối tượng có một lớp. Bỏ qua vật ngoài taxonomy như xe đạp, động vật và xe đẩy tay. Người đi hoặc dắt xe đạp vẫn được xem xét gán `person`. Xe buýt và tuk-tuk cần nhóm chốt cách xử lý trước khi gán diện rộng. Ghi mã ảnh và vị trí của ca vào danh sách review trong thời gian chờ quyết định.

### Thuộc tính

| Thuộc tính | Giá trị cố định | Cách xác định |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Ước lượng tỷ lệ diện tích hình chiếu của đối tượng **trong khung ảnh** bị vật khác che. Mức `0` từ 0% đến dưới 25%, mức `1` từ 25% đến dưới 50%, mức `2` từ 50% đến dưới 75%, mức `3` từ 75% trở lên nếu phần còn thấy vẫn đủ để nhận diện đối tượng. Phần nằm ngoài khung ảnh được xét ở `truncated`. |
| `truncated` | `no`, `yes` | Chọn `yes` khi có dấu hiệu một phần đối tượng nằm ngoài ít nhất một mép ảnh. Chọn `no` khi đối tượng còn nguyên trong ảnh, kể cả khi đường viền vừa chạm mép. |
| `can_review` | `false`, `true` | Chọn `true` khi còn ít nhất một điểm chưa chắc về lớp, ranh giới box, mức che hoặc việc bị cắt mép. Ghi mã ảnh, vị trí và vấn đề cần chốt. Chọn `false` khi các quy tắc hiện tại đủ để quyết định. |

`ignore_region` không có thuộc tính. Với ca chưa đủ căn cứ để vẽ box, ghi mã ảnh và vị trí để trưởng nhóm đưa ra thảo luận.

## 3. Quy tắc vẽ box

1. **Ôm sát phần nhìn thấy.** Đặt bốn cạnh box theo bốn điểm ngoài cùng nhìn thấy của đối tượng. Nếu một vật bị che ở giữa nhưng hai phần lộ ra chắc chắn thuộc cùng một cá thể, dùng một box bao cả hai phần. Khi vật bị cắt mép, dừng box ở biên ảnh và chọn `truncated=yes`. Một vật có thể vừa bị che vừa bị cắt mép.
2. **Tách người và phương tiện.** Vẽ riêng box cho mỗi người nhận diện được và một box cho xe. Box của các cá thể khác nhau được chồng lên nhau khi chúng che nhau. Tính mũ bảo hiểm đang đội và quần áo vào box `person`. Tính bánh xe, gương và thùng gắn cố định vào box phương tiện. Bỏ qua phần hàng hóa, thanh dài hoặc túi rời nhô ra ngoài đối tượng.
3. **Xử lý xe chở xe.** Với đầu kéo và rơ-moóc đang nối, dùng một box `truck` cho phần kết cấu nhìn thấy của cả tổ hợp. Gán box riêng cho từng xe được chở nếu xác định được lớp và tách được cá thể. Cạnh box `truck` dựa vào kết cấu xe chở.
4. **Vật giả, ảnh phản chiếu và xe chủ.** Bỏ qua hình in trên biển quảng cáo hoặc thân xe, tượng, ma-nơ-canh, xe đồ chơi, bóng đổ và hình phản chiếu qua kính, gương hoặc vũng nước. Nếu nhìn thấy cả vật thật và ảnh phản chiếu của nó, chỉ vẽ box quanh vật thật. Bỏ qua các phần của xe đang gắn camera, như nắp ca-pô, mép cửa và gương xe chủ.
5. **Vật nhỏ, nhòe hoặc tối.** Đo chiều cao của box ôm phần nhìn thấy trên ảnh gốc. Dưới 20 px, dùng `ignore_region` cho vùng chứa đối tượng trong phạm vi gán. Từ 20 px trở lên, gán box riêng nếu nhận diện được lớp và xác định được ranh giới. Nếu chỉ còn vệt nhòe hoặc bóng tối không đủ căn cứ để nhận diện, ghi vị trí để review khi cần.
6. **Đám đông và cụm xe.** Gán riêng các cá thể đủ điều kiện, kể cả khi chúng đứng sát nhau. Dùng `ignore_region` ôm sát phần cụm còn lại khi không thể tách từng cá thể. Giữ vùng bỏ qua hẹp nhất có thể để tránh che các đối tượng rõ ở tiền cảnh.

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| `img_002` | Xe tải chở nhiều ô tô trên giàn hai tầng. | Vẽ một box `truck` ôm phần nhìn thấy của cabin và giàn chở. Gán `car` riêng cho từng ô tô xác định được, ví dụ chiếc màu đỏ trên tầng trên. | Giàn là kết cấu của xe chở. Các ô tô trên giàn là những đối tượng riêng, nên box của chúng có thể chồng lên box `truck`. |
| `img_006` | Xe máy bên trái có người lái áo đỏ và người ngồi sau áo vàng chấm đen. Bên phải có người đi xe đạp. | Với cụm bên trái, vẽ hai box `person` và một box `motorcycle`. Với người đi xe đạp bên phải, gán `person` và bỏ qua chiếc xe đạp. | Người lái và người ngồi sau là hai cá thể. Xe máy thuộc taxonomy, còn xe đạp nằm ngoài phạm vi gán. |
| `img_009` | Xe khách màu kem ở giữa ảnh có thân dài và nhiều cửa sổ hành khách. | **Chưa chốt, cần họp.** Ghi mã ảnh và vị trí xe để nhóm quyết định bổ sung lớp `bus` hay đưa loại xe này ra ngoài phạm vi gán. | Taxonomy hiện chưa có lớp xe buýt. Đặc điểm thân xe cũng chưa đủ căn cứ để xếp chắc chắn vào `van` theo định nghĩa hiện tại. |
| `img_014` | Hai người đi bộ ở giữa ảnh rõ nét, xung quanh có các vệt nhòe do phương tiện chuyển động. | Gán hai box `person` riêng. Bỏ qua các vệt nhòe không đủ dấu hiệu để nhận ra lớp hoặc xác định ranh giới. | Đầu, thân và chân của hai người còn phân biệt được. Các vệt nhòe không cho biết bốn điểm ngoài cùng của từng phương tiện. |
| `img_015` | Xe đỏ ở mép trái và xe bạc ở mép phải đều bị cắt một phần thân. Mặt đường ướt có các vệt sáng phản chiếu. | Gán hai box `car` riêng, dừng ở mép ảnh tương ứng. Chọn `truncated=yes` và `occluded_level=0` cho cả hai. Bỏ qua các vệt phản chiếu. | Phần thân thiếu nằm ngoài khung. Phần thân còn trong ảnh không bị vật khác che đáng kể. |
| `img_020` | Gương chiếu hậu và mép cửa xe chủ chiếm phần lớn ảnh. Trong gương có hình phản chiếu của cảnh phía sau. | Bỏ qua gương, mép cửa xe chủ và toàn bộ nội dung phản chiếu trong gương. | Gương và cửa thuộc xe đang gắn camera. Hình trong gương là ảnh phản chiếu. |
| `img_021` | Dòng xe máy dày đặc. Người mặc áo sọc ở gần mép vỉa hè bên phải còn phân biệt được với cụm phía xa. | Gán `person` cho người áo sọc và `motorcycle` cho xe của người đó bằng hai box riêng. Dùng `ignore_region` cho phần cụm phía xa không thể tách từng người và xe. | Ở tiền cảnh còn thấy được phần người và xe thuộc cùng cặp. Ở phía xa, các thân xe và người che chồng qua nhiều lớp. |
| `img_029` | Hai người ở tiền cảnh cùng mang một thanh dài. Một người đi xe máy, người kia đi xe đạp. | Gán hai box `person` và một box `motorcycle` cho các đối tượng trong tình huống này. Bỏ qua xe đạp và phần thanh dài nhô ra ngoài cơ thể hoặc thân xe. | Có thể tách hai người theo phần cơ thể nhìn thấy. Thanh dài là vật mang theo, nên chiều dài của nó không quyết định cạnh box. |
| `img_030` | Bãi xe máy có poster in hình người ở phía trên bên trái. Xe ở tiền cảnh rõ hơn cụm xe phía sâu bên trong. | Bỏ qua người trên poster. Gán riêng từng `motorcycle` ở tiền cảnh nếu tách được xe. Dùng `ignore_region` cho phần cụm xe phía sau không phân biệt được từng chiếc. | Hình người nằm trên mặt phẳng poster. Ở tiền cảnh có thể lần theo yên, thân hoặc bánh để tách xe, trong khi nhiều xe phía sau chỉ lộ các mảnh nhỏ chồng lên nhau. |
| `img_038` | Nhiều ô tô mô hình được xếp thành hàng trong tủ kính. | Bỏ qua toàn bộ xe mô hình. | Các xe nằm trên kệ đồ chơi và có kích thước tương đương các món đồ trưng bày bên cạnh. |

Nếu còn phân vân về lớp hoặc ranh giới của một đối tượng đã vẽ box, đặt `can_review=true` và ghi mã ảnh kèm vị trí. Với ca chưa có box như `img_009`, ghi trực tiếp vào danh sách cần nhóm chốt.

## 5. QC và phiên bản

| Hạng mục | Ngưỡng áp dụng |
|---|---|
| Ghép cặp box để tính IAA | Ghép 1–1 theo vị trí với IoU ≥ 0,50, không yêu cầu cùng lớp ở bước ghép. |
| IoU khi kiểm box | Cao từ 20 px đến dưới 50 px cần IoU ≥ 0,70. Cao từ 50 px trở lên cần IoU ≥ 0,85. |
| Độ đồng thuận giữa người gán | Trên các cặp đã ghép, IoU trung bình ≥ 0,85 và tỷ lệ cùng lớp ≥ 95%. |
| Tỷ lệ lỗi theo ảnh | Số ảnh có ít nhất một lỗi chia cho số ảnh được kiểm. Ngưỡng để Release là ≤ 2%, đồng thời phải đạt các ngưỡng chất lượng và không có lỗi lặp cùng kiểu. |

QC kiểm đủ năm loại lỗi gồm bỏ sót, thừa, sai lớp, sai vị trí hoặc kích thước, và sai thuộc tính. Báo cáo ghi cả số ảnh lỗi trên tổng số ảnh được kiểm và số đối tượng lỗi trên tổng số đối tượng được kiểm. Trưởng nhóm tổng hợp các ca `can_review` để nhóm xem ảnh và chốt quy tắc. Mọi thay đổi có thể làm đổi kết quả gán nhãn đều được ghi vào lịch sử phiên bản.

