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