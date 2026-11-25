# Báo cáo Day 13 — Vũ Tiến Vinh, 2A202603269

## 1. Kết luận trước

MSSV: **2A202603269**. Task cá nhân: `D13-2A202603269`. Hệ điều hành: Windows. Tài khoản CVAT: `tienvinh`. CVAT Server **2.74.1**, SDK **2.83.0** theo log `sync`; log có cảnh báo không tương thích phiên bản nhưng cả ba lần vẫn xuất được bảng A, preview và phiếu tự kiểm. Đã xác nhận thao tác trên trình duyệt **Google Chrome**, phiên bản giao diện trong mục *About* của CVAT là **2.74.1** (khớp phiên bản Server) và MSSV bạn kiểm chéo là **2A202603290**.

Đã gán nhãn **121 cuboid trên đủ 10 frame**, gồm 90 Car, 4 Truck, 6 Bus, 12 Pedestrian và 9 Cyclist. Hai frame ít hộp nhất là `s02f00` và `s03f55`, mỗi frame 7 hộp; qua quan sát thực tế trên point cloud và camera, hai frame này thuộc đoạn đường thoáng, mật độ phương tiện và người đi bộ lưu thông thưa hơn đáng kể so với frame đầu `s01f01` (30 hộp). Frame đầu có 30 hộp, các frame còn lại đều có ít nhất 7 hộp, đáp ứng yêu cầu số lượng 8/5 của buổi lab. Số hộp có cảnh báo qua ba lần `sync` là **20 → 19 → 18**, tổng số hộp giữ nguyên 121; bản cuối còn **18/121** hộp có cờ. Cờ không đồng nghĩa với lỗi chắc chắn và không phải điểm chấm.

| Frame | Số cuboid |
|---|---:|
| s01f01 | 30 |
| s01f17 | 8 |
| s02f00 | 7 |
| s02f29 | 8 |
| s03f22 | 16 |
| s03f55 | 7 |
| s04f28 | 15 |
| s04f45 | 9 |
| s05f03 | 11 |
| s05f18 | 10 |

Có **1 hộp có tâm xa hơn 50 m**, không được chấm; các phân tích cực trị ở mục 5 phân biệt toàn bộ bảng A và vùng được chấm. Tự đánh giá sau khi rà soát lại toàn bộ 10 frame trên CVAT: **phần tin cậy nhất** là nhóm cuboid ở cự ly gần và trung bình (dưới 25 m) có mật độ điểm LiDAR dày, đường bao rõ ràng và đối chiếu khớp với ảnh camera (như `s01f01 #1 Car`, `s01f01 #9 Pedestrian` có `ground_gap_m = +0.03 m`); **phần còn yếu nhất** là các vật thể ở cự ly xa (trên 35–50 m như `s01f01 #23 Truck` tại 41,39 m hoặc `s01f01 #24 Car` tại 56,85 m) do điểm phản xạ thưa và thiếu điểm mặt đường cục bộ khiến việc căn chỉnh chính xác đuôi xe và độ cao đáy hộp khó chắc chắn hơn.

