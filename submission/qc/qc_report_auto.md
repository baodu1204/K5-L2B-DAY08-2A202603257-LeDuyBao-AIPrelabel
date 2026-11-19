# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 24 ảnh (0 ca khó + 24 ngẫu nhiên), 511 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 0/511 = 0.0% | 511 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 0/24 = 0.0% | 24 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 453/523 = 86.6% / 20/523 = 3.8% / 38/523 = 7.3% | 523 box máy |
| Máy: added | 34/507 = 6.7% | 507 đối tượng sau duyệt |
| Bắt lỗi bẫy | 11/12 = 91.7% | 12 bẫy |

## Top 3 loại lỗi
(không ghi nhận lỗi nào)

## Giới hạn thống kê
- 0 ảnh lỗi trong 24 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **14%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**RELEASE**

- ảnh lỗi 0.0% <= 2%, không lỗi hệ thống, bắt bẫy / nhất quán đạt (nếu đã đo)

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
