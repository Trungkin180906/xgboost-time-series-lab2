# Báo cáo Lab 04 – Huấn luyện mô hình XGBoost Regressor

## 1. Mục tiêu

Huấn luyện mô hình XGBoost Regressor để dự đoán mức tiêu thụ điện `AEP_MW` dựa trên 24 giá trị lịch sử (`lag_1` đến `lag_24`). Bước này sử dụng dữ liệu Train được chuẩn bị ở Bước 4.

Các mục tiêu cụ thể:
- Khởi tạo mô hình `XGBRegressor` với bộ tham số baseline.
- Huấn luyện trên tập `(X_train, y_train)`.
- Đo thời gian huấn luyện.
- Ghi nhận RMSE theo từng boosting round và trực quan hóa quá trình hội tụ.

## 2. Dữ liệu đầu vào

Notebook đọc dữ liệu từ `DATA/processed/`:

- `X_train.csv`
- `y_train.csv`

Kết quả kiểm tra dữ liệu sau khi chạy notebook:

| Thông tin | Kết quả |
|---|---:|
| Số mẫu Train | 96.996 |
| Số đặc trưng đầu vào | 24 |
| Kích thước `X_train` | `(96996, 24)` |
| Kích thước `y_train` | `(96996,)` |
| Thời điểm bắt đầu Train | 2004-10-02 01:00:00 |
| Thời điểm kết thúc Train | 2015-10-27 12:00:00 |

Notebook kiểm tra số lượng mẫu giữa `X_train` và `y_train`, đồng thời kiểm tra cột `Datetime` có khớp giữa hai file hay không. Chỉ các cột `lag_1` đến `lag_24` được sử dụng làm đặc trưng. Cột `Datetime` được giữ để kiểm tra thứ tự thời gian, không đưa trực tiếp vào mô hình.

## 3. Khởi tạo mô hình

Sử dụng `XGBRegressor` với các tham số baseline sau:

| Tham số | Giá trị | Ý nghĩa |
|---|---:|---|
| `n_estimators` | 300 | Số cây boosting tối đa |
| `learning_rate` | 0.05 | Mức đóng góp của mỗi cây mới |
| `max_depth` | 6 | Độ sâu tối đa của cây |
| `subsample` | 0.8 | Tỷ lệ mẫu sử dụng để xây dựng mỗi cây |
| `colsample_bytree` | 0.8 | Tỷ lệ đặc trưng sử dụng cho mỗi cây |
| `objective` | `reg:squarederror` | Hàm mục tiêu hồi quy, tối thiểu hóa sai số bình phương |
| `eval_metric` | `rmse` | Chỉ số theo dõi RMSE |
| `random_state` | 42 | Seed để tăng khả năng tái lập |
| `n_jobs` | -1 | Sử dụng các lõi CPU khả dụng |

Đây là bộ tham số khởi đầu để xây dựng baseline, chưa phải bộ tham số tối ưu thông qua tuning.

## 4. Huấn luyện và thời gian thực thi

Mô hình được huấn luyện bằng `model.fit(X_train, y_train)`. Đồng thời, `eval_set` được thiết lập trên tập Train để ghi nhận RMSE theo từng vòng boosting. Tập Test không được đưa vào quá trình huấn luyện.

Kết quả thực tế khi chạy notebook:

| Chỉ số | Kết quả |
|---|---:|
| Số cây đã huấn luyện | 300 |
| Thời gian huấn luyện | **2,28 giây** |

Thời gian này là kết quả của lần chạy notebook hiện tại; có thể thay đổi theo phần cứng, phiên bản thư viện và môi trường thực thi.

## 5. Theo dõi quá trình hội tụ

XGBoost ghi lại RMSE của tập Train tại mỗi boosting round. Kết quả thu được:

| Chỉ số | RMSE |
|---|---:|
| RMSE Train tại vòng 1 | 2.464,7255 |
| RMSE Train tại vòng 300 | 186,8284 |
| Tổng số boosting rounds | 300 |

Đồ thị `Train RMSE` theo boosting round được lưu trực tiếp trong output của notebook `04_xgboost_train.ipynb`.

### Nhận xét

RMSE trên tập Train giảm từ **2.464,7255** ở vòng đầu xuống **186,8284** ở vòng thứ 300. Điều này cho thấy mô hình cải thiện khả năng khớp với dữ liệu Train qua các vòng boosting.

Tuy nhiên, RMSE Train giảm không đủ để kết luận mô hình có khả năng dự báo tốt trên dữ liệu chưa từng thấy. Cần đánh giá thêm trên tập Test ở Bước 6 để xem xét khả năng tổng quát hóa và dấu hiệu overfitting.

## 6. Kết luận

Bước 5 đã hoàn thành các yêu cầu:
- Khởi tạo mô hình `XGBRegressor` với tham số baseline.
- Huấn luyện mô hình trên 96.996 mẫu Train với 24 đặc trưng.
- Đo và ghi nhận thời gian huấn luyện thực tế là 2,28 giây.
- Ghi nhận RMSE qua 300 boosting rounds và vẽ đồ thị hội tụ.

Mô hình đã huấn luyện được lưu trong biến `model` của notebook. Có thể sử dụng biến này cho Bước 6 nếu tiếp tục chạy trong cùng kernel. Nếu chuyển sang kernel khác, cần huấn luyện lại hoặc lưu model trước khi đóng notebook.
