# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| Zone edge & boundary / `adasind_140160.jpg`, `adasind_230910.jpg` | 4 ca: BOX_GEOMETRY, ATTRIBUTE (truncated), SPURIOUS (model tách rider) | Vùng rìa thấu kính fisheye có quang sai và độ méo lớn nhất; vật thể bị biến dạng hình học cực đại hoặc cắt bởi vành kính. Đây là vùng rủi ro an toàn cao nhất khi phương tiện chuyển làn hoặc tấp lề. | Ảnh raw fisheye gốc, qa_overlay.html, ảnh crop chi tiết rider (`screenshot_rider_r03.png`) và mép xe tải (`screenshot_truck_edge_r02.png`). |
| Phương tiện hỗn hợp & che khuất / `adasind_140160.jpg`, `adasind_230910.jpg` | 6 ca: WRONG_CLASS (ThreeWheeler vs Car/Truck), MISSING (người đi bộ bị che khuất một phần) | Môi trường giao thông phức tạp với mật độ dày đặc; model bị nhầm lẫn class giữa ThreeWheeler và Car/Truck, đồng thời suy giảm mạnh độ nhạy khi người đi bộ bị phương tiện che khuất (occluded=true). | File annotations.xml có nhãn occluded, bảng confusion matrix từ `local_quality_confusion.csv`, trích xuất tọa độ cụm người đi bộ. |

Giới hạn của kết luận từ ba frame ADASIND: Bộ dữ liệu 3 frame chỉ cung cấp một mẫu vi mô tức thời tại camera đơn phía trước trong điều kiện ban ngày tiêu chuẩn. Kích thước mẫu quá nhỏ (20 vật thể) không phản ánh được tính liên tục thời gian của bài toán tracking, không bao hàm các biến số thời tiết cực đoan (mưa, lóa sáng ban đêm) và không đại diện cho trường nhìn của 3 camera còn lại (sau, trái, phải).

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` (kể cả tránh đếm nhiều frame liền nhau trong cùng cảnh như nhiều ca độc lập), và vì sao kế hoạch đó chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi:
1. Soát độ phủ 200 frame: Phân bổ cân đối theo ma trận 4 camera (front, rear, left, right) × 2 phân tầng (normal, hard) đạt đúng tổng 200 frame.
2. Tránh đếm trùng tương quan thời gian (temporal auto-correlation): Áp dụng quy tắc khoảng cách khung hình tối thiểu: các frame được trích xuất trong cùng một chuỗi video phải cách nhau ít nhất 3–5 giây (tương đương 30–50 frame ở 10 fps). Tuyệt đối không chọn các frame liên tiếp của cùng một phương tiện đang dừng đỗ để tránh làm sai lệch phân phối ca lỗi.
3. Vì sao chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi thực tế: Phương pháp phân bổ lấy mẫu này là *lấy mẫu có chủ đích* (targeted/stratified sampling) nhằm tối đa hóa khả năng phát hiện các trường hợp biên khó (hard cases/edge cases). Vì tỷ lệ ca khó được đẩy cao nhân tạo trong tập mẫu (chiếm 55% tổng số frame), tỷ lệ lỗi quan sát được trên 200 frame này sẽ cao hơn nhiều so với tỷ lệ lỗi thực tế trên toàn bộ 50.000 frame vận hành tự nhiên của hệ thống. Kế hoạch này là công cụ chẩn đoán điểm yếu mô hình, không dùng làm thước đo xác suất lỗi thống kê toàn hệ thống.
