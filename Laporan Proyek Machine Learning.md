# Laporan Proyek Machine Learning - Thoriq Rivaldi Novendra

## Domain Proyek

**Prediksi Harga Tiket Pesawat Domestik Indonesia untuk Rekomendasi Tanggal Beli Optimal (Khusus Keberangkatan dari Jakarta)**  
Fluktuasi harga tiket pesawat domestik di Indonesia sangat dinamis. Terutama menjelang periode liburan nasional seperti Lebaran, Natal, dan Tahun Baru, harga tiket dapat melonjak drastis. Masalah ini menjadi penting karena memengaruhi keputusan konsumen dalam membeli tiket, serta strategi pricing maskapai.

**Mengapa Masalah Ini Penting?**  
Menurut riset [Putra, et al., 2021](https://scholar.google.com), harga tiket pesawat domestik di Indonesia dapat mengalami fluktuasi hingga 50% pada periode high season. Hal ini membuat konsumen sulit merencanakan pembelian tiket secara optimal. Oleh karena itu, memprediksi harga tiket berbasis waktu beli (days_before_departure) dan musim liburan sangat krusial.

## Business Understanding

### Problem Statements
1. Harga tiket pesawat domestik sangat fluktuatif.
2. Sulit memutuskan waktu terbaik untuk membeli tiket agar hemat biaya.

### Goals
1. Memprediksi harga tiket domestik berbasis waktu beli (`days_before_departure`) dan musim liburan.
2. Memberikan rekomendasi tanggal pembelian tiket terbaik untuk mendapatkan harga termurah.

### Solution Statements
1. Membuat baseline model menggunakan **Linear Regression** untuk memprediksi harga tiket.  
2. Meningkatkan akurasi prediksi dengan **Random Forest Regressor** (model non-linear), termasuk hyperparameter tuning (`n_estimators`, `max_depth`).  
3. Model terbaik dipilih berdasarkan evaluasi metrik (MAE, MSE, R²).  
4. Menggunakan model terbaik untuk rekomendasi tanggal beli optimal bagi user.

## Data Understanding

**Dataset:**  
- [`tiketcom_bestprice.csv`](https://www.kaggle.com/datasets/datasciencerikiakbar/tiketcom-best-price-for-flights-from-jakarta/data?select=tiketcom_bestprice.csv)
- Variabel utama:  
  - `depart_date` (tanggal keberangkatan)  
  - `extract_timestamp` (tanggal booking)  
  - `best_price` (harga tiket)  
  - `is_holiday` (liburan nasional / tidak untuk tanggal keberangkatan) 
  - `is_weekend` (weekend / tidak untuk tanggal keberangkatan)
  - `days_before_departure` (jarak hari beli tiket)

**EDA & Insight:**  
✅ Distribusi harga tiket menunjukkan fluktuasi signifikan, dengan rentang harga yang sangat lebar.  
✅ Korelasi `days_before_departure` vs harga: beli jauh hari cenderung lebih murah, beli mepet cenderung mahal.  
✅ Harga tiket pada hari libur nasional lebih mahal dibanding hari biasa.  
✅ Mayoritas orang beli tiket 100–20 hari sebelum keberangkatan.

**Visualisasi Pendukung:**  
- 📊 **Histogram Harga Tiket:** menunjukkan distribusi harga yang lebar, dengan beberapa outlier mahal.
  ![image](https://github.com/user-attachments/assets/c96e3d61-fa2d-4083-bcb7-03851b1db848)

- 📈 **Scatterplot `days_before_departure` vs Harga:** semakin dekat ke tanggal terbang, harga makin tinggi.
  ![image](https://github.com/user-attachments/assets/65f35ee5-79b0-4ebe-b075-4f0da5a88b45)

- 📦 **Boxplot `is_holiday` vs Harga:** libur nasional → harga tiket sedikit lebih mahal.
  ![image](https://github.com/user-attachments/assets/f49e4a57-96b5-4373-b67a-0fcd968a18da)

- 📦 **Boxplot `is_weekend` vs Harga:** weekend → harga tiket sedikit lebih mahal.
  ![image](https://github.com/user-attachments/assets/c1d330d5-3b84-4c88-8ab7-815e26b4716b)

- 📊 **Histogram `days_before_departure`:** mayoritas pemesanan di rentang 100–120 hari sebelum berangkat.
  ![image](https://github.com/user-attachments/assets/f02467e5-cfcd-4641-952b-852bc9f3bebb)


## Data Preparation

- Parsing `depart_date` dan `extract_timestamp` ke datetime.  
- Buat kolom `days_before_departure` (selisih hari beli tiket).  
- Buat kolom `is_holiday` (0/1) menggunakan library holidays.
- Buat kolom `is_weekend` (0/1) menggunakan method weekday().
- Cek dan handle missing values jika ada.

## Modeling

- Model 1: **Linear Regression** (baseline).  
- Model 2: **Random Forest Regressor** (non-linear, dengan tuning parameter).  
- Fitur:  
  - `days_before_departure`  
  - `is_holiday`
  - `is_holiday`
  - `destination`
- Target: `best_price`

**Kelebihan Linear Regression:**  
✔️ Sederhana, cepat dieksekusi  
❌ Tidak mampu menangkap pola non-linear

**Kelebihan Random Forest Regressor:**  
✔️ Menangkap pola non-linear  
✔️ Robust terhadap outlier  
❌ Butuh tuning parameter agar optimal

**Improvement:** Hyperparameter tuning `n_estimators`, `max_depth` untuk Random Forest.

**Model Terbaik:** Random Forest Regressor → lebih akurat menangkap fluktuasi harga tiket.

## Evaluation

**Metrik Evaluasi:**  
- **Mean Absolute Error (MAE)**  
- **Mean Squared Error (MSE)**  
- **R² Score**  

**Formula:**  
- MAE = \( \frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i| \)  
- MSE = \( \frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2 \)  
- R² = \( 1 - \frac{\sum (y_i - \hat{y}_i)^2}{\sum (y_i - \bar{y})^2} \)

**Hasil Evaluasi (contoh):**  
- Linear Regression MAE: Rp 50.000  
- Random Forest MAE: Rp 35.000
- R2 Random Forest : 0.9103

## Rekomendasi Tanggal Beli Optimal

✅ Model prediksi harga untuk `days_before_departure` dalam rentang 1–200 hari.  
✅ User input: tanggal keberangkatan dan dsetinasi / tujuan
✅ Model prediksi harga untuk berbagai `days_before_departure`, pilih harga prediksi termurah.  
✅ Output: tanggal beli optimal yang direkomendasikan dan tanggal beli yang tidak direkomendasikan

**Contoh Output:**  
- Tanggal terbang: 2025-06-15  
- Tujuan : SUB (Surabaya)
- Tanggal Beli Rekomendasi: 
    - 2025-05-15 (harga prediksi: Rp 650.000)
    - 2025-05-10 (harga prediksi: Rp 650.000)
    - 2025-04-30 (harga prediksi: Rp 655.000)
- Tanggal Beli Tidak Rekomendasi: 
    - 2025-05-14 (harga prediksi: Rp 750.000)
    - 2025-05-09 (harga prediksi: Rp 750.000)
    - 2025-04-29 (harga prediksi: Rp 755.000)

## Kesimpulan

Model regresi ini membantu konsumen memutuskan **waktu terbaik membeli tiket** pesawat domestik Indonesia. Pendekatan ini mempertimbangkan tren musiman, kebiasaan beli tiket, dan pola libur nasional serta pola weekend – mendukung manajemen biaya perjalanan dan strategi maskapai.
