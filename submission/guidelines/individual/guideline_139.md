# Guideline gán nhãn — v0.1 (Cá nhân) — 139

- **Phiên bản:** v0.1 — Bản đề xuất cá nhân, Pha 1.
- **Người viết / tự kiểm:** 139.
- **Loại nhãn:** Bounding box 2D trên ảnh đường phố.
- **Công cụ:** CVAT, xuất định dạng COCO 1.0.
- **Tài liệu tham chiếu:** `guideline/guideline_v0_starter.md`, `guideline/TEMPLATE_guideline.md` và HTML hướng dẫn Day 07.
- **Phạm vi:** Đề xuất cá nhân phục vụ thảo luận Pha 2.

---

## 1. Mục tiêu

Tạo nhãn bounding box 2D cho người thật và phương tiện thật trong ảnh đường phố, phục vụ huấn luyện và đánh giá mô hình phát hiện đối tượng. Nhãn phải nhất quán về lớp, phạm vi box và thuộc tính.

Không suy đoán đối tượng hoàn toàn không nhìn thấy. Phân biệt vật thật với hình in, phản chiếu, tượng, ma-nơ-canh và đồ chơi. Bỏ sót đối tượng, gán nhầm vật giả hoặc kéo box quá rộng đều có thể làm sai dữ liệu huấn luyện.

## 2. Lớp và thuộc tính

### Lớp

Mỗi đối tượng nhận đúng một lớp. Các lớp bổ sung được chốt cùng taxonomy ở Pha 2.

| Lớp | Định nghĩa | Dễ nhầm / điều kiện loại | Ví dụ / đối chiếu |
|---|---|---|---|
| `car` | Ô tô con chở người, gồm sedan, hatchback, SUV khi nhận dạng được. | Không dùng làm lớp mặc định cho mọi phương tiện bốn bánh; phân biệt xe tải và van. | Ô tô trên xe vận chuyển: `img_002.jpg` |
| `truck` | Xe tải hoặc phương tiện có khoang/thùng chở hàng riêng; đề xuất xếp bán tải vào đây. | Bán tải lắp nắp thùng và xe đặc chủng cần quy tắc chốt riêng. | Xe vận chuyển ở `img_002.jpg`; bán tải cần chốt ở `img_037.jpg` |
| `van` | Xe thùng kín cỡ nhỏ với khoang hàng/người liền thân xe. | Không chỉ dựa vào kích thước box để phân biệt van, bus và truck. | Phân biệt với bán tải có nắp thùng ở `img_037.jpg` |
| `motorcycle` | Xe máy hoặc mô tô có động cơ. | Không gồm người điều khiển; không tự ép xe đạp hoặc phương tiện ba bánh vào lớp này. | Đối chiếu ca ngoài lớp: xe đạp/xích lô `img_006.jpg`, `img_029.jpg`, `img_039.jpg` |
| `person` | Người thật nhìn thấy trong ảnh, gồm người đi bộ, người điều khiển và người ngồi trên xe. | Không gán tượng, ma-nơ-canh, chân dung in hoặc người chỉ xuất hiện qua phản chiếu. | Phản ví dụ: `img_017.jpg`, `img_018.jpg`, `img_033.jpg` |
| `ignore_region` | Vùng được bỏ qua khi không gán từng đối tượng theo quy tắc vật quá nhỏ hoặc cụm quá dày. | Không phải lớp vật thể; không dùng tùy tiện để che ca khó hoặc đối tượng bị bỏ sót. | Vật dưới 20 px hoặc cụm quá dày, không thể tách từng vật |

Đề xuất bổ sung `bus` cho xe buýt và `tuk_tuk` cho xe ba bánh có động cơ dạng tuk-tuk. Nhóm chốt hai lớp này trong taxonomy ở Pha 2.

Vật ngoài taxonomy: không gán nhãn sai chỉ để có một lớp. Ghi lại tên ảnh, vị trí và câu hỏi để nhóm quyết định phạm vi. Không tự biến mọi vật ngoài phạm vi thành `ignore_region`.

### Thuộc tính

Ước lượng tỉ lệ che khuất so với toàn bộ vật để chọn thuộc tính. Box vẫn ôm phần nhìn thấy, không bao phần khuất do suy đoán.

