# Guideline gán nhãn — v0.1 (Cá nhân) — 139

- **Phiên bản:** v0.1 — Bản đề xuất cá nhân, Pha 1.
- **Người viết / tự kiểm:** 139.
- **Loại nhãn:** Bounding box 2D trên ảnh đường phố.
- **Công cụ:** CVAT, xuất định dạng COCO 1.0.
- **Tài liệu tham chiếu:** `guideline/guideline_v0_starter.md`, `guideline/TEMPLATE_guideline.md` và HTML hướng dẫn Day 07.
- **Phạm vi:** Đề xuất cá nhân để thảo luận; chưa thay thế guideline thống nhất của nhóm ở Pha 2.

> **Ghi chú:** GPT hỗ trợ tổng hợp tài liệu và diễn đạt. Ví dụ ảnh được tham chiếu từ HTML hướng dẫn, còn chờ người học đối chiếu ảnh; chưa xác nhận hoàn tất khảo sát 40 ảnh. Chi tiết hỗ trợ AI ghi tại `submission/individual_notes/139.md`.

---

## 1. Mục tiêu

Tạo nhãn bounding box 2D cho người thật và phương tiện thật trong ảnh đường phố, phục vụ huấn luyện và đánh giá mô hình phát hiện đối tượng. Nhãn phải nhất quán về lớp, phạm vi box và thuộc tính.

Không suy đoán đối tượng hoàn toàn không nhìn thấy. Phân biệt vật thật với hình in, phản chiếu, tượng, ma-nơ-canh và đồ chơi. Bỏ sót đối tượng, gán nhầm vật giả hoặc kéo box quá rộng đều có thể làm sai dữ liệu huấn luyện.

## 2. Lớp và thuộc tính

### Lớp

Mỗi đối tượng nhận đúng một lớp. Chưa tự thêm lớp vào taxonomy chung.

| Lớp | Định nghĩa thao tác dự kiến | Dễ nhầm / điều kiện loại | Ví dụ / đối chiếu |
|---|---|---|---|
| `car` | Ô tô con chở người, gồm sedan, hatchback, SUV khi nhận dạng được. | Không dùng làm lớp mặc định cho mọi phương tiện bốn bánh; phân biệt xe tải và van. | Ô tô trên xe vận chuyển: `img_002.jpg` theo HTML |
| `truck` | Xe tải hoặc phương tiện có khoang/thùng chở hàng riêng; đề xuất xếp bán tải vào đây. | Bán tải lắp nắp thùng và xe đặc chủng cần quy tắc chốt riêng. | Xe vận chuyển ở `img_002.jpg`; bán tải cần chốt ở `img_037.jpg` theo HTML |
| `van` | Xe thùng kín cỡ nhỏ với khoang hàng/người liền thân xe. | Không chỉ dựa vào kích thước box để phân biệt van, bus và truck. | Đối chiếu ca bán tải `img_037.jpg` theo HTML; ví dụ van cần tự xác nhận |
| `motorcycle` | Xe máy hoặc mô tô có động cơ. | Không gồm người điều khiển; không tự ép xe đạp hoặc phương tiện ba bánh vào lớp này. | Đối chiếu ca ngoài lớp: xe đạp/xích lô `img_006.jpg`, `img_029.jpg`, `img_039.jpg` theo HTML |
| `person` | Người thật nhìn thấy trong ảnh, gồm người đi bộ, người điều khiển và người ngồi trên xe. | Không gán tượng, ma-nơ-canh, chân dung in hoặc người chỉ xuất hiện qua phản chiếu. | Phản ví dụ theo HTML: `img_017.jpg`, `img_018.jpg`, `img_033.jpg` |
| `ignore_region` | Vùng được bỏ qua khi không gán từng đối tượng theo quy tắc vật quá nhỏ hoặc cụm quá dày. | Không phải lớp vật thể; không dùng tùy tiện để che ca khó hoặc đối tượng bị bỏ sót. | Xác định bằng kích thước dưới 20 px hoặc cụm dày theo quy tắc nhóm; không suy từ tên ảnh |

