# Guideline gán nhãn — v0.1 (Cá nhân) — 124

- Phiên bản: v0.1 (Bản đề xuất cá nhân - Pha 1)
- Ngày: 2026-11-16
- Người viết / duyệt: Bùi Lê Đình Văn (MSHV: 124)
- Loại nhãn: Bounding box (hộp chữ nhật 2D) trên ảnh đường phố
- Công cụ: CVAT, xuất định dạng COCO 1.0

---

## 1. Mục tiêu

- **Mục đích sử dụng:** Huấn luyện và đánh giá mô hình thị giác máy tính phát hiện **người và các phương tiện giao thông** trên ảnh đường phố phục vụ hệ thống giám sát giao thông thông minh (ITS) và hỗ trợ lái xe nâng cao (ADAS).
- **Rủi ro sai sót:**
  - **Bỏ sót (False Negative):** Bỏ sót người đi bộ, người đi xe máy hoặc phương tiện cắt ngang gây mất an toàn nghiêm trọng cho hệ thống tự hành/cảnh báo va chạm.
  - **Nhận diện nhầm vật giả (False Positive):** Gán nhãn nhầm tượng, ma-nơ-canh, ảnh in trên biển quảng cáo, hình phản chiếu qua gương/nước hoặc xe đồ chơi làm mô hình học sai đặc trưng vật thể thật.
  - **Lệch ranh giới (Localization Error):** Kéo box bao trùm cả hàng hóa cồng kềnh, bóng đổ hoặc ghép chung người với xe làm giảm độ chính xác định vị (IoU).

---

## 2. Lớp và thuộc tính

### 2.1. Danh sách lớp (mỗi vật thể thuộc đúng một lớp)

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / Dễ nhầm với |
|---|---|---|---|
| `person` | Người thật đang ở trong khung cảnh đường phố hoặc không gian công cộng (đi bộ, ngồi, đứng, điều khiển hoặc ngồi sau phương tiện). | Người đi bộ (`img_034`), người đi xe máy (`img_006`), người ngồi bàn quán ven đường (`img_001`), người đạp xích lô (`img_023`). | Tượng người (`img_011`, `img_017`), ma-nơ-canh (`img_033`), hình người in trên biển quảng cáo/poster/kính xe (`img_018`, `img_030`, `img_040`), mascot/người mặc đồ hóa trang kín đầu (`img_019`), bóng phản chiếu dưới mặt đường ướt (`img_013`). |
| `car` | Ô tô con chở khách dưới 9 chỗ có thiết kế khoang hành khách liền khối, bao gồm sedan, hatchback, SUV, crossover và taxi 4 bánh. | Xe con trắng/đen (`img_015`, `img_023`), taxi xanh-đỏ (`img_040`), ô tô con chở trên xe lồng (`img_002`). | Mô hình ô tô đồ chơi/vỏ xe trưng bày (`img_024`, `img_038`), xe bán tải (`img_001`, `img_012`), xe tải nhỏ/van (`img_005`). |
| `truck` | Phương tiện cơ giới chuyên chở hàng hóa có phần thùng hàng tách biệt hoặc khung gầm tải nặng (bao gồm xe bán tải pickup, xe tải thùng hở, xe tải thùng kín cỡ vừa/lớn, xe đầu kéo). | Xe bán tải Suzuki thùng hở (`img_001`), xe đầu kéo chở ô tô (`img_002`), xe tải Isuzu (`img_007`), xe bán tải độ thùng kín/bạt (`img_007`, `img_036`, `img_037`). | Bia mộ đá tạc hình xe bán tải (`img_027`), xe van chở khách/hàng liền thân không có cabin tách rời (`img_005`, `img_016`). |
| `van` | Xe ô tô thân liền khối (không tách rời cabin và khoang sau) dùng để chở khách cỡ nhỏ (9–16 chỗ) hoặc chở hàng hóa đô thị. | Xe khách cỡ nhỏ/minivan bạc (`img_005`), xe van trắng đậu ven đường (`img_016`). | Xe buýt cỡ trung/lớn trên 24 chỗ (`img_009`, `img_031`, `img_037`), xe bán tải gắn nắp thùng (`img_037`). |
| `bus` *(Đề xuất thêm mới)* | Phương tiện cơ giới cỡ lớn chuyên chở hành khách công cộng hoặc đường dài (từ 24 chỗ trở lên, thân dài, cửa lên xuống lớn). | Xe buýt 29 chỗ (`img_009`), xe buýt công cộng số 62 (`img_031`), xe buýt xanh (`img_037`, `img_040`). | Xe van 16 chỗ (`img_005`), xe tải thùng kín (`img_007`). |
| `motorcycle` | Phương tiện cơ giới 2 hoặc 3 bánh có động cơ dùng để chở người hoặc hàng nhẹ (xe máy, xe tay ga, mô tô phân khối lớn, xe gắn thùng bên sidecar, xe ba gác máy/tuk-tuk). | Xe máy/tay ga (`img_006`, `img_008`), mô tô sidecar (`img_022`), xe ba gác chở hàng (`img_025`), xe tuk-tuk 3 bánh (`img_004`, `img_016`, `img_035`). | Xe đạp thường (`img_003`, `img_006`, `img_029`), xe xích lô đạp không động cơ (`img_023`). |
| `ignore_region` | Vùng hình chữ nhật bao quanh đám đông người hoặc cụm phương tiện đứng quá dày đặc, chồng lấn lên nhau hoặc quá nhỏ ở xa không thể tách rời từng cá thể chính xác. | Đám đông đứng sau rào chắn (`img_019`), hàng người xếp hàng chờ xe buýt (`img_032`), biển xe máy kẹt xe phía xa (`img_021`), bãi giữ xe máy sâu bên trong (`img_030`). | Các vật thể đứng riêng lẻ ở tiền cảnh/trung cảnh có thể phân biệt rõ đường biên. |

