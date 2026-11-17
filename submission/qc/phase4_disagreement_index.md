# Pha 4 — chỉ mục ảnh bất đồng (dữ liệu trước calibration)

**Đây là danh sách ca máy gợi ý xem, không phải kết luận lỗi hay nhãn cuối.** Hai người có thể cùng sai; cờ `can_review` do 112 bật có chủ ý. Mọi quyết định về ảnh/box do nhóm nhìn ảnh gốc và ghi vào phiếu.

- 40 ảnh, 80/80 lượt gán; 37 ảnh bị cờ, 3 ảnh không bị cờ (`img_017.jpg, img_027.jpg, img_033.jpg`).
- 822 mục máy gợi ý; trong đó 275 cờ review. **Điểm ưu tiên tự động không phải mức độ lỗi đã xác nhận.**
- IAA trước calibration: 249 cặp box ghép, IoU TB 0.819 (ngưỡng 0.85), cùng lớp 97.2% (ngưỡng 95%). Box chưa ghép không vào mẫu số IAA.
- Bản HTML và ảnh chồng nhãn kèm `review_sheet.csv` nằm trong `work/phase4_team_packet.zip`; dòng chi tiết từng ca nằm trong `submission/qc/calibration_candidates.csv`.

| Ưu tiên | Ảnh | Người gán · số box | Bỏ sót/thừa | Sai lớp | IoU thấp | Thuộc tính | Ignore | Cờ review | Tổng mục |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | `img_021.jpg` | 042: 53 / 124: 41 | 20 | 0 | 22 | 33 | 2 | 21 | 98 |
| 2 | `img_032.jpg` | 112: 34 / 139: 3 | 35 | 0 | 1 | 0 | 0 | 33 | 69 |
| 3 | `img_003.jpg` | 112: 29 / 139: 7 | 24 | 1 | 6 | 6 | 0 | 27 | 64 |
| 4 | `img_038.jpg` | 124: 23 / 139: 0 | 23 | 0 | 0 | 0 | 0 | 8 | 31 |
| 5 | `img_030.jpg` | 042: 22 / 124: 11 | 12 | 0 | 6 | 8 | 5 | 7 | 38 |
| 6 | `img_039.jpg` | 042: 22 / 112: 21 | 3 | 0 | 13 | 8 | 2 | 20 | 46 |
| 7 | `img_005.jpg` | 112: 13 / 124: 16 | 11 | 2 | 2 | 2 | 0 | 18 | 35 |
| 8 | `img_008.jpg` | 042: 25 / 139: 5 | 20 | 0 | 4 | 2 | 0 | 0 | 26 |
| 9 | `img_011.jpg` | 112: 16 / 124: 11 | 7 | 1 | 1 | 0 | 0 | 24 | 33 |
| 10 | `img_034.jpg` | 124: 24 / 139: 16 | 8 | 0 | 8 | 7 | 0 | 8 | 31 |
| 11 | `img_025.jpg` | 042: 24 / 124: 17 | 5 | 0 | 7 | 8 | 4 | 6 | 30 |
| 12 | `img_035.jpg` | 112: 15 / 124: 12 | 5 | 1 | 4 | 0 | 0 | 17 | 27 |
| 13 | `img_006.jpg` | 042: 19 / 139: 9 | 12 | 0 | 3 | 5 | 0 | 0 | 20 |
| 14 | `img_023.jpg` | 112: 14 / 139: 4 | 10 | 0 | 1 | 2 | 0 | 9 | 22 |
| 15 | `img_016.jpg` | 124: 14 / 139: 5 | 9 | 0 | 2 | 4 | 0 | 6 | 21 |
| 16 | `img_031.jpg` | 042: 19 / 124: 13 | 10 | 0 | 3 | 1 | 4 | 2 | 20 |
| 17 | `img_019.jpg` | 124: 8 / 139: 5 | 9 | 0 | 1 | 1 | 0 | 6 | 17 |
| 18 | `img_009.jpg` | 042: 18 / 124: 12 | 5 | 0 | 2 | 7 | 1 | 4 | 19 |
| 19 | `img_010.jpg` | 112: 8 / 139: 8 | 2 | 0 | 5 | 4 | 0 | 8 | 19 |
| 20 | `img_029.jpg` | 112: 9 / 139: 6 | 3 | 0 | 2 | 4 | 0 | 9 | 18 |
| 21 | `img_001.jpg` | 112: 7 / 124: 8 | 1 | 0 | 2 | 7 | 0 | 9 | 19 |
| 22 | `img_036.jpg` | 042: 10 / 139: 2 | 10 | 0 | 0 | 0 | 0 | 0 | 10 |
| 23 | `img_040.jpg` | 124: 8 / 139: 4 | 4 | 1 | 1 | 4 | 0 | 4 | 14 |
| 24 | `img_014.jpg` | 042: 2 / 124: 7 | 5 | 0 | 1 | 0 | 0 | 4 | 10 |
| 25 | `img_002.jpg` | 042: 10 / 139: 9 | 1 | 0 | 8 | 4 | 0 | 0 | 13 |
| 26 | `img_013.jpg` | 112: 6 / 139: 4 | 2 | 0 | 3 | 1 | 0 | 4 | 10 |
| 27 | `img_015.jpg` | 042: 6 / 112: 6 | 0 | 0 | 1 | 6 | 0 | 5 | 12 |
| 28 | `img_024.jpg` | 042: 7 / 124: 8 | 2 | 0 | 0 | 4 | 1 | 1 | 8 |
| 29 | `img_007.jpg` | 112: 4 / 124: 2 | 2 | 0 | 1 | 0 | 0 | 4 | 7 |
| 30 | `img_018.jpg` | 042: 5 / 124: 6 | 1 | 0 | 1 | 5 | 0 | 1 | 8 |
| 31 | `img_004.jpg` | 042: 4 / 124: 5 | 1 | 0 | 1 | 3 | 0 | 2 | 7 |
| 32 | `img_037.jpg` | 042: 2 / 112: 3 | 1 | 1 | 0 | 0 | 0 | 2 | 4 |
| 33 | `img_012.jpg` | 042: 5 / 139: 3 | 1 | 0 | 1 | 1 | 1 | 0 | 4 |
| 34 | `img_026.jpg` | 112: 3 / 139: 3 | 0 | 0 | 0 | 2 | 0 | 3 | 5 |
| 35 | `img_028.jpg` | 042: 2 / 112: 2 | 0 | 0 | 0 | 2 | 0 | 2 | 4 |
| 36 | `img_020.jpg` | 042: 1 / 112: 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 |
| 37 | `img_022.jpg` | 112: 1 / 139: 1 | 0 | 0 | 1 | 0 | 0 | 1 | 2 |

