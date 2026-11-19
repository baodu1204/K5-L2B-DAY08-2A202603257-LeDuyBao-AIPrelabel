# Báo cáo Day 08 — 2A202603257 - Lê Duy Bảo

> Mọi con số kèm mẫu số và nguồn dữ liệu trong workspace.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn file) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26m | mặc định 0.3 | Trên 4 ảnh gold, `threshold_sweep.csv`: person 16/22 = 72.7% recall tại 0.3; motorbike 8/9 = 88.9% tại 0.1; car 7/8 = 87.5% tại 0.3; bicycle 4/4 = 100% nhưng chỉ có 4 vật gold. Bus 0/0 gold và truck 0/1 gold ở 0.3, không đủ mẫu riêng. |
| three_wheeler | YOLOE-26s, prompt tuk-tuk/cyclo/cargo tricycle | 0.15 | Trên 7 vật gold: recall 7/7 = 100%, precision 7/(7+2) = 77.8%; nguồn `threshold_sweep.csv`. |

Chuỗi ngưỡng Part 4: `--thr "three_wheeler=0.15"`.

- Lệnh hợp nhất pre-label: dùng quy trình `merge_prelabels.py` với các pre-label đã chạy trong lab.
- SAM 2.1 (`sam2.1_b.pt`) được thử cho mask tuk-tuk: 16 mask trên 2 ảnh, 6.45 giây/ảnh theo `model_log.csv` dòng `r04`.
- Lỗi không sửa bằng ngưỡng: tuk-tuk được gọi `car` hoặc tạo box thừa trên mái/nền; sửa bằng mapping sang `three_wheeler` và duyệt mắt người.
- Box điểm cao mà sai có trong QC: `img_003.jpg`, box #3 và #4 thua. Không ghi phiếu box điểm thấp mà đúng riêng; không suy đoán.
- Giới hạn: ngưỡng được chọn trên chính 4 ảnh gold, nên precision/recall gold có thể lạc quan. Kết quả bus/truck/bicycle có mẫu rất nhỏ.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 453 | 453/523 = 86.6% | 523 box máy |
| edited | 20 | 20/523 = 3.8% | 523 box máy |
| deleted | 38 | 38/523 = 7.3% | 523 box máy |
| added | 34 | 34/507 = 6.7% | 507 đối tượng sau duyệt |

- Lớp máy tệ nhất: `three_wheeler` có 17 deleted/38 = 44.7% tổng box bị xóa; còn 21 accepted, 4 edited và 4 added. `img_003.jpg` có bằng chứng box thừa/sai ba bánh.
- So với gold: precision máy 40 TP/(40 TP+34 FP) = 40/74 = 54.1%; recall 40/(40 TP+11 FN) = 40/51 = 78.4%; mẫu 51 đối tượng gold trên 4 ảnh.

## 3. Thiên lệch tự động hóa và ảnh bẫy

- Bắt bẫy: 11/12 = 91.7%. Bẫy missing 2/3, extra 3/3, wrong_class 3/3 và loose 3/3 theo `review_stats.md`; chỉ `img_029.jpg` loại missing lọt.
- Accepted 453/523 = 86.6% cao nhưng vẫn còn 1/12 bẫy lọt. Điều này cho thấy duyệt không thể chỉ tin box máy; cần quét vật thật trước, rồi mới kiểm box.

## 4. Máy có đáng dùng không?

- Gán tay: 12 phút/4 ảnh = 3.0 phút/ảnh theo `time_log.csv`.
- Tốc độ suy luận tạo pre-label theo `model_log.csv`/`time_log.csv`: YOLO26m r01 = 0.92 giây/ảnh (0.0153 phút/ảnh); YOLOE-26s r03 = 0.29 giây/ảnh (0.0048 phút/ảnh). Đây là tốc độ chạy model trên pilot, không phải thời gian người duyệt box.
- Review có pre-label: 30 phút/24 ảnh = 1.25 phút/ảnh theo `time_log.csv` (thời gian người dùng cung cấp). So với gán tay 3.0 phút/ảnh, nhanh hơn 1.75 phút/ảnh trong số đo này; quy trình và độ khó ảnh có thể khác nên chưa phải so sánh có kiểm soát.
- Máy chỉ nên dùng làm hỗ trợ pre-label, không dùng làm nhãn cuối chưa kiểm: 453/523 box giữ, 38/523 box xóa, 34/507 vật thêm; vòng QC sau rework lần 1 ghi nhận 3/24 ảnh lỗi (12.5%), vòng tái kiểm mới nhất ghi nhận 0/24 ảnh lỗi.
- Ba quy tắc giảm thiên lệch:
  1. Quét vật trước khi nhìn box máy; bắt được 11/12 bẫy.
  2. Kiểm riêng `three_wheeler` và không tin box nằm trên mái/nền.
  3. Kiểm kích thước box: trong QC, 2/103 đối tượng có lỗi vị trí/kích thước.

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM | Số click quan sát | Mask gồm người lái/hàng hoá? | Lỗi quan sát | File |
|---|---|---|---|---|---|
| img_017.jpg | SAM 2.1 | 3 điểm xanh | Bao xe và thùng; không tách người lái/hành khách thành mask riêng. | Cần kiểm biên và phần vật gắn liền xe. | `submission/masks/tuktuk_img017_sam2.jpg` |
| img_013.jpg | SAM 2.1 | 3 điểm xanh | Bao cab/tuk-tuk; không tách riêng người ngồi trong. | Mask chưa thể hiện rõ lỗ/cửa hoặc vách; chưa có IoU. | `submission/masks/tuktuk_img013_sam2.jpg` |
| img_004.jpg | SAM 2.1 | 4 điểm xanh | Bao xe ba bánh/chassis; không tách riêng người lái. | Có thể cần điều chỉnh biên và phần hở của xe. | `submission/masks/tuktuk_img004_sam2.jpg` |

