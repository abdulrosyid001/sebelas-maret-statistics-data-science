**Ringkasan Proyek**
- **Nama repository**: `sebelas-maret-statistics-data-science` — koleksi notebook untuk mengekstraksi dan memproses data curah hujan dari gambar grafik.
- **Tujuan**: Menyediakan pipeline berbasis computer vision + OCR untuk mengekstrak data harian curah hujan dari gambar grafik (PNG). Notebook ini ditujukan untuk analisis, pembersihan data, dan produksi dataset terstruktur (CSV) dari kumpulan gambar grafik.

**Daftar Notebook & Penjelasan**
- **`Timnya Abror_Notebook.ipynb`**:
  - **Tujuan**: Implementasi dan eksperimen kelas `OCRRainfallExtractor` untuk mengekstrak nilai curah hujan (daily rainfall) dari gambar grafik lini/plot berwarna (umumnya biru). Notebook berisi fungsi pengujian single-file dan batch-processing untuk folder gambar.
  - **Dataset yang digunakan**: Koleksi gambar PNG yang merepresentasikan grafik "Plot_Daily_Rainfall_Total_mm_{YEAR}.png" untuk berbagai stasiun (contoh path yang digunakan dalam notebook: `Train/<Station>/Plot_Daily_Rainfall_Total_mm_2015.png`). Dataset diharapkan ditempatkan di folder `data/` atau path lokal sesuai struktur proyek.
  - **Metode / Model**: Pendekatan berbasis computer vision dan OCR — tidak menggunakan model ML terlatih untuk prediksi. Komponen utama:
    - Pra-pemrosesan gambar: konversi ke grayscale, CLAHE (contrast-limited adaptive histogram equalization).
    - Deteksi area plot: thresholding, deteksi warna (HSV) untuk memisahkan garis/area biru.
    - Deteksi titik data: kontur, momen untuk centroid, pengurutan berdasarkan sumbu X.
    - OCR: `pytesseract` dipakai untuk membaca label sumbu Y dan sumbu X (bulan/tahun) bila tersedia.
    - Kalibrasi: pemetaan piksel -> nilai curah hujan (mm) dan piksel X -> hari dalam tahun.
    - Output: `pandas.DataFrame` dengan kolom `Date`, `Station`, `Rainfall_mm` dan opsi menyimpan gabungan ke CSV.
  - **Langkah preprocessing dalam notebook**:
    - Membaca gambar dengan `cv2.imread` dan konversi ke RGB.
    - Penguatan kontras menggunakan CLAHE pada citra grayscale.
    - Thresholding Otsu / binary invers untuk memisahkan elemen grafik.
    - Filter warna di ruang HSV untuk mendeteksi garis berwarna (mask biru) dan membersihkan noise dengan operasi morfologi.
    - Deteksi kontur dan sirkularitas untuk menyaring titik data representatif.
  - **Hasil penting / insight utama**:
    - Ekstraksi menghasilkan DataFrame harian yang lengkap (mengisi hari tanpa titik dengan NaN atau 0.0 jika ditemukan piksel biru pada kolom tersebut).
    - Terdapat mekanisme kalibrasi otomatis/heuristik untuk memperkirakan label sumbu Y jika OCR gagal membaca semuanya.
    - Notebook menyediakan utilitas batch untuk memproses banyak gambar dan menyimpan hasil gabungan (contoh output: `/kaggle/working/hasil_ekstraksi.csv`).
  - **Catatan**: Notebook berisi beberapa variasi implementasi (grid manual berbeda) untuk menyesuaikan posisi plot kiri pada image; ini ditujukan agar ekstraksi bekerja pada berbagai template grafik.

**Dataset**
- **Sumber data**: Koleksi file gambar (`.png`) berisi grafik daily rainfall. Notebook mereferensikan folder `Train/<Station>/` di beberapa cell sebagai contoh.
- **Format data output**: CSV dengan kolom utama: `Station`, `Date` (YYYY-MM-DD), `Rainfall_mm` (float), digabung dari hasil ekstraksi tiap gambar.
- **Penempatan yang direkomendasikan**: Buat folder `data/` di root lalu susun sebagai `data/Train/<Station>/Plot_Daily_Rainfall_Total_mm_{YEAR}.png`.

