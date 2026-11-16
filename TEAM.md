# Thông tin nhóm

- Tên nhóm: Nhom036-002
- Mã thành viên (ba chữ số): 036, 002
- Người điều phối: 036
- Người review bản cuối: 002
- Commit chốt bài (điền ở kênh nộp sau khi commit): 5770d87 (cập nhật mã commit cuối sau khi push)

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: 80 / 80 (mỗi thành viên gán độc lập 40/40 ảnh)
- IAA trước calibration (kèm mẫu số): Mean IoU = 0.974, đồng thuận lớp = 97.6% (367/376), đồng thuận occluded_level = 83.0% (312/376), đồng thuận truncated = 97.3% (366/376) trên mẫu số 376 cặp box ghép được
- Số ảnh phải calibration: 30 / 40 (171 ca bất đồng đã giải quyết trong calibration_decisions.csv)
- Vòng QC và số ảnh đã duyệt / cả lô: Vòng 1 (chế độ full) — đã duyệt 40 / 40 ảnh
- Tỉ lệ lỗi: 0.0 % theo ảnh (0/40), 0.0 % theo đối tượng (0/440), 0 vật bỏ sót
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: Release — Độ phủ đạt 80/80, IAA đạt chuẩn (IoU 0.974, Class 97.6%), 171/171 ca calibration đã đóng và kiểm lại trên 40/40 ảnh đạt 0.0% lỗi.
