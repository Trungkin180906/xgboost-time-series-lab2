# Báo cáo Bước 4 – Chia tập Train/Test theo thời gian

## 1. Mục tiêu
Chia dữ liệu đã tạo Lag Features và tách X – y ở các bước trước thành tập huấn luyện (Train) và tập kiểm tra (Test) theo thứ tự thời gian. Việc chia theo thời gian giúp tránh sử dụng thông tin tương lai trong quá trình huấn luyện.

## 2. Phương pháp thực hiện
- Đọc `X_lag_p24.csv` và `y_AEP_MW.csv` được tạo từ Bước 2 và Bước 3.
- Kiểm tra số lượng mẫu, chỉ mục thời gian, thứ tự tăng dần và giá trị thiếu.
- Chọn tỷ lệ chia 80% Train và 20% Test.
- Lấy 80% mẫu đầu tiên theo thời gian làm Train; 20% mẫu cuối chuỗi làm Test.
- Không xáo trộn dữ liệu (`shuffle=False`); việc chia được thực hiện bằng cách cắt theo vị trí với `iloc`.

## 3. Kết quả chia dữ liệu
Theo kết quả Bước 3, dữ liệu có 121.245 mẫu và 24 đặc trưng đầu vào. Với tỷ lệ 80/20:
- Tập Train: 96.996 mẫu; X_train có kích thước 96.996 × 24, y_train có 96.996 giá trị.
- Tập Test: 24.249 mẫu; X_test có kích thước 24.249 × 24, y_test có 24.249 giá trị.
- Tổng số mẫu sau chia: 121.245 mẫu.

## 4. Kiểm tra sau khi chia
- X_train và y_train có cùng số lượng mẫu và cùng chỉ mục thời gian.
- X_test và y_test có cùng số lượng mẫu và cùng chỉ mục thời gian.
- Tập Train nằm trước tập Test theo thời gian; không có timestamp giao nhau giữa hai tập.
- Thứ tự thời gian được giữ nguyên, không thực hiện xáo trộn ngẫu nhiên.

## 5. Tệp kết quả
Các tệp được lưu trong thư mục `data/processed/`:
- `X_train.csv`: ma trận đặc trưng của tập Train.
- `X_test.csv`: ma trận đặc trưng của tập Test.
- `y_train.csv`: biến mục tiêu của tập Train.
- `y_test.csv`: biến mục tiêu của tập Test.

## 6. Lưu ý
Chia dữ liệu theo thời gian là yêu cầu quan trọng đối với bài toán dự báo chuỗi thời gian. Nếu trộn ngẫu nhiên dữ liệu, mô hình có thể được huấn luyện trên các quan sát ở tương lai so với một số quan sát trong tập kiểm tra, dẫn đến đánh giá không phản ánh đúng tình huống dự báo thực tế.

Tỷ lệ 80/20 là lựa chọn thực nghiệm ban đầu theo yêu cầu bài tập, không phải tỷ lệ tối ưu đã được chứng minh. Ở Bước 5, mô hình XGBoost Regressor sẽ được huấn luyện trên tập Train và đánh giá trên tập Test.