- **Xử lý vật thể không thuộc danh sách lớp (xe đạp, xe xích lô đạp, xe đẩy tay, động vật):** **Không gán nhãn** cho bản thân phương tiện đó, chỉ gán nhãn `person` cho người đang điều khiển/dắt phương tiện nếu nhìn thấy người (`img_003`, `img_010`, `img_023`, `img_029`).

---

### 2.2. Thuộc tính (Attributes)

Mỗi bounding box (trừ `ignore_region`) phải gán đầy đủ 3 thuộc tính sau:

| Thuộc tính | Các mức cố định | Định nghĩa & Ngưỡng đo cụ thể |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | **Tỷ lệ diện tích vật thể bị che khuất bởi vật thể khác** (không tính phần bị cắt bởi mép khung ảnh):<br>- **`0` (Không che / Rõ nét):** Bị che **< 10%** diện tích (thấy trọn vẹn hình dáng vật thể).<br>- **`1` (Che nhẹ):** Bị che từ **10% đến < 35%** diện tích (ví dụ: người ngồi sau xe máy bị che chân, xe sau hàng rào thưa `img_018`).<br>- **`2` (Che nhiều):** Bị che từ **35% đến < 70%** diện tích (ví dụ: người ngồi sau bàn quán `img_001`, xe máy đỗ hàng giữa `img_008`).<br>- **`3` (Che nghiêm trọng):** Bị che **≥ 70%** diện tích nhưng vẫn nhận diện chắc chắn được loại vật thể (ví dụ: chỉ lộ đầu/mũ bảo hiểm trong dòng xe `img_021` hoặc chỉ lộ gương xe máy `img_040`). |
| `truncated` | `no`, `yes` | **Vật thể bị cắt ngang bởi 1 trong 4 cạnh biên của khung hình:**<br>- **`no`:** Toàn bộ vật thể nằm trọn bên trong khung ảnh (hoặc chỉ chạm nhẹ mép < 5% chiều dài/rộng).<br>- **`yes`:** Một phần thân vật thể bị cắt mất ở biên trái, phải, trên hoặc dưới ảnh (ví dụ: đầu ô tô cắt mép trái/phải `img_015`, `img_032`, người/xe cắt mép dưới `img_005`, `img_012`, `img_026`). |
| `can_review` | `false` / `true` (tick) | Tick **`true`** khi người gán gặp tình huống ranh giới khó phân loại (ví dụ: xe lai giữa `van` và `truck`, xe quá tối/quá nhòe, phương tiện lạ) để đưa ra họp nhóm chốt lại. |

