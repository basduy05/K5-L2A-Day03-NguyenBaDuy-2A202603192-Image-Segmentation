# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: 2A202603192
- Ngày / CVAT local: 12/11/2026 — CVAT local v2.x
- Công cụ đã dùng: CVAT Polygon, Brush tool, scripts kiểm tra inspect_submissions.py

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
| **Tổng tối đa** | | **14 / 14** | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh 000000181542.jpg, xe ô tô (car) màu bạc ở tiền cảnh góc dưới bên phải.
- Class và quy tắc tôi dùng để chọn biên: Class car. Quy tắc: Vẽ bám sát mép vỏ kim loại xe và tiếp diện bánh xe chạm mặt đường. Không vẽ lan ra vùng bóng râm dưới gầm xe; cụm kính chắn gió và gương chiếu hậu được giữ trọn vẹn bên trong mask xe.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Khi thử nghiệm công cụ tạo vùng tự động trên xe kế tiếp, công cụ bị lem đường viền sang cây xanh phía sau và nuốt bóng đen dưới gầm đường. Tôi dùng Polygon chế độ Subtract cắt bỏ bóng râm và gọt lại ranh giới nắp capo sát tán lá.
- Nếu không dùng gợi ý: Đã dùng phối hợp và giải thích rõ ở trên.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: Task cp2_slice, ảnh 000000017627.jpg, khu vực 2 xe ô tô đỗ sát song song nhau ở giữa khung hình.
- Lỗi thuộc loại: Gộp - tách đối tượng (Instance Merging Error).
- Bằng chứng tôi nhìn thấy: Hai xe sát nhau có màu sơn tương phản thấp, ban đầu bị gộp chung vào 1 mask duy nhất; danh sách Objects chỉ có 1 xe.
- Quy tắc và hành động sửa: Quy tắc độc lập cá thể: Hai xe cùng class car đứng cạnh nhau vẫn là hai instance riêng biệt. Tôi xóa mask gộp, phóng to 400% tìm khe ranh giới giữa 2 xe, dùng Polygon vẽ lần lượt Car #1 và tạo đối tượng mới cho Car #2.
- Sau sửa đã Save và export lại chưa? Đã Save (Ctrl + S), kiểm tra danh sách Objects có 2 ID riêng biệt, export COCO 1.0 vào submissions/cp2_slice.zip.
- Kết quả kiểm tra: inspect_submissions.py báo OK toàn bộ 9 task, cấu trúc COCO và Segmentation mask hợp lệ.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| Task cp4_curb, ảnh 7d83710e-4697c3b2.jpg, ranh giới lòng đường và vỉa hè | 1. Tô toàn bộ vùng xám đậm làm road do vật liệu nhựa đồng nhất.<br>2. Tách phần gờ cao hơn làm sidewalk | Quy tắc phân vùng theo chức năng và gờ bó vỉa (curbstone) nổi lên 10-15cm so với mặt đường | Quyết định: Mặt phẳng cho xe chạy là road, vạch ranh giới dọc chân gờ bó vỉa, bề mặt trên gờ là sidewalk |
| Task cp1_holes, ảnh 000000144300.jpg, xe máy (motorcycle) có khe nan hoa và khoảng hở khung xe | 1. Khoét rỗng các khe nhỏ giữa nan hoa bánh xe, khe khung máy nhìn thấy nền phía sau.<br>2. Bao phủ trọn vẹn thể tích xe máy trong mask motorcycle | Quy tắc bài lab: Khe hẹp/kính/chi tiết nằm trong cấu trúc vật thể không được khoét rỗng tùy tiện làm rách mask | Quyết định: Giữ kín toàn bộ khối thân xe, kính chắn gió và vành bánh xe trong mask motorcycle; chỉ khoét khoảng trống nền lớn bên ngoài |
| Task cp5_occlusion, ảnh 000000336232.jpg, xe bị cột biển báo chắn ngang | 1. Tạo 2 object riêng đầu xe và đuôi xe.<br>2. Giữ 1 object duy nhất với 2 mảng rời nhau chỉ vẽ phần nhìn thấy | Quy tắc che khuất: Chỉ vẽ phần nhìn thấy nhưng giữ nguyên danh tính 1 cá thể | Quyết định: Tạo 1 object car, vẽ 2 polygon rời rạc cho đầu và đuôi xe, không gán cột biển báo vào xe |
