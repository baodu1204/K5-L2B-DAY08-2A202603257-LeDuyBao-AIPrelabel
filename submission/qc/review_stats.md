# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 523; đối tượng sau duyệt: 507; ngưỡng accepted: IoU >= 0.9
- Đã **loại 18 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 453 | 453/523 = 86.6% | 523 box máy |
| edited (sửa) | 20 | 20/523 = 3.8% | 523 box máy |
| deleted (xoá) | 38 | 38/523 = 7.3% | 523 box máy |
| added (vẽ thêm) | 34 | 34/507 = 6.7% | 507 đối tượng sau duyệt |

Trong số deleted + added, **4 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 30.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 257 | 6 | 6 | 14 |
| bicycle | 4 | 1 | 2 | 3 |
| motorbike | 119 | 9 | 4 | 10 |
| car | 47 | 0 | 5 | 3 |
| bus | 2 | 0 | 1 | 0 |
| truck | 3 | 0 | 3 | 0 |
| three_wheeler | 21 | 4 | 17 | 4 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 51 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 40 | 34 | 11 | 54.1% (KTC 95%: 43–65%) | 78.4% (KTC 95%: 65–88%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 11/12 = 91.7% (KTC 95%: 65–99%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - missing: 2/3
  - extra: 3/3
  - wrong_class: 3/3
  - loose: 3/3
  - lọt: img_029.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
