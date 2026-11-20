# Evaluasi Model Klasifikasi: Prediksi Kelayakan Penerima Beasiswa

Praktikum Data Mining tentang evaluasi model klasifikasi (confusion matrix, accuracy, precision, recall, F1-score) menggunakan **Decision Tree** pada data dummy kelayakan penerima beasiswa.

**Kelompok 8**: Ahmad Naufal Azzuhdi dan Dealexa Fatika Dzikra
Program Studi Informatika, Fakultas Teknik, Universitas Negeri Padang (UNP)

## Deskripsi

Modul praktikum aslinya memakai kasus deteksi fraud. Pada proyek ini kasusnya diganti menjadi **prediksi kelayakan penerima beasiswa**, dengan pemetaan berikut:

| Modul (fraud) | Proyek ini (beasiswa) |
|---|---|
| Kelas positif `isFraud = 1` | `isLayak = 1` (pendaftar layak) |
| FN: fraud lolos | FN: pendaftar layak tidak terdeteksi |
| FP: transaksi normal diberi alarm | FP: pendaftar tidak layak diprediksi layak |

Seluruh data adalah **data dummy hasil simulasi**, bukan data mahasiswa sebenarnya. Data dibangkitkan dengan seed tetap (`random_state=42`) sehingga hasilnya dapat direproduksi.

## Dataset

500 pendaftar dengan 5 fitur dan 1 label:

| Kolom | Keterangan |
|---|---|
| `ipk` | IPK pendaftar (2,00 sampai 4,00) |
| `penghasilan_ortu` | Penghasilan orang tua per bulan (juta rupiah, simulasi) |
| `jumlah_tanggungan` | Jumlah tanggungan keluarga (1 sampai 6) |
| `prestasi` | Memiliki prestasi (0/1) |
| `aktif_organisasi` | Aktif berorganisasi (0/1) |
| `isLayak` | **Label**: 0 = tidak layak, 1 = layak |

Peluang layak naik jika IPK tinggi, penghasilan orang tua rendah, tanggungan banyak, memiliki prestasi, dan aktif organisasi. Hasilnya data **tidak seimbang**: 438 tidak layak dan 62 layak (12,4%).

## Isi Notebook

| Bagian | Isi |
|---|---|
| 4.1 | Membangkitkan dataset sintetis |
| 4.2 | Membagi data (70% latih, 30% uji) dan melatih Decision Tree |
| 4.3 | Menghitung confusion matrix |
| 4.4 | Menghitung metrik secara manual dan memverifikasi dengan scikit-learn |
| 4.5 | Visualisasi confusion matrix |
| 4.6 | Interpretasi hasil dalam konteks beasiswa |
| 4.7 | Eksperimen baseline "semua tidak layak" pada data tidak seimbang |
| 4.8 | Eksperimen pengaruh threshold prediksi |

## Model

```python
DecisionTreeClassifier(max_depth=4, class_weight='balanced', random_state=42)
```

- `max_depth=4` membatasi kedalaman pohon agar tidak overfitting.
- `class_weight='balanced'` memberi bobot lebih pada kelas minoritas (layak).
- Data dibagi dengan `stratify=y` agar proporsi kelas terjaga.

## Hasil

Data uji berisi 150 pendaftar (19 layak, 131 tidak layak).

**Confusion matrix:** TP = 13, TN = 87, FP = 44, FN = 6

| Metrik | Nilai |
|---|---|
| Accuracy | 0,667 |
| Precision | 0,228 |
| Recall | 0,684 |
| F1-score | 0,342 |

Perhitungan manual menghasilkan nilai yang sama dengan scikit-learn.

**Baseline vs Decision Tree (4.7)**

| Model | Accuracy | Recall kelas layak |
|---|---|---|
| Baseline (semua tidak layak) | 0,873 | 0,00 |
| Decision Tree | 0,667 | 0,684 |

Baseline memiliki accuracy lebih tinggi, tetapi tidak menemukan satu pun pendaftar layak. Pada data tidak seimbang, accuracy saja bisa menyesatkan, sehingga perlu dilengkapi precision, recall, dan F1 pada kelas minoritas.

**Pengaruh threshold (4.8)**

| Threshold | Precision | Recall |
|---|---|---|
| 0,2 | 0,162 | 0,842 |
| 0,4 | 0,228 | 0,684 |
| 0,5 | 0,228 | 0,684 |
| 0,7 | 0,333 | 0,158 |

Menurunkan threshold menaikkan recall dan menurunkan precision. Jika biaya FN (pendaftar layak tersingkir) dinilai lebih besar daripada FP, threshold 0,2 dipilih karena recall tertinggi.

## Cara Menjalankan

1. Pasang pustaka yang dibutuhkan:
   ```bash
   pip install numpy pandas scikit-learn matplotlib
   ```
2. Buka `Praktikum_Evaluasi_Model_Kelompok8.ipynb` di Jupyter, VS Code, atau Google Colab.
3. Jalankan semua sel secara berurutan (*Run All*). Urutan penting karena sel di bawah memakai variabel dari sel di atasnya.

Menjalankan notebook juga akan menyimpan dataset ke `dataset_beasiswa.csv`.

## Catatan

- Data adalah simulasi, sehingga hasil tidak mewakili kondisi seleksi beasiswa yang sebenarnya.
- Jumlah pendaftar layak di data uji hanya 19, sehingga selisih beberapa data saja dapat mengubah metrik cukup besar.
- Model masih sederhana (precision 0,228) dan dipakai untuk tujuan pembelajaran evaluasi, bukan untuk keputusan nyata.

## Referensi

- Han, J., Kamber, M., & Pei, J. (2012). *Data Mining: Concepts and Techniques* (3rd ed.). Morgan Kaufmann.
- Tan, P.-N., Steinbach, M., Karpatne, A., & Kumar, V. (2019). *Introduction to Data Mining* (2nd ed.). Pearson.
- Pedregosa, F., et al. (2011). Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research, 12*, 2825-2830.
