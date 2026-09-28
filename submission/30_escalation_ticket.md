# Escalation ticket

## Ticket 1

- **Frame:** `adasind_140160.jpg` (kèm ca tương tự trên `adasind_019560.jpg`)
- **Ảnh chụp:** `submission/screenshots/screenshot_rider_r03.png`
- **Expected impact:** Model YOLO26m hiện tại liên tục tách rời người điều khiển xe máy thành 2 bounding box độc lập (`Pedestrian` ở trên và `Bike` ở dưới). Việc này làm tăng gấp đôi số lượng đối tượng rác trong module tracking thời gian thực, gây nhiễu nghiêm trọng cho thuật toán tính toán thời gian chạm trán (Time-to-Collision - TTC) và bộ lập quỹ đạo né tránh va chạm (collision avoidance planner) của xe tự hành SVM.
- **Owner:** `ai_team`
- **Recommendation:**
  1. Tinh chỉnh (fine-tune) lại backbone và detection head của model với tập dữ liệu huấn luyện đặc thù đã tuân thủ chuẩn SVM R03 (hợp nhất người lái và xe hai bánh thành một bounding box duy nhất).
  2. Bổ sung logic hậu xử lý (post-processing / smart NMS rule) để tự động nhận diện và hợp nhất cặp box `Pedestrian` và `Bike` có cấu trúc không gian tương quan người lái.
  3. Bổ sung bài kiểm tra hồi quy (regression test suite) chuyên biệt cho các ca xe hai bánh đa góc nhìn trước khi đưa model vào production.