---

## 3. Quy tắc vẽ box

1. **Nguyên tắc ôm sát (Tight Bounding Box):** Vẽ hộp chữ nhật ôm khít nhất có thể phần **nhìn thấy được** của vật thể thật (tính cả gương chiếu hậu, bánh xe, mũ bảo hiểm đang đội trên đầu người). **Không** tự ý kéo giãn box ra ngoài để đoán phần bị vật khác che khuất hoặc phần nằm ngoài mép ảnh.
2. **Tách biệt người và phương tiện:**
   - Người lái xe máy, người ngồi sau xe máy, người đạp xe đạp hoặc tài xế ngồi trong xe tuk-tuk/ô tô (nếu thấy rõ phần thân/đầu qua cửa kính hoặc khoang mở): vẽ **1 box `person` riêng** cho từng người và **1 box phương tiện riêng** (`motorcycle`, `car`, `truck`...). Không gộp chung người và xe thành 1 box.
3. **Hàng hóa, đồ vật mang theo & Thùng xe:**
   - **Thùng xe cố định / nắp thùng độ:** Tính vào box của xe (`truck`, `van`).
   - **Hàng hóa chất trên thùng xe hoặc chở cồng kềnh:**
     - Nếu hàng hóa nằm gọn trong lòng thùng xe (`img_012`) hoặc chất trên nóc xe tuk-tuk (`img_035`): **không** kéo rộng box xe để bao trùm phần hàng nhô cao quá khổ; box xe chỉ ôm theo khung kết cấu của xe.
     - Nếu người đi xe vác vật dài (thanh nhôm/ống nước trong `img_029`) hoặc gánh hàng rong (`img_003`): box `person` và `motorcycle` **chỉ ôm cơ thể người và thân xe**, tuyệt đối không kéo theo chiều dài thanh vật liệu.
     - Nếu xe tải chở phương tiện khác trên sàn (`img_002`): vẽ 1 box `truck` ôm trọn xe đầu kéo (bao gồm cả dàn khung rơ-moóc) **VÀ** vẽ riêng từng box `car` cho từng chiếc ô tô con đang nằm trên rơ-moóc nếu nhìn thấy rõ từng xe.
4. **Vật giả, hình in, tượng & phản chiếu (KHÔNG GÁN):**
   - **Không gán** tượng người, tượng động vật, bia mộ điêu khắc (`img_011`, `img_017`, `img_027`, `img_028`).
   - **Không gán** ma-nơ-canh trong tủ kính (`img_033`) hoặc nhân vật mặc đồ mascot che kín toàn thân (`img_019`).
   - **Không gán** hình ảnh 2D in trên biển quảng cáo, băng-rôn, tường nhà, vỏ xe hoặc sticker dán kính (`img_018`, `img_030`, `img_031`, `img_040`).
   - **Không gán** mô hình xe đồ chơi trưng bày (`img_024`, `img_038`).
   - **Không gán** hình ảnh phản chiếu qua gương chiếu hậu (`img_020`), qua mặt kính cửa hàng (`img_033`) hoặc bóng phản chiếu trên mặt đường ướt (`img_013`, `img_031`).
