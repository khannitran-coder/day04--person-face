# Nhãn đề xuất để nhập vào CVAT

Đã xử lý đúng 12 ảnh người và 8 ảnh mặt trong hai backup chị cung cấp. Không đổi MSSV, task, job hay assignment.json. Backup gốc được giữ nguyên. Không kết nối hoặc đăng nhập CVAT của chị.

## Tải nhãn vào CVAT

1. Giải nén gói review. Trong đó có hai ZIP riêng để nhập, không giải nén hai ZIP nhãn này.
2. Mở http://localhost:8080/jobs, mở job #18 của người.
3. Bấm Menu → Upload annotations (một số bản ghi Import annotations).
4. Chọn định dạng COCO Keypoints 1.0; chọn job18_person_COCO_Keypoints_1.0.zip; bấm OK/Submit. Nếu xuất hiện xác nhận thay nhãn hiện có, thao tác này sẽ thay nhãn của job bằng bộ đề xuất; hai backup gốc đã được giữ lại.
5. Mở job #19 của mặt, làm tương tự với job19_face_COCO_Keypoints_1.0.zip.
6. Nếu menu của bản CVAT khác, ở trang Tasks mở task tương ứng → Actions → Upload annotations. Chỉ chọn đúng ZIP theo task.
7. Review và sửa trên CVAT, bấm Ctrl+S. Cuối cùng chạy python lab.py export trên máy chị để lấy file nộp. Hai ZIP trong gói này chỉ là nhãn nhập, chưa phải file xuất để nộp.

## Mở ảnh review

Mở index.html sau khi giải nén. Bấm từng ảnh để phóng to. Đỏ = trái của người, xanh = phải, vàng = giữa; số là thứ tự điểm từ 1. Chấm đặc v=2, vòng rỗng v=1, v=0 không vẽ. Mỗi người có mã P1, P2…; các điểm bị bỏ vẫn tồn tại trong mảng nhãn với v=0.

Bảng tom_tat_tung_anh.csv liệt kê số người/mặt và các điểm còn phân vân. Mở bằng Excel; dùng index.html nếu máy hiển thị CSV sai dấu.

## Kết quả và giới hạn

25 bộ điểm người/12 ảnh và 8 bộ điểm mặt/8 ảnh. Ảnh đông người 000000326480.jpg có 8 đối tượng đề xuất, gồm người ở xa và người bị cắt mép. Ba người phía sau xe chỉ nhìn rõ phần trên: chiều cao toàn thân không thể đo chắc từ ảnh, cần chị quyết định theo guideline. Không ép số người về 20 khi ảnh thực tế có thêm người cần kiểm tra.

Đã loại các phát hiện trùng ở ảnh 106051 và 156986, bổ sung người bị che ở ảnh 326480; bỏ chân đoán vào bàn ở 467300; sửa tay người áo trắng ở 518966 và đảo gối/cổ chân trẻ phía xa ở 461002. Đã rà mặt quay lưng, kính râm, ngoài khung; chỉnh các điểm mí/môi được ghi trong bảng.

Checker: 0 lỗi, 0 cảnh báo. Checker chỉ kiểm cấu trúc/hình học, không xác nhận độ chính xác điểm hay OKS. Không có ground truth để đo OKS. Chưa thử nhập thực tế vào CVAT của chị; cần kiểm tra sau nhập một điểm v=1 và một điểm v=0 được giữ đúng.

## Tên và ID

Backup CVAT không lưu image_id/category_id dạng COCO để sao chép nguyên số. Vì vậy dùng image_id = thứ tự frame trong manifest + 1; category_id = thứ tự label trong task.json: person=1, face=2. Tên file ảnh, kích thước, thứ tự frame, tên label/sublabel, thứ tự điểm và skeleton đều lấy/đối chiếu từ đúng backup. CVAT nhập theo tên ảnh và tên nhãn; số ID nội bộ của server có thể khác. File image_category_mapping.json ghi đối chiếu đầy đủ, không khẳng định đó là ID gốc trên CVAT.

## Model và khai báo AI

Chạy trên CPU cloud: RTMDet-m (coco-obj365-person) + RTMPose-m COCO-17/body7 bằng rtmlib/ONNX Runtime; MediaPipe Face Landmarker float16 cho mặt. Không huấn luyện mới, không tải/chép annotation COCO gốc. Đây là dự đoán model kèm chỉnh sửa qua ảnh, cần người học review trong CVAT.

Mapping face 0-based theo thứ tự 13 điểm: 263,362,386,374,33,133,159,145,1,291,61,0,17. Sau mapping có chỉnh mí và môi theo ảnh; kính râm đặt 8 điểm mắt v=1.

Các cách viết MSSV đã thử với pool d04-k5-v2: 2a202603338, 2A202603338, 202603338, 2202603338, a202603338, A202603338, 2a 202603338, 2a-202603338, và chuỗi có khoảng trắng cuối. Không cách nào tái tạo đúng đồng thời cả 12 ảnh người và 8 ảnh mặt trong backup. Không sửa dữ liệu để khớp.

Gợi ý khai báo mục 9: “Dùng Codex, RTMDet + RTMPose và MediaPipe để tạo nhãn đề xuất; người học kiểm tra, sửa trên CVAT và xuất bằng lab.py export.” Chỉ ghi bước review của chị sau khi chị thực sự làm.

## Thử trước trên một ảnh, giữ nguyên job thật

Giải nén backup người gốc → data → lấy 000000106051.jpg. Trong CVAT vào Projects → mở project chứa hai task thật → dấu + tạo task mới tên THU-1-ANH → chọn đúng ảnh trên → Submit. Trong task thử chọn Actions → Upload annotations → COCO Keypoints 1.0 → THU_1_ANH_person_000000106051.zip (đi kèm gói review). Review và thử đổi cờ, kéo điểm trên task thử trước. Không nhập file thử vào task thật vì có thể làm mất các nhãn khác. Nếu CVAT không có Upload annotations ở task, mở job thử → Menu → Upload annotations.