## Vùng cần giải thích cụ thể (chưa chốt đúng/sai)

- `img_016.jpg`: 124 xác nhận hai box gần trùng khác lớp không do gán trùng; nhóm cần xem vùng #65–66 trên ảnh gốc.
- `img_040.jpg`: 124 xác nhận hai box gần trùng khác lớp không do gán trùng; nhóm cần xem vùng #242–243 trên ảnh gốc.
- `img_032.jpg`: 112: 34 box; 139: 3 box. Điều phối xác nhận đây là bất đồng có căn cứ, chưa chốt cách gán.
- `img_038.jpg`: 124: 23 box; 139: 0 box. Điều phối xác nhận đây là bất đồng có căn cứ, chưa chốt cách gán.
- `img_024.jpg`: Đối chiếu R-04: không gán vật trên xe ở ảnh này; xe khác nếu có phải xét riêng.
- `img_035.jpg`: Đối chiếu R-05: tuk-tuk là motorcycle; box không ôm hàng rời.
- `img_020.jpg`: Đối chiếu R-04: không gán xe chỉ hiện trong gương.
- `img_002.jpg`: Đối chiếu R-03: xe thật được chở xét riêng nếu đủ điều kiện.

## Cách dùng trong họp

1. Mở ảnh gốc và ảnh chồng box trong gói HTML; tìm `case_id` của ảnh trong `calibration_candidates.csv`.
2. Ghi quan sát, R-xx, kết luận và người chốt vào cột trống của `review_sheet.csv`. Khi cần nhiều quyết định/ảnh, đối chiếu từng `case_id` trong bảng gốc, không gộp nhầm các vật.
3. Kiểm ca tham chiếu coach để bắt trường hợp hai người đồng thuận nhưng cùng sai. Giữ nguyên IAA trước calibration.
4. Chỉ sau khi nhóm chốt thì sang Pha 5 và điền quyết định chính thức bằng workflow của repo; không sửa bốn file nhãn độc lập để làm đẹp số.

## Nguồn và phiên bản

Sinh từ `submission/qc/{assignment.csv,image_scores.csv,calibration_candidates.csv,iaa_summary.json}` và bốn COCO gốc. Các mã băm đầu vào nằm trong `iaa_summary.json`; script tạo gói từ chối chạy nếu đầu vào đổi.
