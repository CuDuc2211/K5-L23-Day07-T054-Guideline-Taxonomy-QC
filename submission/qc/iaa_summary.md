# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 249 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 249 | 0.819 | 97.2% | 46.6% | 93.6% | 226/42 | 0 | CHƯA ĐẠT |
| 042|112 | 6 | 29 | 0.821 | 96.6% | 48.3% | 93.1% | 2/3 | 0 | CHƯA ĐẠT |
| 042|124 | 9 | 96 | 0.825 | 100.0% | 28.1% | 97.9% | 44/20 | 0 | CHƯA ĐẠT |
| 042|139 | 5 | 26 | 0.785 | 100.0% | 53.8% | 92.3% | 42/2 | 0 | CHƯA ĐẠT |
| 112|124 | 5 | 39 | 0.873 | 89.7% | 84.6% | 92.3% | 16/10 | 0 | CHƯA ĐẠT |
| 112|139 | 9 | 32 | 0.786 | 96.9% | 43.8% | 90.6% | 72/4 | 0 | CHƯA ĐẠT |
| 124|139 | 6 | 27 | 0.792 | 96.3% | 51.9% | 85.2% | 50/3 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 822 ({'bo_sot_thua': 265, 'co_can_review': 275, 'iou_thap': 114, 'sai_thuoc_tinh': 141, 'sai_lop': 7, 'bat_dong_ignore': 20}), trên 37 ảnh cần calibration.
