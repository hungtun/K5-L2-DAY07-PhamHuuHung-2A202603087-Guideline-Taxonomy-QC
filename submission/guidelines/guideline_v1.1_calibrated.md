# Guideline gán nhãn sau Calibration — v1.1 (Calibrated)

> Bản đặc tả hoàn chỉnh của nhóm (`036`, `002`) sau khi họp Calibration ở Pha 5, đối chiếu 171 ca bất đồng từ `submission/qc/calibration_candidates.csv` trên 30 ảnh bị gắn cờ (`flagged_images.txt`). Thay đổi thuộc loại **editorial** (`submission/guideline_change.json`): làm rõ câu chữ và ranh giới áp dụng cho các quy tắc hiện có, giữ nguyên ngữ nghĩa lớp và thuộc tính của `v1.0`.

- **Phiên bản:** `v1.1`
- **Ngày cập nhật:** 2026-11-16
- **Thành viên chốt & kiểm lại:** `036` (Điều phối / Chốt), `002` (Reviewer / Kiểm lại)
- **Taxonomy tương ứng:** `submission/taxonomy.json` (`version: "v1.0"`)

---

## 1. Mục tiêu và Phạm vi áp dụng

Bộ nhãn Bounding Box 2D phục vụ huấn luyện mô hình **phát hiện người và phương tiện cơ giới đường bộ (2D Object Detection)** cho bài toán giám sát giao thông và hỗ trợ lái xe (ADAS) trong điều kiện đường phố đa dạng.

- **Nguyên tắc cốt lõi:**
  1. Chỉ gán các **đối tượng thật (3D thực thể)** thuộc 5 lớp mục tiêu (`car`, `truck`, `van`, `motorcycle`, `person`) hoặc vùng bỏ qua (`ignore_region`).
  2. Mọi box trong bản nhãn cuối (`labels_calibrated.json`) đã được giải quyết dứt điểm cờ `can_review` (`can_review = false`).

---

## 2. Lớp và Thuộc tính (Khớp `submission/taxonomy.json` v1.0)

### 2.1. Định nghĩa 6 lớp (Làm rõ ranh giới sau Calibration)

