#  DỰ BÁO TIÊU THỤ ĐIỆN NĂNG VỚI XGBOOST & LAG FEATURES

**Học phần:** Dự báo & Phân tích chuỗi thời gian (Time Series Forecasting - Lab 02)  
**Dự án:** Biến đổi chuỗi thời gian đơn biến thành bài toán Học có giám sát (Supervised Learning) ứng dụng thuật toán XGBoost Regressor.

---

### 1. WHAT (bài làm này là gì?)
- **Bản chất bài toán:** Dự báo phụ tải điện năng theo giờ (*Hourly Energy Consumption Forecasting*) thuộc dạng **chuỗi thời gian đơn biến** (*Univariate Time Series*).
- **Phương pháp tiếp cận:** Thay vì dùng các mô hình thống kê tuyến tính cổ điển (như ARIMA/SARIMA), bài Lab áp dụng kỹ thuật **Tạo đặc trưng độ trễ (*Lag Feature Engineering*)** để biến đổi cấu trúc chuỗi tuần tự thành bài toán **Học có giám sát (*Supervised Learning*)**:
  $$\text{Dự đoán } y_t = f(y_{t-1}, y_{t-2}, \dots, y_{t-24})$$
- **Mô hình cốt lõi:** Thuật toán tăng cường độ dốc trên cây quyết định **XGBoost Regressor** (`gbtree`), tối ưu hóa hàm mất mát bình phương sai số (*Squared Error*).
- **Sản phẩm đầu ra:** 
  - Pipeline tiền xử lý và tạo 24 đặc trưng trễ (`lag 1` đến `lag 24`).
  - File mô hình đã huấn luyện hoàn chỉnh `models/xgboost_model.json`.
  - Báo cáo đối soát thực tế vs dự báo, biểu đồ trực quan hóa và xếp hạng tầm quan trọng của đặc trưng (*Feature Importance*).

---

### 2. WHY (Tại sao lại thực hiện bài toán & lựa chọn giải pháp này?)
- **Ý nghĩa thực tiễn:** Điện năng không thể lưu trữ đại trà với chi phí rẻ. Việc dự báo chính xác phụ tải tiêu thụ giúp các nhà máy điện lập kế hoạch phát điện tối ưu, giảm thiểu rủi ro quá tải lưới điện hoặc lãng phí nhiên liệu vận hành.
- **Tại sao cần tạo Lag Features?** Các thuật toán cây quyết định (Decision Trees, Random Forest, XGBoost) không có bộ nhớ trạng thái nội tại (*No intrinsic temporal state*) như RNN/LSTM. Kỹ thuật Lag Features biến các điểm dữ liệu lịch sử thành các thuộc tính đầu vào độc lập trên cùng một hàng quan sát, giúp mô hình cây nắm bắt được quán tính và quy luật ngắn hạn.
- **Tại sao chọn XGBoost thay vì ARIMA hay các thuật toán khác**
  1. *Khả năng học phi tuyến vượt trội:* Dễ dàng nắm bắt được các tương tác phi tuyến tính và đột biến phụ tải phức tạp.
  2. *Tốc độ & Hiệu năng:* Cơ chế tính toán song song, phân nhánh dựa trên Histogram giúp xử lý cực nhanh trên tập dữ liệu lớn (> 120.000 bản ghi).
  3. *Tính diễn giải cao (Explainability):* Dễ dàng trích xuất độ quan trọng của từng mốc thời gian lịch sử (*Weight, Gain, Cover*), hỗ trợ giải thích rõ ràng cho nhà vận hành hệ thống.

---

### 3. WHEN (Dữ liệu diễn ra khi nào? Khi nào áp dụng mô hình?)
- **Mốc thời gian dữ liệu quan sát:** Thu thập liên tục từ **01/10/2004** đến **03/08/2018** (gần 14 năm), với chu kỳ lấy mẫu đều đặn **1 giờ/lần**.
- **Chiến lược phân chia thời gian (*Time-based Split*):**
  - Tuyệt đối **không xáo trộn ngẫu nhiên (*No Random Shuffle*)** để tránh rò rỉ dữ liệu tương lai vào quá khứ (*Data Leakage*).
  - **Tập Train (80% đầu):** Từ tháng 10/2004 đến cuối năm 2015 (~96.996 mẫu) dùng để mô hình học quy luật phụ tải.
  - **Tập Test (20% sau):** Từ cuối năm 2015 đến tháng 08/2018 (~24.249 mẫu) dùng để kiểm định độ chính xác trong tương lai chưa từng thấy.
