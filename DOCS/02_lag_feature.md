# Kết quả thực hiện 02_Lag_Features
## 1. Dữ liệu đầu vào
- Tệp: AEP_hourly.csv
- Kích thước ban đầu: 121,273 dòng × 2 cột.
- Các cột: Datetime, AEP_MW.
- Thời gian: 2004-10-01 01:00:00 đến 2018-08-03 00:00:00.
- Missing values ban đầu: 0.
- Số dòng thuộc timestamp trùng: 8; số lần xuất hiện dư: 4.
## 2. Tiền xử lý
- Chuyển Datetime sang kiểu datetime.
- Gộp các bản ghi cùng timestamp bằng trung bình AEP_MW (do timestamp trùng ở giờ chuyển đổi mùa; dữ liệu không cung cấp múi giờ để phân biệt).
- Sắp xếp tăng dần và đặt Datetime làm index.
- Kích thước sau gộp: 121,269 dòng.
- Index tăng dần: True; còn timestamp trùng: False.
- Sau gộp, có 27 khoảng cách lớn hơn 1 giờ (các khoảng cách 2 giờ). Không tự nội suy/điền timestamp.
## 3. Tạo Lag Features
- Chọn p = 24.
- Lý do: dữ liệu theo giờ, 24 lag làm baseline tương ứng một ngày quan sát gần nhất.
- Hàm create_lag_features(data, target_col, p) tạo lag_1 đến lag_p bằng shift.
- Loại bỏ 24 dòng đầu thiếu lịch sử sau khi tạo lag.
## 4. Kết quả X và y
- X có kích thước: 121,245 × 24.
- y có kích thước: 121,245.
- Missing values trong X: 0.
- Missing values trong y: 0.
- y là AEP_MW tại thời điểm hiện tại; X gồm lag_1 ... lag_24.
## 5. Tệp kết quả
- AEP_lag_features_p24.csv: target và toàn bộ lag.
- X_lag_p24.csv: ma trận đặc trưng.
- y_AEP_MW.csv: vector mục tiêu.
## 6. Lưu ý khi báo cáo
p=24 là lựa chọn baseline hợp lý cho dữ liệu theo giờ, không phải kết quả tối ưu đã được xác nhận. Có thể so sánh p=24, 48, 168 ở bước thử nghiệm mô hình. Do còn các khoảng cách 2 giờ, lag biểu diễn 24 quan sát trước đó; không luôn tương ứng đúng 24 giờ tại các vị trí có khoảng trống thời gian.
## Chia dữ liệu thành X và y (Supervised Learning)

Sau khi hoàn thành quá trình tạo các đặc trưng độ trễ (Lag Features) và loại bỏ các dòng chứa giá trị thiếu (NaN), dữ liệu được phân chia thành hai thành phần chính là ma trận đặc trưng đầu vào X và biến mục tiêu y.

## 7. Ma trận đặc trưng đầu vào X
- Lựa chọn 24 cột đặc trưng từ lag_1 đến lag_24 làm biến đầu vào.
- Mỗi dòng trong X đại diện cho 24 giá trị tiêu thụ điện gần nhất trong quá khứ.
- Các đặc trưng này được sử dụng làm dữ liệu đầu vào để mô hình học và nhận diện xu hướng tiêu thụ điện.
- Kích thước ma trận X: 121.245 dòng × 24 cột.
- Không còn giá trị thiếu (NaN) trong ma trận X.
## 8. Biến mục tiêu y
- Lựa chọn cột AEP_MW làm biến mục tiêu cần dự đoán.
- y chứa giá trị tiêu thụ điện thực tế tại thời điểm hiện tại (t).
- Mỗi giá trị y tương ứng với một dòng dữ liệu trong ma trận X.
- Kích thước y: 121.245 dòng × 1 cột (khi biểu diễn dưới dạng DataFrame) hoặc 121.245 phần tử (khi biểu diễn dưới dạng Series).
- Không còn giá trị thiếu (NaN) trong y.
## 9. Kết quả phân chia X – y

Sau khi phân chia, dữ liệu đã được chuyển đổi thành dạng học có giám sát (Supervised Learning), trong đó:
- X là tập hợp các biến đầu vào, bao gồm 24 giá trị tiêu thụ điện trong quá khứ.
- y là giá trị tiêu thụ điện thực tế cần dự đoán tại thời điểm tiếp theo so với chuỗi lag đầu vào.
- Số lượng mẫu của X và y bằng nhau, đảm bảo tính tương ứng giữa dữ liệu đầu vào và biến mục tiêu.
- Thứ tự thời gian của dữ liệu được giữ nguyên, không thực hiện xáo trộn dữ liệu.