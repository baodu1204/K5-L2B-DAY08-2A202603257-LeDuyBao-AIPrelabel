# Label contract — Phố châu Á — BỔ SUNG v1.1 — Le Duy Bao

Tài liệu này chỉ ghi các thay đổi được chốt từ calibration so với `guideline_v1.0_label_contract.md`. Mỗi ca dưới đây có ảnh ví dụ và khớp một dòng trong `submission/qc/calibration_decisions.csv`.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 2 | Khối xe máy đỗ dày đặc gần camera: từng xe hay `ignore_region`? | Gán từng `motorbike` nếu còn nhận ra thân xe và bánh riêng; dùng `ignore_region` chỉ khi các vật không thể tách thành từng cá thể và phần nhìn thấy mờ/sát nhau. Quy tắc vật quá nhỏ/cần che vẫn áp dụng mức cao >= 20 px và nhìn thấy >= 20%. | img_006.jpg | CAL-02 |
| 6 | Box máy chồng nhau quá nhiều trong ảnh cảnh đông: gộp hay giữ từng box? | Giữ từng vật riêng nếu box có thể ôm sát vật thật; không gộp nhiều xe hoặc người vào một box. Box kéo dài sang nền/mái che hoặc bao vật không liên quan thì xóa/sửa. | img_003.jpg | CAL-01 |

## 2. Ca khó mới (từ lỗi của máy và bẫy lọt)

| Ảnh | Tình huống | Máy làm gì | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| img_003.jpg | Máy phát hiện #3 và #4 trên khu vực mái nhà và không có xe ba bánh thực. Box #2 cũng bao vùng rộng ngoài vật. | Tạo các box `three_wheeler` thừa/trùng nền. | Xóa box thừa; sửa box ba bánh còn lại theo phần nhìn thấy của xe thật. | Mục 2 `three_wheeler` và mục 4.1.1: lớp ba bánh phải dựa trên khung xe/bánh ba bánh thật; không gán theo mái che hoặc nền. |
| img_006.jpg | Xe máy đỗ sát nhau ở gần camera; có thể nhầm cụm thành một vùng bỏ qua. | Phát hiện được nhiều box xe riêng, một số box có thể che nhau. | Gán từng xe máy có thể tách; box ôm sát từng xe, không dùng một box chung cho cả cụm. | Mục 6 câu 2: cụm gần camera không tự động trở thành `ignore_region`; chỉ bỏ qua cụm không tách được. |
| img_029.jpg | Bẫy missing trong giao thông đêm đông; dễ bỏ sót motorbike/person bị che dần. | Một vật trong lô đã lọt và không có box tương ứng sau duyệt. | Quét ảnh trước, sau đó quét lại dọc từng luồng xe; gán vật rõ ràng còn cao >= 20 px và phần nhìn thấy >= 20%. | Mục 4.1.2 và mục 7 QC: không bỏ qua vật vì cảnh tối; điều kiện che và kích thước vẫn được áp dụng giống nhau. |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Chốt cách xử lý cụm xe máy gần camera; yêu cầu box ba bánh dựa trên vật thật; siết quy trình bắt vật bỏ sót trong cảnh tối. | Loi may CAL-01; cau hoi mo CAL-02; bay lot CAL-03 |