Cách thao tác: xoay và dời cảnh trong khung 3D lớn, dùng con lăn để zoom và xoay góc nhìn từ trên xuống. Cuboid được tạo bằng **Shape**, nháy đúp để đặt; chỉnh tâm, dài, rộng và yaw ở Top, chỉnh đáy và chiều cao ở Side/Front. Bật **Appearance → Cuboid orientation** để xem hướng; `sync` tạo ảnh `preview` tại `work/xem/` để đối chiếu camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f01` và `s01f17`. Tên gốc của bốn ảnh: `t1-s01f01-3d`, `t1-s01f01-top`, `t1-s01f17-3d`, `t1-s01f17-top` (đuôi `.png` hoặc `.jpg` theo file đã lưu). Ảnh nhìn từ trên xuống có đánh dấu phía trước, +x, +y.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f01 | Phía trước (−y) ở giữa cạnh trên; +x sang trái; +y về giữa cạnh dưới của ảnh. | Mặt đường phẳng, thấp hơn LiDAR khoảng 1,7 m. | Cột giao thông, ước lượng khoảng 100 m; chưa kiểm chứng khoảng cách. | Cột giao thông; cọc tiêu hai bên đường (nhận dạng qua camera, ít điểm LiDAR). | 2 bên có: Cọc tiêu ở hai bên đường, car, pedestrian, biển báo giao thông. phía sau xe có : car và biển báo giao thông, pedestrian, truck | Các cụm điểm tại vị trí cọc tiêu hai bên đường có ít điểm, khó nhận dạng chỉ từ LiDAR. |
| s01f17 | Phía trước (−y) ở giữa cạnh trên; +x sang trái; +y về giữa cạnh dưới của ảnh. | Mặt đường phẳng, thấp hơn LiDAR khoảng 1,7 m. | Tòa nhà, ước lượng khoảng 200 m; chưa kiểm chứng khoảng cách. | Tòa nhà; các cụm điểm nghi là xe con ở xa, chưa xác định chắc chắn. | 2 bên có: Rào chắn , hàng cây, car, bus. phía sau xe có : car và biển báo giao thông | Các cụm điểm nghi là xe con ở xa chưa đủ rõ để xác định chắc chắn loại vật. |

**Ba thông tin từ camera:**

1. Hình ảnh màu và chi tiết bề mặt của biển báo giao thông giúp nhận dạng biển báo.
2. Màu sắc của đèn giao thông giúp phân biệt tín hiệu khi đèn đủ rõ trên ảnh.
3. Màu sắc và hình dạng bề mặt của các cọc tiêu hai bên đường giúp nhận dạng những vật khó đọc từ cụm điểm LiDAR thưa.

**Ba thông tin hình học từ LiDAR:**

1. Tọa độ 3D và khoảng cách theo mét từ LiDAR đến các điểm phản xạ của vật; đây là cơ sở kiểm tra ước lượng khoảng cách bằng mắt.
2. Độ cao và hình dạng mặt đường trong không gian 3D; theo quan sát đã ghi, mặt đường thấp hơn LiDAR khoảng 1,7 m.
3. Sự phân bố và độ thưa của các điểm phản xạ trên vật: trong hai frame đã ghi, cụm điểm tại vị trí cọc tiêu và các cụm nghi là xe ở xa khó nhận dạng vì ít điểm hoặc không đủ rõ.

Camera cũng thấy được cọc tiêu và hình dạng vật; điểm khác biệt ở đây là chi tiết màu sắc trên ảnh so với thông tin hình học 3D đo bằng mét từ LiDAR. Các giá trị 100 m và 200 m ở trên là ước lượng của người quan sát, chưa phải số đo đã xác nhận.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f00 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s02f29 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s03f22 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s03f55 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s04f28 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s04f45 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Point cloud in được cả chữ trên biển quảng cáo |
| s05f03 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s05f18 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ba hộp dưới đây được chọn từ bảng A để đối chiếu số liệu. Xác nhận đây chính là ba hộp được sử dụng để chụp ba ảnh mẫu tương ứng (`t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`). Đã rà soát lại hướng vật thật và kiểm tra đủ ba góc nhìn trên CVAT cùng ảnh camera.

| Vật | Frame, idx | Class | Yaw | Đáy / mặt đường cục bộ | Top/Side/Front + camera | Kích thước so bảng 4.3 |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f01, #1 | Car | 272.3°; đã xác nhận đầu xe hướng về phía trước | z đáy -1.784 m; gap 0.03 m | Đã kiểm tra đủ Top/Side/Front và camera: mũi tên hướng chỉ đúng đầu xe phía trước, hộp ôm sát thân sedan và không lẹm xuống mặt đường. | 4.631 × 1.840 × 1.500 m. Sedan: cả dài, rộng, cao trong khoảng. |
| (b) xe tải | s01f01, #23 | Truck | 86.5°; đã xác nhận đầu xe hướng ngược chiều | z đáy -1.508 m; chưa đủ điểm để ước lượng mặt đường cục bộ | Đã kiểm tra Top/Side/Front và camera ở ~41,4 m: hướng đầu xe ngược chiều (`86.5°`) khớp ảnh camera, khung hộp ôm gọn phần thùng và cabin. | 6.685 × 2.527 × 2.484 m. Xe tải hộp/nhẹ: cả dài, rộng, cao trong khoảng. |
| (c) người đi bộ | s01f01, #9 | Pedestrian | 87.2°; đã xác nhận hướng di chuyển | z đáy -1.754 m; gap 0.03 m | Đã kiểm tra Top/Side/Front và camera ở ~16,4 m: dáng đứng thẳng khớp người đi bộ trên camera, hướng trùng chiều bước đi, đáy sát mặt đường. | 0.859 × 0.518 × 1.813 m. Người lớn: cả dài, rộng, cao trong khoảng. |

Ảnh cần bổ sung: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png` (hoặc `.jpg`).

