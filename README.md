# Dự án Nghiên cứu: Xử lý Dữ liệu Khuyết ở Thang điểm Nhận thức (ADNI)

## 📌 Giới thiệu đề tài
- **Tên đề tài**: Xử lý dữ liệu khuyết ở các thang điểm sàng lọc nhận thức (MMSE/MoCA/CDR) trong dữ liệu đa phương thức sa sút trí tuệ mà không gây sai lệch chẩn đoán (Artificial Normalcy).
- **Môn học**: Machine Learning in Medical
- **Nguồn dữ liệu**: Alzheimer's Disease Neuroimaging Initiative (ADNI) — Bộ trích xuất `ADNIMERGE2` (226 bảng dữ liệu).

---

## 🗂️ Cấu trúc thư mục dự án

```text
missing_data_adni/
├── configs/                  # File cấu hình tham số thí nghiệm
│   └── config.yaml           # Các thiết lập đường dẫn, tỷ lệ khuyết, mô hình
├── csv_data/                 # 226 file CSV trích xuất từ ADNIMERGE2
├── data/                     # Thư mục quản lý các giai đoạn dữ liệu
│   ├── raw/                  # Dữ liệu trích xuất ban đầu
│   ├── processed/            # Dữ liệu sạch sau khi join và tiền xử lý
│   └── simulated/            # Dữ liệu sinh missing nhân tạo (MCAR, MNAR)
├── docs/                     # Tài liệu nghiên cứu & đặc tả dataset
│   └── ADNI_DATASET_DOCUMENTATION.md # Đặc tả chi tiết 226 bảng và biến số
├── document/                 # Tài liệu lưu trữ bổ sung
├── notebooks/                # Các notebook Jupyter phân tích theo giai đoạn
│   ├── 01_eda_missingness.ipynb        # Khám phá tỷ lệ và cơ chế khuyết
│   ├── 02_simulate_missing.ipynb       # Mô phỏng missing có kiểm soát
│   ├── 03_imputation_experiments.ipynb # So sánh các phương pháp điền khuyết
│   └── 04_downstream_classification.ipynb # Đánh giá phân loại CN/MCI/Dementia
├── results/                  # Kết quả xuất ra từ các thí nghiệm
│   ├── checkpoints/          # Trọng số mô hình đã lưu
│   ├── figures/              # Biểu đồ phân tích (Missing matrix, ROC, Boxplot)
│   └── metrics/              # Bảng số liệu MAE, RMSE, F1, McNemar test
├── src/                      # Source code module hóa (Python package)
│   ├── data/                 # Loader, làm sạch mã khuyết (-1, -4), join bảng
│   ├── missingness/          # Kiểm định Little's MCAR, sinh mask MCAR/MNAR
│   ├── imputation/           # Baseline (Mean/Median/KNN/MICE/missForest) & DAE
│   ├── models/               # Mô hình phân loại bệnh (RF, SVM, XGBoost, LR)
│   └── utils/                # Hàm đo lường metrics và trực quan hóa
├── README.md                 # Tài liệu hướng dẫn sử dụng thư mục này
└── requirements.txt          # Danh sách thư viện Python cần thiết
```

---

## 🚀 Luồng thực hiện (Workflow Pipeline)

1. **Giai đoạn 1 - Data Prep (`src/data/`)**:
   - Đọc các bảng gốc (`MMSE`, `MOCA`, `CDR`, `DXSUM`, `PTDEMOG`, `APOERES`, `ADSP_PHC_T1_FS`).
   - Xử lý mã thiếu quy ước (`-1`, `-4`, `Not Done`).
   - Ghép nối dữ liệu theo cặp khóa `(RID, VISCODE2)` tạo thành tập phân tích (`analysis_dataset.csv`).

2. **Giai đoạn 2 - Missingness Analysis (`src/missingness/`)**:
   - Phân tích cơ chế khuyết: Structural MAR (MoCA ở ADNI1), Attrition MNAR (bệnh nhân nặng bỏ test).
   - Kiểm định tương quan giữa tình trạng khuyết với mức độ bệnh.

3. **Giai đoạn 3 - Simulation & Imputation (`src/imputation/`)**:
   - Tạo tập ground truth đầy đủ.
   - Mô phỏng khuyết ngẫu nhiên (MCAR) và khuyết phụ thuộc mức độ bệnh (MNAR) ở các tỷ lệ `10%, 20%, 30%, 40%, 50%`.
   - Áp dụng các thuật toán: Mean, Median, kNN, MICE, missForest, GAIN/DAE.
   - Đánh giá nguy cơ **Artificial Normalcy**.

4. **Giai đoạn 4 - Downstream Evaluation (`src/models/`)**:
   - Huấn luyện mô hình phân loại 3 lớp: `CN` vs `MCI` vs `Dementia`.
   - Đánh giá bằng F1-macro, AUC-ROC, ma trận nhầm lẫn và kiểm định McNemar.

---

## 📦 Cài đặt môi trường

```bash
pip install -r requirements.txt
```
