# Guideline gán nhãn nhóm T054 — v1.0 thống nhất

- Phiên bản: v1.0; theo điều phối mã 042, nhóm đã duyệt dự thảo và chốt các quy tắc dưới đây. Các thành viên gán theo v1.0, không tự áp dụng quy tắc khác trong bản cá nhân.
- Công cụ: CVAT; nhãn bounding box 2D; xuất COCO 1.0.
- Phạm vi: 40 ảnh đường phố của bài lab. Các ví dụ ảnh dưới đây dựa trên ghi chép của nhóm, **không phải kết quả AI tự xem ảnh**.
- Truy vết Pha 1: điều phối 042 thông báo 139 đã hoàn thành; tuy nhiên bản cập nhật `submission/individual_notes/139.md` ở commit `b449c46` vẫn ghi **chưa ghi nhận hoàn tất khảo sát độc lập 40 ảnh**, ba ca lấy từ HTML. Chỉ thành viên 139 có thể tự xác nhận/cập nhật bằng chứng quan sát cá nhân; không dùng văn bản nhóm thay bằng chứng đó.

## 1. Mục tiêu

Tạo nhãn nhất quán cho người thật và phương tiện thật thuộc taxonomy để huấn luyện/đánh giá mô hình phát hiện trên ảnh đường phố. Không đưa vật giả, phản chiếu hay xe ngoài phạm vi vào lớp khác cho đủ nhãn. Bỏ sót người/xe đủ điều kiện, nhầm lớp và box sai ranh giới đều ảnh hưởng chất lượng dữ liệu.

## 2. Lớp và thuộc tính

### Lớp

Mỗi vật được gán đúng một lớp đối tượng nếu nhận biết được lớp, nhìn thấy phần cần thiết và box cao ít nhất 20 px. Dùng đúng 6 tên lớp trong `submission/taxonomy.json` v1.0; `ignore_region` là vùng bỏ qua, không phải một vật.

| Lớp | Định nghĩa thao tác | Ví dụ và ranh giới |
|---|---|---|
| `car` | Ô tô con, gồm SUV, nếu phân biệt được với xe tải/van. | Ô tô thật được chở ở `img_002` có thể là một đối tượng riêng; không gán ô tô chỉ thấy trong gương `img_020`. |
| `truck` | Xe tải, xe có thùng/kết cấu chở hàng. | Xe vận chuyển ô tô thật ở `img_002` tách với các ô tô được chở. |
| `van` | Xe thùng kín cỡ nhỏ. | Không dùng cho xe buýt chỉ vì không có lớp `bus`. |
| `motorcycle` | Xe máy, mô tô; **theo quyết định nhóm gồm tuk-tuk**. | `img_035`: box ôm xe/bộ phận cố định, **không ôm hàng rời**; người trên xe có box `person` riêng nếu đủ điều kiện. |
| `person` | Người thật, kể cả người đi bộ và người ngồi/điều khiển xe. | Không gán tượng, ma-nơ-canh, hình in hay người chỉ hiện trong phản chiếu. |
| `ignore_region` | Vùng bỏ qua cho đối tượng mục tiêu không nhận biết được hoặc đơn lẻ nhận rõ nhưng box cao dưới 20 px không cần gán từng vật.; không phải lớp vật. | Có thể chồng một phần box hợp lệ nếu vẫn giữ box của vật rõ; không thay box của vật nhận rõ và cao ≥20 px; không dùng cho xe buýt/đồ chơi ngoài phạm vi. |

**Ngoài taxonomy:** không thêm `bus`; xe buýt không gán và không ép vào `truck`/`van`. Phương tiện khác ngoài các lớp không tự gán lớp gần nhất. Hình in, tượng, đồ chơi, xe chỉ hiện qua gương và bộ phận xe gắn camera không gán.

### Thuộc tính

