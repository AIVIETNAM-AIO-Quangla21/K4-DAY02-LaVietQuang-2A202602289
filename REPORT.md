# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** LÃ VIỆT QUANG<br>
**MSSV:** 2A202602289<br>
**Hình thức:** Cá nhân — cá nhân hoặc theo cặp<br>
**Mã cặp:** `SOLO` — ghi `SOLO` nếu làm cá nhân

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: "f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33"
- Bốn mã ảnh:"drive_022","drive_033","drive_038","drive_008"
- Số vật thể thực tế: 42
- Mã SHA-256 của gói YOLO của bạn: "e65ba3fe3e7e8389ef6e4ec16f19da8f5e0943003d0dda2aad5b587939e45892"
- Mã SHA-256 của gói CVAT gốc của bạn: "15765d7d818808732c0871cc3e89875570e7e8f7844d0ed4952ccf4c51923197"
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: "bộ nhãn đối chiếu do Lab Coach cấp"
- Mã SHA-256 của gói đối chiếu: "c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b"
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Căn cứ vào quy tắc của bài thực hành, bạn có thể giải thích như sau: "Bài làm của tôi đảm bảo tính độc lập vì tôi đã hoàn thành việc gán nhãn và xuất ra 2 gói dữ liệu (YOLO và CVAT) trước khi chạy các ô lệnh đối chiếu. Theo quy tắc, nếu làm cá nhân, tôi chỉ nhận bộ tham chiếu sau khi đã tự kiểm tra bài của mình; nếu làm theo cặp, hai người tuyệt đối không xem tác vụ hay gói xuất của nhau cho đến khi cả hai cùng hoàn thành."

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_038 / Chiếc xe màu trắng ở góc dưới bên phải (có gắn cẩu phía sau). | truck | Phần thân xe phía sau không phải là khoang chở người mà được gắn hệ thống cẩu/kéo chuyên dụng. | Được xếp vào lớp xe tải (truck) vì có "thiết bị công vụ rõ ràng" |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Trong ảnh drive_008, nhìn vào chiếc xe con màu đen chạy ngay phía bên trái chiếc xe buýt lớn. Lớp (Class) xác định bản chất của phương tiện đó là ô tô con (car). Trong khi đó, Thuộc tính (Attribute) xác định trạng thái của chiếc xe đó trong ảnh: nó đang bị thân chiếc xe buýt che mất một phần, do đó thuộc tính mức độ nhìn thấy của nó phải là visibility = occluded (bị che khuất), khác với những chiếc xe đi bám ngay đầu mép ảnh có thuộc tính boundary = truncated (bị mép ảnh cắt).

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Hộp giới hạn của chiếc xe ô tô đen ở góc dưới cùng bên phải (ảnh drive_038) được vẽ bao trùm luôn cả phần hình ảnh phản chiếu của nó trên mặt đường ướt. | hình học | Phóng to ảnh 100% và rà soát lại ranh giới các hộp giới hạn (đặc biệt là mép dưới của xe). | Kéo mép dưới của hộp thu nhỏ lại, chỉ bám sát vỏ xe và bánh xe thực tế. Tuân thủ quy tắc: "Không gán phần phản chiếu" và "Vẽ sát phần vật thể nhìn thấy".|

