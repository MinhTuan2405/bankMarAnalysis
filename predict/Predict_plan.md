# Prediction Plan — `bank-full.csv`

- Bài toán: phân loại nhị phân — dự đoán khách có đăng ký tiền gửi kỳ hạn (`subscribed_term_deposit` = 1) trước khi gọi
- Dữ liệu: `dataset/bank/bank-full.csv` — 45,211 dòng, 11.7% đăng ký
- Notebook: `predict/BankMarPredict.ipynb`
- Căn cứ: `analysis/result.md` (mục 7.2 Recommendations for modeling)

---

## [ ] 1. Data Preparation

Đọc và chuẩn hoá dữ liệu giống notebook EDA

- [ ] Load dữ liệu, áp dụng `COLUMN_MAP` (copy từ `analysis/BankMarAnalysis_EDA.ipynb`, bước 1.3)
- [ ] Đổi các biến yes/no sang 1/0
- [ ] Bỏ `last_contact_duration` (leakage — chỉ biết sau cuộc gọi)

## [ ] 2. Feature Engineering

Tạo feature theo các phát hiện của EDA

- [ ] `was_contacted_before = days_since_prev_campaign != -1`
- [ ] `days_since_prev_campaign`: tách giá trị `-1` (vd. thay bằng 0 / giá trị lớn, đi kèm cờ ở trên)
- [ ] Giữ `unknown` làm category riêng (`job`, `education`, `contact_type`, `prev_campaign_outcome`)
- [ ] Biến lệch phải (`avg_yearly_balance`, `n_contacts_this_campaign`, `n_contacts_before_campaign`): log / clip — chỉ cho model tuyến tính
- [ ] Encoding: one-hot cho categorical (gồm `last_contact_month`), `ColumnTransformer` trong `Pipeline`

## [ ] 3. Train / Test Split

Chia dữ liệu phù hợp với mất cân bằng

- [ ] Stratified random 80/20, `random_state=42`
- [ ] Cross-validation trên tập train: `StratifiedKFold(5)`

## [ ] 4. Imbalanced Data

Xử lý mất cân bằng (11.7% đăng ký), chọn cách theo kết quả CV

- [ ] Phân bố lớp trên tập train
- [ ] So sánh trên Logistic Regression: không xử lý / `class_weight` / undersampling / SMOTE

## [ ] 5. Baseline & Models

So sánh các model từ đơn giản đến phức tạp

- [ ] Baseline: `DummyClassifier` (luôn dự đoán 0)
- [ ] Linear: Logistic Regression (`class_weight="balanced"`)
- [ ] Bagging: Random Forest (`class_weight="balanced"`)
- [ ] Boosting:
  - XGBoost (`scale_pos_weight ≈ 7.5`)
  - LightGBM (`class_weight="balanced"`)
- [ ] Ensemble: Soft Voting / Stacking từ 3 model tốt nhất
- [ ] Mỗi model: fit trên train, đánh giá trên train và test (classification report, confusion matrix, ROC, PR)
- [ ] Bảng + chart so sánh tất cả model
- [ ] Cài thêm `xgboost`, `lightgbm`, `imbalanced-learn` vào `requirements.txt`

## [ ] 6. Interpretation & Recommendations

Giải thích model và đề xuất cho chiến dịch

- [ ] Feature importance: permutation importance (+ hệ số Logistic Regression)
- [ ] Đối chiếu với các yếu tố ở dashboard EDA (`prev_campaign_outcome`, `last_contact_month`, `age`, `contact_type`, ...)
- [ ] Tổng hợp kết quả vào `predict/result.md`, hình vào `predict/figures/`
- [ ] Đề xuất: nhóm khách nên ưu tiên gọi, số lần gọi tối đa, hạn chế của model (drift theo thời gian)
