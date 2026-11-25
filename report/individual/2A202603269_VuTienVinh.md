# Báo cáo Day 13 — <Họ và tên>, <MSSV>

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
TODO(học viên): mã số, task `D13-…`, 10 frame của bạn; hệ điều hành, trình duyệt, phiên bản CVAT (tên tài khoản → About); MSSV bạn ngồi cạnh. Rồi 2–3 câu: tổng bao nhiêu cuboid, frame nào ít nhất và vì sao; `selfcheck` cờ bao nhiêu cuboid trên tổng số; bạn tin phần nào nhất và phần nào còn yếu nhất (hướng, đáy, kích thước, class, vật xa).

TODO(học viên): cách dùng CVAT, 3 dòng: đổi góc nhìn (nhìn từ trên xuống, xoay, dời); vẽ và chỉnh một cuboid (Shape, nháy đúp, Top/Side/Front); bật mũi tên hướng (Cuboid orientation) và xem cuboid trên ảnh camera (`preview`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ: `s01f01` và `s01f17`. Tên gốc của bốn ảnh: `t1-s01f01-3d`, `t1-s01f01-top`, `t1-s01f17-3d`, `t1-s01f17-top` (đuôi `.png` hoặc `.jpg` theo file đã lưu). Ảnh nhìn từ trên xuống có đánh dấu phía trước, +x, +y.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f01 | Phía trước (−y) ở giữa cạnh trên; +x sang trái; +y về giữa cạnh dưới của ảnh. | Mặt đường phẳng, thấp hơn LiDAR khoảng 1,7 m. | Cột giao thông, ước lượng khoảng 100 m; chưa kiểm chứng khoảng cách. | Cột giao thông; cọc tiêu hai bên đường (nhận dạng qua camera, ít điểm LiDAR). | 2 bên có:  Cọc tiêu ở hai bên đường, car, pedestrian, biển báo giao thông. phía sau xe có : car và biển báo giao thông, pedestrian, truck | Các cụm điểm tại vị trí cọc tiêu hai bên đường có ít điểm, khó nhận dạng chỉ từ LiDAR. |
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
| s02f00 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. |Không ghi nhận điều lạ. |
| s02f29 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s03f22 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ.|
| s03f55 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s04f28 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |
| s04f45 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Point cloud in được cả chữ trên biển quảng cáo |
| s05f03 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. |Không ghi nhận điều lạ. |
| s05f18 | Phía trước (−y) hướng về giữa cạnh trên của ảnh nhìn từ trên xuống. | Không ghi nhận điều lạ. |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | TODO(học viên) | | | | | |
| (b) xe buýt hoặc xe tải | TODO(học viên) | | | | | |
| (c) người đi bộ hoặc xe hai bánh | TODO(học viên) | | | | | |

TODO(học viên): vòng hiệu chuẩn: cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào? Và các vật bạn **bỏ qua vì không đủ cơ sở** (frame, vị trí, mô tả, loại nghi vấn như UNCERTAIN_CLASS / LOW_VISIBILITY), hoặc ghi "không có".

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | TODO(học viên) | | | |
| 2. Thưa ở xa | TODO(học viên) | | | |
| 3. Phản xạ thấp / ít điểm bất thường | TODO(học viên) | | | |
| 4. Điểm lạ | TODO(học viên) | | | |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |
| TODO(học viên) | | | | | | | |

TODO(học viên): (a) cuboid **gần nhất** và **xa nhất**: `dist_m` và r của từng cái. (b) Kích thước l, w, h của một người đi bộ (hoặc xe hai bánh) và một xe con so với bảng 4.3: chiều nào ngoài khoảng, vì sao. (c) `yaw_deg` của một xe cùng chiều phía trước và một xe ngược chiều (nếu có): có gần 270° và 90° không.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| TODO(học viên) | | |

TODO(học viên): một câu quy luật ("xa gấp … lần thì số điểm còn khoảng …") và so với đường cong ở slide bài giảng (xe con: khoảng 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m). Rồi phần tự kiểm: hai cuboid bạn **đã sửa** sau `preview` / `selfcheck` (frame, idx, sửa gì), và những cảnh báo bạn **giữ nguyên** kèm lý do.

TODO(học viên): đổi kiểm: MSSV người đã kiểm chéo với bạn, hai con số nào được đối chiếu với bảng A, khớp hay lệch (lệch thì đã sửa gì).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
TODO(học viên): (a) hai câu, nói từ phía model sẽ học trên nhãn này: vì sao nhãn 3D đo bằng mét chứ không phải pixel; vì sao vật thưa điểm cần quy tắc riêng. (b) Dùng AI? Ghi 3 dòng: công cụ, câu hỏi chính, bạn dùng kết quả thế nào. Không dùng: ghi "không dùng". (c) Điều còn chưa chắc, lỗi phát hiện sau hạn (không chấm; viết bằng lời, không chép số từ phiếu hồ sơ lỗi).
