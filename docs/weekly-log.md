# Log Mingguan

## Minggu 1

**Dikerjakan**
- Menentukan topik: skrining awal masalah gigi dari foto (karies, karang gigi, gingivitis)
- Mengunduh dataset Oral Diseases (Kaggle) dan menyiapkan lingkungan kerja
- Audit dataset: struktur folder, jumlah per kelas, deteksi duplikat dengan perceptual hash

**Temuan**
- 3.864 gambar terpakai (gingivitis 2.349, karang gigi 1.296, karies 219)
- Hanya 1.167 kelompok unik; 83,1% gambar punya kembaran visual (hasil awal, ambang 6)
- Sebagian kembaran adalah foto yang sama dengan rotasi atau perubahan warna
- Ada foto yang sama dengan label berbeda
- Data karies sedikit (190 kelompok unik)

**Kendala**
- Dataset publik banyak mengandung augmentasi, sehingga jumlah efektifnya kecil

**Rencana minggu 2**
- Uji ambang pengelompokan, bagi data per kelompok, tambah data karies, latih baseline

## Minggu 2

### Uji ambang pengelompokan (pHash, dengan flip)

| Ambang | Kelompok | % gambar punya kembaran | Kelompok campur | Unik calculus | Unik caries | Unik gingivitis |
|---|---|---|---|---|---|---|
| 0 | 1776 | 72,5 | 283 | 643 | 193 | 1227 |
| 2 | 1515 | 77,4 | 237 | 535 | 191 | 1030 |
| 4 | 1301 | 80,7 | 195 | 448 | 191 | 861 |
| 6 | 1167 | 83,1 | 179 | 394 | 190 | 766 |
| 8 | 1103 | 84,4 | 175 | 376 | 188 | 718 |

Keputusan: ambang 6. Dari 6 ke 8 jumlah kelompok hanya turun 64 (dari 4 ke 6 turun 134), jadi kurvanya mulai datar. Jumlah karies unik tetap sekitar 190 di semua ambang, sehingga kekurangan data karies bukan efek ambang. Kelompok campur sudah ada di ambang 0 (283), artinya foto yang sama memang diberi label berbeda di dataset asli.

### Kelompok berlabel campur

- 179 kelompok (1.501 gambar, sekitar 39% data). Rinciannya: calculus-gingivitis 169 kelompok (1.371 gambar), calculus-caries-gingivitis 4 (89), calculus-caries 3 (21), caries-gingivitis 3 (20).
- Aturan: semua kelompok campur dibuang, karena label yang benar tidak bisa ditentukan.
- Sisa: 2.363 gambar dalam 988 kelompok (calculus 575 gambar / 218 kelompok, caries 205 / 180, gingivitis 1.583 / 590).
- Dampak: calculus kehilangan 56% gambar, gingivitis 33%, caries hanya 14 gambar.

### Data karies tambahan

- Sumber: Roboflow Universe, dental_project v1 (11 April 2024), lisensi CC BY 4.0. https://universe.roboflow.com/classification-kogl6/dental_project-4qdtp
- 9.986 gambar, multi-label. README menyatakan tidak ada augmentasi. Beberapa nama file berasal dari Google Drive pengguna lain, jadi asal-usul gambar tidak sepenuhnya jelas.
- Kelas Cavity dibuang: contoh visual berisi foto rontgen panoramik dan slide presentasi.
- Hanya label caries yang dipakai. Label decay tidak dipakai: tidak pernah muncul bersama caries pada satu gambar, tetapi 640 kelompok visual berisi keduanya, jadi maknanya belum jelas (sinonim atau label keliru).
- Setelah Cavity dibuang: 4.563 kandidat. 22 gambar mirip data Kaggle (21 caries, 1 gingivitis) dibuang.
- Dari 1.983 kelompok caries Roboflow, 400 kelompok diambil acak (seed 42): 465 gambar, agar karies tidak mendominasi.
- Data gabungan: 2.828 gambar, 1.388 kelompok (calculus 575, caries 670 [205 Kaggle + 465 Roboflow], gingivitis 1.583).

### Pembagian data

Per kelompok, per kelas, 70/15/15, seed 42. Tidak ada kelompok yang muncul di lebih dari satu bagian.

| Kelas | Train | Val | Test |
|---|---|---|---|
| calculus | 414 | 81 | 80 |
| caries | 463 | 107 | 100 |
| gingivitis | 1145 | 209 | 229 |
| Total | 2022 | 397 | 409 |

### Baseline MobileNetV2

- Bobot ImageNet, seluruh jaringan dilatih, input 224x224, batch 16, AdamW (lr 1e-4), 8 epoch, cross-entropy berbobot kelas, mixed precision (RTX 2050, sekitar 19 detik per epoch).
- Augmentasi latih: random resized crop (0,7 sampai 1), flip horizontal, color jitter.
- Model terbaik: epoch 3 berdasarkan F1 makro validasi (F1 0,912, akurasi 0,922). Setelah itu loss latih terus turun tetapi loss validasi naik: overfitting.

Hasil pada data uji (dipakai satu kali): akurasi 0,914, F1 makro 0,898.

| Kelas | Precision | Recall | F1 |
|---|---|---|---|
| calculus | 0,955 | 0,787 | 0,863 |
| caries | 0,839 | 0,940 | 0,887 |
| gingivitis | 0,939 | 0,948 | 0,943 |

Akurasi karies per sumber: Kaggle 0,871 (n=31), Roboflow 0,971 (n=69). Confusion matrix: docs/confusion_baseline.png.

### Keterbatasan

- Data uji kecil (409 gambar; karies Kaggle hanya 31), jadi angka bisa naik-turun beberapa poin.
- 69% gambar karies berasal dari Roboflow, sedangkan kelas lain 100% dari Kaggle. Model mungkin belajar gaya foto sumber. Belum bisa diuji karena tidak ada gambar non-karies dari Roboflow.
- Foto ambigu dibuang, jadi hasil kemungkinan lebih optimistis daripada kondisi nyata. Kembaran visual yang lolos pHash masih mungkin ada.
- Makna label decay belum jelas. Sebagian gambar Roboflow punya watermark.
- Satu seed, satu kali pelatihan.
- Tidak ada kelas gigi sehat. Hasil model adalah skrining awal, bukan diagnosis. Uji foto HP ditunda.

### Rencana minggu 3

EfficientNet dan ResNet sebagai pembanding, beberapa seed, Grad-CAM (termasuk memeriksa watermark), uji ketahanan terhadap sumber data, lalu Streamlit.