5. **Xe chủ (Ego-vehicle):** Không gán các bộ phận thuộc chính chiếc xe đang gắn camera chụp ảnh (mép cửa, gương chiếu hậu xe chủ trong `img_020`).
6. **Ngưỡng kích thước tối thiểu, độ nhòe, ánh sáng & Đám đông:**
   - **Kích thước tối thiểu:** Bỏ qua (không gán đơn lẻ) các vật thể có chiều rộng **< 12 px** hoặc chiều cao **< 15 px** ở tít phía chân trời.
   - **Nhòe chuyển động (Motion blur) & Thiếu sáng:** Nếu vật thể bị phơi sáng kéo vệt mờ không còn nhận ra hình hài biên dạng (`img_014`, xe trắng bị nhòe giữa `img_015`) hoặc nằm sâu trong nhà tối đen không tham gia giao thông (`img_039`) thì **bỏ qua** (hoặc khoanh `ignore_region` nếu tạo thành vệt nhiễu lớn).
   - **Đám đông / Bãi xe dày đặc:** Với nhóm từ **≥ 4 người** hoặc **≥ 4 xe** đứng san sát, che khuất lẫn nhau trên 50% và không thể tách box từng cá thể (`img_019`, `img_021`, `img_030`, `img_032`): tách riêng các cá thể đứng ở hàng đầu tiên nhìn rõ mặt/thân, phần cụm dày đặc phía sau gom chung vào **1 box `ignore_region`**.

---

## 4. Ca khó (Phân tích từ 40 ảnh mẫu)

