# Báo cáo Day 13 — Nguyễn Duy Khánh, 2A202603055

## 1. Kết luận trước
Mã số 2A202603055, task `D13-2A202603055`, 10 frame: s01f30, s01f51, s02f34, s02f54, s03f01, s03f31, s04f06, s04f58, s05f03, s05f40. Windows, Chrome, CVAT 2.74.1 (máy cá nhân; khác bản 2.82/2.83 của đề, các lệnh vẫn chạy). Đổi kiểm với 2A202603070.

Tổng 53 cuboid: frame đầu (s01f30) 8 hộp, 9 frame sau mỗi frame 5 hộp. `selfcheck` cờ 20/53 cuboid, phần lớn do kích thước ngoài khoảng của class. Tôi tin nhất phần chỉnh hộp ở khung Top và Side; yếu nhất là các vật ở xa, ít điểm point cloud.

Cách dùng CVAT: xoay/phóng khung 3D và xem 3 khung Top/Side/Front; vẽ cuboid dạng Shape rồi chỉnh kích thước, hướng, đáy ở Top/Side/Front; bật Cuboid orientation và đối chiếu ảnh camera.

## 2. Đọc frame (Bước 2)
Ảnh: `t1-s01f30-3d.png`, `t1-s01f30-top.png`, `t1-s01f51-3d.png`, `t1-s01f51-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f30 | Phía trước (−y) ở cạnh trên; +x chỉ sang trái; +y chỉ ra sau (cạnh dưới) | Phẳng | Không ước được | Car cùng chiều và ngược chiều | Phía sau có Car; bên đối diện là dòng Car đi ngược chiều | Các cụm ở xa, ít điểm nên không thấy rõ |
| s01f51 | Phía trước (−y) ở cạnh trên; +x chỉ sang trái; +y chỉ ra sau (cạnh dưới) | Phẳng | Không ước được | Car cùng chiều và ngược chiều | Phía sau có Car; bên đối diện là dòng Car đi ngược chiều | Các cụm ở xa, ít điểm nên không thấy rõ |

Chỉ camera cho biết: màu sắc (ô tô màu đỏ, đèn giao thông màu xanh); biển số xe và chữ trên biển báo (không ghi nội dung); kết cấu bề mặt (texture).
Chỉ LiDAR cho biết: vị trí các điểm trên thân xe trong không gian 3D; cường độ phản xạ laser (intensity); khoảng cách thực (depth, theo mét).

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f34 | Không cố định cùng một cạnh: phải xoay khung nhìn lại mỗi frame | |
| s02f54 | Như trên | |
| s03f01 | Như trên | |
| s03f31 | Như trên | |
| s04f06 | Như trên | |
| s04f58 | Như trên | |
| s05f03 | Như trên | |
| s05f40 | Như trên | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s03f31, idx chưa xác định | Car | Không đúng | Không chạm | Có (Top/Side/Front) | Có (đã sửa lại sau tự kiểm) |
| (b) xe buýt hoặc xe tải | s03f31, idx chưa xác định | Bus | Không đúng | Không chạm | Có (Top/Side/Front) | Có (đã sửa lại sau tự kiểm) |
| (c) người đi bộ hoặc xe hai bánh | s03f31, idx chưa xác định | Cyclist | Không đúng | Không chạm | Có (Top/Side/Front) | Có (đã sửa lại sau tự kiểm) |

Vòng hiệu chuẩn: không làm. Vật bỏ qua vì không đủ cơ sở: không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguồn: 10 frame của tôi. Nguyên nhân ghi là giả thuyết.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | không ghi lại frame lúc chụp (một trong 10 frame của task) / `t4-1-bong-che.png` | Giả thuyết: xe Car chặn tia laser nên tia không tới vùng phía sau | Sau xe Car có vùng trống không có điểm | Bật occluded, cuboid bao phần suy luận được |
| 2. Thưa ở xa | không ghi lại frame lúc chụp / `t4-2-thua-o-xa.png` | Giả thuyết: tia laser toả rộng và yếu dần khi đi xa nên ít điểm quay về | Vật ở xa chỉ còn ít điểm thưa | Vẽ nếu thấy ~15–20% thể tích; không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | không ghi lại frame lúc chụp / `t4-3-phan-xa-thap.png` | Giả thuyết: bề mặt phản xạ yếu nên chỉ một phần nhỏ tia quay về | Vật có bề mặt lớn nhưng chỉ có ít điểm point cloud | Ghi giả thuyết, không khẳng định |
| 4. Điểm lạ | không ghi lại frame lúc chụp / `t4-4-diem-la.png` | Giả thuyết: nhiễu cảm biến | Cụm điểm nhiễu trên mặt đường và lơ lửng trong không khí | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603055-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| chưa làm | | | | | | | |
| chưa làm | | | | | | | |
| chưa làm | | | | | | | |

(a) Cuboid gần nhất: chưa làm. Cuboid xa nhất: chưa làm.

(b) Kích thước so với bảng 4.3:
- Bus: l = 24,63 m, w = 2,62 m, h = 3,38 m. Dài vượt khoảng xe buýt thường 10,0–12,5 m (và xe buýt nối toa 17,0–18,5 m). Lý do: chưa làm.
- Car: l = 4,78 m, w = 2,19 m, h = 1,49 m. Rộng vượt khoảng 1,70–2,10 m. Lý do: chưa làm.
- Cyclist: l = 2,54 m, w = 1,37 m, h = 1,92 m. Rộng vượt khoảng xe máy có người 0,70–0,95 m. Lý do: chưa làm.

(c) `yaw_deg` xe cùng chiều: chưa làm °; xe ngược chiều: chưa làm °.

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| chưa làm | | |
| chưa làm | | |
| chưa làm | | |
| chưa làm | | |

Quy luật: chưa làm.

Tự kiểm: đã sửa hộp bằng cách xoay lại hướng — chưa làm. Cờ giữ nguyên: chưa làm.

Đổi kiểm: với 2A202603070. Hai số đối chiếu và kết quả: chưa làm.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Nhãn 3D đo bằng mét để phản ánh chính xác kích thước thật và khoảng cách vật lý trong không gian thực, nên model học được vật cách xe bao xa và to cỡ nào. Vật thể thưa điểm thiếu hình dạng bề mặt rõ ràng, nên cần quy tắc riêng để tránh gán sai kích thước hộp bao hoặc bỏ sót vật thể quan trọng.

(b) Công cụ: Claude (app Claude và Claude Code). Câu hỏi chính: lệnh PowerShell/Python, hệ toạ độ VF và cách kiểm hướng cuboid, khái niệm phản xạ thấp, cấu trúc bài nộp; có gửi 2 ảnh chụp khung Top/3D của CVAT để hỏi cách kiểm hướng (vi phạm quy định, tôi khai báo ở đây). Cách dùng: Claude xếp câu trả lời của tôi vào mẫu báo cáo này; số liệu, quan sát và cuboid do tôi tự làm và tự đọc từ bảng A, phiếu tự kiểm.

(c) Còn chưa chắc: các vật ở xa, ít điểm point cloud; idx của ba cuboid mẫu ở s03f31 chưa xác định; ba cuboid mẫu còn sai mũi tên và đáy chưa chạm đường; báo cáo nộp sau hạn 23:59 ngày làm lab.
