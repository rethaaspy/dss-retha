# Sistem Pendukung Keputusan (DSS) Optimalisasi Waktu Tanam Padi

Repository ini berisi Sistem Pendukung Keputusan hybrid yang mengintegrasikan LSTM (Long Short-Term Memory) dengan Rule-Based System untuk optimalisasi waktu tanam padi di Sumatera Utara.

## Struktur Repository

```
├── dataset/                           # Data input
│   ├── dataset_rainfall_all.xlsx      # Data curah hujan harian (2014-2024)
│   ├── dataset_harga_beras.xlsx       # Data harga beras (2021-2024)
│   └── dataset_serangan_hama.xlsx     # Data historis serangan hama (2020-2024)
│
├── output/                                              # Output model dan prediksi
│   ├── harga_beras_forecast.csv                         # Hasil prediksi harga beras
│   ├── hasil_dss_evaluasi_2014_2024.csv                 # Evaluasi sistem DSS tahun 2014–2024
│   ├── hasil_dss_final_label_cocok.csv                  # Rekomendasi DSS dengan label yang cocok
│   ├── hasil_dss_final_label_cocok_toleransi_filtered.csv  # Hasil DSS cocok + filter toleransi
│   ├── hasil_dss_forecast_2024_2025.csv                 # Prediksi DSS untuk tahun 2024–2025
│   ├── hasil_dss_gabungan_2014_2025.csv                 # Gabungan hasil DSS dari 2014 hingga 2025
│   ├── hasil_dss_gabungan_label.csv                     # Gabungan DSS dengan label
│   ├── hasil_rolling_forecast_2014_2025.csv             # Hasil prediksi rolling forecast 2014–2025
│   ├── lstm_classification_model.h5                     # Model LSTM untuk klasifikasi hari kering/basah
│   ├── lstm_price_model.h5                              # Model LSTM untuk prediksi harga beras
│   ├── lstm_rainfall_model.h5                           # Model LSTM untuk prediksi curah hujan
│   ├── output.txt                                       # File keluaran teks
│   ├── scaler_price                                     # Scaler untuk data harga beras
│   ├── scaler_rainfall_input                            # Scaler fitur input curah hujan
│   └── scaler_rainfall_output                           # Scaler output curah hujan
│
├── streamlit/                          # Aplikasi web Streamlit
│   ├── streamlit_dss3.py               # File utama aplikasi 
│   └── dll                             # Data yang dibutuhkan untuk deploy
│
├── other/                                       # Kode eksperimental
│   ├── grid-search.ipynb                        # Grid search hyperparameter LSTM
│   ├── baseline-model-vs-best-tuning.ipynb      # Model baseline LSTM 
│   ├── comparation model.xlsx                   # Hasil grid search
|   ├── dataset-exploratory.ipynb                #Code exploratory curah hujan
│
└── final_code-v7.ipynb              # Kode eksekusi utama sistem
```

## Fitur Sistem

1. **Prediksi Curah Hujan**: LSTM multivariate untuk prediksi curah hujan harian
2. **Klasifikasi Hari Kering/Basah**: Klasifikasi hari sebagai kering (<5mm) atau basah (≥5mm)
3. **Rolling Forecast**: Prediksi curah hujan dan status hari kering/basah untuk 90 hari ke depan
4. **Sistem Rekomendasi**: Evaluasi prediksi berdasarkan tiga parameter kunci:
   - Total curah hujan dalam 5 hari pertama
   - Maksimum hari kering berturut-turut dalam 15 hari pertama
   - Total curah hujan dalam 30 hari pertama
5. **Informasi Pendukung**: Prediksi harga beras dan visualisasi data serangan hama

## Spesifikasi Model

### Model Prediksi Curah Hujan
- LSTM Multivariate (64 unit, lookback 30 hari, dropout 0.2)
- Input: Suhu rata-rata, kelembaban relatif, durasi sinar matahari
- Metrik: MAE = 12,93 mm, R² = 0,1677, SMAPE = 125,47%

### Model Klasifikasi Hari Kering/Basah
- LSTM (64 unit, lookback 30 hari, dropout 0.2)
- Metrik: Akurasi = 70,07%, Presisi = 71,07%, Recall = 58,88%, F1-Score = 64,40%

### Model Prediksi Harga Beras
- LSTM Univariate (64 unit, lookback 30 hari)
- Metrik: MAE = 0,07 (ribu Rp), MAPE = 0,57%, R² = 0,6762

## Rule-Based System

Sistem rekomendasi menggunakan mekanisme skoring:

| Parameter | Nilai | Skor |
|-----------|-------|-------|
| Total 5 Hari | ≥ 38 mm | 2 |
|  | 30-37,9 mm | 1 |
|  | < 30 mm | 0 |
| Max Hari Kering (15 hari) | ≤ 10 hari | 2 |
|  | 11-14 hari | 1 |
|  | ≥ 15 hari | 0 |
| Total 30 Hari | ≥ 500 mm | 2 |
|  | 300-499,9 mm | 1 |
|  | < 300 mm | 0 |

Label rekomendasi:
- Skor ≥ 5: 🟢 Hijau (Sangat Direkomendasikan)
- Skor 2-4: 🟡 Kuning (Direkomendasikan dengan Catatan)
- Skor < 2: 🔴 Merah (Tidak Direkomendasikan)

## Validasi Sistem
- Akurasi 67,84% tanpa toleransi
- Akurasi 98,51% dengan toleransi antara label hijau-kuning

## Penulis
Retha Novianty Sipayung - Institut Teknologi Sepuluh Nopember (ITS)

## Sumber Data
- Badan Meteorologi, Klimatologi, dan Geofisika (BMKG): data curah hujan
- Badan Pangan Nasional: data harga beras
- Direktorat Jenderal Tanaman Pangan: data serangan hama
