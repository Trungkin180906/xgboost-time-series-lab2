## timeseries_lab2
* Mục tiêu chính của bài làm là biến đổi bài toán chuỗi thời gian đơn biến thành bài toán **Học có giám sát (Supervised Learning)** thông qua kỹ thuật tạo **Lag Features** (biến độ trễ)[cite: 1]. Mô hình XGBoost sau đó được huấn luyện để dự đoán mức tiêu thụ điện $y_t$ tại thời điểm tiếp theo dựa trên các giá trị lịch sử trong quá khứ ($y_{t-1}, y_{t-2}, \dots, y_{t-p}$)[cite: 1].
## Phân công nhiệm vụ nhóm (6 Thành viên)

| STT | Họ và Tên | MSSV | Vai trò & Nhiệm vụ chính | Sản phẩm bàn giao |
| :---: | :--- | :---: | :--- | :--- |
| 1 | |  | Quản trị Git/Repo, thực hiện Bước 1: Chuẩn bị & làm sạch dữ liệu | `notebooks/01_data_prep.ipynb`<br>`docs/lab01_data_prep.md` |
| 2 |  |  | Thực hiện Bước 2 & 3: Tạo Lag Features & Tách ma trận X, y | `notebooks/02_lag_features.ipynb`<br>`docs/lab02_lag_features.md` |
| 3 |  |  | Thực hiện Bước 4: Chia tập Train/Test theo chuỗi thời gian | `notebooks/03_time_split.ipynb`<br>`docs/lab03_time_split.md` |
| 4 |  |  | Thực hiện Bước 5: Fit mô hình XGBoost Regressor | `notebooks/04_xgboost_train.ipynb`<br>`docs/lab04_xgboost_train.md` |
| 5 |  |  | Thực hiện Bước 6: Dự đoán & Trực quan hóa so sánh Thực tế vs Dự báo | `notebooks/05_evaluation.ipynb`<br>`reports/figures/` |
| 6 | || Thực hiện Bước 7: Phân tích feature importance để tìm hiểu lag nào ảnh hưởng mạnh nhất đến dự báo | `docs/lab05_evaluation.md` |

## Cấu trúc thư mục 

```text
xgboost-time-series-lab/
├── README.md                          # Hướng dẫn tổng quan & phân công nhiệm vụ
├── requirements.txt                   # Danh sách thư viện phụ thuộc
├── .gitignore                         # Các file/folder loại trừ khi commit Git
│
├── data/
│   └── raw/                           # Chứa file dữ liệu thô AEP_hourly.csv
│
├── notebooks/                         # Mã nguồn thực thi Jupyter Notebook
│   ├── 01_data_prep.ipynb             # Bước 1: Chuẩn bị dữ liệu
│   ├── 02_lag_features.ipynb          # Bước 2 & 3: Tạo Lag Features & Tách X-y
│   ├── 03_time_split.ipynb            # Bước 4: Chia Train/Test theo thời gian
│   ├── 04_xgboost_train.ipynb         # Bước 5: Huấn luyện XGBoost
│   └── 05_evaluation.ipynb            # Bước 6 & 7: Đánh giá & Feature Importance
│
├── docs/                              # Tài liệu giải thích & báo cáo chi tiết (.md)
│   ├── lab01_data_prep.md
│   ├── lab02_lag_features.md
│   ├── lab03_time_split.md
│   ├── lab04_xgboost_train.md
│   └── lab05_evaluation.md
│
└── reports/
    └── figures/                       # Các đồ thị xuất ra dạng ảnh PNG
        ├── 01_raw_series.png
        ├── 05_actual_vs_pred.png
        └── 05_feature_importance.png
