# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Đôn Quốc Tuấn<br>
**MSSV:** 2A202602127<br>
**Hình thức:** Cá nhân — cá nhân hoặc theo cặp<br>
**Mã cặp:** SOLO — ghi `SOLO` nếu làm cá nhân

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_022` — xe lớn ở tiền cảnh, box tâm (0.384, 0.720), kích thước 0.437×0.360
- Dấu hiệu nhìn thấy: Thân xe dài, có nhiều cửa kính dọc thân, dáng hộp kín. Ban đầu nhìn giống xe buýt vì thân dài và có nhiều cửa sổ.
- Quy tắc áp dụng: Guideline phân biệt `bus` = "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế" vs `van` = "thân hộp nhỏ, kín, dùng chở người hoặc hàng". Cần xem xét: xe này có thực sự là xe khách công cộng nhiều hàng ghế, hay chỉ là xe van thân hộp dài?
- Quyết định: Gán `bus` (class 2) — vì thấy thân xe dài và nhiều cửa kính hành khách, phù hợp đặc trưng xe buýt hơn xe van.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `review_state = needs_review`, ghi chú nghi vấn vào nhật ký quyết định, và chờ đối chiếu với bộ tham chiếu hoặc hỏi Lab Coach để xác nhận. *(Sau đối chiếu: tham chiếu gán `van` — xe này thực ra là xe van thân hộp dài, không phải xe buýt.)*

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_008` — xe ở vị trí tâm (0.269, 0.238), kích thước 0.083×0.098, và xe tại tâm (0.289, 0.177), kích thước 0.071×0.073
- Dấu hiệu nhìn thấy: Hai xe có dáng hộp, kích thước trung bình, một xe có phần thùng kín phía sau giống xe van chở hàng, xe kia có dáng cabin tách biệt khoang phía sau.
- Quy tắc áp dụng: `truck` = "thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng", `van` = "thân hộp nhỏ, kín". Ranh giới phụ thuộc vào việc khoang chở hàng có tách biệt cabin hay không: tách biệt → `truck`; liền khối kín → `van`.
- Quyết định: Gán `van` (class 3) cho cả hai — vì thân xe liền khối, hộp kín, không thấy thùng/ben/sàn hàng tách biệt rõ ràng.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng to 100% để kiểm tra chi tiết cabin và khoang chở, đánh dấu `review_state = needs_review`. Nếu vẫn không rõ, hỏi Lab Coach kèm ảnh crop vùng xe. *(Sau đối chiếu: tham chiếu gán `truck` cho cả hai — cho thấy cần chú ý hơn đến đặc trưng cabin tách biệt dù thân xe nhìn liền khối.)*

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_008` — xe ở sát mép phải ảnh, tâm (0.985, 0.391), kích thước 0.031×0.078; và xe tại tâm (0.987, 0.471), kích thước 0.026×0.131
- Dấu hiệu nhìn thấy khi phóng 100%: Chỉ thấy một phần rất nhỏ thân xe (khoảng 2–3% diện tích ảnh), phần lớn xe nằm ngoài khung hình. Không đủ chi tiết để xác định loại xe một cách tự tin.
- Giá trị `visibility`: `occluded` — phần lớn vật thể không nhìn thấy được (bị mép ảnh cắt mất)
- Giá trị `boundary`: `truncated` — xe bị mép phải ảnh cắt, chỉ hiện một dải hẹp
- Trạng thái `review_state`: `needs_review` — không đủ bằng chứng để phân lớp tự tin
- Lý do: Theo guideline "Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán". Xe này bị cắt >80% bởi mép ảnh, phần còn lại quá hẹp để phân biệt car/van/truck. Nên cân nhắc xoá box thay vì đoán lớp. *(Sau đối chiếu: tham chiếu không gán box cho vị trí này — xác nhận rằng xe bị cắt quá nhiều không nên gán.)*

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi. *(Không áp dụng — làm cá nhân)*
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: **112** — 40–60 là mục tiêu khối lượng, không phải điểm cắt. *(Lưu ý: vượt khuyến nghị do gán nhiều vật thể nhỏ/xa ở hậu cảnh; tham chiếu chỉ có 50 box.)*

