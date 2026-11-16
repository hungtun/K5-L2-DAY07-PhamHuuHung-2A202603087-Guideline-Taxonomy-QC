# Guideline gán nhãn — v0.1-individual — 002

> Bản khảo sát và đề xuất quy tắc cá nhân (Pha 1) của thành viên `002` trên bộ 40 ảnh đường phố (`data/images/img_001.jpg` – `img_040.jpg`).

- **Phiên bản:** `v0.1-individual-002`
- **Ngày:** 2026-11-16
- **Người viết:** `002`
- **Công cụ:** CVAT (xuất định dạng COCO 1.0)

---

## 1. Mục tiêu

Xây dựng bộ dữ liệu Bounding Box 2D chuẩn xác phục vụ huấn luyện mô hình thị giác máy tính **nhận diện người tham gia giao thông và phương tiện cơ giới đường bộ** trong môi trường đô thị phức tạp (nhiều xe máy, ánh sáng ban đêm, che khuất cao).

- **Đối tượng mục tiêu:** Con người thật và các phương tiện cơ giới đường bộ thuộc 5 lớp định danh (`car`, `truck`, `van`, `motorcycle`, `person`).
- **Rủi ro nếu guideline mơ hồ:**
  - *Bất đồng số lượng box:* Với cảnh đường phố Việt Nam/Đông Nam Á (`img_005.jpg`, `img_008.jpg`, `img_030.jpg`), nếu không quy định rõ cách tách người ngồi trên xe máy và cách gom đám đông ở xa, hai người gán sẽ lệch số lượng box rất lớn.
  - *Nhầm lẫn giữa các dòng xe:* Xe bán tải đậy nắp thùng (`img_037.jpg`), xe tải nhỏ (`img_036.jpg`), xe tuk-tuk 3 bánh (`img_004.jpg`, `img_035.jpg`) rất dễ bị hai người gán vào các lớp khác nhau (`car` vs `truck` vs `van` vs `motorcycle`).
  - *Nhiễu từ vật thể nhân tạo:* Tượng người cưỡi ngựa (`img_017.jpg`), tượng đài xe tải (`img_027.jpg`), ma-nơ-canh (`img_033.jpg`) và hình in quảng cáo (`img_018.jpg`) nếu bị gán như vật thật sẽ làm hỏng độ chính xác (Precision) của mô hình.

---

## 2. Lớp và thuộc tính

### 2.1. Bảng phân lớp đối tượng