| Thuộc tính | Giá trị | Quy tắc |
|---|---|---|
| `occluded_level` | `0` | 0% đối tượng bị vật khác che. |
| | `1` | Trên 0% đến 30% bị che, gồm đúng 30%. |
| | `2` | Trên 30% đến 70% bị che, gồm đúng 70%. |
| | `3` | Trên 70% bị che nhưng vẫn nhận rõ lớp; không vẽ vật suy đoán. |
| `truncated` | `yes` / `no` | `yes` khi vật tiếp tục ra ngoài biên ảnh; box dừng ở mép ảnh. Che bên trong ảnh không tự động là truncated. |
| `can_review` | tick / không | Tick box đã gán nhưng còn nghi ngờ về lớp, ranh giới hoặc thuộc tính; ca không thể vẽ box phải ghi vấn đề cấp ảnh, không tạo box suy đoán. |

Ước lượng `occluded_level` theo phần vật bị che, không dùng mức này để biểu diễn nhòe chuyển động hay bị cắt bởi biên ảnh. Giữ đúng giá trị thuộc tính trong taxonomy.

## 3. Quy tắc vẽ box

- **R-01 — Box và ngưỡng:** mỗi vật mục tiêu nhận một box trục song song ôm sát phần nhìn thấy; không đoán phần bị che. Chỉ gán box **đối tượng** khi chiều cao box đạt ít nhất **20 px** theo `taxonomy.rules.min_box_height_px`. Vật đúng 20 px đủ điều kiện nếu lớp rõ; vật đơn lẻ nhận rõ nhưng dưới 20 px dùng vùng `ignore_region` theo R-08 thay vì box lớp đối tượng.
- **R-02 — Người và xe:** người ngồi/điều khiển xe và phương tiện là các đối tượng riêng; mỗi đối tượng cần nhận rõ lớp, có phần thấy được và đạt ngưỡng 20 px.
- **R-03 — Xe chở xe:** `img_002`: xe vận chuyển và từng xe **thật** được chở có box riêng nếu đủ điều kiện; box xe vận chuyển không gộp các xe được chở. Không suy ra phải gán vật trên xe ở `img_024`.
- **R-04 — Vật không gán:** không gán ảnh phản chiếu trong gương (`img_020`), xe buýt, vật trên xe ở `img_024` theo quyết định nhóm, tượng/ma-nơ-canh/hình in, đồ chơi hay bộ phận xe gắn camera. Không dùng `ignore_region` chỉ vì vật ngoài phạm vi.
- **R-05 — Tuk-tuk và hàng:** tuk-tuk dùng `motorcycle`; box ôm xe và kết cấu cố định đang nhìn thấy, không tính hàng rời nhô ra. Người ngồi trên xe nếu đủ điều kiện gán `person` riêng.
- **R-06 — Che và biên ảnh:** người/xe bị vật khác che thì box chỉ ôm phần còn nhìn thấy; gán `occluded_level` theo bảng trên. Vật bị mép ảnh cắt thì box dừng ở mép và `truncated=yes`.
- **R-07 — Nhòe chuyển động:** khi vẫn nhận biết được lớp và đủ điều kiện gán, box ôm cả vệt nhòe **quan sát được** của vật; vệt không nhận diện được không được biến thành box người/xe. Nhòe không phải che khuất.
- **R-08 — Ignore:** cho phép `ignore_region` giao một phần box hợp lệ; vẫn gán vật nhận biết rõ và đủ 20 px. Dùng ignore cho vùng đối tượng mục tiêu không nhận biết được **hoặc vật đơn lẻ nhận rõ nhưng box đối tượng sẽ cao <20 px**; ôm đúng vùng cần bỏ qua thay vì phủ vật rõ lân cận. Không gán box lớp đối tượng dưới ngưỡng; không dùng ignore cho xe buýt hay vật giả chỉ vì ngoài phạm vi.
- **R-09 — Ca chưa chắc:** nếu có box hợp lệ nhưng còn băn khoăn, bật `can_review`; nếu chưa thể gán thì ghi ảnh, vùng, người gặp ca và câu hỏi trong nhật ký vấn đề; không thêm lớp hoặc box suy đoán.

## 4. Ca khó cần đối chiếu khi gán

