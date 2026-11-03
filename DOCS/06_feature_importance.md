# Báo cáo Lab 06 – Phân tích Feature Importance

## 1. Mục tiêu
- Sử dụng mô hình XGBoost đã huấn luyện để trích xuất Feature Importance.
- Phân tích mức độ quan trọng của các đặc trưng dựa trên Gain, Weight và Cover.
- Xác định những Lag Features đóng góp nhiều nhất vào khả năng dự đoán mức tiêu thụ điện `AEP_MW`.

## 2. Dữ liệu đầu vào

Sử dụng các file có sẵn trong project:

- `models/xgboost_model.pkl`
- `data/processed/X_test.csv`

Kết quả kiểm tra sau khi chạy:

| Thông tin | Kết quả |
|---|---:|
| Loại mô hình | XGBRegressor |
| Số cây boosting | 300 |
| Số đặc trưng trong mô hình | 24 |
| Số cột trong X_test | 25 |
| Số đặc trưng Lag Features | 24 |

Trong tập `X_test.csv`, cột `Datetime` được giữ lại để xác định thời gian, không được sử dụng làm đặc trưng đầu vào. Mô hình chỉ sử dụng 24 đặc trưng từ `lag_1` đến `lag_24`.

## 3. Phương pháp phân tích

Sử dụng hàm `get_score()` của XGBoost để trích xuất Feature Importance theo ba chỉ số:

| Chỉ số | Ý nghĩa |
|---|---|
| Gain | Mức cải thiện chất lượng dự đoán khi đặc trưng được sử dụng để chia nhánh |
| Weight | Số lần đặc trưng được sử dụng để chia nhánh |
| Cover | Số lượng mẫu trung bình chịu ảnh hưởng bởi các lần chia nhánh sử dụng đặc trưng |

Trong đó, Gain được sử dụng làm chỉ số chính để xếp hạng mức độ quan trọng của các đặc trưng.

Tỷ lệ Gain được tính theo công thức:

`Gain_Percent = Gain của đặc trưng / Tổng Gain × 100%`

## 4. Kết quả Feature Importance

Sau khi trích xuất dữ liệu từ mô hình, kết quả Top 15 đặc trưng có mức độ quan trọng cao nhất như sau:

| Xếp hạng | Feature | Gain_Percent (%) |
|---:|---|---:|
| 1 | `lag_1` | 57.0543 |
| 2 | `lag_2` | 23.8199 |
| 3 | `lag_22` | 5.3573 |
| 4 | `lag_23` | 3.8462 |
| 5 | `lag_21` | 1.7243 |
| 6 | `lag_3` | 1.5019 |
| 7 | `lag_4` | 1.0461 |
| 8 | `lag_24` | 0.7743 |
| 9 | `lag_10` | 0.7175 |
| 10 | `lag_5` | 0.5843 |
| 11 | `lag_15` | 0.4245 |
| 12 | `lag_20` | 0.3981 |
| 13 | `lag_9` | 0.3946 |
| 14 | `lag_11` | 0.3845 |
| 15 | `lag_12` | 0.3727 |

Biểu đồ Top 15 Feature Importance được tạo bằng thư viện Matplotlib, thể hiện tỷ lệ Gain của từng đặc trưng theo thứ tự giảm dần.

### Nhận xét

Kết quả cho thấy `lag_1` có mức độ quan trọng cao nhất với 57.0543%, tiếp theo là `lag_2` với 23.8199%. Hai đặc trưng này chiếm tổng cộng 80.8742% tổng Gain của mô hình.

Các đặc trưng `lag_22` và `lag_23` cũng có mức đóng góp đáng chú ý, lần lượt là 5.3573% và 3.8462%.

Đáng chú ý, `lag_24` chỉ chiếm 0.7743%, thấp hơn nhiều so với `lag_1` và `lag_2`. Điều này cho thấy trong mô hình hiện tại, dữ liệu tiêu thụ điện của những giờ gần nhất đóng vai trò quan trọng hơn so với dữ liệu của 24 giờ trước.

## 5. Kết luận

Bước phân tích Feature Importance đã hoàn thành các yêu cầu:

- Sử dụng mô hình XGBoost đã huấn luyện với 300 cây.
- Phân tích 24 đặc trưng Lag Features.
- Trích xuất mức độ quan trọng theo Gain, Weight và Cover.
- Xếp hạng các đặc trưng theo tỷ lệ Gain.
- Trực quan hóa kết quả bằng biểu đồ Top 15 Feature Importance.

Kết quả cho thấy `lag_1` và `lag_2` là hai đặc trưng quan trọng nhất trong mô hình, chiếm tổng cộng khoảng 80.87% Gain. Qua đó, nhóm có thể nhận thấy mô hình XGBoost sử dụng chủ yếu thông tin tiêu thụ điện trong các giờ gần nhất để đưa ra dự đoán.

Lưu ý: Feature Importance phản ánh mức độ sử dụng và đóng góp của đặc trưng trong mô hình, không chứng minh quan hệ nhân quả hoặc khẳng định rằng có thể loại bỏ các đặc trưng còn lại.