| Thuộc tính | Giá trị | Quy tắc |
|---|---|---|
| `occluded_level` | `0` | Không bị vật khác che khuất. |
| | `1` | Bị che trên 0% đến 30%. |
| | `2` | Bị che trên 30% đến 70%. |
| | `3` | Bị che trên 70% nhưng vẫn nhận dạng chắc chắn được đối tượng và lớp. Không dùng để hợp thức hóa việc đoán vật không rõ. |
| `truncated` | `yes` | Một phần đối tượng bị cắt bởi biên ảnh. |
| | `no` | Đối tượng không bị biên ảnh cắt. |
| `can_review` | `true` | Còn nghi ngờ về lớp, box hoặc thuộc tính cần reviewer giải quyết. |
| | `false` | Không còn vấn đề cần reviewer theo hiểu biết hiện tại. |

Che bởi vật khác và cắt bởi biên ảnh là hai hiện tượng khác nhau; có thể cùng xảy ra. Cờ occluded mặc định của CVAT không thay thế thuộc tính `occluded_level`.

## 3. Quy tắc vẽ box

- **R-01 — Phạm vi box:** dùng hình chữ nhật trục song song ôm sát toàn bộ phần nhìn thấy của một vật. Nếu vật có nhiều phần nhìn thấy rời nhau, dùng một box bao các phần đó; không vẽ mỗi mảnh thành một vật riêng. Không nới box để dự đoán phần bị che.
- **R-02 — Người và xe:** người điều khiển/người ngồi trên xe là `person`; xe có box riêng theo lớp xe. Chỉ gán khi từng đối tượng đủ nhìn thấy và đạt điều kiện kích thước. Không gộp người và xe thành một đối tượng.
- **R-03 — Xe chở xe:** đề xuất gán riêng xe vận chuyển và từng xe thật được chở nếu nhận dạng chắc chắn, đủ kích thước. Không gộp cả cụm thành một box duy nhất. Trường hợp thân vỏ rời hoặc hàng hóa giống xe cần hỏi reviewer.
- **R-04 — Vật giả và phản chiếu:** không gán tượng, ma-nơ-canh, đồ chơi, hình in hay vật chỉ nhìn thấy trong gương như một đối tượng thật trực tiếp trong cảnh. Nếu ảnh chỉ chứa những trường hợp bị loại, vẫn giữ ảnh trong Task và export dù có 0 annotation.
- **R-05 — Vật nhỏ:** giữ ngưỡng chiều cao box 20 px theo `rules`: vật có chiều cao dưới 20 px không gán box đối tượng riêng; dùng vùng ignore phù hợp theo quy tắc đã chốt. Không mặc nhiên bỏ qua vật đúng 20 px.
- **R-06 — Cụm dày:** chỉ dùng `ignore_region` cho khu vực không thể tách vật đáng tin cậy theo quy tắc nhóm; ôm vùng cần bỏ qua nhỏ nhất hợp lý, tránh phủ cả đối tượng rõ bên cạnh.
- **R-07 — Cắt mép:** tọa độ box phải nằm trong ảnh, ôm phần còn nhìn thấy và đặt `truncated=yes` khi vật bị biên ảnh cắt.
- **R-08 — Xe chủ:** không gán phần xe chủ gắn camera vào đối tượng mục tiêu.
- **R-09 — Ca chưa chắc:** nếu đã vẽ được box nhưng chưa chắc quyết định, bật `can_review`. Nếu chưa thể vẽ box, ghi tên ảnh, mã người gán, vị trí và câu hỏi vào phiếu vấn đề cấp ảnh; không đoán để điền cho đủ.
- **R-10 — Hàng hóa:** không kéo box phương tiện bao toàn bộ hàng hóa nhô ra. Nếu không xác định được ranh giới xe và hàng, đánh dấu cần review.

## 4. Ca khó

Nguồn tình huống: `K5-L23-Day07-Guideline-Taxonomy-QC.html` của repo gốc. Bảng ghi các phương án đề xuất cho buổi thảo luận Pha 2.

