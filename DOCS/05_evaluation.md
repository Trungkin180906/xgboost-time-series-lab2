# Báo cáo bước 05 – đánh giá và kiểm thử mô hình XGBoost Regressor

## 1. Mục tiêu
- Mục tiêu chính: Đánh giá độ chính xác và khả năng dự báo chuỗi thời gian (Time Series Forecasting) của mô hình XGBoost đã huấn luyện từ bước 04_xgboost_train trên tập kiểm thử (Test set).
- Kiểm tra hiện tượng Overfitting/Underfitting: So sánh sai số giữa tập Train và tập Test theo trục thời gian thực tế để đảm bảo mô hình không học vẹt.
- Định lượng sai số thực tế: Đo lường độ lệch giữa giá trị thực tế (y_{true}) và giá trị dự báo ({y^}_{pred}) để phục vụ báo cáo phân tích rủi ro.
## 2. dữ liệu đầu vào
Nguồn dữ liệu: 
- Tập dữ liệu sau khi tạo biến trễ (Lag features, Rolling statistics) từ 02_lag_feature.   
- Dữ liệu phân tách theo thời gian từ 03_time_split 
Kích thước dữ liệu kiểm thử:
- Tập huấn luyện (Train Set): 
    X_train: 96,996 dòng, 24 đặc trưng   
    y_train: 96,996 mẫu nhãn mục tiêu 
- Tập kiểm thử (Test Set - Dùng để đánh giá):
    X_test: 24,249 dòng, 24 đặc trưng   
    y_test: 24,249 mẫu nhãn mục tiêu
Đặc trưng đầu vào (Features = 24):Bao gồm các biến trễ (Lag features), cửa sổ trượt (Rolling statistics) và các thuộc tính thời gian được sinh ra từ các bước tiền xử lý trước đó.
Mô hình đầu vào: Mô hình XGBRegressor được huấn luyện từ 04_xgboost_train.ipynb trên tập (X_train, y_train) và tiến hành dự báo trên tập X_test.
## 3. tiêu chí và thước đo đánh giá
Vì đây là bài toán hồi quy chuỗi thời gian (Regression / Forecasting), các độ đo đánh giá tiêu chuẩn gồm:
- MAE (Mean Absolute Error): Sai số tuyệt đối trung bình, đo lường độ lệch trung bình thực tế giữa dự đoán và thực tế
- RMSE (Root Mean Squared Error): Căn bậc hai sai số toàn phương trung bình (phạt nặng các điểm dự báo sai lệch lớn/outlier)
- MAPE (Mean Absolute Percentage Error): Phần trăm sai số tuyệt đối trung bình (đo mức độ sai số theo tỷ lệ % so với giá trị thực)
- R^2 Score (Hệ số xác định): Mức độ giải thích của mô hình đối với phương sai của dữ liệu chuỗi thời gian.
## 4. kết quả thực nghiệm
## 4.1. Bảng chỉ số hiệu năng (Performance Metrics)
Mô hình XGBoost Regressor được đánh giá đồng thời trên tập Train (96.996 mẫu) và tập Test (24.249 mẫu). Kết quả đo lường cụ thể như sau:

| Dataset   |      MAE |     RMSE |   MAPE (%) |   R2 Score |
|:----------|---------:|---------:|-----------:|-----------:|
| Train Set | 140.668  | 188.14   |       0.9  |     0.9947 |
| Test Set  | 149.968  | 202.973  |       1.04 |     0.9931 |

## 4.2. Trực quan hóa kết quả dự báo (Visualizations)
1. Đồ thị So sánh Thực tế và Dự báo (Actual vs. Predicted)
Đường dự báo bám sát chu kỳ dao động và biên độ của giá trị thực tế trên tập kiểm thử
- Nhận xét trực quan: Mô hình nắm bắt chính xác các nhịp sóng đỉnh và đáy theo chu kỳ ngày/đêm. Độ trễ pha (phase lag) hầu như không đáng kể
2. Phân tích Sai số phần dư (Residual Analysis)
Kiểm tra tính ngẫu nhiên và phân phối của sai số dự báo e = y_{true} - {y^}_{pred}:
- Biểu đồ phân phối phần dư (trái): Phân phối sai số có dạng hình chuông đối xứng chuẩn quanh giá trị 0 (đường đứt nét đỏ), khẳng định mô hình không bị thiên lệch (unbiased).
- Biến thiên sai số theo thời gian (phải): Sai số dao động ổn định trong dải [-500, +500] quanh trục cân bằng 0 trong suốt chiều dài chuỗi thời gian, ngoại trừ một vài điểm ngoại lai (outliers) cá biệt.
3. Mức độ quan trọng của đặc trưng (Feature Importance)
Top các biến ảnh hưởng lớn nhất đến quyết định phân nhánh của cây XGBoost:
- lag_1 chiếm trọng số cao nhất (hơn 57%), phản ánh tính tự tương quan tức thời cực mạnh của chuỗi thời gian
- lag_2 đứng thứ hai (~24%)
- Các biến trễ chu kỳ ngày như lag_22, lag_23, lag_21 và lag_24 đóng vai trò hỗ trợ nhận diện tính mùa vụ 24 giờ
## 5. phân tích và đánh giá chi tiết
- Độ chính xác vượt trội: Chỉ số R^2 đạt 0.9931 và sai số tỷ lệ MAPE chỉ 1.04% trên tập Test chứng minh mô hình giải thích được hơn 99.3% biến thiên của dữ liệu thực tế.   
- Kiểm soát Overfitting hiệu quả: Độ chênh lệch sai số giữa Train và Test rất nhỏ (MAE chênh lệch ~9.3 đơn vị; RMSE tăng từ 188.14 lên 202.97). Điều này khẳng định mô hình có tính khái quát hóa (generalization) cao, không bị học vẹt dữ liệu quá khứ.   
- Xử lý điểm cực trị: Tại một vài đỉnh nhọn đột biến trên 16.000 đơn vị, giá trị dự đoán có xu hướng hơi thấp hơn đỉnh thực tế một biên độ nhỏ (hiện tượng thường gặp do hàm mục tiêu bình phương tối ưu hóa về trung bình)[cite: 9]. Tuy nhiên, sai số này vẫn nằm trong ngưỡng kiểm soát an toàn.