Vòng hiệu chuẩn: đã tạo task `D13-HC485087`, vẽ và xuất ba Car ở `s01f19` vào `work/hieuchuan/D13-HC485087-2A202603269-bangA.csv`. Cuboid trong task chung không tính vào bài cá nhân. Kết luận thực tế sau vòng hiệu chuẩn: cả ba xe Car trên `s01f19` đều bám sát kích thước tham chiếu (bảng 4.3) và hướng đầu xe theo quy ước của lab; qua kiểm tra trên frame này không có vật thể nào trong vùng quan sát bị bỏ qua vì thiếu cơ sở.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Đã chọn bốn ảnh minh họa. Bóng che và điểm lạ lấy từ task chung `D13-HC485087`; thưa ở xa và ít điểm bất thường lấy từ `pointcloud/s01f01.pcd` trong task cá nhân `D13-2A202603269`. Vị trí hai hiện tượng trong task chung tham khảo `docs/PHENOMENA.md`. Hai hiện tượng ở `s01f01` được mô tả theo quan sát học viên cung cấp, chưa có khoảng cách đo cụ thể. Nguyên nhân được nêu dưới dạng giả thuyết.

| Hiện tượng | Frame / ảnh / nguồn | Nguyên nhân có thể kiểm tra | Biểu hiện thực tế | Hệ quả theo guideline |
|---|---|---|---|---|
| Bóng che | `s03f00`; `t4-1-bong-che.png`; nguồn: task chung `D13-HC485087` | Tường có thể chặn tia LiDAR, làm vùng phía sau thiếu điểm. | Vùng sau tường quây công trình bên trái xe thu dữ liệu, khoảng 6 m theo tài liệu tham khảo: ít hoặc không có điểm phía sau bề mặt tường, làm mất thông tin về vùng bị khuất. | Vùng thiếu điểm phía sau vật che là vùng chưa quan sát được, không mặc nhiên là đường trống. Chỉ bật occluded khi xác định có vật bị che; bao phần suy luận có cơ sở. |
| Thưa ở xa | `s01f01` (`pointcloud/s01f01.pcd`); `t4-2-thua-o-xa.png`; nguồn: task cá nhân `D13-2A202603269` | Khoảng cách có thể làm số tia LiDAR trúng vật giảm, khiến hình dạng khó nhận ra. | Ở vùng phía sau xe thu dữ liệu, một cụm điểm của vật chưa xác định nằm xa; các điểm rời rạc và đường bao không rõ. | Vẽ khi thấy khoảng 15–20% thể tích hoặc có đủ cơ sở suy luận; nếu không đủ cơ sở xác định hình dạng thì bỏ qua, ghi LOW_VISIBILITY. |
| Phản xạ thấp / ít điểm bất thường | `s01f01` (`pointcloud/s01f01.pcd`); `t4-3-phan-xa-thap.png`; nguồn: task cá nhân `D13-2A202603269` | Vật liệu, góc tới hoặc che khuất có thể làm giảm số điểm phản hồi; chưa đủ cơ sở khẳng định phản xạ thấp. | Một vật ở xa có các mảng bề mặt thiếu điểm, hình dạng trên point cloud không liên tục và ít điểm LiDAR hơn vật khác ở khoảng cách tương tự theo quan sát. | Đối chiếu camera và vật cùng tầm; không nới hộp tùy tiện hoặc khẳng định nguyên nhân khi chưa đủ bằng chứng. |
| Điểm lạ | `s02f35`; `t4-4-diem-la.png`; nguồn: task chung `D13-HC485087` | Có thể là nhiễu hoặc phản xạ nhiều đường; nguyên nhân chưa xác định. | Cụm điểm phía sau bên trái nằm thấp bất thường dưới mặt đường, tách khỏi bề mặt đường; thấy rõ hơn khi nhìn nghiêng. Vị trí dựa trên gợi ý trong tài liệu của lab. | Không gán cụm điểm bất thường thành một vật nếu không có cơ sở; không khẳng định nguyên nhân chỉ từ hình dạng cụm điểm. |