| Lớp | Định nghĩa chuẩn (v1.1) | Ví dụ điển hình & Đính chính sau Calibration | Phản ví dụ / Loại trừ |
|---|---|---|---|
| `car` | Ô tô con chở người từ 4–7 chỗ (sedan, hatchback, coupe, SUV, crossover, station wagon, xe độ chạy đường thật có biển số). | Sedan/SUV (`img_015.jpg`, `img_040.jpg`), xe AMC Pacer wagon (`img_024.jpg` — đính chính ca `C118`–`C120`: wagon chở người là `car`, không phải `van`), 8 ô tô con trên xe lồng (`img_002.jpg`), xe vàng diễu hành (`img_019.jpg`). | Xe bán tải pickup (`truck`), xe van hộp kín (`van`), mô hình ô tô đồ chơi (`img_038.jpg`). |
| `truck` | Xe cơ giới 4 bánh trở lên chuyên chở hàng hóa hoặc có thùng hàng tách biệt với cabin (xe tải thùng kín/bạt, đầu kéo/rơ-moóc, xe tải nhỏ kei-truck, và **mọi dòng xe bán tải pickup** kể cả có lắp nắp thùng). | Bán tải pickup (`img_001.jpg`, `img_012.jpg` ca `C094`, `img_028.jpg` ca `C131`), bán tải lắp nắp thùng kín (`img_037.jpg`), xe tải chở hàng (`img_007.jpg`, `img_036.jpg`), xe lồng chở ô tô (`img_002.jpg`). | Tượng đá hình xe bán tải (`img_027.jpg` — vật giả), SUV/wagon liền khối (`img_024.jpg` -> `car`). |
| `van` | Xe thân liền khối dạng hộp kín (minivan, MPV cỡ lớn, xe tải van liền khoang không tách rời đầu cabin và thùng sau). | Xe van/minivan lưu thông hoặc đỗ trên phố (`img_005.jpg`, `img_011.jpg`, `img_016.jpg`, `img_031.jpg`, `img_040.jpg`). | Xe wagon 2 cửa (`img_024.jpg` -> `car`), xe bán tải lắp nắp thùng (`img_037.jpg` -> `truck`), xe buýt cỡ lớn (`img_032.jpg` -> ngoài phạm vi). |
| `motorcycle` | Phương tiện cơ giới 2 bánh hoặc 3 bánh gắn động cơ và điều khiển bằng tay lái ghi-đông (xe máy số, xe tay ga, mô tô phân khối lớn, mô tô 3 bánh sidecar, xe tuk-tuk / auto-rickshaw). | Xe máy đang chạy/đỗ (`img_005.jpg`, `img_008.jpg`, `img_026.jpg`, `img_030.jpg`), mô tô lễ hội (`img_011.jpg`), mô tô sidecar (`img_022.jpg`), tuk-tuk & auto-rickshaw (`img_004.jpg`, `img_035.jpg` ca `C161`–`C162`). | Người ngồi trên xe (tách riêng lớp `person`), xe đạp (`img_003.jpg`) và xích lô đạp chân (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`). |
| `person` | Con người thật (đi bộ, đứng, ngồi quán ăn, người điều khiển hoặc hành khách ngồi trên xe máy, xích lô, tuk-tuk, xe mui trần khi nhìn rõ phần thân trên/đầu). | Người qua đường (`img_014.jpg`), người đi dạo (`img_025.jpg`), người ngồi quán (`img_001.jpg`), người đi xe máy (`img_005.jpg`, `img_026.jpg`), người đạp và khách ngồi xích lô (`img_006.jpg`). | Tượng đồng (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh trên biển quảng cáo (`img_018.jpg`), búp bê (`img_038.jpg`), nhân vật giả trang trí (`img_019.jpg`), mô hình Predator (`img_011.jpg`). |
| `ignore_region` | Vùng bỏ qua (không tính là vật thể): dùng cho đối tượng thật có chiều cao phần nhìn thấy `< 20 px` hoặc đám đông người/xe ở xa chồng lấn không thể tách từng cá thể. | Đám đông khán giả sau rào chắn (`img_019.jpg`), cụm người/xe ở xa hoặc quá nhỏ `< 20 px` (`img_008.jpg`, `img_009.jpg`, `img_011.jpg`, `img_013.jpg`, `img_025.jpg`). | Không dùng `ignore_region` cho vật giả (tượng, ma-nơ-canh, biển quảng cáo, đồ chơi) hoặc phương tiện ngoài taxonomy cỡ lớn. |

---

### 2.2. Thuộc tính và Đính chính quy ước sau Calibration

| Thuộc tính | Kiểu | Giá trị & Mặc định | Quy ước định lượng chuẩn hóa (v1.1) |
|---|---|---|---|
| `occluded_level` | `select` | `"0"`, `"1"` (Mặc định: `"0"`) | Đánh giá theo mốc **1/2 (`50%`) diện tích đối tượng**:<br/>- **`0` (Thấy rõ / Che nhẹ `< 50%`):** Nhìn thấy từ một nửa đối tượng trở lên. *Làm rõ sau Calibration:* Người cầm lái xe máy ở tiền cảnh nhìn thấy toàn bộ đầu, thân trên và đùi/chân bên gần (`img_005.jpg`, `img_006.jpg`, `img_008.jpg`, `img_010.jpg`) được chuẩn hóa về `0`.<br/>- **`1` (Bị che nhiều `>= 50%`):** Bị vật khác che mất từ một nửa diện tích trở lên (người ngồi sau xe máy bị che khuất thân, người ngồi trong tuk-tuk/xích lô chỉ lộ đầu-vai, xe bị xe khác che quá nửa). |
| `truncated` | `select` | `"no"`, `"yes"` (Mặc định: `"no"`) | Đánh giá đối tượng bị cắt bởi 4 cạnh biên của ảnh:<br/>- **`no`:** Nằm trọn trong ảnh (cách mép ảnh $> 4\text{ px}$ hoặc chỉ chạm nhẹ mép mà không mất bộ phận).<br/>- **`yes`:** Tọa độ box chạm sát mép ảnh ($\le 4\text{ px}$ từ biên trái/phải/trên/dưới) và bị khung hình cắt mất một phần cấu trúc đối tượng (`img_005.jpg`, `img_012.jpg`, `img_016.jpg`, `img_030.jpg`). |
| `can_review` | `checkbox` | `false`, `true` (Mặc định: `false`) | Trong bản nhãn cuối sau Calibration (`labels_calibrated.json`), toàn bộ các ca mơ hồ (như `C013` ở `img_003.jpg`) đã được nhóm chốt quyết định nên `can_review = false` trên 100% đối tượng. |

---

## 3. Hệ thống Quy tắc vẽ box (`R-01` – `R-10`, làm rõ tại v1.1)

- **`R-01` (Biên box ôm sát phần nhìn thấy — Visible Tight Box):**
  - Box ôm sát các pixel thực sự nhìn thấy của đối tượng. Với xe máy đỗ/chạy sát nhau (`img_003.jpg`, `img_005.jpg`, `img_008.jpg`, `img_009.jpg`, `img_030.jpg`), cần tách rõ biên bánh xe và tay lái của từng chiếc, không kéo lấn sang xe bên cạnh (khắc phục 23 ca `iou_thap`).
- **`R-02` (Tách riêng Người và Xe máy / Phương tiện hở):**
  - Luôn vẽ box riêng cho `person` và box riêng cho `motorcycle`. Khi có 2 người ngồi trên 1 xe (`img_005.jpg`, `img_022.jpg`, `img_026.jpg`), tách đủ 2 box `person` riêng biệt, không gộp 2 người vào 1 box `person` chung (khắc phục ca `C025`/`C028` ở `img_005.jpg` và `C110` ở `img_022.jpg`).
- **`R-03` (Quy tắc cho người bên trong phương tiện có mui):**
  - Không gán `person` trong cabin kính kín của `car`, `truck`, `van`; có gán `person` trên xe máy, xích lô, tuk-tuk hở, xe mui trần khi nhìn rõ đầu và thân trên.
- **`R-04` (Loại trừ vật giả, tượng, ma-nơ-canh, biển quảng cáo, đồ chơi, ảnh phản chiếu):**
  - Giữ nguyên 0 box cho 5 ảnh đối chứng (`img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg`), không gán mô hình Predator (`img_011.jpg`), hình người trên biển quảng cáo (`img_018.jpg`), chuột giả (`img_019.jpg`), tượng ngựa (`img_028.jpg`).
- **`R-05` (Loại trừ xe chủ — Ego Vehicle):**
  - Không gán gương chiếu hậu hay thân xe của người chụp (`img_020.jpg`, `img_033.jpg`).
- **`R-06` (Quy tắc kích thước tối thiểu `< 20 px` và Đám đông — `ignore_region`):**
  - Mọi đối tượng thật có chiều cao `< 20 px` hoặc cụm người/xe ở xa chồng lấn được gom vào `ignore_region`. Đồng bộ các vùng `ignore_region` ở `img_009.jpg` (`C074`), `img_011.jpg` (`C087`), `img_013.jpg` (`C097`), `img_025.jpg` (`C123`).
- **`R-07` (Quy tắc phương tiện ngoài Taxonomy — Xe đạp, Xích lô, Xe buýt):**
  - Không gán xe đạp (`img_003.jpg`), xích lô (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`), xe buýt cỡ lớn (`img_032.jpg`, `img_037.jpg`); vẫn gán đầy đủ `person` và các xe cơ giới thuộc taxonomy xung quanh.