| Ảnh | Tình huống | Quyết định đề xuất | Lý do |
|---|---|---|---|
| `img_002.jpg` | Xe tải chở ô tô trên hai sàn. | Box riêng cho xe vận chuyển và từng ô tô thật đủ điều kiện. | Mỗi xe là đối tượng độc lập; R-03. |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk chưa có lớp phù hợp trong v0. | Đề xuất thêm `tuk_tuk` ở Pha 2. | Không ép vào lớp motorcycle khi định nghĩa không phù hợp. |
| `img_009.jpg`, `img_037.jpg` | Xe buýt chưa có lớp trong v0. | Đề xuất thêm `bus` ở Pha 2. | Loại phương tiện là thông tin mô hình cần dự đoán. |
| `img_017.jpg`, `img_027.jpg` | Tượng người hoặc tượng xe. | Không gán người/xe thật. | Vật mô phỏng ngoài phạm vi; R-04. |
| `img_020.jpg` | Đối tượng xuất hiện qua gương. | Không gán đối tượng chỉ nhìn thấy qua phản chiếu. | Tránh tính phản chiếu như vật thật trực tiếp trong cảnh; R-04. |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh và đồ chơi. | Không gán person hoặc lớp phương tiện cho vật mô phỏng. | Không phải đối tượng thật; R-04. |
| `img_035.jpg` | Tuk-tuk có hàng hóa nhô cao. | Đề xuất không bao cả hàng hóa vào box xe; cần nhóm chốt. | Làm rõ phạm vi box; R-10. |
| `img_037.jpg` | Bán tải có nắp thùng cao, dễ nhầm van. | Đề xuất truck khi nhận dạng rõ cấu trúc bán tải; chưa chắc thì hỏi reviewer. | Phân loại theo cấu trúc, không riêng hình dạng nắp. |

## 5. QC và phiên bản

### Các ngưỡng cố định lấy từ taxonomy local

Không sửa mục `rules` trong taxonomy để làm số liệu đạt chuẩn.

| Tiêu chí | Ngưỡng |
|---|---|
| Chiều cao tối thiểu box đối tượng | 20 px |
| Ranh giới box lớn | Chiều cao từ 50 px |
| IoU từng box lớn / nhỏ | 0.85 / 0.70 |
| Ngưỡng ghép box khi so sánh | IoU từ 0.50 |
| IAA IoU trung bình | Từ 0.85 trên các cặp ghép |
| Đồng thuận lớp | Từ 95% trên các cặp ghép |
| Tỉ lệ ảnh lỗi tối đa để xét Release | 2%; còn phải đáp ứng điều kiện bằng chứng và QC khác |

IAA cao không chứng minh nhãn đúng: hai người có thể cùng sai hoặc cùng bỏ sót. Phải review nội dung thực tế. Số liệu chạy mẫu trong hướng dẫn không phải kết quả nhóm.

### Xử lý ca mơ hồ và quản lý phiên bản

- Ca chưa rõ lớp, phạm vi box hoặc thuộc tính: bật `can_review`; nếu chưa vẽ được box, ghi vấn đề cấp ảnh kèm vị trí cụ thể.
- Nhóm chốt các bất đồng trước khi khóa guideline v1.0. Trong lượt gán độc lập, dùng cùng bản guideline và taxonomy đã khóa.
- Giữ nguyên bản guideline cá nhân sau khi nộp riêng. Quy tắc thay đổi sau calibration được ghi ở bản chung; thay đổi ngữ nghĩa cần kiểm lại toàn lô.

### Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do / bằng chứng |
|---|---|---|
| v0 — bản khởi đầu | Định nghĩa sơ lược các lớp và thuộc tính. | `guideline/guideline_v0_starter.md` chưa có ngưỡng che khuất cụ thể và bảng ca khó. |
| v0.1 — đề xuất cá nhân | Làm rõ định nghĩa lớp, ngưỡng thuộc tính, quy tắc R-01–R-10; bổ sung ca khó và đề xuất lớp bus, tuk-tuk. | Đối chiếu template, taxonomy và các tình huống trong HTML: xe chở xe, xe ba bánh, xe buýt, tượng, phản chiếu và bán tải có nắp thùng. |
