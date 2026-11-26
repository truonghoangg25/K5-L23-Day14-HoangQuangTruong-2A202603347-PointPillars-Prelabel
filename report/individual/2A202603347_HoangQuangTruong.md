# Báo cáo Day 14 — Hoàng Quang Trường — 2A202603347

> Sao chép file này thành `report/individual/2A202603347_<HoTen>.md` (không dấu), ví dụ `2A20260000_NguyenVanAn.md`.

- Mã học viên dùng với `--student`: `347`
- `seed_tag` trong `frames_A.txt`: `82ac3100fd`

## 1. Dự đoán trước sweep (bước 5b)

Ghi **trước** khi chạy `scripts/pp_sweep.py`, rồi commit và push ngay (lịch sử commit là bằng chứng thời điểm viết). Không sửa phần này sau khi có kết quả.

- Tham số tôi nghĩ giảm công sửa nhiều nhất: Kết hợp tăng cả score_thr và min_points.
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?): Tôi cho rằng kết hợp hai tham số sẽ giúp giữ lại các dự đoán rõ ràng và đáng tin cậy hơn, từ đó dễ kiểm tra đối tượng và giảm công sửa nhãn.

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc | 20 | 1 | 0 | 7.0 |
| `best_config.json` | 20 | 1 | 0 | 7.0 |

- Prediction: 118 cuboid; reference: 99 cuboid.
- Đã thử 15 cấu hình.
- Tham số đã đổi so với baseline: không có. `score_thr=0.1`, `min_points=0`; cấu hình 0.2/0 đồng hạng.
- Chọn cấu hình có chi phí nhỏ nhất theo kết quả sweep.

### Leave-one-out (tính từ sweep.csv)

| Holdout | score_thr | min_points | Train cost | Holdout cost | Oracle cost |
|---|---:|---:|---:|---:|---:|
| s01f42 | 0.1 | 0 | 0.6 | 6.4 | 6.4 |
| s03f52 | 0.1 | 0 | 6.4 | 0.6 | 0.6 |
| s05f19 | 0.1 | 0 | 7.0 | 0.0 | 0.0 |

Cả ba lượt chọn 0.1/0 nếu ưu tiên ngưỡng nhỏ hơn khi đồng hạng; chi phí holdout bằng oracle. Tập A chỉ gồm 3 frame.

## 3. Trước/sau (bước 8)

![s01f42](../../d14_347/compare/s01f42.jpg)

![s03f52](../../d14_347/compare/s03f52.jpg)

![s05f19](../../d14_347/compare/s05f19.jpg)

| score_thr | min_points | Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|---:|---:|
| 0.1 | 0 | 20 | 1 | 0 | 7.0 |
| 0.2 | 0 | 20 | 1 | 0 | 7.0 |
| 0.1 | 5 | 20 | 2 | 0 | 8.0 |
| 0.2 | 5 | 20 | 2 | 0 | 8.0 |
| 0.1 | 10 | 20 | 4 | 0 | 10.0 |
| 0.2 | 10 | 20 | 4 | 0 | 10.0 |
| 0.3 | 0 | 15 | 11 | 0 | 15.5 |
| 0.3 | 5 | 15 | 12 | 0 | 16.5 |
| 0.3 | 10 | 15 | 14 | 0 | 18.5 |
| 0.4 | 0 | 9 | 37 | 0 | 39.7 |
| 0.4 | 5 | 9 | 38 | 0 | 40.7 |
| 0.4 | 10 | 9 | 39 | 0 | 41.7 |
| 0.5 | 0 | 4 | 54 | 0 | 55.2 |
| 0.5 | 5 | 4 | 54 | 0 | 55.2 |
| 0.5 | 10 | 4 | 54 | 0 | 55.2 |

- Chi phí baseline: 7.0; chi phí tối ưu: 7.0; mức giảm: 0%.
- Cấu hình 0.5/10: xóa 4, thêm 54, sửa 0; chi phí 55.2.

**Kết luận đối chiếu dự đoán 5b của học viên:** chưa bổ sung.

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_347/frames_B.txt --config d14_347/best_config.json --gt d14_347/ref_B.json` (với đường dẫn Data-VF thực tế):

- 12 frame, 356 box dự đoán, 184 box tham chiếu hiện tại, 184 box khớp.

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| 172 | 0 | 0 | 51.6 |

| Frame | Prediction | Reference | Xóa | Chi phí |
|---|---:|---:|---:|---:|
| s01f03 | 29 | 20 | 9 | 2.7 |
| s01f19 | 28 | 14 | 14 | 4.2 |
| s01f34 | 40 | 14 | 26 | 7.8 |
| s02f10 | 28 | 20 | 8 | 2.4 |
| s02f35 | 32 | 15 | 17 | 5.1 |
| s02f54 | 38 | 11 | 27 | 8.1 |
| s03f06 | 20 | 14 | 6 | 1.8 |
| s03f24 | 35 | 12 | 23 | 6.9 |
| s03f45 | 40 | 14 | 26 | 7.8 |
| s04f07 | 26 | 19 | 7 | 2.1 |
| s04f28 | 20 | 18 | 2 | 0.6 |
| s04f49 | 20 | 13 | 7 | 2.1 |
| Tổng | 356 | 184 | 172 | 51.6 |

| Lớp | Prediction | Reference | Xóa |
|---|---:|---:|---:|
| Car | 256 | 157 | 99 |
| Truck | 0 | 0 | 0 |
| Bus | 0 | 0 | 0 |
| Cyclist | 80 | 21 | 59 |
| Pedestrian | 20 | 6 | 14 |

- Car có số box bị xóa nhiều nhất (99); Cyclist có tỷ lệ xóa cao nhất (73.75%).
- Chi phí trung bình: 4.3/frame.
- Công cụ hỗ trợ: AI rà soát ảnh chiếu và LiDAR; CVAT SDK sao lưu và áp dụng đề xuất xóa 172 box nghi vấn.
- Reference B hiện tại chỉ phản ánh kết quả sàng lọc: 184 box còn lại giữ nguyên hình học và lớp của prediction, chưa xác nhận kiểm tra thủ công toàn bộ. Chưa xác nhận đầy đủ đối tượng bị sót, hướng, kích thước, đáy và thuộc tính occluded.
- Các nghi vấn cần kiểm tra theo guideline: s01f03 pp_id 11 (UNCERTAIN_CLASS, xe tải/xe con bị che), s01f19 pp_id 9 và 12 (LOW_VISIBILITY/ATTRIBUTE_CHECK, vùng xe khuất cạnh xe buýt), s02f10 pp_id 2 và 12 (UNCERTAIN_CLASS, van và người cạnh xe đạp). Đây là đề xuất QA, chưa phải kết luận gán nhãn.

## 5. Giới hạn

- Tập A chỉ có 3 frame, chưa đủ đại diện cho các điều kiện khác nhau.
- PointPillars huấn luyện trên KITTI không trực tiếp nhận diện Truck và Bus theo taxonomy của lab; việc đổi lớp dựa vào luật kích thước có thể sai.
- Chi phí sửa là công thức quy đổi, không phải thời gian sửa thực tế.
- Reference hiện tại chưa được kiểm chứng độc lập đầy đủ; không dùng chi phí này để khẳng định độ chính xác của model.
