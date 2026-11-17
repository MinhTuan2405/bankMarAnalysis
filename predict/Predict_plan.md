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

Chia dữ liệu phù hợp với mất cân bằng và drift theo thời gian

- [ ] Split chính: stratified random 80/20, `random_state=42`
- [ ] Split phụ: theo thời gian — 80% dòng đầu train, 20% dòng cuối test (dữ liệu sắp theo thời gian, tỉ lệ đăng ký tăng từ ~3% lên ~50%)
- [ ] Cross-validation trên tập train: `StratifiedKFold(5)`

## [ ] 4. Baseline & Models

So sánh các model từ đơn giản đến phức tạp

- [ ] Baseline: `DummyClassifier` (luôn dự đoán 0)
- [ ] Linear: Logistic Regression (`class_weight="balanced"`)
- [ ] Bagging: Random Forest (`class_weight="balanced"`)
- [ ] Boosting:
  - XGBoost (`scale_pos_weight ≈ 7.5`)
  - LightGBM (`class_weight="balanced"`)
- [ ] Ensemble: Soft Voting / Stacking từ 3 model tốt nhất
- [ ] Bảng so sánh tất cả model theo CV PR-AUC, thời gian train
- [ ] Cài thêm `xgboost`, `lightgbm` vào `requirements.txt`

## [ ] 5. Tuning

Tối ưu model tốt nhất

- [ ] `RandomizedSearchCV` với `scoring="average_precision"` (PR-AUC)
- [ ] Chọn ngưỡng phân loại (threshold) trên dữ liệu validation: tối đa F1 hoặc đạt recall mục tiêu

## [ ] 6. Evaluation

Đánh giá trên tập test bằng metric phù hợp với dữ liệu mất cân bằng

- [ ] PR-AUC (metric chính), ROC-AUC, precision, recall, F1
- [ ] Confusion matrix, PR curve, ROC curve
- [ ] Gain / lift chart: gọi top k% khách theo xác suất → bắt được bao nhiêu % khách đăng ký
- [ ] So sánh kết quả random split vs time-based split

## [ ] 7. Interpretation & Recommendations

Giải thích model và đề xuất cho chiến dịch

- [ ] Feature importance: permutation importance (+ hệ số Logistic Regression)
- [ ] Đối chiếu với các yếu tố ở dashboard EDA (`prev_campaign_outcome`, `last_contact_month`, `age`, `contact_type`, ...)
- [ ] Tổng hợp kết quả vào `predict/result.md`, hình vào `predict/figures/`
- [ ] Đề xuất: nhóm khách nên ưu tiên gọi, số lần gọi tối đa, hạn chế của model (drift theo thời gian)
