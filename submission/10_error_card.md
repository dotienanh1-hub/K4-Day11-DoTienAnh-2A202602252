# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B3 | BOX_GEOMETRY | 1 |
| center | B3 | MISSING | 4 |
| center | B3 | SPURIOUS | 3 |
| center | C0 | BOX_GEOMETRY | 1 |
| edge | B3 | ATTRIBUTE | 2 |
| edge | B3 | BOX_GEOMETRY | 1 |
| edge | B3 | MISSING | 1 |
| edge | B3 | SPURIOUS | 2 |
| edge | C0 | WRONG_CLASS | 1 |
| mid | B3 | BOX_GEOMETRY | 1 |
| mid | B3 | MISSING | 5 |
| mid | B3 | SPURIOUS | 3 |
| unknown | C0 | IGNORE_SCOPE | 1 |

## Top defects
- MISSING: 10 (ví dụ frame adasind_128310.jpg)
- SPURIOUS: 8 (ví dụ frame adasind_128310.jpg)
- BOX_GEOMETRY: 4 (ví dụ frame adasind_019560.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

- Nguyên nhân khả dĩ (`why`) và vì sao bạn nghĩ vậy:
  - Lỗi nổi bật nhất là hiện tượng Model YOLO26m tách rời người lái và xe hai bánh thành hai bounding box riêng biệt (M1 Pedestrian và M2 Bike trên frame `adasind_140160.jpg`), dẫn đến việc bỏ sót box hợp nhất của ground truth (ca `L1+R1`, cell `LR_noM`, `what=MISSING`) đồng thời sinh ra hai box dư thừa sai lệch (`M1, M2`, cell `M_only`, `what=SPURIOUS`).
  - Phán đoán nguyên nhân (`why=E4_model_domain`): Model YOLO26m được kế thừa trọng số huấn luyện từ tập dữ liệu phổ quát (COCO dataset) với quy ước phân rã người và xe. Khi đưa vào domain xe tự hành mắt cá SVM, model gặp sự dịch chuyển phân phối dữ liệu (domain shift) cả về hình học méo quang học lẫn sự xung đột quy tắc gán nhãn với bài toán downstream (hệ thống tự hành cần một khối động lực học duy nhất để ước lượng quỹ đạo tránh va chạm).

- Cách sửa và ai nhận việc (`owner`):
  - Người nhận việc (`owner=ai_team`): AI team cần tinh chỉnh (fine-tune) model trên tập dữ liệu đặc thù SVM360 với nhãn đã hợp nhất theo quy ước R03; bổ sung module hậu xử lý (post-processing / NMS rule) để tự động gộp các cặp Pedestrian + Bike có IoU và cấu trúc không gian tương ứng với tư thế người ngồi điều khiển phương tiện.
  - Về phía quy trình dán nhãn (`owner=annotator`): Giữ vững quy chuẩn R03 trong mọi vòng gán nhãn và kiểm thử gold set, đảm bảo dữ liệu ground truth huấn luyện luôn nhất quán.

- Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule):
  - Ảnh bằng chứng trực quan: `submission/screenshots/screenshot_rider_r03.png` ghi lại rõ sự khác biệt giữa box hợp nhất của annotator (viền xanh lá `Bike`) và hai box tách rời của model (viền đỏ `M1 Pedestrian` và viền cam `M2 Bike`).
  - Dòng findings liên quan:
    + `r3_diag,B3-mid,adasind_140160.jpg,L1+R1,LR_noM,MISSING,E4_model_domain,P1,ai_team,R03,...`
    + `r3_diag,B3-mid,adasind_140160.jpg,M1,M_only,SPURIOUS,E4_model_domain,P1,ai_team,R03,...`
    + `r3_diag,B3-mid,adasind_140160.jpg,M2,M_only,SPURIOUS,E4_model_domain,P1,ai_team,R03,...`
  - Quy tắc vi phạm: Quy tắc `R03 — Rider` trong `docs/02-rules-vi.md` quy định rõ: *người lái + xe hai bánh = một box Bike duy nhất; người dắt xe mới tách thành Pedestrian và Bike*.