- **`R-08` (Quy tắc Xe chở xe — Car Carrier `img_002.jpg`):**
  - 1 box `truck` cho xe tải lồng + 8 box `car` cho 8 xe con trên giàn + 1 box `person` đứng cạnh đuôi xe (đồng nhất `occluded_level` cho ca `C005`).
- **`R-09` (Quy tắc hàng hóa gắn trên xe máy `img_029.jpg` và Sidecar `img_022.jpg`):**
  - `img_022.jpg`: 1 box `motorcycle` bao cả thân xe và thùng sidecar (khắc phục ca `C111` nơi 1 người quên bao phần sidecar).
  - `img_029.jpg`: 1 box `motorcycle` ôm phần thân/bánh xe máy lộ ra dưới kiện hàng + 1 box `person`.
- **`R-10` (Quy tắc cho Xe 3 bánh có động cơ Tuk-tuk / Auto-rickshaw `img_004.jpg`, `img_035.jpg`):**
  - Gán các xe tuk-tuk / auto-rickshaw 3 bánh vào `motorcycle` (khắc phục ca `C161`, `C162` ở `img_035.jpg`).

---

## 4. Tóm tắt kết quả xử lý 171 ca Calibration (Pha 5)

| Nhóm bất đồng (`issue_type`) | Số ca | Nguyên nhân chính | Quyết định chuẩn hóa ở v1.1 (`calibration_decisions.csv`) |
|---|---:|---|---|
| `sai_thuoc_tinh` | 70 | Lệch đánh giá `occluded_level` (`0` vs `1`) ở các ca bị che mấp mé 30–50% và quên bật `truncated=yes` ở các box chạm mép ảnh. | Chuẩn hóa theo `R-01`: chỉ đặt `occluded_level=1` khi bị che $\ge 50\%$; bật `truncated=yes` cho mọi box bị cắt ở biên ảnh. |
| `bo_sot_thua` | 64 | Một người bỏ sót các xe/người ở trung cảnh/hậu cảnh (`img_003`, `006`, `008`, `011`, `030`, `032`) hoặc gộp 2 người trên 1 xe máy thành 1 box (`img_005`). | Giữ lại các box đối tượng thật $\ge 20\text{ px}$ bị 1 người bỏ sót; tách riêng từng người trên xe máy theo `R-02`. |
| `iou_thap` | 23 | Lệch biên box ở các xe máy đỗ sát nhau (`img_008`, `030`), thùng xe bán tải (`img_001`) hoặc thiếu phần sidecar (`img_022` ca `C111`). | Chốt biên box ôm sát toàn bộ phần nhìn thấy theo `R-01` và `R-09`. |
| `sai_lop` | 9 | Nhầm xe bán tải thành `car` (`img_012` `C094`, `img_018` `C106`, `img_028` `C131`), nhầm xe wagon thành `van` (`img_024` `C118`–`C120`), nhầm auto-rickshaw ở xa thành `car` (`img_035` `C161`–`C162`). | Chuẩn hóa bán tải về `truck`, xe wagon (`img_024`) về `car`, auto-rickshaw (`img_035`) về `motorcycle`. |
| `bat_dong_ignore` | 4 | Một người vẽ `ignore_region` cho cụm vật nhỏ ở xa (`img_009`, `011`, `013`, `025`) còn người kia bỏ trống. | Giữ vùng `ignore_region` bao cụm vật nhỏ $< 20\text{ px}$ theo `R-06`. |
| `co_can_review` | 1 | `036` đánh dấu `can_review=true` cho chiếc `car` ở hậu cảnh `img_003.jpg` (`C013`). | Xác nhận là `car` thật cao $\ge 20\text{ px}$, giữ box và tắt `can_review=false`. |

---

## 5. Lịch sử phiên bản

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| `v0` | 2026-11-16 | Giảng viên | Bản khởi đầu sơ sài. |
| `v0.1-individual-036` | 2026-11-16 | `036` | Bản khảo sát độc lập của `036`. |
| `v0.1-individual-002` | 2026-11-16 | `002` | Bản khảo sát độc lập của `002`. |
| `v1.0` | 2026-11-16 | `036`, `002` | Bản thống nhất trước khi gán độc lập; rút gọn `occluded_level` về `0` và `1`. |
| `v1.1` | 2026-11-16 | `036`, `002` | Đính chính câu chữ & ví dụ (`editorial`) sau khi giải quyết 171 ca Calibration (`C001`–`C171`) trên 30 ảnh. |