**Đề xuất cần thảo luận:** nếu phạm vi dữ liệu yêu cầu nhận diện xe buýt và tuk-tuk, cân nhắc lớp `bus` và `tuk_tuk` ở Pha 2. Chưa coi đây là taxonomy đã thống nhất.

Vật ngoài taxonomy: không gán nhãn sai chỉ để có một lớp. Ghi lại tên ảnh, vị trí và câu hỏi để nhóm quyết định phạm vi. Không tự biến mọi vật ngoài phạm vi thành `ignore_region`.

### Thuộc tính

Các ngưỡng che khuất dưới đây là đề xuất định lượng, người học cần tự xác nhận sau khảo sát. Tỉ lệ che ước lượng tương đối so với toàn bộ vật; không vẽ box suy diễn phần bị che.

| Thuộc tính | Giá trị | Quy tắc dự kiến |
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
- **R-08 — Xe chủ:** đề xuất loại phần xe gắn camera khỏi đối tượng mục tiêu. Người học cần kiểm tra tính phù hợp và chốt với nhóm trước lượt gán độc lập.
- **R-09 — Ca chưa chắc:** nếu đã vẽ được box nhưng chưa chắc quyết định, bật `can_review`. Nếu chưa thể vẽ box, ghi tên ảnh, mã người gán, vị trí và câu hỏi vào phiếu vấn đề cấp ảnh; không đoán để điền cho đủ.
- **R-10 — Hàng hóa:** đề xuất không kéo box phương tiện bao toàn bộ hàng hóa nhô ra; cần tự kiểm tra ca thực tế và chốt ranh giới trong buổi họp nhóm.

## 4. Ca khó

Các ca dưới đây được mô tả trong `K5-L23-Day07-Guideline-Taxonomy-QC.html` của repo gốc. Hướng xử lý là đề xuất từ tài liệu, cần người học tự mở ảnh kiểm tra; không ghi nhận là ca đã tự tìm ra.

| Ảnh | Tình huống theo HTML | Quyết định đề xuất | Lý do |
|---|---|---|---|
| `img_002.jpg` | Xe tải chở ô tô trên hai sàn. | Box riêng cho xe vận chuyển và từng ô tô thật đủ điều kiện. | Mỗi xe là đối tượng độc lập; R-03. |
| `img_004.jpg`, `img_035.jpg` | Tuk-tuk chưa có lớp phù hợp trong v0. | Đề xuất thêm `tuk_tuk` ở Pha 2. | Không ép vào lớp motorcycle khi định nghĩa không phù hợp. |
| `img_009.jpg`, `img_037.jpg` | Xe buýt chưa có lớp trong v0. | Đề xuất thêm `bus` ở Pha 2. | Loại phương tiện là thông tin mô hình cần dự đoán. |
| `img_017.jpg`, `img_027.jpg` | Tượng người hoặc tượng xe. | Không gán người/xe thật. | Vật mô phỏng ngoài phạm vi; R-04. |
| `img_020.jpg` | Đối tượng xuất hiện qua gương. | Không gán đối tượng chỉ nhìn thấy qua phản chiếu. | Tránh tính phản chiếu như vật thật trực tiếp trong cảnh; R-04. |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh và đồ chơi. | Không gán person hoặc lớp phương tiện cho vật mô phỏng. | Không phải đối tượng thật; R-04. |
| `img_035.jpg` | Tuk-tuk có hàng hóa nhô cao. | Đề xuất không bao cả hàng hóa vào box xe; cần nhóm chốt. | Làm rõ phạm vi box; R-10. |
| `img_037.jpg` | Bán tải có nắp thùng cao, dễ nhầm van. | Đề xuất truck khi nhận dạng rõ cấu trúc bán tải; chưa chắc thì hỏi reviewer. | Phân loại theo cấu trúc, không riêng hình dạng nắp. |

Sau khi tự khảo sát đủ 40 ảnh, bổ sung hoặc sửa các đề xuất trên và ghi ba ca quan sát thật vào ghi chú cá nhân.

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

