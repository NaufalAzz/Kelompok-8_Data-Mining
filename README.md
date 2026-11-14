# Praktikum Association Rule Mining (Apriori & FP-Growth)

Praktikum mata kuliah **Data Mining** — mencari pola barang yang sering dibeli bersamaan (*market basket analysis*) dengan Python dan pustaka `mlxtend`.

**Kelompok 8**

| Nama | NIM |
| --- | --- |
| Ahmad Naufal Azzuhdi | 24343029 |
| Dealexa Fatika Dzikra | 24343031 |

Program Studi Informatika, Departemen Teknik Elektronika, Fakultas Teknik, Universitas Negeri Padang, 2026.

---

## Tentang proyek ini

Bayangkan struk belanja di sebuah toko. Kalau banyak orang yang beli **Mentega** juga beli **Roti**, toko bisa menaruh keduanya berdekatan atau menjualnya sebagai paket hemat. Pola seperti inilah yang dicari oleh *Association Rule Mining* (ARM).

Praktikum ini mengerjakan:

- mengubah data transaksi menjadi tabel one-hot (`TransactionEncoder`);
- mencari *frequent itemset* dengan **Apriori**;
- membentuk aturan asosiasi dan menghitung **Support, Confidence, Lift**, ditambah **Leverage** dan **Conviction**;
- melihat pengaruh `min_support` dan `min_confidence` terhadap jumlah aturan;
- membandingkan **Apriori vs FP-Growth** (hasil dan waktu);
- menerapkan pipeline yang sama pada dataset mandiri (**toko baju**) dan menyusun rekomendasi bisnis.

## Isi repositori

```
.
├── README.md
├── Praktikum_ARM_Kelompok8.ipynb      # notebook lengkap beserta output
├── transactions_arm_praktikum.csv  # Lampiran A: 60 transaksi toko kelontong
├── transactions_toko_baju.csv      # Lampiran B: 50 transaksi toko baju (sintetis)
└── Laporan Praktikum/
    └── Kelompok 8_Laporan_PraktikumARM.docx
```

## Dataset

| Dataset | Transaksi | Jenis item | Keterangan |
| --- | --- | --- | --- |
| Toko kelontong | 60 | 12 | Dari modul praktikum. Ada kelompok sarapan, minuman, mie instan, dan mandi. |
| Toko baju | 50 | 10 | Dibuat sendiri dengan `random.seed(42)`. Isinya pakaian saja: Kemeja, Celana Bahan, Blazer, Kaos, Celana Jeans, Jaket, Hoodie, Celana Jogger, Blouse, Rok. |

Format CSV: kolom `transaction_id` dan `items` (item dipisah koma dalam satu sel).

> Dataset toko baju bersifat **sintetis** (dibangkitkan program), bukan data penjualan nyata.

## Cara menjalankan

1. Pasang pustaka:

   ```bash
   pip install pandas numpy matplotlib mlxtend
   ```

2. Taruh file `.csv` satu folder dengan notebook atau skrip.
3. Jalankan:

   ```bash
   jupyter notebook Praktikum_ARM_Kelompok8.ipynb
   ```

Program toko baju membuat ulang `transactions_toko_baju.csv` secara otomatis, jadi hasilnya selalu sama (seed 42).

## Hasil utama

### Toko kelontong (`min_support = 0,15`, `min_confidence = 0,5`)

- 17 frequent itemset dan 18 aturan asosiasi.
- Aturan terkuat: **{Mentega, Susu} → {Roti}** (confidence 1,000; Lift 3,333).
- Tiga kelompok barang yang jelas: paket sarapan (Mentega, Roti, Susu), mie instan (Mie Instan, Telur, Kecap), dan mandi (Sabun, Shampo).

### Pengaruh ambang batas

| Yang diubah | Perubahan | Dampak |
| --- | --- | --- |
| `min_support` 0,10 → 0,35 | naik | aturan turun dari 20 menjadi 0 |
| `min_confidence` 0,5 → 0,9 | naik | aturan turun dari 18 menjadi 1 |

