# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 2
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 376 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 376 | 0.974 | 97.6% | 83.0% | 97.3% | 26/38 | 0 | ĐẠT |
| 002|036 | 40 | 376 | 0.974 | 97.6% | 83.0% | 97.3% | 26/38 | 0 | ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 171 ({'iou_thap': 23, 'sai_thuoc_tinh': 70, 'bo_sot_thua': 64, 'co_can_review': 1, 'bat_dong_ignore': 4, 'sai_lop': 9}), trên 30 ảnh cần calibration.
