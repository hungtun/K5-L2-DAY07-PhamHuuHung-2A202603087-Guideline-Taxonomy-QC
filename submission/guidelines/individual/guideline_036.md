# Guideline gán nhãn — v0.1-individual — 036

> Bản đề xuất cá nhân (Pha 1) của thành viên `036` sau khi khảo sát 40 ảnh trong `data/images/` và đối chiếu với bản khởi đầu `v0`.

- **Phiên bản:** `v0.1-individual-036`
- **Ngày:** 2026-11-16
- **Người viết:** `036`
- **Công cụ & Định dạng:** CVAT, xuất COCO 1.0 (2D Bounding Box)

---

## 1. Mục tiêu

Bộ nhãn dùng để huấn luyện và đánh giá mô hình **phát hiện người và phương tiện giao thông đường bộ 2D (Object Detection)** từ góc nhìn camera đường phố / camera hành trình (ADAS / giám sát giao thông đô thị).

- **Yêu cầu cốt lõi:** Mô hình cần nhận diện chính xác các đối tượng **thật, có khả năng tham gia hoặc tương tác giao thông** trên đường/vỉa hè.
- **Tác hại nếu gán sai:**
  - *False Negative (Bỏ sót):* Bỏ sót người đi bộ, người đi xe máy hoặc phương tiện thật trên đường gây nguy cơ va chạm trực tiếp.
  - *False Positive (Gán thừa vật giả):* Gán nhầm tượng đá/đồng (`img_017.jpg`, `img_027.jpg`), ma-nơ-canh (`img_033.jpg`), biển quảng cáo (`img_018.jpg`), mô hình đồ chơi (`img_038.jpg`) hoặc ảnh phản chiếu qua gương (`img_020.jpg`) khiến xe tự lái phanh gấp vô cớ hoặc nhiễu đặc trưng thị giác của mô hình.
  - *Lệch quy ước tách/gộp box:* Gộp chung người lái và xe máy thành 1 box hoặc vẽ khác nhau giữa các annotator làm giảm mạnh IoU và khiến mô hình học sai biên đối tượng.

---

## 2. Lớp và thuộc tính

### 2.1. Định nghĩa lớp (Mỗi đối tượng thuộc đúng 1 lớp)

