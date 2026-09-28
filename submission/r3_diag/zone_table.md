# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 8 | 0 | 0 | 3 | 3 | — |
| mid | 9 | 0 | 0 | 4 | 3 | — |
| edge | 3 | 0 | 0 | 1 | 2 | — |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên:
  - Về phía Người dán nhãn (L): Nhãn L đạt độ khớp hoàn hảo 100% với teaching reference trên cả 3 zone (0 missing, 0 spurious across center: 8/8, mid: 9/9, edge: 3/3). Điều này đạt được nhờ quy trình self-QC 9 mục chặt chẽ, kiểm soát đúng ngưỡng chiều cao H=40 (R01), gán nhãn rider hợp nhất (R03) và bám sát hình học fisheye gốc (R02).
  - Về phía Model (M): Model gãy nhiều nhất về số lượng tuyệt đối ở zone `mid` (4 missing / 9 ref, 3 spurious) và zone `center` (3 missing / 8 ref, 3 spurious). Ở zone `edge`, model gãy 1 missing (xe tải lớn bị méo quang học sát biên) và 2 spurious (tách người lái xe máy thành 2 box Pedestrian + Bike).

- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame:
  - Nguyên nhân chính khiến Model (M) gãy:
    1. Domain shift quang học fisheye: Model được pretrain trên ảnh phối cảnh thông thường (perspective), dẫn đến nhầm lẫn hình học khi vật thể bị cong ở zone mid/edge (ví dụ nhầm ô tô con Car L7 thành Truck M9 do đuôi xe phình to).
    2. Sai lệch quy ước gán nhãn (Annotation Guideline mismatch): Model theo chuẩn COCO tự động tách người lái và xe hai bánh thành 2 box riêng biệt (M1 Pedestrian + M2 Bike trên frame 140160), vi phạm trực tiếp quy ước R03 của bài toán xe tự hành SVM.
    3. Nhược điểm nhận diện class đặc thù: Model thiếu dữ liệu huấn luyện cho lớp `ThreeWheeler`, dẫn đến dự đoán đè 2 nhãn Car và Truck lên cùng một xe ba bánh (M3 và M5).
    4. Bỏ sót đối tượng bị che khuất: Các ca missing ở mid/center chủ yếu là người đi bộ bị che khuất một phần (`occluded=true`, L8, L10, L11).
  - Giới hạn của slice ba frame: Kích thước mẫu rất nhỏ (tổng cộng 20 vật thể reference, trong đó zone edge chỉ có 3 vật thể), mang tính chất minh họa chẩn đoán cục bộ; chưa đủ độ bao phủ thống kê để đại diện cho toàn bộ phân phối giao thông thực tế 4 camera quanh xe.
