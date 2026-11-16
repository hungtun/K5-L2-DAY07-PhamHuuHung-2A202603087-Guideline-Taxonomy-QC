# Guideline gán nhãn thống nhất của nhóm — v1.0 (Agreed)

> Bản đặc tả chính thức của nhóm (`036`, `002`) sau khi họp đối chiếu hai bản cá nhân `guideline_036.md` và `guideline_002.md` ở Pha 2. Bản này được **khóa** để phục vụ lượt gán nhãn độc lập ở Pha 3.

- **Phiên bản:** `v1.0`
- **Ngày khóa:** 2026-11-16
- **Thành viên tham gia & thống nhất:** `036` (Điều phối), `002` (Reviewer)
- **Taxonomy tương ứng:** `submission/taxonomy.json` (`version: "v1.0"`)

---

## 1. Mục tiêu và Phạm vi áp dụng

Bộ nhãn Bounding Box 2D này phục vụ huấn luyện mô hình **phát hiện người và phương tiện cơ giới đường bộ (2D Object Detection)** cho bài toán giám sát giao thông và hỗ trợ lái xe (ADAS) trong điều kiện đường phố đa dạng (ngày, đêm, mưa, mật độ xe máy cao).

- **Nguyên tắc cốt lõi:**
  1. Chỉ gán các **đối tượng thật (3D thực thể)** thuộc 5 lớp mục tiêu (`car`, `truck`, `van`, `motorcycle`, `person`) hoặc vùng bỏ qua (`ignore_region`).
  2. Mọi quy tắc đều có ngưỡng định lượng rõ ràng để 2 người gán độc lập (`036` và `002`) đạt đồng thuận cao nhất (IAA Mean IoU $\ge 0.85$, Class Agreement $\ge 95\%$).

---

## 2. Lớp và Thuộc tính (Khớp `submission/taxonomy.json` v1.0)

### 2.1. Định nghĩa 6 lớp

