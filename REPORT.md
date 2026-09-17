# Mẫu tham khảo để điền REPORT.md

- Mã học viên theo lớp: 2A202602292
- Ngày / CVAT local: 17/09/2026 — CVAT Local
- Công cụ đã dùng: Brush, Polygon, Intelligent Scissors, SAM


## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Không tự điền điểm nếu chưa có phản hồi từ người chấm. Nếu export lỗi, ghi task, trạng thái Save và thông báo đã gửi coach.


## 2. Một quyết định trước khi dùng gợi ý

* **Ảnh, vị trí và object Medium đầu tiên tự vẽ:** 
  * **Ảnh:** `000000458325.jpg` (Hình 3 trong bộ dữ liệu)
  * **Vị trí vật thể:** Vùng bên trái màn hình (tọa độ bounding box `x: 0`, `y: 211`, `rộng: 70`, `cao: 194`)
  * **Đối tượng:** Chiếc xe ô tô bị che khuất một phần (ID: 38)

* **Class và quy tắc dùng để chọn biên:** 
  * **Class:** `car`
  * **Quy tắc chọn biên:** Vẽ đường biên ôm sát theo đúng các góc cạnh còn nhìn thấy rõ của chiếc xe ô tô ở mép ngoài cùng bên trái. Khi chạm mép trái của ảnh (`x = 0`), dừng mask men theo đúng đường viền mép ảnh chứ không kéo tràn ra ngoài không gian ảnh.

* **Nếu dùng gợi ý sau đó:** Vùng gợi ý tự động sau đó khớp đúng 100% với góc viền xe bên trái nhưng có xu hướng mở rộng nhẹ sang các điểm che khuất phía sau. Thực hiện hành động **sửa** (trim/tỉa bớt) bằng cách xóa vùng gợi ý dư thừa lấn sang phần gầm và điểm bị xe khác đè lên, giữ lại đúng phần khung xe gốc hiển thị trực quan.

* **Nếu không dùng gợi ý:** Không dùng.

## 3. Một lỗi tôi tìm thấy và sửa

* **Task/ảnh/vùng:** 
  * **Ảnh:** `000000458325.jpg`
  * **Vùng:** Cụm xe phía hậu cảnh góc phải màn hình.

* **Lỗi thuộc loại:** Gộp-tách

* **Bằng chứng tôi nhìn thấy:** 
  * Hai chiếc ô tô đỗ liền kề bị công cụ tạo gộp thành 1 mask duy nhất. 
  * Quan sát kỹ thấy khe hở khoảng cách nhỏ giữa hai bánh xe và kính chiếu hậu của hai xe riêng biệt.

* **Quy tắc và hành động sửa:** 
  * **Quy tắc:** Mỗi đối tượng rời rạc phải được gán 1 instance ID/mask độc lập.
  * **Hành động:** Sử dụng công cụ cắt/tách (Split) để chia mask gộp thành 2 mask độc lập (`car_1` và `car_2`), sau đó tỉa lại đường biên theo đúng khe hở giữa 2 xe.

* **Sau sửa đã Save và export lại chưa?** Rồi (Đã bấm Save và Export lại tệp JSON/Annotation chuẩn).


| Ảnh / Vị trí | Hai cách hiểu có thể | Quy tắc / Chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| **000000458325.jpg** <br>`bbox: [345.0, 434.0, 34.0, 28.0]` | **`car`** (Xe ô tô) vs **`truck`** (Xe tải) | **Dữ liệu nhãn:** Đối tượng tại vị trí này được gắn `category_id: 7` (`car`).<br>**Hình ảnh:** Phương tiện có kích thước tương đối nhỏ ở vùng đường xa, hình dáng xe du lịch. | **Quyết định:** Gán nhãn **`car`** theo `category_id: 7`. |
| **000000458325.jpg** <br>`bbox: [180.0, 441.0, 12.0, 10.0]` | **`person`** (Người) vs **`motorcycle`** (Xe máy) | **Dữ liệu nhãn:** Vị trí này chứa hai nhãn đè nhau:<br>- `category_id: 6` (`person`)<br>- `category_id: 10` (`motorcycle`)<br>**Kích thước:** Rất nhỏ (12x10 px) ở khoảng cách xa. | **Câu hỏi cho coach:** Khi người điều khiển đang ngồi trên xe máy ở khoảng cách rất xa, quy tắc dự án yêu cầu gán nhãn tách biệt (`person` và `motorcycle`) hay gộp chung toàn bộ thành phương tiện? |
| **000000458325.jpg** <br>`bbox: [46.0, 308.0, 481.0, 141.0]` | **`sidewalk`** (Vỉa hè) vs **`road`** (Lòng đường) | **Dữ liệu nhãn:** Vùng không gian này được gắn `category_id: 2` (`sidewalk`).<br>**Đặc điểm:** Khu vực phân ranh giữa lối đi bộ và phần đường xe chạy. | **Quyết định:** Chọn **`sidewalk`** theo `category_id: 2` trong tập annotation. |
| **000000458325.jpg** <br>`bbox: [0.0, 305.0, 542.0, 175.0]` | **`road`** (Lòng đường) vs **`sidewalk`** (Vỉa hè) | **Dữ liệu nhãn:** Vùng diện tích lớn phía dưới ảnh được gắn `category_id: 1` (`road`).<br>**Đặc điểm:** Bề mặt mặt đường chính cho xe di chuyển. | **Quyết định:** Chọn **`road`** theo `category_id: 1`. |
