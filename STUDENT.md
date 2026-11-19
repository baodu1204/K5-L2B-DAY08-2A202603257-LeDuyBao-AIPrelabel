# Thông tin học viên

- Họ và tên: Lê Duy Bảo
- MSHV: 2A202603257
- Lớp (L2 / L3 / khác): L2B
- Tài khoản GitHub: baodu1204
- Repo bài nộp (URL): https://github.com/baodu1204/K5-L2B-DAY08-2A202603257-LeDuyBao-AIPrelabel
- Commit chốt bài (hash): chưa xác định (workspace không được quản lý như Git repository)
- Máy: Windows / GPU: không
- CVAT (Docker): 2.74.1

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: 453 / 20 / 38 (mẫu số: 523 box máy); added: 34 (mẫu số: 507 đối tượng sau duyệt)
- So với gold: precision / recall của **máy** 40/74 = 54.1% / 40/51 = 78.4% (mẫu số: 74 dự đoán có thể match/51 đối tượng gold)
- Bắt bẫy: 11/12 = 91.7%
- Thời gian: gán tay 12/4 = 3.0 phút/ảnh; review batch 30/24 = 1.25 phút/ảnh; suy luận pre-label YOLO26m 0.92 giây/ảnh, YOLOE-26s 0.29 giây/ảnh
- Lỗi QC sau rework lần 3/tái kiểm: 0.0% theo ảnh (0/24), 0.0% theo đối tượng (0/511); cận trên Wilson 95% theo ảnh: 14%
- **Quyết định:** Release — 0/24 ảnh lỗi = 0.0%, đạt ngưỡng quan sát tối đa 2%; cận trên Wilson 95% vẫn là 14% nên chưa chứng minh thống kê tỷ lệ lỗi thật dưới 2%
