# Sebelas Maret Statistics - Data Science

* **Tujuan**: Mengekstrak nilai curah hujan harian dari grafik berwarna (biasanya biru) menggunakan kelas `OCRRainfallExtractor`. Notebook ini juga berisi contoh pemrosesan satu file maupun batch untuk folder berisi banyak gambar.
* **Dataset yang digunakan**: File PNG dengan nama format `Plot_Daily_Rainfall_Total_mm_{YEAR}.png` per stasiun, misalnya:

  ```
  Train/<Station>/Plot_Daily_Rainfall_Total_mm_2015.png
  ```

  Direkomendasikan menaruh semua gambar di folder `data/` mengikuti struktur ini.
* **Metode / Pendekatan**:
  Notebook menggunakan **OpenCV + pytesseract** tanpa model machine learning:

  1. **Pra-pemrosesan gambar**: grayscale + CLAHE (contrast-limited adaptive histogram equalization).
  2. **Deteksi area plot**: thresholding + filter warna HSV untuk memisahkan garis/area biru.
  3. **Deteksi titik data**: menggunakan kontur dan centroid, diurutkan berdasarkan sumbu X.
  4. **OCR**: membaca label sumbu X/Y.
  5. **Kalibrasi**: konversi piksel → nilai curah hujan (mm) dan piksel X → hari dalam tahun.
* **Hasil output**: DataFrame dengan kolom `Date`, `Station`, `Rainfall_mm`. Bisa disimpan sebagai CSV gabungan untuk seluruh dataset.
* **Insight utama**:

  * Ekstraksi menghasilkan DataFrame harian lengkap. Hari tanpa titik data diisi `NaN` atau `0.0` bila ditemukan piksel biru.
  * Ada mekanisme kalibrasi heuristik untuk memperkirakan label sumbu Y jika OCR gagal.
  * Notebook mendukung batch processing dan menyimpan hasil gabungan (contoh: `/kaggle/working/hasil_ekstraksi.csv`).
  * Terdapat beberapa variasi grid manual untuk menyesuaikan posisi plot, supaya ekstraksi bisa bekerja pada berbagai template grafik.

---

## Dataset

* **Sumber**: Koleksi file gambar `.png` berisi grafik curah hujan harian.
* **Output yang dihasilkan**: CSV dengan kolom:

  * `Station`
  * `Date` (format YYYY-MM-DD)
  * `Rainfall_mm` (float)
* **Penempatan rekomendasi**:

  ```
  data/Train/<Station>/Plot_Daily_Rainfall_Total_mm_{YEAR}.png
  ```

---

## Metodologi

Pendekatan **deterministik berbasis CV + OCR** tanpa ML:

1. Pra-pemrosesan gambar (grayscale, CLAHE)
2. Ekstraksi region sumbu X/Y untuk OCR label
3. Deteksi area plot (filter warna HSV + pembersihan masker)
4. Deteksi titik data (kontur + centroid/scan kolom) → kalibrasi piksel → nilai curah hujan
5. Susun DataFrame per tahun, isi nilai, dan simpan hasil

---

## Cara Menjalankan

**Prasyarat**:

* Python 3.8+
* Tesseract OCR terpasang di sistem

**Langkah cepat (lokal)**:

1. Pasang Tesseract (Ubuntu):

```bash
sudo apt update && sudo apt install -y tesseract-ocr
```

2. Buat virtual environment dan install dependency:

```bash
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

Jika tidak ada `requirements.txt`:

```bash
pip install numpy pandas opencv-python pillow pytesseract matplotlib
```

3. Jalankan Jupyter Notebook / Lab:

```bash
jupyter lab
# atau
jupyter notebook
```

4. Buka `Timnya Abror_Notebook.ipynb`, sesuaikan path gambar (`directory_path`) lalu jalankan sel.

---

## Kebutuhan Instalasi

* **Python packages**: `numpy`, `pandas`, `opencv-python`, `pillow`, `pytesseract`, `matplotlib`
* **Sistem**: `tesseract-ocr`

  * Jika bahasa selain default diperlukan, pasang paket bahasa tambahan (misal: `tesseract-ocr-eng`).
* **Catatan**: Jika `pytesseract` tidak menemukan executable, set path manual:

```python
OCRRainfallExtractor(tesseract_path='...')
```

atau export `TESSDATA_PREFIX` sesuai lokasi Tesseract.

Kalau mau, saya bisa buatkan juga versi **lebih ringkas untuk GitHub**, yang bisa langsung dipakai sebagai `README.md` tanpa terlalu banyak penjelasan teknis. Apakah mau dicoba saya buatkan?