| Ảnh | Tình huống thực tế | Quyết định đề xuất | Lý do & Nguyên tắc áp dụng |
|---|---|---|---|
| `img_001` | Xe bán tải Suzuki thùng hở đỗ ven đường; nhiều người ngồi uống nước phía sau bàn quán. | Gán xe là `truck` (`truncated=yes`). Gán từng người ngồi sau bàn là `person` với `occluded_level=2`. | Xe bán tải có thùng chở hàng tách biệt cabin nên xếp vào `truck`. Người bị bàn và xe che phần thân dưới nhưng thấy rõ đầu/vai. |
| `img_002` | Xe đầu kéo chuyên dụng chở 7 chiếc ô tô con trên 2 tầng sàn. | Gán 1 box `truck` cho xe đầu kéo, đồng thời gán 7 box `car` riêng biệt cho 7 chiếc ô tô trên sàn (với `occluded_level` tương ứng từ `1` đến `2`). | Các ô tô trên sàn có đầy đủ đặc trưng hình ảnh của `car`, mô hình cần học nhận diện được `car` ở nhiều góc độ khác nhau. |
| `img_003` | Người dắt xe đạp trên vỉa hè và người gánh quang gánh bán mít. | Chỉ gán `person` cho các cá nhân nhìn thấy; **không gán** xe đạp và **không bao** quang gánh vào box `person`. | Bộ nhãn không có lớp `bicycle`; gánh hàng là vật dụng rời, kéo rộng box sẽ làm sai lệch tỷ lệ khung hình của `person`. |
| `img_004`, `img_016`, `img_035` | Xe tuk-tuk 3 bánh chở khách/chở hàng chất cao trên nóc (`img_035`). | Gán xe tuk-tuk vào lớp `motorcycle`; tài xế ngồi bên trong gán riêng `person` (`occluded_level=1` hoặc `2`). Box xe tuk-tuk trong `img_035` **không** kéo lên bao trùm đống bao tải hàng trên nóc. | Tuk-tuk là xe cơ giới 3 bánh thuộc nhóm xe gắn máy; bao tải hàng là vật thể biến dạng nhất thời, không thuộc kết cấu xe. |
| `img_005`, `img_026`, `img_040` | Góc chụp cận cảnh người đi xe máy: chỉ thấy đầu/vai người (`img_005`) hoặc chỉ thấy gương chiếu hậu xe máy ở góc trái (`img_040`). | Vẫn gán `person` và `motorcycle` với `truncated=yes`, `occluded_level=2` hoặc `3`. | Vật thể nằm ngay tiền cảnh, dù bị cắt mép nhưng đặc trưng mũ bảo hiểm/gương xe máy rất rõ ràng. |
| `img_009`, `img_031`, `img_037` | Xe buýt chở khách cỡ trung/lớn (29–45 chỗ) xuất hiện trên đường. | Đề xuất thêm lớp **`bus`** (nếu nhóm không đồng ý thêm lớp thì tạm gộp vào `truck` và thống nhất chung). | Xe buýt có kích thước và hình dáng hộp chữ nhật lớn khác hẳn `car` hay `van` nhỏ, tách lớp `bus` giúp mô hình không bị nhầm lẫn. *(Chưa chốt, cần họp Pha 2)*. |
| `img_011`, `img_017`, `img_027`, `img_028` | Tượng Predator khổng lồ (`img_011`), tượng đồng người cưỡi ngựa (`img_017`), bia mộ hình xe bán tải (`img_027`), tượng ngựa trên thùng xe (`img_028`). | **Không gán** bất kỳ tượng/bia mộ nào; chỉ gán người thật phía xa (`img_011`) và xe bán tải thật (`img_028`). | Mô hình phát hiện giao thông chỉ học vật thể sống/phương tiện thật, tránh phanh gấp hoặc báo động giả trước tượng đài bên đường. |
| `img_013`, `img_031` | Mặt đường nhựa ướt ban đêm phản chiếu hình bóng người và đèn xe. | Vẽ box `person` / `car` **chỉ ôm phần vật thể thật phía trên mặt đường**, cắt ngang tại điểm tiếp xúc bàn chân/lốp xe với mặt đất. | Tránh tạo ra bounding box dài gấp đôi chiều cao thực tế của người/xe. |
| `img_014` | Chụp phơi sáng chậm: 2 người đi bộ rõ nét ở giữa, xung quanh là các vệt xe máy nhòe mờ kéo dài. | Gán 2 `person` rõ nét ở giữa; **bỏ qua** các vệt chuyển động mờ không rõ hình dạng xung quanh. | Gán nhãn trên vùng nhòe mất biên dạng sẽ đưa nhiễu (noise) vào tập huấn luyện. |
| `img_018`, `img_030`, `img_031`, `img_040` | Biển quảng cáo in hình ứng cử viên (`img_018`), poster ca sĩ (`img_030`), tranh tường hoạt hình (`img_031`), ảnh dán kính taxi (`img_040`). | **Tuyệt đối không gán** các hình ảnh 2D này. | Các hình in trên mặt phẳng không phải người tham gia giao thông. |
| `img_019` | Xe diễu hành mô hình màu vàng chở người lái thật và nhân vật mascot chuột khổng lồ; đám đông đứng sau rào sắt. | Gán xe mô hình là `car` (có biển số thật), người lái là `person`; **không gán** mascot chuột; khoanh `ignore_region` cho dãy khán giả đứng dày đặc sau rào. | Mascot không mang hình thái người thật; đám đông khán giả bị rào chắn và chồng lấn nhiều lớp không thể tách box chính xác. |
| `img_020` | Ảnh chụp từ ghế lái ô tô nhìn qua gương chiếu hậu thấy một ô tô phía sau. | **Không gán** xe trong gương chiếu hậu và **không gán** mép cửa/gương của xe chủ. | Ảnh trong gương là ảnh ảo đảo chiều, gán nhãn sẽ làm sai lệch bối cảnh không gian 3D của mô hình. |
| `img_021`, `img_030`, `img_032` | Kẹt xe máy dày đặc (`img_021`), bãi giữ xe máy (`img_030`), hàng người chờ xe buýt (`img_032`). | Tách riêng các xe/người ở hàng ngoài cùng nhìn rõ hình dáng; gom cụm chồng lấn dày đặc phía sau vào `ignore_region`. | Cân bằng giữa việc tận dụng dữ liệu tiền cảnh và tránh làm giảm chất lượng nhãn ở vùng bị che khuất quá mức. |
| `img_024`, `img_038` | Mô hình vỏ xe màu vàng gắn trên nóc ô tô (`img_024`) và tủ kính trưng bày ô tô đồ chơi (`img_038`). | Gán các ô tô thật trong bảo tàng (`img_024`); **không gán** vỏ xe mô hình trên nóc và toàn bộ xe đồ chơi trong tủ kính (`img_038`). | Xe đồ chơi có kích thước và tỷ lệ chi tiết giả, không phải phương tiện giao thông thật. |
| `img_029` | Hai người đi xe máy và xe đạp cùng vác thanh kim loại dài 4–5m ngang vai. | Gán 2 `person` (ôm sát cơ thể từng người) và 1 `motorcycle` (ôm sát xe máy); **không** kéo box bao thanh kim loại. | Thanh kim loại là vật mang theo tách rời, nếu bao trùm sẽ làm 2 box `person` chồng lấn lên nhau gần 100%. |