### Quy trình cá nhân và thay đổi

1. Người học tự khảo sát 40 ảnh, hoàn thiện các chỗ CHƯA ĐIỀN và ghi nhận việc dùng AI hỗ trợ soạn nháp trong ghi chú cá nhân.
2. Nộp riêng bản guideline; giữ nguyên bản đã nộp. Chỉ xem bản đồng đội sau khi mọi người nộp xong.
3. Họp Pha 2 để chốt guideline v1.0 và taxonomy chung. Các quy tắc đề xuất ở bản này không tự trở thành quy tắc nhóm.
4. Gán nhãn Pha 3 độc lập theo v1.0 đã khóa, không theo bản nháp chưa duyệt này.
5. Thay đổi sau calibration phải ghi lịch sử; thay đổi ngữ nghĩa cần kiểm lại toàn lô theo quy trình bài.

### Lịch sử chỉnh sửa guideline cá nhân

Bảng này ghi các thay đổi của riêng bản guideline này, không phải lịch sử thống nhất của nhóm và không phải bảng kết quả quan sát ảnh. Có thể ghi thay đổi dựa trên tài liệu; chỉ ghi “đã quan sát/kiểm chứng” khi thực sự tự xem ảnh. Lịch sử bản chung được nhóm quản lý trong guideline thống nhất ở Pha 2 và bản calibration ở Pha 5.

| Phiên bản | Thay đổi | Lý do / bằng chứng |
|---|---|---|
| v0 — tài liệu tham chiếu | Dùng guideline v0 của bài làm điểm xuất phát; không sửa file v0 gốc. | `guideline/guideline_v0_starter.md` định nghĩa lớp sơ lược, mô tả mức che bằng từ định tính và chưa có ca khó. |
| Cá nhân — nháp lần 1 | Bổ sung định nghĩa lớp, điều kiện loại trừ, ngưỡng che khuất và các quy tắc R-01 đến R-10; giữ nguyên ngưỡng QC trong taxonomy. | Đối chiếu v0, `guideline/TEMPLATE_guideline.md` và `submission/taxonomy.json`. AI hỗ trợ soạn; chưa xác nhận khảo sát ảnh. |
| Cá nhân — nháp lần 2 | Chuẩn hóa bố cục 5 mục; bổ sung ví dụ và bảng ca khó tham chiếu từ HTML; đề xuất thảo luận thêm bus và tuk-tuk ở Pha 2. | Hướng dẫn HTML nêu xe chở xe (`img_002.jpg`), tuk-tuk (`img_004.jpg`, `img_035.jpg`), xe buýt (`img_009.jpg`, `img_037.jpg`), tượng, phản chiếu và vật mô phỏng. Đây là nguồn tài liệu, chưa phải quan sát cá nhân đã kiểm chứng. |
| Cá nhân — nháp lần 3 | Làm rõ phạm vi lịch sử cá nhân và ghi lại những chỉnh sửa thực tế, thay dòng hẹn cập nhật chung chung. Tạm dùng MSHV trong tên file, để trống mã thành viên 3 chữ số. | Người học đã đọc qua bản nháp và yêu cầu chỉnh file cá nhân; không ghi nhận quyết định nhóm, kết quả IAA hoặc QC chưa thực hiện. |

| Cá nhân — cập nhật mã 139 (hiện tại) | Đồng bộ mã thành viên 139, đổi tên guideline và ghi chú cá nhân sang tên dùng ba chữ số, cập nhật liên kết giữa hai file. | Theo mã tạm trong `TEAM.md` và xác nhận của người học; không thay đổi quy tắc gán nhãn. |

Sau khi tự đối chiếu ảnh, thêm dòng mới nêu đúng quy tắc đã đổi, tên ảnh và lý do; không cần tạo phiên bản giả nếu chưa có thay đổi. Các nhãn “nháp lần 1–3” ở đây chỉ mô tả các lượt biên tập, không chứng minh đã có commit hay bản lưu riêng cho từng lượt. Giữ nguyên bản cá nhân sau khi đã nộp riêng.
