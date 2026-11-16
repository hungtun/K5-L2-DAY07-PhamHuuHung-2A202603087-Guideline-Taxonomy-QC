# Báo cáo QC — Nhóm 036-002

Báo cáo tổng hợp kết quả kiểm soát chất lượng (QC) và nghiệm thu bàn giao bộ nhãn Bounding Box 2D trên 40 ảnh đường phố (Day 07).

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Huấn luyện và đánh giá mô hình phát hiện người và phương tiện giao thông đường bộ 2D (Object Detection); bộ dữ liệu gồm 40 ảnh tuyển chọn chứa nhiều ca biên (tượng, ma-nơ-canh, phản chiếu, xe chở xe, tuk-tuk, xích lô) nên chỉ dùng cho mục đích thực hành đặc tả & QC, chưa đại diện đầy đủ cho phân phối dữ liệu vận hành thực tế.
- Nguồn và phép sử dụng dữ liệu: Toàn bộ 40 ảnh (`img_001.jpg` – `img_040.jpg`) có nguồn từ Wikimedia Commons với giấy phép mở (`CC BY`, `CC BY-SA`, `CC0`, `Public domain`), giữ nguyên ghi công tác giả và liên kết gốc tại `data/ATTRIBUTION.csv`.
- Manifest và checksum nhãn cuối: Hồ sơ bàn giao tại `submission/qc/batch_manifest.json`; file nhãn cuối `submission/qc/labels_calibrated.json` có SHA-256 `d43da68b23926084eaf66d0321cf70384876bc763f2ec21d0066b2cf4df2e12a` (bản CSV `submission/qc/labels_calibrated.csv` có SHA-256 `486cd2bf8b24002493ceff5efeff8fe94bc47bdd7ae73b3cf0f5ae107f5e2e87`).
- Guideline và taxonomy khi gán / khi bàn giao: Khi gán độc lập (Pha 3) sử dụng `submission/guidelines/guideline_v1.0_agreed.md` và `submission/taxonomy.json` (`version: v1.0`); khi bàn giao (Pha 5–6) sử dụng `submission/guidelines/guideline_v1.1_calibrated.md` (thay đổi kiểu `editorial` ghi nhận tại `submission/guideline_change.json`, giữ nguyên schema `submission/taxonomy.json` `v1.0`). Ghi chú kỹ thuật theo `RULES.md`: Trong Pha 2, khi dán `cvat/labels_generated.json` (do `scripts/make_cvat_labels.py` gốc của BTC sinh ra) vào tab Raw của CVAT, giao diện CVAT báo 2 lỗi chặn tạo Project là `Attribute: "can_review": attribute values must be a non-empty array` (do `"values": []`) và `Label "car": unknown label type "undefined"` (do thiếu `"type": "rectangle"`); nhóm đã sửa 2 dòng tương ứng tại `scripts/make_cvat_labels.py` (thêm `"type": "rectangle"` cho từng lớp và đặt `"values": ["false"]` cho thuộc tính kiểu `checkbox`) rồi chạy lại để sinh `cvat/labels_generated.json` hợp lệ.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80 / 80 lượt gán (2 thành viên `036` và `002`, mỗi người gán độc lập 40/40 ảnh theo `submission/qc/assignment.csv`, đạt độ phủ 100%).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: Trên mẫu số **376 cặp box ghép được** ($\text{IoU} \ge 0.50$), đạt **Mean IoU = 0.974** (vượt ngưỡng $\ge 0.85$), **đồng thuận lớp = 97.6%** (367/376 cặp cùng lớp, vượt ngưỡng $\ge 95\%$), đồng thuận `occluded_level` = 83.0% (312/376), đồng thuận `truncated` = 97.3% (366/376).
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: Có 26 box của `002` và 38 box của `036` chưa ghép cặp (tổng 64 ca `bo_sot_thua`), 0 box bị bỏ qua do vùng ignore, và 4 ca bất đồng vùng `ignore_region` (`bat_dong_ignore` tại `img_009.jpg`, `img_011.jpg`, `img_013.jpg`, `img_025.jpg`). Tổng cộng có 171 ca bất thường trên 30/40 ảnh cần đưa vào Calibration.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Trường hợp điển hình là tượng đồng người cưỡi ngựa (`img_017.jpg`), tượng đá xe bán tải (`img_027.jpg`) hoặc ma-nơ-canh (`img_033.jpg`) — nếu cả 2 người cùng vẽ box `person`/`truck` thì IAA trên ảnh đó đạt 1.00 nhưng vẫn sai bản chất bài toán; nhóm đã kiểm chứng bằng quy tắc `R-04` (để trống 0 box cho cả 5 ảnh đối chứng `img_017`, `020`, `027`, `033`, `038`).

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: Toàn bộ 171/171 ca (`C001` – `C171`) trên 30 ảnh bị gắn cờ đã được người điều phối (`036`) chốt quyết định theo các quy tắc `R-01`, `R-02`, `R-06`, `R-09`, `R-10` trong `submission/qc/calibration_decisions.csv`, lưu nhãn đã chuẩn hóa vào `submission/qc/labels_calibrated_597304d3dbf95cf0.json` và được thành viên `002` kiểm lại, xác nhận đạt 100%.
- Tác động v1.1 lên nhãn và ảnh: Bản `guideline_v1.1_calibrated.md` là cập nhật `editorial` (làm rõ câu chữ cho ngưỡng che khuất 50%, tách riêng 2 người ngồi trên 1 xe máy ở `img_005.jpg`, `img_022.jpg`, chuẩn hóa lớp `truck` cho xe bán tải ở `img_012.jpg`, `img_028.jpg`, lớp `car` cho xe wagon ở `img_024.jpg`, và lớp `motorcycle` cho auto-rickshaw ở `img_035.jpg`), phạm vi sửa áp dụng trên 30 ảnh bị gắn cờ (`pham_vi=anh`) và gộp cùng 10 ảnh đồng thuận tuyệt đối thành 40 ảnh cuối (442 annotations: 440 đối tượng + 2 vùng `ignore_region`).
- Tham chiếu bằng chứng khi Rework: Không phát sinh Rework sang vòng 2 vì ngay tại Vòng QC 1 (sau khi đã thực hiện Calibration kỹ lưỡng ở Pha 5), toàn bộ 40/40 ảnh đã đạt 0% lỗi.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: 002 (ngày 2026-11-16)
- Đã duyệt / cả lô: 40 / 40 ảnh (chế độ `full` duyệt toàn bộ lô: gồm 30/30 ảnh nhóm `ca_kho` và 10/10 ảnh nhóm `con_lai`).
- Ảnh lỗi / ảnh đã duyệt: 0 / 40 = 0.0% (đạt ngưỡng nghiệm thu $\le 2.0\%$).
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0 / 440 = 0.0% (trên tổng số 440 box đối tượng thuộc 5 lớp chính và 2 vùng `ignore_region`).
- Vật bỏ sót, ảnh và vùng: 0 vật bỏ sót trên cả 40 ảnh đã duyệt.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: Nhóm `ca_kho` đã duyệt 30/30 ảnh (0/30 lỗi = 0.0%), nhóm `con_lai` đã duyệt 10/10 ảnh (0/10 lỗi = 0.0%), không còn lỗi lặp hệ thống hay cờ `can_review` tồn đọng (chi tiết tại `submission/qc/reviews/round-01/qc_review.csv` và `qc_report_auto.md`).
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Script `validate_labels.py` chặn tự động được lỗi cấu trúc kỹ thuật (như thiếu thuộc tính `occluded_level`, tọa độ bbox vượt ngoài kích thước ảnh, thiếu ảnh trong phân công hoặc vật có chiều cao `< 20 px`); trong khi lỗi ngữ nghĩa thị giác (như vẽ nhầm box `person` lên tượng đồng ở `img_017.jpg`, gộp 2 người trên xe máy thành 1 box ở `img_005.jpg`, hoặc phân biệt xe bán tải lắp nắp thùng `img_037.jpg` là `truck` thay vì `van`) bắt buộc cần con người mở ảnh kiểm tra bằng mắt.

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Bộ nhãn đạt đầy đủ tiêu chuẩn nghiệm thu của lab (độ phủ gán độc lập 80/80, IAA trước calibration đạt Mean IoU 0.974 và đồng thuận lớp 97.6%, 171/171 ca bất đồng đã đóng có bằng chứng kiểm lại, duyệt QC 100% lô 40/40 ảnh với tỷ lệ lỗi 0/40 = 0.0% $\le 2\%$); bàn giao cho Lab Coach / nhóm phát triển mô hình Detection sử dụng trong phạm vi thực hành huấn luyện và đánh giá mô hình phát hiện người & phương tiện 2D.
- Nếu còn việc: Không còn hạng mục tồn đọng (0 guideline gap, 0 data problem).
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: Truy vết ca `C094` trên ảnh `img_012.jpg` — trong `submission/qc/calibration_candidates.csv`, `002` gán `car` (`[292,247,668,393]`) còn `036` gán `truck` (`[293,247,666,393]`); đối chiếu `submission/qc/calibration_decisions.csv` dòng `C094`, người chốt `036` áp dụng quy tắc `R-01` & định nghĩa lớp Mục 2.1 trong `submission/guidelines/guideline_v1.1_calibrated.md` (xe bán tải có thùng chở hàng phía sau phải gán `truck`), cập nhật vào `submission/qc/labels_calibrated_597304d3dbf95cf0.json` và bản cuối `submission/qc/labels_calibrated.json` (`image_id=12`, box `[292.5, 247.0, 667.0, 393.0]`, `category_id=2` tức `truck`), được reviewer `002` xác nhận `khong` lỗi tại `submission/qc/reviews/round-01/qc_review.csv`.