Ambang rendah menghasilkan banyak aturan tetapi ada yang kebetulan (*noise*). Ambang tinggi menghasilkan aturan yang andal tetapi sedikit.

### Apriori vs FP-Growth

- Hasil **selalu sama** (itemset identik), baik pada `min_support` 0,15 (17 itemset) maupun 0,05 (42 itemset).
- Rata-rata dari 200 kali eksekusi, FP-Growth sedikit lebih cepat: 1,54× pada 0,15 dan 1,38× pada 0,05.
- Pada data sekecil ini selisihnya tidak membesar saat ambang diturunkan. Perlu dataset yang jauh lebih besar untuk melihat perbedaan nyata.

### Toko baju (`min_support = 0,10`, `min_confidence = 0,6`)

20 frequent itemset dan 21 aturan asosiasi.

| Aturan | Support | Confidence | Lift |
| --- | --- | --- | --- |
| {Hoodie} ↔ {Celana Jogger} | 0,14 | 1,000 | 7,143 |
| {Jaket} → {Kaos, Celana Jeans} | 0,16 | 1,000 | 5,000 |
| {Blouse} ↔ {Rok} | 0,12 | 0,750 | 4,688 |
| {Blazer} → {Kemeja, Celana Bahan} | 0,12 | 0,750 | 2,344 |
| {Kemeja} ↔ {Celana Bahan} | 0,32 | 0,941 | 2,768 |

**Rekomendasi untuk toko:**

1. Paket **Hoodie + Celana Jogger** (setelan santai), digantung berdampingan.
2. Paket **Kaos + Celana Jeans + Jaket**; tawarkan Jaket sebagai tambahan di kasir.
3. Paket **Blouse + Rok** (setelan kerja wanita).
4. Paket **Kemeja + Celana Bahan + Blazer** (setelan kerja pria); Blazer sebagai pelengkap.

## Cara membaca Support, Confidence, dan Lift

| Ukuran | Artinya dengan bahasa sederhana |
| --- | --- |
| **Support** | Seberapa sering barang itu muncul di seluruh struk. |
| **Confidence** | Dari struk yang ada barang A, berapa persen yang juga ada barang B. |
| **Lift** | Berapa kali lebih sering A dan B muncul bersama dibanding kalau keduanya tidak berhubungan. Lift > 1 berarti ada hubungan. |

## Keterbatasan

- Dataset toko baju kecil dan sintetis, jadi pola yang muncul mencerminkan pola yang dibuat program, bukan perilaku pembeli sebenarnya.
- Banyak aturan hanya didukung 6–8 transaksi; confidence 100% pada angka sekecil itu belum tentu bertahan di data nyata.
- Banyak aturan saling berulang karena berasal dari kelompok barang yang sama, sehingga 21 aturan bukan berarti 21 pola berbeda.
- Sebelum dipakai di toko sungguhan, aturan perlu diuji pada data asli, misalnya lewat A/B testing paket promo.

## Referensi

- Agrawal, R., & Srikant, R. (1994). Fast algorithms for mining association rules. *VLDB*, 487–499.
- Han, J., Kamber, M., & Pei, J. (2012). *Data Mining: Concepts and Techniques* (3rd ed.). Morgan Kaufmann.
- Han, J., Pei, J., & Yin, Y. (2000). Mining frequent patterns without candidate generation. *ACM SIGMOD Record, 29*(2), 1–12.
- Raschka, S. (2018). MLxtend: Providing machine learning and data science utilities and extensions to Python's scientific computing stack. *JOSS, 3*(24), 638.
- Tan, P.-N., Steinbach, M., & Kumar, V. (2019). *Introduction to Data Mining* (2nd ed.). Pearson.
- Dokumentasi mlxtend: https://rasbt.github.io/mlxtend/user_guide/frequent_patterns/
