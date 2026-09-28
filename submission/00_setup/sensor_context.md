# Sensor context

- **Rig:**
  - Qua quan sát trực tiếp từ các frame ảnh ADASIND, đây là một camera fisheye đơn (monocular fisheye camera) được gắn hướng về phía trước xe (khu vực nắp capo / chân kính chắn gió trước / cản trước), ghi lại toàn cảnh giao thông phía trước theo chiều di chuyển của xe.
  - *Giới hạn cảm biến:* Dữ liệu ADASIND chỉ cung cấp frame từ một camera mắt cá độc lập, hoàn toàn không kèm theo tài liệu thông số rig chi tiết, không có ma trận calibration nội/ngoại suy (intrinsics/extrinsics), chiều cao lắp đặt hay góc nghiêng (pitch/roll/yaw). Đây không phải là hệ thống ghép 4 camera SVM 360 hoàn chỉnh trong dữ liệu thực tế này, nên không thể tự suy đoán hay khẳng định các thông số rig chưa được cung cấp.

- **`ego_body`:**
  - Vùng thân xe ego xuất hiện ở phần đáy mép dưới của khung hình (nhìn thấy viền nắp capo / cản trước của xe chủ).
  - Thân xe ego nhìn thấy được ở 46/48 frame ADASIND và cần được vẽ polygon `ignore_region` với thuộc tính `reason="ego_body"`.
  - Hai frame ngoại lệ không nhìn thấy thân xe ego là `adasind_006840.jpg` và `adasind_271039.jpg` (theo quy tắc R07, không vẽ polygon ego_body tại hai frame này).

- **Vòng kính (Lens circle):**
  - Khung hình có kích thước 1080 × 1920 pixel (định dạng dọc). Vòng tròn quang học của thấu kính fisheye nằm ở trung tâm ảnh với tọa độ tâm khoảng cx ≈ 450–610 px, cy ≈ 890–1020 px và bán kính r ≈ 770–830 px (tham chiếu theo `assets/frames.csv`), chiếm khoảng 80% – 85% diện tích khung hình.
  - Vùng tối/đen nằm ngoài vòng kính là vành đen quang học (lens border/vignetting). Vùng này đã được công cụ tạo sẵn 2 polygon `ignore_region` với thuộc tính `reason="lens_border"` cho mỗi frame (R08) để loại khỏi phạm vi đánh giá. Độ méo fisheye tăng dần từ vùng trung tâm (center zone) ra rìa ngoài (edge zone).
