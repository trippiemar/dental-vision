# Skrining Awal Masalah Gigi dari Foto

Proyek mata kuliah Data Mining dan Machine Learning.

Aplikasi web yang memprediksi kondisi gigi dari foto (karies, karang gigi, gingivitis)
menggunakan transfer learning dan Grad-CAM. Hasilnya adalah skrining awal, bukan diagnosis.

## Status

Sedang dikerjakan. Progres mingguan ada di `docs/weekly-log.md`.

- Minggu 1: audit dataset (duplikat visual, label campur).
- Minggu 2: pembagian data per kelompok, penambahan data karies, baseline MobileNetV2.

## Notebook

- `audit_gigi.ipynb`: audit dataset dan uji ambang pengelompokan.
- `siapkan_data.ipynb`: data tambahan, penggabungan, dan pembagian data.
- `latih_baseline.ipynb`: pelatihan dan evaluasi baseline MobileNetV2.

## Sumber data

Dataset tidak disertakan di repo ini. Unduh dari tautan di bawah.

- **Oral Diseases** (Kaggle, salmansajid05):
  https://www.kaggle.com/datasets/salmansajid05/oral-diseases
  Lisensi: <isi sesuai halaman Kaggle>
- **dental_project v1** (Roboflow Universe, workspace classification-kogl6), lisensi CC BY 4.0:
  https://universe.roboflow.com/classification-kogl6/dental_project-4qdtp
  Lisensi: https://creativecommons.org/licenses/by/4.0/
  Perubahan yang dilakukan: hanya label `caries` yang dipakai, 400 kelompok gambar
  dipilih acak, gambar diubah ukurannya dan diaugmentasi saat pelatihan.

## Catatan

Hasil model adalah skrining awal. Dataset tidak memiliki kelas gigi sehat.
Keterbatasan lengkap ada di `docs/weekly-log.md`.
