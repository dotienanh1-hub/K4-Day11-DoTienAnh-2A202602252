# Quan sát vạch ô đỗ

- Hai vạch `parking_line` đã vẽ (mô tả vị trí trong ảnh): 
  - Vạch 1: Vạch sơn trắng phân chia ô đỗ ở khu vực tiền cảnh trung tâm (chạy từ tọa độ khoảng (400, 652) xuống sát mép dưới khung hình (538, 720)).
  - Vạch 2: Vạch sơn trắng phân chia ô đỗ ở tiền cảnh phía bên phải (chạy từ (694, 624) xuống góc phải dưới (948, 680)).
  - (Bổ sung vạch 3 ở góc dưới bên trái để làm rõ thêm ranh giới ô đỗ).
- Một vạch/dấu sơn hoặc biên **không** vẽ, và vì sao:
  - Gờ vỉa hè (curb) và vạch biên giới hạn mép đường ở phía xa không được gán nhãn `parking_line` vì chúng đóng vai trò là biên dẫn hướng / mép đường giao thông nội bộ, không phải vạch sơn phân chia ranh giới giữa hai ô đỗ xe riêng biệt theo quy định tại `docs/11-parking-lines-vi.md`.
- Polygon `free_space` dừng ở đâu; có phần bị che nào không:
  - Polygon bao phủ mặt đường trống của lối xe chạy chính giữa hai dãy ô đỗ (khoảng y từ 535 đến 610).
  - Ranh giới dừng ngay tại mép đầu các ô đỗ và mép vỉa hè; không đi xuyên qua thân xe, chướng ngại vật hay vùng mặt đường bị che khuất.
- Ca chưa chắc cần hỏi người soát (nếu không có, ghi “không có”):
  - Không có. (Lưu ý quan sát: các vạch chia ô ở hậu cảnh phía xa bị góc chụp phẳng nén lại và mờ dần, nếu cần gán nhãn cả các hàng xa thì cần quy định rõ ngưỡng độ phân giải tối thiểu).