**Metodologi**
- **Pendekatan**: Deterministik berbasis CV + OCR (OpenCV + pytesseract) — tidak bergantung pada model ML terlatih.
- **Alur kerja utama**:
  1. Pra-pemrosesan gambar (grayscale, CLAHE).
  2. Ekstraksi region sumbu Y dan sumbu X untuk OCR label.
  3. Deteksi area plot (filter warna HSV) dan pembersihan masker.
  4. Deteksi titik data pada plot (kontur + centroid / scanning kolom) lalu kalibrasi piksel->nilai.
  5. Penyusunan DataFrame lengkap per tahun, pengisian nilai dan penyimpanan hasil.

**Cara Menjalankan**
- **Prasyarat**: Python 3.8+; Tesseract OCR terinstal di sistem.
- **Langkah singkat (lokal)**:
  - Pasang Tesseract (Ubuntu):
    ```bash
    sudo apt update && sudo apt install -y tesseract-ocr
    ```
  - Buat environment dan pasang dependency:
    ```bash
    python -m venv .venv
    source .venv/bin/activate
    pip install --upgrade pip
    pip install -r requirements.txt
    ```
    Jika tidak ada `requirements.txt`, pasang paket utama:
    ```bash
    pip install numpy pandas opencv-python pillow pytesseract matplotlib
    ```
  - Jalankan Jupyter Notebook / Lab dan buka `Timnya Abror_Notebook.ipynb`:
    ```bash
    jupyter lab
    # atau
    jupyter notebook
    ```
  - Sesuaikan path gambar dalam notebook (variabel `directory_path` atau argumen pada fungsi) kemudian jalankan sel.

**Kebutuhan Instalasi**
- **Paket Python utama**:
  - `numpy`
  - `pandas`
  - `opencv-python`
  - `pillow`
  - `pytesseract`
  - `matplotlib`
- **Dependensi sistem**:
  - `tesseract-ocr` (engine OCR). Untuk penggunaan bahasa selain default, pasang paket bahasa yang diperlukan (mis. `tesseract-ocr-eng`).
- **Catatan konfigurasi**: Jika `pytesseract` tidak menemukan executable, set path manual pada inisialisasi `OCRRainfallExtractor(tesseract_path='...')` atau export `TESSDATA_PREFIX` sesuai instalasi.

**Struktur Direktori**
- **Root (direkomendasikan)**:
  - `Timnya Abror_Notebook.ipynb`   : Notebook utama untuk ekstraksi dan eksperimen.
  - `README.md`                     : (file ini)
  - `data/`                         : (direktori disarankan) menyimpan gambar `*.png` terorganisir.
  - `output/`                       : (direktori disarankan) menyimpan CSV hasil ekstraksi.
  - `requirements.txt`              : (opsional) daftar dependensi pip.

**Kontributor**
- **Penulis awal**: Tim / kontributor sesuai metadata proyek (sesuaikan nama jika ingin mencantumkan).
- **Cara berkontribusi**: Fork repository, tambahkan dataset / perbaikan notebook, buka pull request dengan penjelasan perubahan.

**Lisensi**
- Lisensi proyek: **MIT** (atau sesuaikan sesuai kebijakan tim). Jika repository belum memiliki file `LICENSE`, tambahkan file lisensi yang sesuai.

Catatan penutup: README ini disusun berdasarkan isi `Timnya Abror_Notebook.ipynb` yang mengimplementasikan `OCRRainfallExtractor` (OpenCV + pytesseract). Jika ada notebook lain atau dataset tambahan, perbarui bagian "Daftar Notebook & Penjelasan" dan "Dataset" untuk mencerminkan konten terbaru.
