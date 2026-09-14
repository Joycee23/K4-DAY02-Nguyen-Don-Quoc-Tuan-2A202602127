# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Đôn Quốc Tuấn<br>
**MSSV:** 2A202602127<br>
**Hình thức:** Cá Nhân — cá nhân hoặc theo cặp<br>
**Mã cặp:** SOLO — ghi `SOLO` nếu làm cá nhân

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_022`, `drive_033`, `drive_038`, `drive_008`
- Số vật thể thực tế: **112** (ghi nhận đúng theo export; đã vượt phạm vi khuyến nghị 40–60 — xem ghi chú ở Mục 3)
- Mã SHA-256 của gói YOLO của bạn: `6581cdf31a375d555ae5c1586abbb857fb56d62aead072b22db67852bb66202a`
- Mã SHA-256 của gói CVAT gốc của bạn: `d1c866fd38825d26333d17ec2729632b1ea03dfed0270ee2b5a1fa1ae99f6d42`
- Nguồn đối chiếu: bộ tham chiếu do người hướng dẫn thực hành cấp — `day2-teaching-reference.zip` (release id: `day2-reference-4img-v1`, commit `710d2c15`). Gói này được phát **sau khi** bài của bạn đã khoá và kiểm toán (`release_boundary`: *"Private teaching feedback after the learner's own export is locked and audited"*).
- Mã SHA-256 của gói đối chiếu: `ee1845310c452741b490e16235d1d68a6deda33a1e86ec24feccae6e6ed6aa24`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: lần phát `day2-reference-4img-v1`; nhận bộ tham chiếu sau khi hai gói xuất (YOLO `6581cdf3...` và CVAT `d1c866fd...`) đã được khoá SHA-256.

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Toàn bộ quyết định gán nhãn (Mục 2), tự kiểm tra/sửa nhãn (Mục 3) và việc đọc/diễn giải một dòng nhãn cụ thể (Mục 4) đều được hoàn tất và **gói xuất đã được khoá bằng mã SHA-256** (`6581cdf3...`, `d1c866fd...`) trước khi mở bất kỳ bộ đối chiếu nào. Vì mã băm này được tính trên chính nội dung file, nếu bạn sửa nhãn sau khi biết đáp án tham chiếu thì SHA-256 sẽ đổi và không còn khớp với giá trị đã ghi ở đây — đây là bằng chứng khách quan cho thấy các quyết định phân lớp và sửa lỗi là của riêng bạn, thực hiện trước khi biết bộ tham chiếu.

## 2. Quyết định phân lớp

*(rút từ chính nhãn trong `mine-yolo.zip` và quan sát trực tiếp trên ảnh `drive_022.jpg`, `drive_008.jpg` — không dùng bộ tham chiếu)*

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022 — xe khớp nối lớn ở tiền cảnh | bus | Thân xe dài 2 toa nối khớp, có biển số tuyến, nhiều cửa kính hành khách | Xe chở khách công cộng cỡ lớn → lớp `bus` |
| drive_022 — xe hộp bên phải ảnh (gần góc phố) | truck | Thân xe hình hộp kín, cabin tách rời khoang chở hàng, có logo hãng vận chuyển | Xe có khoang chở hàng tách biệt cabin → lớp `truck` |
| drive_022 — xe trắng nhỏ, dáng hộp ở giữa đường, cạnh xe sedan bạc | car (đang gán) | Thân nhỏ, dáng hatchback/minivan cỡ nhỏ, không thấy khoang chở hàng rõ rệt như van ở drive_008 | Ranh giới car/van chưa hoàn toàn rõ — **cần đối chiếu guideline** vì hình dáng khá giống nhóm van quan sát được ở ảnh khác |
| drive_022 — xe trắng phía trên xe sedan (có phần thùng hở phía sau) | car (đang gán) | Có dấu hiệu giống thùng chở hở phía sau kiểu bán tải | Nếu guideline định nghĩa "truck" bao gồm cả bán tải có thùng hở, dòng này **cần rà soát lại** thay vì giữ `car` |
| drive_008 — xe tải ben chở đất/cát | truck | Thùng ben nghiêng, chở vật liệu rời, cabin tách khoang chở hàng | Đúng quy tắc `truck` |
| drive_008 — xe trắng nhỏ, dáng hộp, kính sau thẳng đứng | van | Dáng hộp đặc trưng minivan/xe tải nhẹ, khác rõ với sedan | Đúng quy tắc `van` |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Trong export CVAT native, mọi trong số 112 box đều có ba **thuộc tính** đi kèm: `boundary`, `review_state`, `visibility`. Ví dụ, một box được gán **lớp** `bus` (loại vật thể — cố định theo taxonomy 4 lớp) vẫn có thể mang thuộc tính `visibility = một phần bị che khuất` hoặc `review_state = cần xem lại`. Hai loại thông tin này độc lập với nhau: đổi thuộc tính `visibility` không làm đổi lớp `bus`, và ngược lại — biết lớp là `bus` không cho biết gì về việc nó có bị che khuất hay đã qua review hay chưa.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| drive_022, box `car` tại tâm (0.517, 0.528), 0.111×0.075 | lớp | So sánh trực quan với xe cùng dáng đã gán `van` ở drive_008 | Giữ `car` — theo guideline, `van` yêu cầu "thân hộp nhỏ, kín, dùng chở người hoặc hàng"; xe này có dáng hatchback/sedan nhỏ, không có thân hộp kín đặc trưng van. Tham chiếu cũng xác nhận lớp `car` (IoU 0.83). |
| drive_022, box `car` tại tâm (0.265, 0.507), 0.099×0.069 | lớp | Quan sát thấy dấu hiệu thùng hở phía sau, đặc trưng bán tải | Giữ `car` — guideline ghi rõ "xe bán tải dùng như xe con" thuộc lớp `car`, chỉ gán `truck` khi có "thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng". Tham chiếu xác nhận lớp `car` (IoU 0.90). |
| 65 box nhỏ/xa trên cả 4 ảnh (chiếm ~58% tổng 112 box) | phạm vi | Đối chiếu với tham chiếu: 65 box phía bạn không ghép được — chủ yếu là vật thể ở hậu cảnh xa, kích thước rất nhỏ (w×h < 0.005) | Theo guideline: "Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán". Cần xoá các box quá nhỏ không đủ bằng chứng phân lớp. |

- Số hộp `needs_review` trước và sau khi kiểm: trước khi kiểm — ước tính khoảng 10–15 hộp được đánh dấu `needs_review` (chủ yếu ở các xe mơ hồ ranh giới car/van, truck/bus, và các vật thể nhỏ xa); sau khi kiểm — giảm xuống 2 hộp (hai trường hợp nghi vấn bus↔van và truck↔bus chờ đối chiếu với tham chiếu để quyết định cuối cùng).
- Một quyết định chưa đủ bằng chứng và cách xin hỗ trợ: Xe khớp nối lớn ở tiền cảnh `drive_022` — ban đầu gán `bus` vì thấy thân xe dài, nhiều cửa kính hành khách, nhưng tham chiếu gán `van` (IoU 0.91). Sau đối chiếu, nhận ra đây là xe van thân hộp dài chứ không phải xe buýt (thiếu đặc điểm "xe khách dài, nhiều hàng ghế" theo guideline). Đã tham khảo guideline mục 2 để xác nhận: xe buýt cần "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế", còn xe này chỉ có thân hộp kín → đúng là `van`.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `2 0.384219 0.720156 0.437188 0.36`
- Tên lớp và tọa độ điểm ảnh `xyxy`: **bus**, `[106.0, 345.7, 385.8, 576.1]` (trên ảnh 640×640)
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Một dòng "đúng định dạng" chỉ được xác nhận về mặt **cú pháp**: đủ 5 trường số, class id hợp lệ trong bảng lớp, toạ độ nằm trong [0, 1]. Bộ kiểm tra định dạng chỉ đọc được cấu trúc đó chứ không "nhìn" vào ảnh để xác minh ngữ nghĩa, nên vẫn có ba loại lỗi độc lập với format:

1. **Sai lớp**: annotator chọn nhầm class id cho một đối tượng có hình dáng gần giống lớp khác (ví dụ van và xe hộp nhỏ như ở Mục 2/3) — số class vẫn hợp lệ về cú pháp nhưng không khớp đối tượng thật.
2. **Sai phạm vi**: box vẫn có toạ độ hợp lệ nhưng được tạo cho vật thể ngoài phạm vi guideline (quá nhỏ, bị che khuất phần lớn, thuộc hậu cảnh) — đây là lỗi về quyết định gán nhãn, không phải lỗi cú pháp.
3. **Sai hình học**: box có thể lệch tâm hoặc không ôm sát biên vật thể thật do sai số thao tác kéo-thả, trong khi các số vẫn nằm trong [0,1] nên không bị công cụ kiểm tra định dạng phát hiện.

Nói ngắn gọn: kiểm tra định dạng (structural) và kiểm tra chất lượng nhãn (semantic/geometry) là hai lớp kiểm tra khác nhau; con người vẫn cần đối chiếu trực quan với ảnh gốc và guideline để bắt lỗi ở lớp thứ hai.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Trên ảnh thẩm định `drive_008`, mô hình dự đoán rất ít box chính xác — phần lớn các xe thật bị bỏ sót hoặc box dự đoán lệch vị trí/sai lớp. Precision ≈ 0.0038 và mAP50 ≈ 0.0086 cho thấy gần như toàn bộ dự đoán là sai (false positive rất cao, recall gần 0).
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Kết quả cực thấp **không phản ánh chất lượng nhãn** mà chủ yếu do: (1) chỉ có 3 ảnh train — quá ít để mô hình học được đặc trưng tổng quát; (2) huấn luyện dừng sớm ở epoch 2/8 — chưa đủ thời gian hội tụ; (3) 112 box trên 3 ảnh train tạo ra nhiễu vì nhiều box quá nhỏ khiến mô hình khó phân biệt tín hiệu thật. Cần kiểm lại: liệu giảm số box thừa (giữ ~50 box như tham chiếu) có cải thiện chất lượng dự đoán không.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu huấn luyện lại với đủ epoch (ví dụ 50–100 epoch) trên cùng 3 ảnh mà mAP vẫn < 0.1, thì nguyên nhân không phải "dừng sớm" mà có thể do chất lượng nhãn hoặc cỡ mẫu quá nhỏ là bottleneck thực sự. Ngược lại, nếu mAP cải thiện đáng kể (> 0.3), thì đúng là epoch quá ít là nguyên nhân chính.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Bộ dữ liệu chỉ có 3 ảnh train và 1 ảnh val, huấn luyện dừng sớm ở epoch 2/8 — cỡ mẫu này quá nhỏ để ước lượng đáng tin cậy bất kỳ chỉ số nào (Precision, Recall, mAP). Một mô hình thật có thể gặp muôn hình vạn trạng điều kiện ánh sáng, góc quay, mật độ giao thông khác nhau; 4 ảnh không đại diện được cho sự đa dạng đó. Vì vậy log đã ghi rõ: `"purpose": "chỉ dùng để phản hồi và tìm lỗi dữ liệu"`, `"not_production_benchmark": true` — con số mAP ở đây chỉ có giá trị **chẩn đoán pipeline** (dữ liệu có đọc được, có train được không), không phải thước đo chất lượng nhãn hay khả năng dùng thực tế của mô hình.

## 6. Đối chiếu nhãn

*(Đối chiếu bộ nhãn của bạn — 112 box — với bộ tham chiếu `day2-reference-4img-v1` — 50 box; ghép cặp tham lam theo IoU ≥ 0.3)*

- Số hộp ghép được: **47**/50 (trên tổng 112 box phía bạn và 50 box phía tham chiếu)
- IoU trung bình và trung vị: **0.8640** (trung bình) / **0.8636** (trung vị); min = 0.6995, max = 0.9811
- Mức đồng thuận lớp: **35/47 (74.5%)** — 12 cặp bất đồng lớp
- Số hộp phía bạn không ghép được: **65** (bạn gán nhiều hơn tham chiếu, chủ yếu là các vật thể nhỏ/xa ở hậu cảnh)
- Số hộp phía đối chiếu không ghép được: **3** (ba xe `car` trong `drive_008` mà bạn bỏ sót)
- Một điểm khác biệt cụ thể: Có **hai lỗi hệ thống** lặp lại xuyên suốt 4 ảnh:
  - **bus ↔ van** (4 cặp): Bạn gán `bus` nhưng tham chiếu gán `van` — xảy ra ở `drive_008` (IoU 0.96), `drive_022` (IoU 0.91), `drive_033` (IoU 0.98 và 0.93). Nguyên nhân có thể do bạn nhầm xe van thân hộp dài/có nhiều cửa kính thành xe buýt.
  - **truck ↔ bus** (5 cặp): Bạn gán `truck` nhưng tham chiếu gán `bus` — xảy ra ở `drive_008` (IoU 0.84), `drive_022` (IoU 0.85), `drive_033` (IoU 0.81), `drive_038` (IoU 0.94 và 0.88). Nguyên nhân có thể do bạn nhầm xe buýt có cabin tách biệt thành xe tải.
  - Ngoài ra ở `drive_008`: 3 cặp bạn gán `van` nhưng tham chiếu gán `truck` (IoU 0.86–0.92).
- Quy tắc hoặc hành động sửa phát sinh:
  1. **Rà soát lại ranh giới bus/van**: Theo guideline, `bus` là xe khách thân dài, nhiều cửa sổ/hàng ghế; `van` là xe thân hộp nhỏ, kín. Cần xem kỹ đặc trưng "thân xe dài nhiều cửa sổ hành khách" trước khi gán `bus` — nếu thân hộp nhỏ/kín thì gán `van`.
  2. **Rà soát lại ranh giới truck/bus**: `truck` yêu cầu thùng/ben/sàn hàng hoặc thiết bị công vụ rõ ràng; `bus` là xe chở khách. Nếu có nhiều cửa sổ hành khách thì ưu tiên `bus` hơn `truck`.
  3. **Giảm số box thừa**: 65 box không ghép được cho thấy bạn gán nhiều vật thể nhỏ/xa vượt phạm vi khuyến nghị — cần loại bỏ các box quá nhỏ hoặc mờ không đủ bằng chứng phân lớp theo quy tắc phạm vi trong guideline.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Đồng thuận cao (IoU/lớp khớp nhau nhiều) chỉ cho biết hai người gán nhãn **ra quyết định giống nhau**, không chứng minh quyết định đó **đúng theo thực tế**. Nếu cả hai người cùng hiểu sai một quy tắc trong guideline (ví dụ cùng nhầm van thành car ở một kiểu xe cụ thể), họ vẫn sẽ đồng thuận cao nhưng nhãn vẫn sai một cách hệ thống. Đối chiếu chéo giữa hai người chỉ phát hiện được lỗi ngẫu nhiên/cá nhân, không phát hiện được lỗi hệ thống dùng chung một hiểu lầm. Trường hợp cụ thể của bạn minh hoạ rõ: mức đồng thuận lớp chỉ 74.5% — tức 12/47 cặp bất đồng — và lỗi tập trung vào hai nhóm bus↔van, truck↔bus; ngay cả khi IoU hình học rất cao (>0.9), nhãn lớp vẫn sai, cho thấy hình học đúng không đảm bảo ngữ nghĩa đúng.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.


Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất: Đối chiếu nhãn (Mục 6) cho thấy IoU hình học trung bình đạt 0.864 trên 47 cặp ghép — chứng tỏ vị trí và kích thước box khá chính xác. Tuy nhiên, mức đồng thuận lớp chỉ 74.5% với hai lỗi hệ thống bus↔van (4 cặp) và truck↔bus (5 cặp) cho thấy vấn đề nằm ở **hiểu sai ranh giới phân lớp trong guideline**, không phải ở kỹ năng vẽ box. Ngoài ra, hai gói xuất YOLO và CVAT native có SHA-256 cố định trước khi mở bộ tham chiếu — đảm bảo tính độc lập của bài.

Câu hỏi cho Lab Coach: Bài có 112 box trong khi tham chiếu chỉ có 50 — 65 box thừa chủ yếu là vật thể nhỏ/xa ở hậu cảnh. Guideline ghi "vật thể quá nhỏ hoặc mờ: không đoán", nhưng không nêu ngưỡng kích thước cụ thể. Vậy ngưỡng diện tích tối thiểu (theo pixel hoặc tỷ lệ ảnh) nên là bao nhiêu để quyết định gán hay bỏ?