- **Khi nào áp dụng kiến trúc này trong thực tế?** Thích hợp nhất cho bài toán dự báo ngắn hạn (từ 1 giờ đến 24 giờ tới - *Day-Ahead / Next-Hour Forecasting*).

---

### 4. WHERE (Dữ liệu thu thập ở đâu? Mô hình ứng dụng tại đâu?)
- **Nguồn gốc thu thập:** Dữ liệu thực tế từ **American Electric Power (AEP)** – tập đoàn năng lượng lớn quản lý lưới điện phục vụ hơn 5 triệu hộ dân và cơ sở kinh doanh tại 11 tiểu bang Hoa Kỳ (thuộc mạng lưới điều phối điện bán buôn **PJM Interconnection**).
- **Phạm vi ứng dụng thực tế:**
  - *Trung tâm điều độ hệ thống điện (EMS / SCADA):* Điều phối công suất phát của các nhà máy nhiệt điện, thủy điện và năng lượng tái tạo theo giờ.
  - *Thị trường bán buôn điện:* Dự toán chi phí và xây dựng chiến lược chào giá điện trên thị trường giao ngay (*Spot Market*).

---

### 5. HOW (Hệ thống được thiết kế và thực thi như thế nào?)
Hệ thống được xây dựng qua quy trình 5 giai đoạn liên kết tuần tự (*Pipeline*):
* Mục tiêu chính của bài làm là biến đổi bài toán chuỗi thời gian đơn biến thành bài toán **Học có giám sát (Supervised Learning)** thông qua kỹ thuật tạo **Lag Features** (biến độ trễ)[cite: 1]. Mô hình XGBoost sau đó được huấn luyện để dự đoán mức tiêu thụ điện $y_t$ tại thời điểm tiếp theo dựa trên các giá trị lịch sử trong quá khứ ($y_{t-1}, y_{t-2}, \dots, y_{t-p}$)[cite: 1].
## Phân công nhiệm vụ nhóm (6 Thành viên)

| STT | Họ và Tên | MSSV | Vai trò & Nhiệm vụ chính | Sản phẩm bàn giao |
| :---: | :--- | :---: | :--- | :--- |
| 1 | |  | Quản trị Git/Repo, thực hiện Bước 1: Chuẩn bị & làm sạch dữ liệu | `notebooks/01_data_prep.ipynb`<br>`docs/lab01_data_prep.md` |
| 2 |  |  | Thực hiện Bước 2 & 3: Tạo Lag Features & Tách ma trận X, y | `notebooks/02_lag_features.ipynb`<br>`docs/lab02_lag_features.md` |
| 3 |  |  | Thực hiện Bước 4: Chia tập Train/Test theo chuỗi thời gian | `notebooks/03_time_split.ipynb`<br>`docs/lab03_time_split.md` |
| 4 |  |  | Thực hiện Bước 5: Fit mô hình XGBoost Regressor | `notebooks/04_xgboost_train.ipynb`<br>`docs/lab04_xgboost_train.md` |
| 5 |  |  | Thực hiện Bước 6: Dự đoán & Trực quan hóa so sánh Thực tế vs Dự báo | `notebooks/05_evaluation.ipynb`<br>`reports/figures/` |
| 6 | || Thực hiện Bước 7: Phân tích Feature Importance & Viết báo cáo lý thuyết | `docs/lab05_evaluation.md` |


## Cấu trúc thư mục 

```text
xgboost-time-series-lab/
├── README.md                          # Hướng dẫn tổng quan & phân công nhiệm vụ
├── requirements.txt                   # Danh sách thư viện phụ thuộc
├── .gitignore                         # Các file/folder loại trừ khi commit Git
│
├── data/
│   └── raw/                           # Chứa file dữ liệu thô AEP_hourly.csv
│   │     └── AEP_housely.csv
│   └── processed/
│         ├── AEP_clearned.csv
│         ├── X_lag.csv
│         ├── y.csv
│         ├── X_test.csv
│         ├── X_train.csv
│         ├── y_test.csv
│         └── y_train.csv
│          
├──models
│   └── xgboost_model.json
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
