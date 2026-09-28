# QA review · B3-mid

Mã khóa: 0737-3E17

*Hình thức:* Cold review cá nhân độc lập (sau khoảng nghỉ >15 phút sau khi khóa P2, chỉ dựa trên ảnh gốc và guideline v1.0.0; chưa mở teaching reference, model overlay hay tài liệu worked).

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_128310.jpg | L4 | R04 | Ô tô con ở làn trung tâm (420, 925, 476, 991), zone center, phân loại đúng Car, box ôm khít thân xe nhìn thấy, không bị che khuất. |
| adasind_140160.jpg | L1 | R05 | Người lái ngồi trên xe hai bánh ở rìa phải (xbr=1080), zone edge, gộp thành một Bike duy nhất theo R03, thuộc tính truncated=true chính xác do cắt mép ảnh. Cần lưu ý kiểm tra mức độ che khuất (occluded) ở bánh trước. |
| adasind_140160.jpg | L2 | R01 | Xe tải nhỏ L2 tại zone mid có chiều cao 52px (>= 40px) được gán đúng; các phương tiện nhỏ ở xa phía sau (xe máy 28px, ô tô 9px) có chiều cao < 40px nên không vẽ box theo quy tắc R01 (đúng phạm vi H=40). |
| adasind_230910.jpg | L6 | R02 | Xe tải tại mép trái (0, 868, 93, 1071), zone edge, bị méo cong fisheye mạnh và bị vành kính cắt (truncated=true). Box bám sát toàn bộ phần thân xe nhìn thấy trên ảnh gốc, không nắn thẳng theo R02. |

Ghi finding r2_qa: cell=L_only, rule_id có giá trị, why để trống.