| Lớp | Định nghĩa (một câu) | Ví dụ điển hình trong 40 ảnh | Phản ví dụ / Dễ nhầm lẫn |
|---|---|---|---|
| `car` | Xe ô tô con phục vụ chở người (sedan, hatchback, SUV, coupe, station wagon) có từ 4–7 chỗ ngồi. | Ô tô con trên đường mưa (`img_015.jpg`), xe wagon đen (`img_024.jpg`), xe vàng độ trong lễ diễu hành (`img_019.jpg`), ô tô trên phố Bangkok (`img_040.jpg`). | Không gán xe mô hình trong tủ kính (`img_038.jpg`), xe tải/bán tải có thùng hàng lộ thiên (`img_001.jpg`, `img_012.jpg` -> `truck`). |
| `truck` | Xe cơ giới chuyên chở hàng hóa có thùng hàng hở hoặc thùng tải tách biệt với đầu cabin (xe tải thùng, đầu kéo, xe bán tải pickup thùng hở). | Xe bán tải đỗ quán ăn (`img_001.jpg`), bán tải chở hàng (`img_012.jpg`), xe tải trong kho (`img_007.jpg`), đầu kéo chở xe (`img_002.jpg`), xe tải nhỏ (`img_036.jpg`). | Không gán tượng đá hình xe bán tải (`img_027.jpg`). Với xe bán tải có nắp chụp kín thùng (`img_037.jpg`), cá nhân `002` đang phân vân với `van` (cần họp nhóm chốt). |
| `van` | Xe thân liền khối dạng hộp kín (MPV, minivan chở khách hoặc chở hàng nhẹ có khoang sau liền trần với cabin). | Xe van trên đường phố (`img_016.jpg`, `img_031.jpg`, `img_040.jpg`), đề xuất xếp xe có nắp thùng kín liền trần (`img_037.jpg`) vào nhóm này nếu nhìn theo hình dáng hộp kín. | Không gán xe buýt công cộng cỡ lớn (`img_032.jpg`) vì kích thước và hình thái khác hẳn `van`. |
| `motorcycle` | Xe cơ giới 2 bánh hoặc 3 bánh điều khiển bằng ghi-đông (xe số, xe ga, mô tô phân khối lớn, mô tô 3 bánh sidecar, xe tuk-tuk/auto-rickshaw). | Xe máy lưu thông (`img_005.jpg`, `img_026.jpg`, `img_030.jpg`), dàn mô tô lễ hội (`img_011.jpg`), mô tô sidecar (`img_022.jpg`), tuk-tuk/rickshaw (`img_004.jpg`, `img_035.jpg`). | Không gán xe đạp thồ bán mít (`img_003.jpg`) hoặc xích lô đạp chân (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`) vì là xe thô sơ không động cơ. |
| `person` | Con người bằng xương bằng thịt xuất hiện trong cảnh (đi bộ, đứng, ngồi quán, điều khiển hoặc ngồi trên phương tiện). | Người sang đường (`img_014.jpg`), người dạo phố biển (`img_025.jpg`), người lái xe máy (`img_005.jpg`), người đạp và khách đi xích lô (`img_006.jpg`, `img_034.jpg`). | Loại trừ tượng đồng (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh người trên biển bầu cử (`img_018.jpg`), búp bê (`img_038.jpg`), chuột hoạt hình (`img_019.jpg`). |
| `ignore_region` | Vùng ảnh chứa các đối tượng quá nhỏ (`< 20 px`), quá mờ tối hoặc chồng lấn dày đặc không thể tách rời từng box. | Đám đông khán giả đứng xếp lớp sau hàng rào (`img_019.jpg`), dòng người/xe nhỏ li ti ở cuối đường (`img_008.jpg`, `img_025.jpg`, `img_040.jpg`). | Không vẽ `ignore_region` đè lên tượng hay biển quảng cáo (vật không phải đối tượng thật thì để trống hoàn toàn). |

#### Quy tắc cho đối tượng không thuộc lớp nào:
- **Xe thô sơ (xe đạp `img_003.jpg`, xích lô `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`) và Xe buýt lớn (`img_032.jpg`):** Bỏ qua phần phương tiện (không vẽ box), chỉ gán `person` cho người thật ở trên hoặc xung quanh xe.

---

### 2.2. Định nghĩa thuộc tính và ngưỡng định lượng

Tất cả các box thuộc 5 lớp chính (`car`, `truck`, `van`, `motorcycle`, `person`) phải gán các thuộc tính sau:

1. **`occluded_level`** (`0`, `1`, `2`, `3` — Mặc định: `0`): Đo theo tỷ lệ phần trăm diện tích cơ thể/thân xe bị vật cản phía trước che mất:
   - **Mức `0` (Không bị che):** Bị che **`0%`** (hoặc dưới `5%` không đáng kể), thấy rõ toàn bộ hình dáng đối tượng.
   - **Mức `1` (Bị che nhẹ):** Bị che từ **`5%` đến `25%`** diện tích (ví dụ: người đi bộ bị túi xách hoặc cọc tiêu che một phần chân; xe máy bị xe trước che một phần bánh trước).
   - **Mức `2` (Bị che trung bình – nhiều):** Bị che từ **`> 25%` đến `60%`** diện tích (ví dụ: người ngồi lái xe máy bị yếm xe che nửa thân dưới; người ngồi sau xe máy bị người trước che khuất phần ngực/bụng như `img_026.jpg`).
   - **Mức `3` (Bị che nặng):** Bị che **`> 60%`** diện tích (ví dụ: người ngồi trong khoang xe tuk-tuk `img_004.jpg`, người lái bị hàng hóa chất cao che khuất phần lớn ở `img_029.jpg`). Nếu chỉ thấy một mẩu quá nhỏ (bị che `> 85%`) không thể khẳng định là người/xe thì không gán.

2. **`truncated`** (`no`, `yes` — Mặc định: `no`):
   - **`no`:** Đối tượng nằm trọn trong phạm vi 4 cạnh bức ảnh.
   - **`yes`:** Đối tượng bị rìa bức ảnh (trái, phải, trên, dưới) cắt mất một phần thân thể/kết cấu (ước tính phần bị mất ngoài khung hình **`>= 5%`**), ví dụ người/xe máy ở sát mép dưới `img_005.jpg` hoặc mép trái/phải `img_006.jpg`, `img_014.jpg`.

3. **`can_review`** (`true` / `false` — Mặc định: `false`):
   - Đánh dấu `true` cho các box mà người gán chưa chắc chắn 100% về phân lớp hoặc ranh giới box để đưa vào thảo luận nhóm / Reviewer kiểm tra.

---

## 3. Quy tắc vẽ box

1. **Nguyên tắc đường bao sát phần nhìn thấy:**
   - Chỉ vẽ box bao quanh phần pixel thực sự nhìn thấy của đối tượng. Không tự ý kéo rộng box ra ngoài vùng bị vật khác che khuất.
2. **Cách gán Người đi xe máy / mô tô / xích lô:**
   - Vẽ **box riêng biệt** cho người (`person`) và phương tiện (`motorcycle`). Không vẽ 1 box gộp cả người lẫn xe.
   - Nếu trên 1 chiếc xe máy có 2 người (`img_026.jpg`), vẽ 1 box `motorcycle` và 2 box `person` riêng biệt.
   - Với hàng hóa chở trên xe máy (`img_029.jpg`): Đề xuất của `002` là box `motorcycle` **chỉ ôm phần thân xe máy nhìn thấy**, không ôm phần kiện hàng cồng kềnh chất thêm (điểm này cần họp thống nhất với `036`).
3. **Cách gán người bên trong ô tô / xe tải:**
   - Không gán tài xế hoặc hành khách ngồi bên trong cabin kính kín của `car`, `truck`, `van` (trừ khi xe mui trần `img_019.jpg` hoặc phương tiện hở hai bên như tuk-tuk `img_004.jpg`, `img_035.jpg` mà nhìn rõ cơ thể người).
4. **Quy tắc đối với vật thể giả, hình ảnh 2D, phản chiếu và xe chủ (Ego-vehicle):**
   - **Không vẽ box** cho: tượng điêu khắc (`img_017.jpg`, `img_027.jpg`, `img_028.jpg`), ma-nơ-canh (`img_033.jpg`), búp bê và xe đồ chơi (`img_038.jpg`), hình người trên biển quảng cáo (`img_018.jpg`), hình phản chiếu trong gương chiếu hậu (`img_020.jpg`) và phần thân/gương của chính chiếc xe gắn camera chụp ảnh (`img_020.jpg`, `img_033.jpg`).
5. **Quy tắc cho đối tượng nhỏ và đám đông (`ignore_region`):**
   - Bất kỳ người hoặc phương tiện thật nào có chiều cao box **`< 20 px`** (theo chuẩn `min_box_height_px` trong `taxonomy.json`) đều phải đưa vào `ignore_region`.
   - Các cụm người/xe ở xa dính chùm nhau không thể phân tách từng cá thể độc lập (`img_008.jpg`, `img_019.jpg`, `img_025.jpg`) thì bao chung bằng một vùng `ignore_region`.

---

## 4. Ca khó (Ghi nhận khi xem 40 ảnh)

| Ảnh | Tình huống | Rule ID | Quyết định của `002` | Lý do & Phân loại (Quy tắc / Cần họp) |
|---|---|---|---|---|
| `img_001.jpg`, `img_008.jpg` | Người ngồi ăn uống dưới ánh đèn tối ở quán vỉa hè (`img_001.jpg`) và đám đông chợ đêm (`img_008.jpg`). | Quy tắc đám đông & che khuất | Người ở gần nhìn rõ đầu-vai và cao $\ge 20\text{ px}$: gán `person` (`occluded_level=2`). Cụm người ngồi khuất sâu/quá nhỏ phía trong: gom vào `ignore_region`. | **Cần thống nhất nhóm:** Ranh giới giữa người ngồi gán riêng và cụm người đưa vào `ignore_region`. |
| `img_002.jpg` | Xe tải lớn chở 8 chiếc ô tô con trên giàn 2 tầng. | Quy tắc xe chở xe | Gán 1 box `truck` cho xe tải chở và gán riêng từng box `car` cho các ô tô con nằm trên rơ-moóc; gán 1 `person` đứng ở đuôi xe. (Tick `can_review=true` cho phần bao `truck`). | **Cần họp nhóm:** Cần chốt xem box `truck` chỉ bao đầu kéo + khung gầm rơ-moóc hay bao trùm cả các xe con phía trên. |
| `img_004.jpg`, `img_035.jpg` | Xe tuk-tuk đỗ vỉa hè (`img_004.jpg`) và xe auto-rickshaw chở đông người (`img_035.jpg`). | Phân lớp phương tiện | Đề xuất gán thân xe vào `motorcycle` (vì là xe 3 bánh gắn máy, điều khiển bằng ghi-đông) và gán `person` cho những người nhìn rõ bên trong/bám bên ngoài. | **Cần hỏi Reviewer / Họp nhóm:** Cần thống nhất với `036` xem tuk-tuk có tính là `motorcycle` hay loại khỏi taxonomy. |
| `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg` | Đoàn xích lô du lịch trên đường phố Huế. | Ngoài taxonomy | Không gán chiếc xích lô; gán `person` cho tài xế đạp xích lô và khách du lịch ngồi trên ghế xích lô. | **Loại theo quy tắc:** Xích lô chạy bằng sức người (giống xe đạp ở `img_003.jpg`), không thuộc lớp cơ giới nào trong taxonomy. |
| `img_011.jpg` | Mô hình/người mặc giáp Predator đứng trước hàng xe mô tô phân khối lớn. | Vật giả vs Người hóa trang | Gán `motorcycle` cho các xe máy. Với nhân vật Predator: không gán `person` (nghi là mô hình/tượng trưng bày tại sự kiện), ghi chú để họp nhóm kiểm tra. | **Cần hỏi Reviewer:** Khó phân biệt tượng trưng bày kích thước thật với người mặc đồ cosplay qua 1 ảnh tĩnh. |
| `img_017.jpg`, `img_027.jpg`, `img_028.jpg` | Tượng đồng người cưỡi ngựa (`img_017.jpg`), tượng đá xe bán tải (`img_027.jpg`), tượng ngựa đặt trên thùng xe bán tải thật (`img_028.jpg`). | Loại trừ vật giả | `img_017.jpg` và `img_027.jpg` để trống 0 box. `img_028.jpg` chỉ gán 1 box `truck` cho chiếc xe bán tải thật bên dưới, bỏ qua con ngựa giả bên trên. | **Loại theo quy tắc:** Tượng điêu khắc không phải là người hay phương tiện thật. |
| `img_018.jpg`, `img_020.jpg`, `img_033.jpg`, `img_038.jpg` | Biển quảng cáo in hình người (`img_018.jpg`), gương chiếu hậu phản chiếu (`img_020.jpg`), ma-nơ-canh (`img_033.jpg`), xe đồ chơi & búp bê (`img_038.jpg`). | Loại trừ 2D / Phản chiếu / Đồ chơi | Không gán bất kỳ đối tượng giả, hình in hay hình phản chiếu nào trong các ảnh này. | **Loại theo quy tắc:** Tránh đưa False Positive vào tập huấn luyện. |
| `img_019.jpg` | Xe hoạt hình Dangermouse màu vàng chạy giữa đám đông khán giả. | Kết hợp nhiều quy tắc | Gán `car` cho chiếc xe vàng; gán `person` cho người lái thật ngồi cạnh con chuột giả; bỏ qua chuột giả; vẽ `ignore_region` cho hai dãy khán giả dày đặc hai bên. | **Xử lý theo quy tắc:** Xe có biển đăng ký thật và đang chạy trên đường phố; khán giả đứng quá dày cần dùng `ignore_region`. |
| `img_029.jpg` | Xe máy chở kiện hàng lớn che khuất gần hết thân xe. | Quy tắc vẽ biên box | Vẽ box `motorcycle` chỉ ôm phần bánh xe/thân xe máy lộ ra bên dưới, không bao phần kiện hàng trên yên; gán `person` (`occluded_level=3`, `can_review=true`) cho người lái. | **Cần họp nhóm:** Cần chốt với `036` xem hàng hóa chở trên xe máy có tính vào diện tích box `motorcycle` hay không. |
| `img_037.jpg` | Xe bán tải GO Transit có lắp thùng kín phía sau (Truck Cap). | Ranh giới `truck` vs `van` | Tạm đề xuất gán `van` (hoặc `truck` kèm `can_review=true`) vì phần mui sau kín liền mạch nhìn giống xe van. | **Cần họp nhóm:** Ranh giới ngoại hình giữa pickup lắp nắp thùng kín và xe `van`. |

---

## 5. QC và phiên bản

- **Tiêu chuẩn kiểm soát chất lượng (QC):**
  - Chiều cao tối thiểu của box vật thể: `20 px` (`min_box_height_px = 20`).
  - Yêu cầu độ khớp biên (IoU): $\ge 0.85$ với vật cao $\ge 50\text{ px}$ và $\ge 0.70$ với vật cao $< 50\text{ px}$.
  - Yêu cầu IAA toàn nhóm: Mean IoU $\ge 0.85$, tỷ lệ đồng thuận lớp $\ge 95\%$.
  - Điều kiện Release lô 40 ảnh: Kiểm duyệt 100% ảnh, tỷ lệ ảnh lỗi $\le 2\%$ (tức là $0/40$ ảnh lỗi).
- **Ai quyết định ca mơ hồ:** Các ca đánh dấu `can_review` hoặc phát sinh bất đồng giữa `036` và `002` sẽ được đưa ra đối chiếu ở Pha 2 (trước khi gán) và Pha 5 (Calibration sau khi đo IAA) để người điều phối (`036`) và người review (`002`) cùng chốt.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| `v0` | Bản nháp ban đầu của lớp. | Định nghĩa lớp quá ngắn, không có ngưỡng `%` che khuất, thiếu hoàn toàn hướng dẫn cho 40 ảnh thực tế. |
| `v0.1-individual-002` | Viết chi tiết định nghĩa 6 lớp, đặt ngưỡng `%` cho `occluded_level` (`0 / 5–25 / 25–60 / >60%`), bổ sung quy tắc tách người đi xe máy và phân tích 10 nhóm ca khó trên 40 ảnh. | Chuẩn bị tài liệu đối chiếu độc lập của thành viên `002` để họp thống nhất Guideline `v1.0` cùng thành viên `036`. |