| Ảnh | Tình huống từ bản cá nhân/tài liệu | Rule ID | Quyết định nhóm theo mã 042 | Lý do thao tác |
|---|---|---|---|---|
| `img_002` | Xe chở ô tô thật. | R-01, R-03 | Box riêng xe chở và từng ô tô thật đủ điều kiện. | Mỗi xe thật là một đối tượng; không gộp xe được chở vào box xe chở. |
| `img_020` | Xe chỉ xuất hiện trong gương. | R-04 | Không gán xe trong gương. | Phản chiếu không là vật trực tiếp trong cảnh. |
| `img_024` | Vật trên xe, quan điểm cá nhân khác nhau. | R-04 | Không gán vật trên xe ở ảnh này. | Không lấy hình dạng giống xe để suy ra xe thật; xe khác nếu có phải xét độc lập trên ảnh gốc. |
| `img_035` | Tuk-tuk có hàng nhô lên. | R-02, R-05 | Tuk-tuk=`motorcycle`; box không ôm hàng rời. | Tách ranh giới xe với hàng. |
| `img_009`, `img_031`, `img_037` | Xe buýt. | R-04 | Không gán xe buýt; không thêm lớp `bus`. | Xe ngoài taxonomy được nhóm loại khỏi phạm vi. |
| `img_014`, `img_015`, `img_030` | Vật nhòe chuyển động nếu có. | R-01, R-07 | Vật còn nhận rõ lớp thì box ôm vệt nhòe quan sát được và xét ngưỡng 20 px. | Nhất quán ranh giới box; không gán vệt không nhận diện được. |

Những ví dụ trên không xác nhận rằng AI đã kiểm từng đối tượng hoặc đo chiều cao box; người gán tự đối chiếu ảnh gốc trước khi tạo annotation.

## 5. QC và phiên bản

- **Không thay đổi `taxonomy.rules`:** chiều cao box tối thiểu 20 px; box lớn từ 50 px; IoU từng box lớn/nhỏ lần lượt 0.85/0.70; ghép so sánh tại IoU 0.50; IAA IoU trung bình tối thiểu 0.85; đồng thuận lớp tối thiểu 0.95; tỉ lệ ảnh lỗi tối đa 0.02. Các số này là ngưỡng, **không phải kết quả đo của nhóm**.
- Người gán độc lập theo guideline đã khóa; điều phối mã 042 tập hợp bất đồng, thành viên đối chiếu ảnh gốc và ghi quyết định calibration/QC thực tế. Không lấy việc hai người cùng đồng ý làm bằng chứng rằng nhãn đúng.
- Dùng `submission/taxonomy.json` v1.0 và cấu hình CVAT sinh từ file đó. Thành viên 139 cần tự bổ sung xác nhận/bằng chứng Pha 1; bản cập nhật ở `b449c46` vẫn tự khai chưa hoàn tất khảo sát độc lập. Không sửa `rules` để vượt kiểm thử. Nếu nhóm đổi ngữ nghĩa sau khi đã gán, ghi lịch sử thay đổi và kiểm lại nhãn liên quan trước khi tiếp tục.

| Mốc | Thay đổi | Cơ sở |
|---|---|---|
| v0 (starter) | Năm lớp đối tượng, một vùng ignore; ngưỡng che còn định tính. | `guideline/guideline_v0_starter.md`. |
| Các bản cá nhân v0.1 | Quan điểm khác nhau về phản chiếu, tuk-tuk, bus, vật trên xe, ignore, nhòe và mức che. | `submission/guidelines/individual/guideline_{042,112,124,139}.md`; không sửa hồi tố. |
| v1.0 nhóm chốt | Theo điều phối 042, nhóm duyệt dự thảo: phản chiếu/bus/vật trên xe `024` không gán; tuk-tuk là `motorcycle` không ôm hàng; ngưỡng che 0/30/70%; vùng ignore cho vật không nhận rõ và vật đơn lẻ dưới 20 px, cho phép giao box hợp lệ; box vật nhòe có thể ôm vệt; loại đồ chơi/xe chủ. | Quyết định do điều phối 042 chuyển đạt; các bản cá nhân giữ nguyên để truy vết bất đồng, 139 tự xác nhận bằng chứng Pha 1 còn thiếu. |