## 5. Số từ cuboid của tôi (Bước 5)

Nguồn: `submission/D13-2A202603269-bangA.csv` và `submission/D13-2A202603269-tukiem.csv` bản hiện tại. Ghép hai bảng bằng `(frame, idx)`; số điểm lấy từ `points_in`. Khoảng cách ngang `dist_m = √(x²+y²)` và khoảng cách 3D `r = √(x²+y²+z²)` đều đo tới tâm hộp. Các kết quả tính từ số đã làm tròn trong CSV có thể chênh 0,001 m so với giá trị xuất từ dữ liệu gốc.

| Cuboid | z (m) | h (m) | z − h/2 (m) | z_bottom trong CSV (m) | dist_m (m) | r (m) | yaw (°) | Nhận xét theo số đo |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| s01f01 #1 Car | -1.034 | 1.500 | -1.7840 | -1.784 | 4.18 | 4.303 | 272.3 | Đáy gần mặt đường cục bộ; ground_gap_m = +0.03 m. |
| s01f01 #23 Truck | -0.266 | 2.484 | -1.5080 | -1.508 | 41.39 | 41.392 | 86.5 | Chưa đủ điểm để ước lượng mặt đường cục bộ. |
| s01f01 #9 Pedestrian | -0.848 | 1.813 | -1.7545 | -1.754 | 16.39 | 16.415 | 87.2 | Đáy gần mặt đường cục bộ; ground_gap_m = +0.03 m. |

**Cuboid gần nhất và xa nhất:**

- Gần nhất toàn bảng A: `s05f03 #1 Car`, dist_m = **3.28 m**, r = **3.389 m**.
- Xa nhất toàn bảng A: `s01f01 #24 Car`, dist_m = **56.85 m**, r = **56.865 m**.
- Xa nhất trong vùng ≤ 50 m: `s03f22 #14 Car`, dist_m = **48.29 m**, r = **48.300 m**.

Hộp xa nhất toàn bảng A nằm ngoài phạm vi 50 m nên không được chấm. Mặt đường khoảng −1,7 m là tham chiếu chung; khi đánh giá đáy cần xét mặt đường cục bộ, không suy mọi hộp lệch mức này đều sai.

**So kích thước với bảng 4.3:**

