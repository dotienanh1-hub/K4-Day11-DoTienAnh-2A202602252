# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy tắc riêng? Vì sao?
   - Đây **không phải lỗi `DUPLICATE`** ở tầng gán nhãn 2D cục bộ, mà **cần một quy tắc riêng cho vùng chồng lấn (Cross-camera Seam Policy)**.
   - Vì sao: Mỗi camera mắt cá có một vị trí đặt và tâm quang học riêng biệt. Một vật thể vật lý duy nhất trong thế giới 3D khi nằm tại góc giao thoa giữa hai trường nhìn (ví dụ camera trước và camera hông trái) sẽ tạo ra hai hình chiếu quang học hoàn toàn có thật và hợp lệ trên hai mặt cảm biến khác nhau, với góc nhìn và mức độ méo fisheye khác nhau. Nếu xóa bỏ một trong hai box trên ảnh raw, hệ thống nhận thức 2D của camera đó sẽ bị mất dấu đối tượng. Quy tắc chuẩn trong hệ thống SVM là: giữ nguyên cả hai box độc lập trên không gian raw 2D của từng camera (kèm nhãn hoặc Cross-camera Global ID liên kết), và để module hợp nhất đa góc nhìn (Multi-view Fusion / Bird's-Eye View mapping) gộp chúng thành một thực thể duy nhất trong không gian 3D quanh xe.

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.
   - Khi nào giữ cùng Track ID: Khi vật thể di chuyển liên tục trong trường nhìn của một camera và chuyển động tuân theo quy luật động học hợp lý (kinematic consistency), hoặc khi vật thể bị che khuất tạm thời trong thời gian rất ngắn (dưới 1 giây / vài frame) rồi tái xuất hiện ngay tại quỹ đạo dự đoán.
   - Khi nào thêm Keyframe hoặc trạng thái Outside:
     + Thêm Keyframe: Khi đối tượng đổi hướng di chuyển đột ngột, thay đổi hình dáng/tư thế mạnh (pose change, ví dụ người đi bộ từ đi thẳng chuyển sang quay lưng hoặc cúi xuống) khiến việc nội suy tuyến tính giữa các frame bị sai lệch.
     + Trạng thái Outside: Khi vật thể di chuyển hoàn toàn ra ngoài biên ảnh hoặc đi vào vùng vành đen/vùng che khuất hoàn toàn của vành kính mắt cá.
   - Bằng chứng cần thiết trước khi nối track qua hai camera:
     1. Đồng bộ thời gian phần cứng (hardware timestamp synchronization < 5ms) giữa hai camera.
     2. Ma trận hiệu chuẩn ngoại (extrinsic calibration matrix) chính xác thể hiện vị trí không gian tương đối giữa hai cảm biến.
     3. Tính liên tục không-thời gian: vector vận tốc, vị trí thoát khỏi camera 1 phải khớp với vị trí và thời điểm xuất hiện trên camera 2.
     4. Tính nhất quán về đặc trưng nhận dạng trực quan (Re-ID / visual appearance feature consistency).

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`), bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?
   - Tình huống thực tế: Trên frame `adasind_140160.jpg`, tại hậu cảnh xa (tọa độ x=172..207, y=855..883) có hai vật thể chuyển động nhỏ: xe máy có người lái (chiều cao h=28px) và ô tô con (chiều cao h=9px). Trong teaching reference thô ban đầu có gán box cho hai vật này. Tuy nhiên theo luật R01 (ngưỡng H=40px đo trên ảnh fisheye gốc), hai vật thể này nằm ngoài phạm vi bắt buộc gán nhãn.
   - Cách xử lý: Tôi kiên định tuân thủ đúng luật R01 và checklist self-QC, không gán box cho các vật thể có chiều cao dưới 40px để tránh vi phạm phạm vi gán nhãn và tránh sinh cảnh báo lỗi tự động. Khi công cụ so sánh `scoped` của lab chạy, nó cũng tự động loại bỏ các box dưới 40px của reference, khẳng định quyết định bám sát luật R01 là hoàn toàn chính xác.
   - Nếu làm lại slice này: Tôi sẽ chú ý kiểm tra và thiết lập các thuộc tính hình học (`truncated=true`) cho các vật thể cắt mép ảnh (như xe máy L1 tại biên phải) ngay từ bản phác đầu tiên trong CVAT để tiết kiệm thời gian soát lỗi, đồng thời vẽ các polygon K12 với mật độ đỉnh tối ưu hơn để số liệu fill ratio phản ánh trực quan nhất độ méo hình học của thấu kính fisheye.