---

## 5. QC và phiên bản

### 5.1. Quy trình kiểm soát chất lượng (QC)
- **Ngưỡng đạt chuẩn (Acceptance Criteria):**
  - Độ đồng thuận giữa các người gán (IAA): **Mean IoU ≥ 0.85** và tỷ lệ trùng khớp lớp (Class Agreement) **≥ 95%**.
  - Kiểm tra mẫu ngẫu nhiên cuối cùng (Pha 6): Tỷ lệ lỗi nghiêm trọng (bỏ sót vật thể rõ, gán nhầm vật giả, sai lớp) **≤ 5%**, không còn lỗi `guideline_gap` chưa giải quyết.
- **Cơ chế xử lý ca mơ hồ:**
  - Trong quá trình gán nhãn, nếu gặp trường hợp chưa có trong bảng Ca khó, người gán tick thuộc tính `can_review = true` và ghi chú số thứ tự ảnh (`img_0xx`).
  - Trưởng nhóm tổng hợp danh sách `can_review`, đưa ra thảo luận tại buổi họp **Pha 2** (chốt `v1.0`) và **Pha 5 Calibration** (chốt `v1.1`). Quyết định cuối cùng được cập nhật thành văn bản trước khi xuất bộ nhãn chính thức.

### 5.2. Lịch sử phiên bản

| Phiên bản | Ngày | Người thực hiện | Thay đổi chính | Lý do (ca minh họa) |
|---|---|---|---|---|
| `v0` | — | Giảng viên | Bản khởi đầu gồm 6 lớp cơ bản (`car`, `truck`, `van`, `motorcycle`, `person`, `ignore_region`) và 3 thuộc tính chưa định lượng. | Khung sườn ban đầu cho bài lab. |
| `v0.1` | 2026-11-16 | Bùi Lê Đình Văn (`2A202603124`) | - Bổ sung định nghĩa chi tiết, ví dụ và phản ví dụ cho từng lớp.<br>- Đề xuất thêm lớp `bus` tách khỏi `truck`/`van`.<br>- Định lượng hóa `occluded_level` theo mốc % diện tích (`<10%`, `10–35%`, `35–70%`, `≥70%`) và `truncated` (`<5%` biên).<br>- Bổ sung 6 quy tắc vẽ box (tách người-xe, hàng hóa cồng kềnh, vật giả/ảnh in/phản chiếu, xe chủ, đám đông `ignore_region`).<br>- Lập bảng phân tích 15 nhóm ca khó từ 40 ảnh dữ liệu. | Giải quyết toàn bộ các tình huống mơ hồ phát hiện khi rà soát 40 ảnh (`img_001` – `img_040`). |