- Guideline mục 4.2 giúp với bám biên và bỏ bóng đổ, nhưng chưa nêu đủ cách tách người trong xe ba bánh khỏi mask vật xe.
- Không ghi log thử Interactor SAM trong CVAT; chỉ có mask SAM offline trong thư mục `submission/masks/`.

## 6. Calibration (Part 6)

- Có 3 quyết định trong `submission/qc/calibration_decisions.csv`: CAL-01 `loi_may`, CAL-02 `cau_hoi_mo`, CAL-03 `bay_lot`.
- Quan trọng nhất: CAL-01 `img_003.jpg`, box #3/#4 là thừa vì máy phát hiện trên mái/nền thay vì xe ba bánh thật.
- Quy tắc mới trong `submission/guidelines/guideline_v1.1_calibrated.md`: chỉ gán ba bánh khi thấy xe thật; cụm xe máy gần camera chỉ dùng `ignore_region` khi không tách được; cảnh tối vẫn gán vật rõ đạt kích thước 20 px và che <80%.
- Nguyên nhân gốc: máy tạo box thừa; quy tắc cụm xe ba bánh còn mơ hồ; bẫy missing cho thấy cần quét lại cảnh tối.

## 7. QC và quyết định (Part 6)

- **Vòng QC ban đầu:** 5 ảnh (0 ảnh `can_review` + 5 ảnh ngẫu nhiên, seed 42), 103 đối tượng. Phiếu ghi 5/103 = 4.9% lỗi theo đối tượng và 2/5 = 40.0% ảnh có lỗi. Lỗi: `thua` 3 lần và `sai_vi_tri_kich_thuoc` 2 lần; cận trên Wilson 95% là 77% (2/5 ảnh lỗi).
- **Vòng QC sau rework lần 1:** kiểm đủ 24 ảnh, 513 đối tượng (không tính `ignore_region`). Phiếu ghi 3/513 = 0.6% lỗi theo đối tượng và 3/24 = 12.5% ảnh có lỗi: sai lớp ở `img_004.jpg`, box thừa ở `img_008.jpg`, box sai kích thước ở `img_009.jpg`. Cận trên Wilson 95% là 31% (3/24).
- **Rework lần 2 và QC lại:** release mới có 24 ảnh, 523 đối tượng; đã kiểm lại 24 ảnh (511 đối tượng, không tính `ignore_region`). Phiếu `qc_review.csv` ghi 2/511 = 0.4% lỗi theo đối tượng và 2/24 = 8.3% ảnh có lỗi. Hai lỗi vị trí/kích thước còn lại: `img_008.jpg` #100 (box ba bánh quá rộng, phủ nhiều xe và chồng #97) và `img_009.jpg` #109 (box xe máy bao tải chuối nhô xa).
- **Rework lần 3 và tái kiểm:** đã thu gọn box ở `img_008.jpg` và `img_009.jpg`, build release mới (24 ảnh, 523 đối tượng), rồi kiểm lại mẫu 24 ảnh (511 đối tượng, không tính `ignore_region`). Phiếu `qc_review.csv` ghi 0/511 = 0.0% lỗi theo đối tượng và 0/24 = 0.0% ảnh có lỗi; không ghi nhận lỗi vị trí/kích thước.
- Cận trên Wilson 95% ở lần tái kiểm mới nhất là 14% (0/24 ảnh lỗi). Vì mẫu chỉ có 24 ảnh, kết quả không chứng minh tỷ lệ lỗi thật dưới 2%.
- Script kiểm được cấu trúc file, ảnh, lớp và số đếm theo phiếu. Script không xác nhận box có ôm sát vật thật, vật cả máy lẫn người cùng bỏ sót, hay người kiểm có thực hiện checklist.

**Quyết định hiện tại: Release**, ngày 2026-11-19. Vòng tái kiểm sau rework ghi nhận 0/24 ảnh lỗi (0.0%), trong ngưỡng quan sát tối đa 2%; cận trên Wilson 95% vẫn là 14%, nên đây không phải bằng chứng thống kê rằng tỷ lệ lỗi thật dưới 2%.

- Rework các box đã sửa được xác nhận trong mẫu QC mới; tiếp tục dùng quy trình kiểm bằng mắt và lấy mẫu bổ sung nếu cần tăng độ tin cậy thống kê.

## 8. Ghi chú cá nhân

- Dùng YOLO26m, YOLOE-26s và SAM 2.1 để tạo dự đoán/mask; tất cả kết quả QC được kiểm trực tiếp trên ảnh.
- Gold có thể bị nhiễm: workspace không ghi rõ gold được gán hoàn toàn trước pre-label hay không. Không suy đoán đủ; lần sau lưu dấu thời gian và quy trình gán vàng trước pre-label.
- Máy: Windows, GPU không. Phiên bản CVAT/Docker không ghi trong hồ sơ, chưa xác nhận.
- Cache `data/cache/` có trong workspace, nhưng log không ghi rõ từng kết quả có lấy từ cache hay chạy lại; không khẳng định.
- Không ghi bug script làm sai báo cáo. `qc_report.py` đã chạy lại và tạo số liệu nhất quán với phiếu.
- Lần sau: ghi thời gian duyệt, số click SAM, phiên bản CVAT và sản phẩm vàng đã từng nhìn pre-label hay chưa ngay trong log.