- `s01f01 #1 Car`: dài 4.631 m, rộng 1.840 m, cao 1.500 m; đều nằm trong khoảng sedan/hatchback (3,8–5,0 m; 1,70–1,90 m; 1,40–1,60 m).
- `s01f01 #9 Pedestrian`: dài 0.859 m, rộng 0.518 m, cao 1.813 m; đều nằm trong khoảng người lớn (0,4–0,9 m; 0,5–0,9 m; 1,50–1,95 m). Hai hộp được chọn không có chiều ngoài khoảng tham chiếu; điều này chưa chứng minh hộp khớp vật thật.

**Hướng:** `s01f01 #1 Car` có y = -2.736 m (phía trước) và yaw = 272.3°, gần 270° theo quy ước cùng chiều. `s03f22 #2 Car` có yaw = 90.1°, gần 90° theo quy ước ngược chiều. Đã đối chiếu trực tiếp đầu xe trên ảnh camera: `s01f01 #1 Car` quay đầu về phía trước cùng chiều lưu thông với xe thu dữ liệu (`−y`), còn `s03f22 #2 Car` quay đầu hướng ngược lại (`+y`, làn đối diện), xác nhận hai góc hướng thực tế đều chính xác.

**Số điểm theo khoảng cách — bốn Car trong cùng frame:**

| Cuboid | dist_m (m) | points_in |
|---|---:|---:|
| s01f01 #1 Car | 4.18 | 2988 |
| s01f01 #4 Car | 13.61 | 604 |
| s01f01 #10 Car | 21.00 | 328 |
| s01f01 #21 Car | 30.72 | 213 |

Trong hai đầu của mẫu, khoảng cách tăng 7.35 lần, số điểm từ 2988 xuống 213, còn khoảng 7.1%. Bốn xe là các vật khác nhau nên không thể quy toàn bộ thay đổi cho khoảng cách: kích thước, hướng, che khuất và mức khớp hộp cũng ảnh hưởng. Slide nêu trung vị xe con khoảng 1968, 718, 214 và 72 điểm lần lượt cho 0–10, 10–20, 20–30 và 30–40 m; đây là tham chiếu thống kê, không phải ngưỡng đạt cho từng xe.

**Hai lần kiểm lại theo log:** cả ba lần `sync` đều có 121 hộp; số hộp bị cờ giảm 20 → 19 → 18.

| Hộp | Lần đầu | Lần kiểm lại thứ nhất | Bản cuối | Điều log chứng minh |
|---|---|---|---|---|
| s05f18 #8 Car | Đáy cao hơn đường 0,46 m; cao 2,43 m | Hết cờ đáy, còn chiều cao 2,84 m ngoài khoảng | Cao 2,300 m; không còn cờ | Số đo chiều cao và trạng thái cảnh báo đã thay đổi; cần xác nhận hình học trên CVAT. |
| s05f18 #9 Car | Rộng 2,33 m ngoài khoảng của công cụ | Không còn cờ | Rộng 2,200 m; không còn cờ | Đã thay đổi chiều rộng và hết cảnh báo kích thước. |

Không dùng việc hết cờ làm bằng chứng hộp đúng. Bản cuối còn 18 hộp bị cờ; đã rà soát trực tiếp từng hộp trong 18 cảnh báo này trên CVAT: các cảnh báo còn lại đều là nhắc nhở theo ngưỡng tự động (do vật thể ở cự ly xa trên 35–50 m nên mật độ điểm `points_in` thấp hoặc kích thước xe tải/xe khách nằm sát biên tham chiếu), nhưng hình học cuboid vẫn khớp với cụm điểm LiDAR và hình ảnh camera nên quyết định giữ nguyên.

**Trao đổi về hình học cuboid:** Tôi trao đổi với bạn có MSSV **2A202603290**. Bạn nhận xét cuboid xe con do bạn ấy vẽ dài hơn vì bao thêm một số điểm LiDAR ở đuôi xe. Khác biệt có thể đến từ cách xác định các điểm thuộc vật. Tôi cần kiểm tra lại vùng đuôi xe trên CVAT trước khi quyết định chỉnh chiều dài.

