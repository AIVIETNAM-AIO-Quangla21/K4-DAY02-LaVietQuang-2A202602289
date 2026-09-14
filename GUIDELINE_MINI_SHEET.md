# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** LÃ VIỆT QUANG<br>
**MSSV:** 2A202602289<br>
**Hình thức:** Cá nhân — cá nhân hoặc theo cặp<br>
**Mã cặp:** `SOLO` — ghi `SOLO` nếu làm cá nhân

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

- Ảnh và mã vật thể: drive_008 / Hộp số 12 (Chiếc xe màu bạc/trắng ở ngay giữa ngã tư, phía sau chiếc xe tải ben màu đỏ).
- Dấu hiệu nhìn thấy: Xe có thân dạng hộp kín, có cửa sổ kính nhưng chiều dài xe khá ngắn, không có hàng ghế dài hay thân hình quá khổ như xe khách.
- Quy tắc áp dụng: Theo mục 2 của phiếu quy tắc, lớp van được mô tả là "thân hộp nhỏ, kín, dùng chở người hoặc hàng", dùng để phân biệt với lớp bus là những xe có "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế"
- Quyết định: Gán nhãn lớp van
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu trạng thái needs_review, chụp màn hình gửi Lab Coach để xác nhận. Nếu ảnh bị nhòe và không thể xác định độ dài thân xe, tôi sẽ xóa bỏ hộp để tuân thủ quy tắc "không đoán"

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_038 / Hộp số 5 (Chiếc xe màu trắng ở góc dưới cùng bên phải ảnh).
- Dấu hiệu nhìn thấy: Phần đầu xe giống xe tải nhỏ, phía sau không phải khoang chở người kín mà là hệ thống cẩu/kéo xe chuyên dụng
- Quy tắc áp dụng: Theo mục 2, lớp truck (xe tải) bao gồm các xe có "thiết bị công vụ rõ ràng", không bị xếp vào lớp car (chỉ áp dụng cho "xe bán tải dùng như xe con") hay lớp van (không bao gồm "khoang hàng tách biệt như xe tải").
- Quyết định: Gán nhãn lớp truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Giữ trạng thái needs_review và đối chiếu kỹ lại định nghĩa. Nếu vật thể quá mờ khiến phần đuôi xe bị hòa vào nền đường ướt, không thể khẳng định là cẩu hay mui bạt, tôi sẽ tiến hành xóa hộp.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_033 / Hộp số 2 (Chiếc xe buýt màu đỏ nằm ở sát mép dưới cùng bên trái ảnh).
- Dấu hiệu nhìn thấy khi phóng 100%: Chỉ nhìn thấy một phần nóc và kính lái phía trước của một chiếc xe lớn màu đỏ đang đi vào khung hình, phần lớn thân xe bị cắt bỏ nằm ngoài mép ảnh.
- Giá trị `visibility`: clear (vì phần thân lộ ra trong ảnh nhìn rất rõ ràng, không bị vật thể khác che lấp, chỉ bị cắt mép).
- Giá trị `boundary`: truncated (vì vật thể đang bị mép ảnh cắt ngang)
- Trạng thái `review_state`: confident
- Lý do: Tuy xe bị mép ảnh cắt ngang (truncated), phần thân lộ ra vẫn đủ rõ ràng (clear) và có đủ đặc điểm (kính lái phẳng rộng, nóc xe to) để tôi tự tin (confident) phân loại nó vào lớp bus. Theo quy tắc mục 3, "Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp"

## 6. Xác nhận tự kiểm tra

- [X] Đã rà đủ bốn ảnh.
- [X] Đã kiểm vật thể thiếu và trùng.
- [X] Đã kiểm lớp và hình học từng hộp.
- [X] Mỗi hộp có đủ ba thuộc tính.
- [X] Đã xử lý mọi hộp `needs_review`.
- [X] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [X] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [X] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [X] Số vật thể thực tế: 42 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