| Lớp | Định nghĩa chuẩn của nhóm (v1.0) | Ví dụ trong bộ 40 ảnh | Phản ví dụ / Loại trừ |
|---|---|---|---|
| `car` | Ô tô con chở người từ 4–7 chỗ (sedan, hatchback, coupe, SUV, crossover, station wagon, xe độ chạy đường thật có biển số). | Sedan/SUV (`img_015.jpg`, `img_040.jpg`), xe wagon (`img_024.jpg`), 8 ô tô con trên xe lồng (`img_002.jpg`), xe vàng diễu hành có biển số (`img_019.jpg`). | Xe bán tải pickup (`truck`), xe van hộp kín (`van`), mô hình ô tô đồ chơi (`img_038.jpg`). |
| `truck` | Xe cơ giới 4 bánh trở lên chuyên chở hàng hóa hoặc có thùng hàng tách biệt với cabin (xe tải thùng kín/bạt, đầu kéo/rơ-moóc, xe tải nhỏ kei-truck, và **mọi dòng xe bán tải pickup** kể cả có lắp nắp thùng). | Bán tải pickup (`img_001.jpg`, `img_012.jpg`, `img_028.jpg`), bán tải lắp nắp thùng kín (`img_037.jpg`), xe tải chở hàng (`img_007.jpg`, `img_036.jpg`), xe lồng chở ô tô (`img_002.jpg`). | Tượng đá hình xe bán tải (`img_027.jpg` — vật giả), SUV liền khối (`car`), xe van liền khối (`van`). |
| `van` | Xe thân liền khối dạng hộp kín (minivan, MPV cỡ lớn, xe tải van liền khoang không tách rời đầu cabin và thùng sau). | Xe van/minivan lưu thông hoặc đỗ trên phố (`img_016.jpg`, `img_031.jpg`, `img_040.jpg`). | Xe bán tải lắp nắp thùng (`img_037.jpg` -> `truck`), xe buýt công cộng cỡ lớn (`img_032.jpg` -> ngoài phạm vi). |
| `motorcycle` | Phương tiện cơ giới 2 bánh hoặc 3 bánh gắn động cơ và điều khiển bằng tay lái ghi-đông (xe máy số, xe tay ga, mô tô phân khối lớn, mô tô 3 bánh sidecar, xe tuk-tuk / auto-rickshaw). | Xe máy đang chạy/đỗ (`img_005.jpg`, `img_008.jpg`, `img_026.jpg`, `img_030.jpg`), mô tô lễ hội (`img_011.jpg`), mô tô sidecar (`img_022.jpg`), tuk-tuk & auto-rickshaw (`img_004.jpg`, `img_035.jpg`). | Người ngồi trên xe (tách riêng lớp `person`), xe đạp (`img_003.jpg`) và xích lô đạp chân (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`). |
| `person` | Con người thật (đi bộ, đứng, ngồi quán ăn, người điều khiển hoặc hành khách ngồi trên xe máy, xích lô, tuk-tuk, xe mui trần khi nhìn rõ phần thân trên/đầu). | Người qua đường (`img_014.jpg`), người đi dạo (`img_025.jpg`), người ngồi quán (`img_001.jpg`), người đi xe máy (`img_005.jpg`, `img_026.jpg`), người đạp và khách ngồi xích lô (`img_006.jpg`). | Tượng đồng (`img_017.jpg`), ma-nơ-canh (`img_033.jpg`), ảnh trên biển quảng cáo (`img_018.jpg`), búp bê (`img_038.jpg`), nhân vật giả trang trí (`img_019.jpg`), mô hình Predator (`img_011.jpg`). |
| `ignore_region` | Vùng bỏ qua (không tính là vật thể): dùng cho đối tượng thật có chiều cao phần nhìn thấy `< 20 px` hoặc đám đông người/xe ở xa chồng lấn không thể tách từng cá thể. | Đám đông khán giả sau rào chắn (`img_019.jpg`), cụm người/xe ở xa hoặc quá nhỏ `< 20 px` (`img_008.jpg`, `img_025.jpg`, `img_040.jpg`). | Không dùng `ignore_region` cho vật giả (tượng, ma-nơ-canh, biển quảng cáo, đồ chơi) hoặc phương tiện ngoài taxonomy cỡ lớn (xe buýt, xích lô). |

---

### 2.2. Thuộc tính và Ngưỡng định lượng thống nhất

Mọi box thuộc 5 lớp đối tượng (`car`, `truck`, `van`, `motorcycle`, `person`) phải có đủ 3 thuộc tính:

| Thuộc tính | Kiểu | Giá trị & Mặc định | Quy ước định lượng thống nhất của nhóm (Đã rút gọn) |
|---|---|---|---|
| `occluded_level` | `select` | `"0"`, `"1"` *(Mặc định: `"0"`, không dùng `"2"`, `"3"`)* | Đánh giá nhanh theo mốc **1/2 (`50%`) diện tích đối tượng**:<br/>- **`0` (Thấy rõ / Che nhẹ `< 50%` — GIỮ MẶC ĐỊNH):** Nhìn thấy từ một nửa đối tượng trở lên (bao gồm cả người lái xe máy thông thường). Vẽ box xong giữ nguyên mặc định `0`.<br/>- **`1` (Bị che nhiều `>= 50%`):** Bị vật khác che mất từ một nửa diện tích trở lên (ví dụ: người ngồi sau xe máy bị che khuất phần lớn, người ngồi trong tuk-tuk chỉ lộ đầu-vai, xe bị xe khác che một nửa).<br/>*(Lưu ý: Nếu bị che quá nặng `> 85%` không nhận diện được thì không vẽ box).* |
| `truncated` | `select` | `"no"`, `"yes"` (Mặc định: `"no"`) | Đánh giá đối tượng bị cắt bởi 4 cạnh biên của ảnh:<br/>- **`no` (GIỮ MẶC ĐỊNH):** Nằm trọn trong ảnh.<br/>- **`yes`:** Bị mép ảnh cắt mất một phần rõ rệt (`>= 10%` diện tích, ví dụ xe/người ở sát cạnh dưới `img_005.jpg`, cạnh bên `img_014.jpg`). |
| `can_review` | `checkbox` | `false`, `true` (Mặc định: `false`) | Giữ mặc định `false`; chỉ tick `true` khi thực sự không chắc chắn. |

---

## 3. Hệ thống Quy tắc vẽ box (Mã hóa `R-01` – `R-10`)

- **`R-01` (Biên box ôm sát phần nhìn thấy — Visible Tight Box):**
  - Chỉ vẽ box ôm sát các pixel thực sự nhìn thấy của đối tượng (sai số biên $\le 2\text{ px}$). Không vẽ đón (amodal) phần khuất sau vật khác hay ngoài rìa ảnh.
  - Nếu một đối tượng bị cột điện/thanh chắn chia làm 2 phần nhìn thấy nhưng vẫn nhận diện rõ là 1 vật thể duy nhất (ví dụ các ô tô trên xe lồng `img_002.jpg`), vẽ **1 box duy nhất** bao trọn từ cực trái sang cực phải phần nhìn thấy của đối tượng đó và gán `occluded_level = 0` (nếu che `< 50%`) hoặc `1` (nếu che `>= 50%`).
- **`R-02` (Tách riêng Người và Xe máy / Phương tiện hở):**
  - Luôn vẽ **box riêng** cho `person` và **box riêng** cho `motorcycle` (tuyệt đối không gộp người + xe máy vào chung 1 box).
  - Nếu 1 xe máy có 2 người ngồi (`img_026.jpg`), vẽ 1 box `motorcycle` + 2 box `person` riêng biệt.
- **`R-03` (Quy tắc cho người bên trong phương tiện có mui):**
  - **Không gán `person`** cho tài xế/hành khách ngồi trong cabin kính kín của `car`, `truck`, `van` (do bị kính phản chiếu và khung xe che khuất phần lớn).
  - **Có gán `person`** nếu phương tiện không có cửa kính kín và nhìn rõ đầu + thân trên của người đó (bao gồm: người lái xe mui trần `img_019.jpg`, người đạp và khách ngồi xích lô `img_006.jpg`, tài xế/khách lộ rõ trên tuk-tuk `img_004.jpg`, `img_035.jpg`).
- **`R-04` (Loại trừ vật giả, tượng, ma-nơ-canh, biển quảng cáo, đồ chơi, ảnh phản chiếu):**
  - **Không vẽ bất kỳ box nào** (kể cả `ignore_region`) cho:
    1. Tượng đồng, tượng đá, mô hình trang trí (`img_017.jpg` tượng cưỡi ngựa; `img_027.jpg` tượng đá xe bán tải; `img_028.jpg` tượng ngựa trên thùng xe; `img_011.jpg` mô hình quái vật Predator; `img_019.jpg` chuột trắng giả).
    2. Ma-nơ-canh (`img_033.jpg`), búp bê và mô hình ô tô đồ chơi (`img_038.jpg`).
    3. Hình người/xe in trên biển quảng cáo 2D (`img_018.jpg`).
    4. Hình phản chiếu trong gương chiếu hậu (`img_020.jpg`), cửa kính (`img_033.jpg`) hoặc mặt đường ướt (`img_013.jpg`).
- **`R-05` (Loại trừ xe chủ — Ego Vehicle):**
  - Không gán phần gương chiếu hậu, khung cửa hoặc thân xe của chính phương tiện đặt máy ảnh (`img_020.jpg`, góc phải `img_033.jpg`).
- **`R-06` (Quy tắc kích thước tối thiểu `< 20 px` và Đám đông — `ignore_region`):**
  - Mọi đối tượng thật có chiều cao phần nhìn thấy **`< 20 px`** bắt buộc gom vào `ignore_region`, không vẽ box vật thể riêng.
  - Các đám đông người đi bộ hoặc dãy xe máy ở xa chồng chéo dày đặc ($\ge 5$ đối tượng dính liền bị che khuất nặng không thể tách rời từng cá thể, như đám đông khán giả `img_019.jpg` hoặc nền xa chợ đêm `img_008.jpg`) thì bao bằng 1 box `ignore_region`.
- **`R-07` (Quy tắc phương tiện ngoài Taxonomy — Xe đạp, Xích lô, Xe buýt):**
  - Xe đạp (`img_003.jpg`), xích lô đạp chân (`img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`) và xe buýt công cộng cỡ lớn (`img_032.jpg`) nằm ngoài 5 lớp của taxonomy $\rightarrow$ **Không gán box cho bản thân phương tiện đó** (và không gán `ignore_region`), nhưng **vẫn gán lớp `person`** cho người đạp xe, khách ngồi trên xích lô hoặc hành khách đứng chờ xe buýt.
- **`R-08` (Quy tắc Xe chở xe — Car Carrier `img_002.jpg`):**
  - Vẽ **1 box `truck`** bao trọn đầu kéo và khung rơ-moóc của xe tải chở xe (không kéo đỉnh box `truck` lên bao trùm nóc hàng xe con ở tầng 2 mà chỉ ôm theo kết cấu khung rơ-moóc và đầu kéo).
  - Vẽ **riêng từng box `car`** cho mỗi chiếc ô tô con nằm trên 2 tầng của rơ-moóc (8 box `car`).
  - Vẽ **1 box `person`** cho người đứng cạnh đuôi rơ-moóc bên phải.
- **`R-09` (Quy tắc hàng hóa gắn trên xe máy `img_029.jpg` và Sidecar `img_022.jpg`):**
  - Với xe máy có thùng thuyền sidecar gắn liền (`img_022.jpg`): vẽ **1 box `motorcycle`** bao trọn cả thân xe máy và phần sidecar.
  - Với xe máy chở kiện hàng rời quá khổ (`img_029.jpg`): Box `motorcycle` **chỉ ôm phần kết cấu xe máy nhìn thấy** (bánh xe, lốc máy, chắn bùn), **không** kéo rộng box để bao toàn bộ kiện hàng/bao tải cồng kềnh chất phía trên.
- **`R-10` (Quy tắc cho Xe 3 bánh có động cơ Tuk-tuk / Auto-rickshaw `img_004.jpg`, `img_035.jpg`):**
  - Thống nhất gán các xe tuk-tuk / auto-rickshaw 3 bánh gắn động cơ vào lớp **`motorcycle`** (vì dùng động cơ và cơ cấu lái ghi-đông mô tô, tương tự mô tô 3 bánh), đồng thời gán **`person`** (`occluded_level = 1`) cho tài xế và hành khách nhìn thấy rõ.

---

## 4. Tổng hợp & Giải quyết bất đồng giữa 2 bản cá nhân (`036` và `002`)

Trong buổi họp Pha 2, hai thành viên `036` và `002` đã đối chiếu hai bản cá nhân và thống nhất giải quyết 5 điểm khác biệt sau đây:

| STT | Vấn đề / Ca ảnh | Đề xuất ban đầu của `036` | Đề xuất ban đầu của `002` | Quyết định thống nhất (`v1.0`) & Lý do |
|---|---|---|---|---|
| 1 | **Ngưỡng % của `occluded_level`** | `0`: `<5%`<br/>`1`: `5–35%`<br/>`2`: `35–75%`<br/>`3`: `>75%` | `0`: `<5%`<br/>`1`: `5–25%`<br/>`2`: `25–60%`<br/>`3`: `>60%` | **Chốt (`R-01` — Rút gọn thành 2 mức dùng thực tế):**<br/>- **`0` (`< 50%` — Mặc định):** Thấy rõ từ một nửa vật trở lên.<br/>- **`1` (`>= 50%`):** Bị che từ một nửa vật trở lên (không dùng mức `2`, `3`).<br/>*Lý do:* Chia 4 mức gây nhiễu chủ quan rất lớn khi gán bằng mắt; rút gọn về mốc `50%` (1/2 vật) giúp giữ mặc định `0` cho đa số box, tăng tốc độ gán và đạt đồng thuận thuộc tính cao nhất. |
| 2 | **Phân lớp xe Tuk-tuk / Auto-rickshaw (`img_004.jpg`, `img_035.jpg`)** | Đề xuất loại khỏi `motorcycle` (coi là ngoài taxonomy) hoặc hỏi Reviewer. | Đề xuất gán vào `motorcycle` vì là phương tiện cơ giới 3 bánh lái bằng ghi-đông. | **Chốt (`R-10`):** Gán vào **`motorcycle`**.<br/>*Lý do:* Tuk-tuk là phương tiện cơ giới phổ biến trên đường phố châu Á trong tập dữ liệu; xếp chung với mô tô 3 bánh (`img_022.jpg`) vào `motorcycle` giúp mô hình không bỏ sót phương tiện cơ giới đang lưu thông trên làn đường. |
| 3 | **Xe bán tải lắp nắp thùng kín (`img_037.jpg`)** | Gán là `truck` (theo khung gầm xe bán tải có khoang thùng tách rời cabin). | Phân vân gán là `van` vì có nắp chụp kín phía sau nhìn giống xe van. | **Chốt (Mục 2.1):** Gán là **`truck`**.<br/>*Lý do:* Xe trong `img_037.jpg` là dòng pickup (GO Transit pickup) lắp phụ kiện nắp chụp (Truck Cap), vẫn có khe tách biệt giữa cabin và thùng hàng; gán `truck` giúp nhất quán 100% với mọi xe pickup khác (`img_001`, `img_012`, `img_028`). |
| 4 | **Biên box `motorcycle` khi chở hàng quá khổ (`img_029.jpg`)** | Box `motorcycle` bao gồm cả xe lẫn khối hàng hóa cồng kềnh chất trên xe. | Box `motorcycle` chỉ ôm phần thân xe/bánh xe nhìn thấy, không bao kiện hàng rời. | **Chốt (`R-09`):** Chỉ ôm **phần thân/bánh xe máy nhìn thấy**, không bao kiện hàng rời chất chồng lên trên.<br/>*Lý do:* Kiện hàng có hình thù ngẫu nhiên và to gấp nhiều lần chiếc xe; chỉ ôm phần xe thật giúp box chặt hơn, tránh làm nhiễu đặc trưng hình học của lớp `motorcycle`. |
| 5 | **Nhân vật Predator (`img_011.jpg`) và phạm vi box `truck` xe lồng (`img_002.jpg`)** | `img_011`: phân vân người hóa trang (`person` + `can_review`).<br/>`img_002`: 1 box `truck` + 8 box `car`. | `img_011`: coi là tượng/mô hình trưng bày, không gán.<br/>`img_002`: cần làm rõ biên trên của box `truck`. | **Chốt (`R-04`, `R-08`):**<br/>- `img_011.jpg`: **Không gán `person`** cho mô hình Predator (là tượng trưng bày bằng kim loại/nhựa tại lễ hội xe).<br/>- `img_002.jpg`: Box `truck` ôm đầu kéo và toàn bộ khung rơ-moóc màu cam; 8 xe con trên giàn gán 8 box `car` riêng biệt. |

---

## 5. Danh mục tra cứu nhanh Ca khó cho lượt gán độc lập (Pha 3)

| Ảnh | Quy tắc áp dụng | Hướng dẫn thống nhất khi gán độc lập |
|---|---|---|
| `img_002.jpg` | `R-08` | 1 box `truck` (đầu kéo + khung rơ-moóc), 8 box `car` trên giàn, 1 box `person` đứng ở đuôi xe bên phải. |
| `img_003.jpg` | `R-07` | Không gán xe đạp thồ bán mít; chỉ gán `person` cho người bán/người đi đường thật $\ge 20\text{ px}$. |
| `img_004.jpg`, `img_035.jpg` | `R-10`, `R-03` | Gán `motorcycle` cho từng chiếc tuk-tuk / auto-rickshaw; gán `person` (`occluded_level=1`) cho tài xế và hành khách nhìn rõ. |
| `img_006.jpg`, `img_009.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg` | `R-07`, `R-02` | Không gán chiếc xích lô; gán `person` cho người đạp xích lô và khách ngồi trên xích lô; gán bình thường các xe `motorcycle`, `car`, `van` xung quanh. |
| `img_011.jpg` | `R-04` | Gán `motorcycle` cho dãy mô tô trưng bày; **không gán** mô hình Predator. |
| `img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg` | `R-04`, `R-05` | **Không gán** tượng đồng (`img_017`), gương xe chủ & ảnh phản chiếu (`img_020`), tượng đá bán tải (`img_027`), ma-nơ-canh (`img_033`), xe đồ chơi/búp bê (`img_038`). Nếu cả ảnh không có đối tượng thật nào đạt chuẩn thì giữ ảnh trong COCO với 0 annotation. |
| `img_018.jpg` | `R-04`, `R-06` | Không gán hình người trên biển quảng cáo bầu cử; chỉ gán phương tiện thật dưới đường (hoặc `ignore_region` nếu `< 20 px`). |
| `img_019.jpg` | `R-03`, `R-04`, `R-06` | Gán 1 box `car` cho xe vàng Dangermouse; 1 box `person` (`occluded_level=1`) cho người lái thật; bỏ qua chuột trắng giả; dùng `ignore_region` cho đám đông khán giả hai bên rào chắn. |
| `img_022.jpg` | `R-09`, `R-02` | 1 box `motorcycle` bao cả xe và thùng sidecar; 2 box `person` cho 2 người ngồi trên xe. |
| `img_028.jpg` | `R-04` | Gán 1 box `truck` cho thân xe bán tải thật (không kéo box bao lên phần tượng con ngựa gắn trên thùng); không gán tượng ngựa. |
| `img_029.jpg` | `R-09` | 1 box `motorcycle` chỉ ôm phần thân/bánh xe máy lộ ra dưới kiện hàng (`occluded_level=1`); 1 box `person` cho người lái (`occluded_level=1`). |
| `img_032.jpg` | `R-07` | Không gán xe buýt công cộng cỡ lớn; chỉ gán `person` cho người đứng ở trạm chờ và `car`/`truck`/`van` thật nếu có. |
| `img_036.jpg`, `img_037.jpg` | Mục 2.1 | Gán cả xe tải nhỏ CMC Veryca (`img_036.jpg`) và xe bán tải lắp nắp thùng kín (`img_037.jpg`) vào lớp `truck`. |

---

## 6. QC và Quản lý Phiên bản

- **Ngưỡng kỹ thuật cố định (`submission/taxonomy.json`):**
  - `min_box_height_px`: `20`
  - `large_px`: `50` (`iou_thr_large`: `0.85`, `iou_thr_small`: `0.70`)
  - `match_iou`: `0.50`
  - `iaa_iou`: `0.85`, `iaa_class`: `0.95`
  - `error_rate_img_max`: `0.02` ($\le 2\%$ theo ảnh khi duyệt toàn bộ 40 ảnh).
- **Lịch sử phiên bản:**

| Phiên bản | Ngày | Tác giả | Nội dung thay đổi |
|---|---|---|---|
| `v0` | 2026-11-16 | Giảng viên | Bản khởi đầu sơ sài, chưa có ngưỡng định lượng và quy tắc ca biên. |
| `v0.1-individual-036` | 2026-11-16 | `036` | Bản khảo sát độc lập của `036` trên 40 ảnh. |
| `v0.1-individual-002` | 2026-11-16 | `002` | Bản khảo sát độc lập của `002` trên 40 ảnh. |
| `v1.0` | 2026-11-16 | `036`, `002` | Họp thống nhất 5 điểm khác biệt giữa `036` và `002`, khóa 10 quy tắc `R-01`..`R-10` và đồng bộ `taxonomy.json` lên `v1.0`. |