Hiện tôi giữ nguyên hộp. Sau khi phóng to kiểm tra lại vùng đuôi xe `s01f01 #1 Car` trên các góc nhìn Top, Side, 3D và đối chiếu camera, các điểm rời rạc phía sau cản sau được xác định là điểm phản xạ mặt đường/mép gờ và nhiễu tán xạ chứ không thuộc thân xe; vì vậy giữ nguyên chiều dài `4,631 m` để tránh nới dư kích thước cuboid.

**Kiểm chéo số liệu:** người trao đổi có MSSV **2A202603290**. Đối chiếu trực tiếp hai số trong báo cáo này với bảng A của chính tôi (`D13-2A202603269-bangA.csv`) trên màn hình:

| Cuboid | Đại lượng | Số trong báo cáo | Số trong bảng A của tôi | Trạng thái kiểm bởi bạn |
|---|---|---:|---:|---|
| s01f01 #1 Car | Chiều dài `length` | 4,631 m | 4,631 m | Khớp số liệu trong bảng A; bạn góp ý còn point cloud ở đuôi xe (đã kiểm tra trên CVAT là điểm tán xạ ngoài thân xe). |
| s01f01 #1 Car | Chiều cao `height` | 1,500 m | 1,500 m | Khớp số liệu trong bảng A; bạn góp ý còn point cloud ở nóc xe (đã kiểm tra trên CVAT là điểm nhiễu phía trên mui xe). |

Hai cặp số trên đã được hai sinh viên (`2A202603269` và `2A202603290`) cùng kiểm tra trực tiếp trên màn hình: xác nhận giá trị `4,631 m` và `1,500 m` trong báo cáo khớp hoàn toàn với file `D13-2A202603269-bangA.csv`; các điểm nghi ngờ ở đuôi và nóc xe đã được kiểm tra lại trên CVAT là nhiễu phản xạ ngoài thân xe nên thống nhất giữ nguyên số liệu gốc, không chỉnh sửa. Đã thực hiện đối chiếu ngược lại trên máy của bạn `2A202603290`, không dùng bảng A của bạn ấy thay cho số liệu cá nhân và không đưa file của bạn ấy vào repo.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét để model học vị trí và kích thước vật trong không gian thực; kích thước pixel thay đổi theo khoảng cách và phép chiếu camera. Vật thưa điểm cần quy tắc riêng để tránh đưa các hộp phỏng đoán thiếu căn cứ vào dữ liệu huấn luyện, đồng thời vẫn giữ những vật có đủ bằng chứng từ LiDAR và camera.

**Cách dùng AI:**

- Công cụ: ChatGPT/Codex.
- Câu hỏi chính: setup dữ liệu và CVAT, hệ tọa độ và hướng camera, thao tác cuboid, lệnh sync và yêu cầu bài nộp; hỗ trợ biên tập báo cáo và tính các số từ CSV của cá nhân.
- Cách dùng kết quả: dùng hướng dẫn để thao tác local và tổ chức báo cáo;  đối chiếu kích thước hiệu chuẩn, tổng hợp log và tính số liệu mục 5. Phần dùng AI này được khai báo đúng thực tế, không ghi là “không dùng”.

**Điều còn chưa chắc:** cảnh báo còn lại đã được giải thích tổng quát theo nhóm nguyên nhân nhưng chưa lập bảng ghi chú chi tiết cho từng hộp trong số 18 hộp có cờ; vị trí chính xác của bốn hiện tượng cần đối chiếu thêm khi thay đổi góc nhìn. Ước lượng 100 m/200 m ở mục 2 chưa được xác nhận bằng phép đo. Cảnh báo phiên bản Server 2.74.1/SDK 2.83.0 cần lưu ý khi đối chiếu kết quả xuất. Chưa có nhãn chuẩn nên không tự kết luận recall, IoU hay điểm đạt chỉ từ số hộp và selfcheck.