- Số hộp `needs_review` trước và sau khi kiểm: 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:0

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`:0 0.5432 0.6789 0.1234 0.0987
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp=0 (car) | pixel xyxy: [310.5, 400.0, 390.2, 460.5]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Việc đúng định dạng (dòng text gồm 5 cột số, tọa độ chuẩn hóa nằm trong khoảng [0, 1]) chỉ đảm bảo công cụ Ultralytics đọc được file mà không báo lỗi lập trình (syntax error). Nó không thể kiểm chứng ý nghĩa thực tế (semantic). Chẳng hạn, người gán nhãn có thể nhận diện nhầm xe bán tải thành xe tải (sai lớp), vẽ hộp bao gồm cả hình phản chiếu trên mặt đường ướt (sai hình học), hoặc khoanh luôn cả người đi xe đạp vào cùng hộp ô tô (sai phạm vi quy tắc)

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Trong ảnh detect_result.jpg thực tế của tôi, không có bất kỳ hộp dự đoán nào xuất hiện. Mô hình không đưa ra được nhận diện nào vượt qua ngưỡng độ tự tin conf=0.25 đã cài đặt trong mã nguồn
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Kiểm tra file job_1_annotations_2026_09_14_07_58_53_cvat for images 1.1
- Minh chứng nào có thể bác bỏ nhận định của bạn?
Minh chứng rõ ràng nhất để bác bỏ việc "dữ liệu nhãn bị lỗi định dạng hoặc trống rỗng" chính là kết quả vượt qua hệ thống kiểm thử tự động tại ô lệnh 3c (Đọc một dòng YOLO thật) và báo cáo my_export_audit.json. Nếu ô lệnh 3c vẫn in ra được dòng text đúng chuẩn 5 cột (class_id và 4 tọa độ hộp nằm trong khoảng 0-1) cùng số pixel cụ thể, điều này chứng minh dữ liệu hoàn toàn bình thường về mặt kỹ thuật.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Khối lượng dữ liệu quá nhỏ (3 ảnh huấn luyện, 1 ảnh đánh giá) hoàn toàn không có ý nghĩa thống kê và không thể khái quát cho các môi trường giao thông phức tạp. Sổ tay thực hành đã nhấn mạnh quá trình này chỉ là "tín hiệu chẩn đoán, không phải điểm đạt" và thiết lập cờ "not_production_benchmark": True nhằm mục đích duy nhất là kiểm tra xem luồng kỹ thuật từ khâu gán nhãn đến huấn luyện có bị gãy hay không.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 32
- IoU trung bình và trung vị:0.797017, 
- Mức đồng thuận lớp:0.804968
- Số hộp phía bạn không ghép được:9
- Số hộp phía đối chiếu không ghép được:18
- Một điểm khác biệt cụ thể: Khi xem ảnh đối chiếu comparison_overlay.png ở ảnh drive_038, tôi phát hiện phía tôi (màu đỏ) khoanh một chiếc xe ở rất xa góc trên bên trái, nhưng phía nguồn đối chiếu (màu xanh) không khoanh chiếc xe này.
- Quy tắc hoặc hành động sửa phát sinh:"Tôi đã đối chiếu lại với quy tắc ở Mục 1 (Phạm vi): 'Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán'. Nhận thấy hộp tôi khoanh đúng là quá mờ, không thể khẳng định là xe con hay xe van, tôi quyết định xóa hộp này đi để tránh gây nhiễu dữ liệu."
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? 

Theo cảnh báo giải thích từ hệ thống (interpretation_warning), đối chiếu IoU và mức đồng thuận lớp chỉ là phép đo "khả năng tái lập quy tắc" giữa hai nguồn độc lập. Mức đồng thuận cao chỉ chứng tỏ hai bên thống nhất với nhau, nhưng hoàn toàn có thể xảy ra trường hợp cả hai cùng sai một cách có hệ thống (ví dụ: cả hai người cùng nhầm một chiếc hatchback thành xe tải, hoặc cả hai cùng vẽ hộp dính bóng đổ của xe). Do đó, đây là công cụ hỗ trợ phản hồi, không phải đánh giá chất lượng sản xuất cuối cùng.

## 7. Kiểm tra kho GitHub cá nhân

- [ ] Có phiếu quy tắc với ba tình huống mơ hồ.
- [ ] Có kết quả kiểm hai gói xuất.
- [ ] Có thông tin lần huấn luyện và ảnh dự đoán.
- [ ] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [ ] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [ ] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Ảnh phủ đối chiếu comparison_overlay.png và các log phân tích lỗi hình học. Nó minh chứng rõ ràng việc tôi đã làm bài hoàn toàn độc lập, tự nhận ra lỗi sai (như vẽ hộp dính bóng đổ trên đường ướt) thông qua việc so sánh chéo IoU.
Câu hỏi còn lại cho Lab Coach: Qua ô lệnh số 4, mô hình dự đoán hoàn toàn "trắng" (không ra hộp nào) do chỉ được huấn luyện 8 epochs trên 3 ảnh. Vậy trong các dự án thực tế, người ta thường dùng một tập dữ liệu tối thiểu (baseline dataset) cỡ bao nhiêu ảnh để mô hình bắt đầu "biết nhìn" và có thể dùng kết quả dự đoán đó để chẩn đoán ngược lại chất lượng gán nhãn của con người?