| Lớp | Định nghĩa (một câu) | Ví dụ trong bộ ảnh | Phản ví dụ / Dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô chở người cỡ nhỏ/trung (sedan, hatchback, coupe, SUV, crossover, wagon, xe độ chạy đường thật có biển số). | Sedan/SUV trên đường (`img_015.jpg`, `img_040.jpg`), xe wagon (`img_024.jpg`), các xe con nằm trên xe lồng (`img_002.jpg`), xe diễu hành có biển số thật (`img_019.jpg`). | Không gán xe bán tải có thùng hàng (`truck`), xe van thùng kín (`van`), mô hình ô tô đồ chơi (`img_038.jpg`). |
| `truck` | Phương tiện bốn bánh trở lên có kết cấu chuyên chở hàng hóa (xe tải thùng kín/thùng bạt, đầu kéo/xe lồng, xe bán tải pickup có thùng sau, xe tải nhỏ kei-truck). | Xe bán tải pickup (`img_001.jpg`, `img_012.jpg`, `img_028.jpg`), bán tải lắp nắp thùng (`img_037.jpg`), xe tải chở hàng (`img_007.jpg`, `img_036.jpg`), xe tải chở ô tô (`img_002.jpg`). | Không gán tượng đài hình xe bán tải bằng đá (`img_027.jpg`); không nhầm SUV 5–7 chỗ liền khối (`car`) với pickup có tách khoang thùng (`truck`). |
| `van` | Xe hình hộp liền khối (minivan, MPV cỡ lớn,승합차/xe khách nhỏ, xe tải van kín không tách rời cabin và thùng hàng). | Xe van/minivan chở khách hoặc chở hàng nhẹ trên phố (`img_016.jpg`, `img_031.jpg`, `img_040.jpg`). | Không bao gồm xe buýt cỡ lớn (`img_032.jpg` — ngoài phạm vi lớp) hoặc xe bán tải lắp nắp thùng rời (`img_037.jpg` -> `truck`). |
| `motorcycle` | Phương tiện cơ giới 2 bánh hoặc 3 bánh gắn động cơ/tay lái mô tô (xe máy, xe tay ga, mô tô phân khối lớn, mô tô sidecar, tuk-tuk/auto-rickshaw). | Xe máy đang chạy/đỗ (`img_005.jpg`, `img_008.jpg`, `img_026.jpg`, `img_030.jpg`), mô tô trưng bày (`img_011.jpg`), mô tô sidecar (`img_022.jpg`). *(Đề xuất họp nhóm chốt riêng tuk-tuk `img_004.jpg`, `img_035.jpg`)*. | Không bao gồm người ngồi trên xe (người gán riêng lớp `person`); không bao gồm xe đạp hoặc xích lô đạp chân (`img_003.jpg`, `img_006.jpg`, `img_009.jpg`). |
| `person` | Con người thật (người đi bộ, người đứng/ngồi quán vỉa hè, người lái hoặc ngồi sau xe máy/xích lô/ô tô nếu nhìn thấy rõ cơ thể). | Người đi bộ qua đường (`img_014.jpg`, `img_025.jpg`), người ngồi quán (`img_001.jpg`), người lái/ngồi xe máy (`img_005.jpg`, `img_026.jpg`), người đạp và khách ngồi xích lô (`img_006.jpg`, `img_023.jpg`). | Không gán tượng đồng/đá (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh in trên biển quảng cáo (`img_018.jpg`), búp bê (`img_038.jpg`), thú nhồi bông/nhân vật hoạt hình (`img_019.jpg`). |
| `ignore_region` | Vùng chứa đám đông/bãi xe quá dày không thể tách từng cá thể, đối tượng thật cao `< 20 px`, hoặc vùng lấp lửng không thể xác định. | Đám đông đứng sau rào chắn (`img_019.jpg`), dãy người/xe ở quá xa dưới `20 px` (`img_008.jpg`, `img_025.jpg`, `img_040.jpg`). | Không dùng `ignore_region` cho vật giả (tượng, biển quảng cáo, đồ chơi) — vật giả thì **không vẽ box nào**. |

#### Xử lý vật ngoài phạm vi lớp (Out-of-taxonomy):
- **Xe đạp, xích lô (`img_003.jpg`, `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`), xe buýt cỡ lớn (`img_032.jpg`), tàu điện:** Không có lớp tương ứng trong taxonomy $\rightarrow$ **Không gán box cho phương tiện đó**, nhưng **vẫn gán lớp `person`** cho người đạp xích lô, hành khách ngồi trên xích lô hoặc người đứng chờ xe buýt.
- **Tuk-tuk / Auto-rickshaw 3 bánh (`img_004.jpg`, `img_016.jpg`, `img_035.jpg`):**
  - *Quan điểm đề xuất của `036`:* Không gán vào `motorcycle` (vì có khung mui kín/3 bánh khác hình học xe máy 2 bánh) hoặc đưa vào thảo luận nhóm ở Pha 2 để thống nhất 100% (tránh một người gán `motorcycle`, một người bỏ trống). Nếu chưa chốt, gán tạm và bật `can_review = true`.

---

### 2.2. Thuộc tính (Attributes) và ngưỡng đo được

Mọi đối tượng thuộc 5 lớp vật thể (`car`, `truck`, `van`, `motorcycle`, `person`) bắt buộc khai báo đủ 3 thuộc tính sau (lớp `ignore_region` không có thuộc tính):

1. **`occluded_level`** (Kiểu `select`, mặc định `"0"` — đánh giá tỷ lệ diện tích thực tế của vật bị vật khác che khuất):
   - **`0` (Thấy rõ / Không che):** Bị che **`0%` – `< 5%`** diện tích đối tượng. Nhìn thấy trọn vẹn đường bao chính.
   - **`1` (Che ít):** Bị che từ **`5%` – `< 35%`** diện tích đối tượng (ví dụ: người đi xe máy bị thân xe/tay lái che một phần chân dưới; xe hơi bị cọc tiêu hoặc người đứng trước che một góc nhỏ).
   - **`2` (Che nhiều):** Bị che từ **`35%` – `75%`** diện tích đối tượng (ví dụ: người ngồi trong cabin xe/tuk-tuk chỉ thấy nửa thân trên; xe đỗ nối đuôi nhau chỉ thấy nửa thân trước/sau).
   - **`3` (Che rất nặng / Khó nhận diện):** Bị che **`> 75%`** diện tích hoặc bị hàng hóa chất kín gần hết (`img_029.jpg`), chỉ lộ một phần nhỏ (đầu, bánh xe) nhưng vẫn chắc chắn là đối tượng thật. *(Lưu ý: nếu bị che > 85–90% không còn đủ đặc trưng nhận diện thì không vẽ box riêng)*.

2. **`truncated`** (Kiểu `select`, mặc định `"no"` — đánh giá việc đối tượng bị cắt bởi 1 trong 4 cạnh biên của bức ảnh):
   - **`no`:** Toàn bộ đối tượng nằm gọn bên trong khung hình (cách mép ảnh $\ge 1\text{ px}$ hoặc chỉ chạm sát mép).
   - **`yes`:** Một phần thực thể của đối tượng bị khung hình cắt mất (**`> 5%`** diện tích ước tính nằm ngoài biên ảnh), ví dụ xe hoặc người ở tiền cảnh bị cắt mất phần đầu/đuôi/chân ở mép trái, phải, dưới (`img_005.jpg`, `img_014.jpg`, `img_034.jpg`).
   - *Phân biệt:* Vật ở giữa ảnh bị vật khác che khuất là `occluded_level > 0`, **không phải** `truncated = yes`. Một vật ở mép ảnh vừa bị cắt mép vừa bị vật khác che thì nhận cả hai (`truncated = yes` và `occluded_level > 0`).

3. **`can_review`** (Kiểu `checkbox`, mặc định `false`):
   - **`false`:** Annotator chắc chắn về lớp, tọa độ box và thuộc tính theo guideline.
   - **`true`:** Tick khi gặp ca biên/mơ hồ chưa chắc chắn (ví dụ: xe ở xa mờ trong đêm `img_007.jpg`, `img_013.jpg`, người hóa trang `img_011.jpg`, phương tiện lai tạp `img_004.jpg`, `img_035.jpg`).

---

## 3. Quy tắc vẽ box (Mã hóa quy tắc `R-xx`)

- **`R-01` (Ôm sát phần nhìn thấy — Visible Tight Box):**
  - Box phải ôm sát **phần thực tế nhìn thấy được (visible pixels)** của đối tượng, không vẽ phỏng đoán (amodal) phần đã bị che khuất hoặc nằm ngoài mép ảnh. Độ lệch biên cho phép $\le 2\text{ px}$.
- **`R-02` (Tách riêng Người và Phương tiện):**
  - Với người đi xe máy (`img_005.jpg`, `img_008.jpg`, `img_026.jpg`, `img_030.jpg`): **Luôn tách riêng** 1 box `motorcycle` cho chiếc xe và 1 box `person` cho mỗi người ngồi trên xe (nếu có 2 người ngồi trên 1 xe như `img_026.jpg` thì vẽ 2 box `person` + 1 box `motorcycle` = 3 box).
  - Box `person` bao gồm từ đỉnh đầu (hoặc mũ bảo hiểm) đến hết phần chân nhìn thấy được; thường có `occluded_level = 1` hoặc `2` do bị thân xe/người ngồi trước che.
  - Box `motorcycle` bao gồm toàn bộ thân xe, bánh xe, kính chiếu hậu và **hàng hóa/thùng chở gắn liền trên xe** (`img_029.jpg`); nếu có sidecar gắn liền (`img_022.jpg`) thì bao trọn cả phần sidecar trong 1 box `motorcycle`.
- **`R-03` (Hành khách bên trong phương tiện có mui):**
  - Người ngồi trong ô tô (`car`, `truck`, `van`) qua lớp kính mờ hoặc chỉ lộ bóng mờ trong cabin: **không gán** `person`.
  - Chỉ gán `person` trên phương tiện nếu nhìn rõ đầu và phần thân trên không bị kính phản chiếu che mờ (ví dụ người lái/khách trên xích lô `img_006.jpg`, xe mui trần `img_019.jpg`, hoặc người ngồi lộ rõ trên tuk-tuk `img_004.jpg`, `img_035.jpg` với `occluded_level = 2`).
- **`R-04` (Loại trừ đối tượng không thật / 2D / Phản chiếu):**
  - **Tuyệt đối không gán box** (và cũng không gán `ignore_region`) cho:
    1. Tượng điêu khắc người/động vật/xe cộ bằng đồng, đá, nhựa (`img_017.jpg` tượng cưỡi ngựa, `img_027.jpg` tượng đài xe bán tải, `img_028.jpg` tượng ngựa trên thùng xe).
    2. Ma-nơ-canh trong tủ kính hoặc trên vỉa hè (`img_033.jpg`).
    3. Hình người/xe in trên biển quảng cáo, poster, băng-rôn (`img_018.jpg`).
    4. Đồ chơi, búp bê, xe mô hình thu nhỏ (`img_038.jpg`) hoặc nhân vật hoạt hình giả trên xe (`img_019.jpg` — chuột trắng Dangermouse).
    5. Hình ảnh người/xe phản chiếu qua gương chiếu hậu (`img_020.jpg`), vũng nước (`img_013.jpg`) hoặc cửa kính (`img_033.jpg`).
- **`R-05` (Loại trừ xe chủ — Ego Vehicle):**
  - Nếu ảnh chụp từ bên trong xe hoặc thấy một phần gương chiếu hậu/cột A/thân xe của chính người chụp (`img_020.jpg`, góc phải `img_033.jpg`), **không gán** phần xe chủ đó.
- **`R-06` (Ngưỡng kích thước tối thiểu & Đám đông — `ignore_region`):**
  - Theo `taxonomy.json` (`min_box_height_px = 20`): Mọi đối tượng có chiều cao phần nhìn thấy **`< 20 px`** thì **không gán lớp vật thể riêng** mà gom vào 1 box `ignore_region`.
  - Khi có đám đông người đi bộ hoặc hàng xe máy đỗ chồng chéo ở xa (> 4–5 đối tượng dính liền, bị che > 70%, không thể tách biên từng vật chính xác như đám đông khán giả ở `img_019.jpg` hoặc cuối đường chợ đêm `img_008.jpg`), vẽ 1 box `ignore_region` bao phủ toàn bộ cụm đó.
- **`R-07` (Vật bị chia tách thành 2 phần rời nhau bởi vật chắn):**
  - Nếu một chiếc xe hoặc người bị cột điện/cây/người khác đứng chắn ngang ở giữa nhưng vẫn nhận ra cùng 1 đối tượng thống nhất: vẽ **1 box duy nhất** bao từ cực trái sang cực phải phần nhìn thấy của đối tượng đó và đặt `occluded_level` tương ứng (`1` hoặc `2`).
- **`R-08` (Xe chở xe — Car Carrier):**
  - Trên xe lồng chở ô tô (`img_002.jpg`): Đầu kéo + rơ-moóc khung chở được gán **1 box `truck`** bao trọn xe tải chở; từng chiếc ô tô con nằm trên sàn chở (nếu cao $\ge 20\text{ px}$ và nhìn rõ hình dáng) được gán riêng từng box **`car`** với `occluded_level = 1` hoặc `2` (do bị thanh chắn khung rơ-moóc che).

---

## 4. Ca khó (Khảo sát thực tế trên 40 ảnh)

Bảng dưới đây ghi lại các tình huống thực tế phát hiện khi duyệt 40 ảnh, phân biệt rõ **ca xử lý dứt điểm theo quy tắc** và **ca cần đưa ra họp nhóm / hỏi Reviewer (`can_review`)**:

| Ảnh | Tình huống thực tế trong ảnh | Rule ID | Phân loại ca | Quyết định đề xuất của `036` | Lý do |
|---|---|---|---|---|---|
| `img_002.jpg` | Xe tải lồng (Renault 380) chở 8 ô tô con trên 2 tầng + 1 người đứng cạnh đuôi rơ-moóc bên phải. | `R-08` | Cần thống nhất nhóm | Gán 1 box `truck` cho cả xe tải + rơ-moóc; gán 8 box `car` riêng cho từng xe con trên giàn (`occluded_level=1,2`); gán 1 box `person` ở đuôi xe. | Các xe con trên giàn là ô tô thật có đầy đủ đặc trưng thị giác của `car`; nếu bỏ qua sẽ thành False Negative cho bộ phát hiện `car`. |
| `img_004.jpg`, `img_035.jpg` | Xe tuk-tuk Thái Lan (`img_004.jpg`) và xe auto-rickshaw Ấn Độ chở quá tải (`img_035.jpg`). | `R-02`, `R-03` | Cần hỏi Reviewer / Họp nhóm (`can_review`) | Gán `person` cho tài xế và hành khách lộ rõ. Với thân xe 3 bánh có mui: đề xuất thảo luận nhóm xem gán vào `motorcycle` hay coi là ngoài taxonomy (không gán). | Taxonomy chỉ có `car`, `truck`, `van`, `motorcycle`. Xe 3 bánh có mui nằm ở ranh giới giữa `motorcycle` và ngoài taxonomy. |
| `img_005.jpg`, `img_026.jpg` | Nhiều người đi xe máy ở TP.HCM bị cắt mép dưới (`img_005.jpg`) và cặp đôi đi xe máy trong hầm (`img_026.jpg`). | `R-01`, `R-02` | Loại/Xử lý theo quy tắc | Tách 1 box `motorcycle` + 1 box `person` cho mỗi người. Ở `img_005.jpg` các xe sát mép dưới đặt `truncated=yes`. Ở `img_026.jpg` vẽ 2 box `person` (người lái và người ngồi sau, `occluded_level=1`) + 1 box `motorcycle`. | Đảm bảo nhất quán số lượng box giữa các annotator, không gộp chung người + xe. |
| `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg` | Đoàn xích lô đạp chân chở khách du lịch ở Huế. | `R-02`, `R-03` | Loại theo quy tắc (phương tiện) | **Không gán** chiếc xích lô (vì là xe đạp 3 bánh không động cơ, ngoài taxonomy). **Gán `person`** cho cả người đạp xích lô và khách ngồi trên xích lô; gán `motorcycle`/`car` cho các xe xung quanh. | Xích lô không phải `motorcycle` hay `car`; nhưng người ngồi trên xích lô là người thật lộ rõ toàn thân. |
| `img_011.jpg` | Nhân vật mặc bộ đồ hóa trang quái vật Predator đứng cạnh hàng mô tô độ ở lễ hội. | `R-04` | Cần hỏi Reviewer (`can_review`) | Gán `motorcycle` cho từng xe mô tô trong hàng. Với Predator: nếu là tượng trưng bày thì **không gán**; nếu là người thật mặc đồ hóa trang thì gán `person` kèm `can_review=true`. | Đầu và giáp che kín không rõ là người thật hóa trang hay mô hình đứng cố định; cần chốt ở buổi họp nhóm. |
| `img_017.jpg` | Tượng đồng Công tước Wellington cưỡi ngựa đội nón chóp giao thông. | `R-04` | Loại theo quy tắc | **Không gán bất kỳ box nào** (mảng `annotations` rỗng cho đối tượng này). | Tượng đồng không phải người thật; nếu 2 người cùng gán `person` thì IAA cao nhưng vẫn sai bản chất bài toán. |
| `img_018.jpg` | Biển quảng cáo bầu cử cỡ lớn ven đường có ảnh chân dung người đàn ông. | `R-04` | Loại theo quy tắc | **Không gán** khuôn mặt/người trên biển quảng cáo; chỉ gán các xe hơi thật (`car`) đỗ/chạy phía xa nếu $\ge 20\text{ px}$ hoặc `ignore_region` nếu quá nhỏ. | Hình in 2D trên pano không phải đối tượng giao thông. |
| `img_019.jpg` | Xe vàng Dangermouse trong lễ diễu hành: có nhân vật chuột đồ chơi trắng, 1 tài xế người thật ngồi bên phải, và đám đông khán giả dày đặc sau rào. | `R-03`, `R-04`, `R-06` | Xử lý kết hợp quy tắc | Gán 1 box `car` cho chiếc xe vàng (xe thật có biển số `YR04 NUM`); gán 1 box `person` (`occluded_level=2`) cho người lái thật; **không gán** chuột trắng giả; dùng `ignore_region` cho đám đông khán giả chồng chéo phía sau. | Phân tách rõ phương tiện thật, người thật, mô hình trang trí trên xe và đám đông nền. |
| `img_020.jpg` | Gương chiếu hậu của xe đang chụp (ego vehicle) phản chiếu cầu vồng và đường phố. | `R-04`, `R-05` | Loại theo quy tắc | **Không gán** gương/thân xe chủ và không gán vật phản chiếu trong gương (ảnh trống đối tượng nếu ngoài gương không có xe/người thật). | Gương xe chủ là ego-vehicle (`R-05`), hình trong gương là ảnh phản chiếu (`R-04`). |
| `img_022.jpg` | Mô tô cổ có thùng thuyền (sidecar) bên cạnh và 2 người ngồi. | `R-02` | Loại/Xử lý theo quy tắc | Gán 1 box `motorcycle` bao cả thân xe chính lẫn thùng sidecar gắn liền; gán 2 box `person` riêng cho người cầm lái và người ngồi ghế sau/sidecar (`occluded_level=1,2`). | Sidecar là một phần kết cấu cố định của chiếc mô tô đó, không phải phương tiện độc lập. |
| `img_027.jpg`, `img_028.jpg` | `img_027.jpg`: Bia tưởng niệm bằng đá tạc hình xe bán tải. `img_028.jpg`: Xe bán tải thật đỗ trên phố có đặt tượng con ngựa trên thùng xe. | `R-04` | Loại theo quy tắc | `img_027.jpg`: **Không gán** (tượng đá). `img_028.jpg`: Gán `truck` cho chiếc xe bán tải thật (không bao phần tượng ngựa nhô cao nếu tách được, hoặc chỉ ôm thân xe tải) và không gán tượng ngựa. | Phân biệt rõ tượng đá hình xe (`img_027.jpg`) với xe bán tải thật chở mô hình trang trí (`img_028.jpg`). |
| `img_029.jpg` | Xe máy chở hàng hóa/bao tải cồng kềnh chất cao che gần kín xe và người lái. | `R-02` | Cần hỏi Reviewer (`can_review`) | Gán 1 box `motorcycle` bao gồm cả xe và khối hàng gắn trên xe (`occluded_level=2`), 1 box `person` cho phần người lái lộ ra (`occluded_level=2` hoặc `3`), tick `can_review=true`. | Ranh giới giữa thân xe máy và khối hàng quá lớn dễ làm 2 annotator vẽ kích thước box lệch nhau (`IoU < 0.85`). |
| `img_033.jpg`, `img_038.jpg` | Ma-nơ-canh trong tủ kính (`img_033.jpg`) và cửa hàng trưng bày mô hình xe hơi đồ chơi, búp bê (`img_038.jpg`). | `R-04`, `R-05` | Loại theo quy tắc | **Không gán** ma-nơ-canh, búp bê, xe đồ chơi và không gán phần gương xe trắng phản chiếu bên phải `img_033.jpg`. | Toàn bộ là vật thể giả / mô hình trưng bày trong tủ kính. |
| `img_036.jpg`, `img_037.jpg` | Xe tải nhỏ CMC Veryca (`img_036.jpg`) và xe bán tải GO Transit lắp nắp thùng kín (`img_037.jpg`). | Định nghĩa lớp | Cần thống nhất nhóm | Gán cả hai là **`truck`**. | Dù `img_037.jpg` lắp nắp thùng kín (Truck Cap), kết cấu gốc vẫn là xe bán tải có cabin tách biệt với khoang thùng hàng phía sau, không phải `van` liền khối. |

---

## 5. QC và phiên bản

### 5.1. Ngưỡng QC áp dụng (Theo `submission/taxonomy.json`)
- **Chiều cao box tối thiểu (`min_box_height_px`):** `20 px` (nhỏ hơn phải đưa vào `ignore_region`).
- **Ngưỡng IoU đạt chuẩn theo kích thước:**
  - Đối tượng lớn (chiều cao trung bình $\ge 50\text{ px}$): $\text{IoU} \ge 0.85$.
  - Đối tượng nhỏ (chiều cao trung bình $< 50\text{ px}$): $\text{IoU} \ge 0.70$.
- **Ngưỡng ghép cặp tính IAA (`match_iou`):** $\text{IoU} \ge 0.50$.
- **Mục tiêu đồng thuận toàn lô (IAA):** Mean IoU $\ge 0.85$ và đồng thuận lớp $\ge 95\%$ trên các cặp ghép được.
- **Ngưỡng nghiệm thu cuối (Release):** Duyệt 100% lô (40/40 ảnh), tỷ lệ ảnh có lỗi $\le 2\%$ (tức là tối đa **0 ảnh lỗi** trên 40 ảnh, vì $1/40 = 2.5\% > 2\%$) và không có lỗi hệ thống lặp lại.

### 5.2. Quy trình xử lý ca mơ hồ
- Trong lúc gán độc lập (Pha 3): Tuyệt đối không trao đổi nhãn; gặp ca khó tự quyết theo bản `v1.0` và tick `can_review = true` (hoặc ghi vào `image_issues.csv` nếu chưa vẽ được box).
- Trong buổi Calibration (Pha 5): Người điều phối (`036`) và Reviewer (`002`) cùng mở ảnh chồng box (`work/viz/`), chốt quyết định vào `calibration_decisions.csv` và cập nhật quy tắc vào `guideline_v1.1_calibrated.md`.

### 5.3. Lịch sử phiên bản

| Phiên bản | Thay đổi chính | Lý do / Ca dẫn chứng |
|---|---|---|
| `v0` | Bản khởi đầu của giảng viên. | Chưa có ngưỡng `%` cho `occluded_level`, chưa quy định tách/gộp người đi xe máy, chưa có quy tắc xử lý tượng, ma-nơ-canh, xe chở xe, tuk-tuk, xích lô. |
| `v0.1-individual-036` | Bổ sung định nghĩa 6 lớp + ví dụ/phản ví dụ; đặt ngưỡng `%` đo được cho `occluded_level` (`0/5/35/75%`) và `truncated`; thiết lập 8 quy tắc vẽ box (`R-01`..`R-08`) và bảng phân tích 14 nhóm ca khó thực tế trên 40 ảnh. | Khắc phục sự mơ hồ của `v0` dựa trên khảo sát trực tiếp `img_001.jpg` – `img_040.jpg` để chuẩn bị họp chốt `v1.0` ở Pha 2. |
