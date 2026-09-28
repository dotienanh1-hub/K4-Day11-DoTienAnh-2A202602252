# Guideline patch

- **Rule mới đề xuất:** `R12 — Quy ước hành khách trên khoang chở hàng hở của phương tiện thương mại`: Người ngồi hoặc đứng trên thùng xe tải hở, xe bán tải hoặc khoang sau của xe ba bánh chở hàng (`ThreeWheeler`) không gán box `Pedestrian` tách rời, mà được tính gộp trọn vẹn vào khối hình học bounding box của phương tiện đó (`Truck` hoặc `ThreeWheeler`). Bổ sung attribute tùy chọn `has_exposed_passenger` (true/false) để cảnh báo an toàn cho hệ thống ADAS khi có người ngồi hở ngoài cabin kín.
- **Áp dụng cho:** Các class `Truck`, `ThreeWheeler`, `Pedestrian` trên toàn bộ các camera và mọi vùng không gian (`center`, `mid`, `edge`).
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:** Luật R03 hiện hành chỉ nêu rõ quy ước cho xe hai bánh (rider + xe máy = một `Bike`) và người ngồi *trong* ô tô kín/xe buýt thì không box riêng. Trong dữ liệu giao thông thực tế hỗn hợp (như ADASIND), thường xuyên xuất hiện trường hợp người ngồi trên thùng xe tải chở hàng hoặc xích lô/xe lôi chở người. Việc thiếu chỉ dẫn rõ ràng khiến annotator phân vân giữa hai cách làm: vẽ thêm box `Pedestrian` đè lên xe tải (dễ gây lỗi DUPLICATE/OVERLAP) hoặc bỏ qua hoàn toàn.
- **`rules_version` mới:** `v1.1.0`
- **Hiệu lực từ:** Bắt đầu áp dụng từ đợt kiểm toán chất lượng (Audit) và vòng huấn luyện dữ liệu kế tiếp sau P6.
