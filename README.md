# Bali Tourism Data Integration & Analytics

**Sistem Akuisisi, Integrasi, dan Analisis Data Pariwisata Bali
Menggunakan Open Dataset, API, dan Web Scraping**

Proyek ini merupakan tugas UTS mata kuliah **Data Acquisition &
Management**. Notebook membangun alur kerja untuk mengumpulkan,
membersihkan, mengintegrasikan, memvalidasi, menganalisis, dan
mendokumentasikan data pariwisata Bali dari beberapa sumber.

> **Catatan:** Notebook yang menjadi dasar README ini berisi kode
> analisis yang menghitung hasil secara dinamis ketika dijalankan. Nilai
> hasil akhir seperti jumlah destinasi yang berhasil di-scrape, jumlah
> baris API, negara asal terbesar, objek wisata teratas, dan nilai
> korelasi harus diambil dari output eksekusi notebook. README ini tidak
> mengarang angka hasil yang belum tersedia.

## Tujuan

1.  Membaca dan mengolah lima dataset terbuka (4 CSV dan 1 Excel) sesuai
    struktur masing-masing.
2.  Mengambil data cuaca historis harian melalui Open-Meteo Historical
    Weather API.
3.  Mengambil informasi destinasi wisata Bali dari Indonesia.Travel
    melalui web scraping dengan memeriksa `robots.txt`.
4.  Memeriksa aspek lisensi dan etika data, membersihkan, serta
    menormalisasi data.
5.  Melakukan dua integrasi data menggunakan `merge(validate=...)`.
6.  Memvalidasi kualitas hasil integrasi, membuat analisis eksploratif
    dan visualisasi, serta mencatat provenance.

## Metode Akuisisi Data

Proyek menggunakan tiga metode:

-   **Open dataset:** lima file sumber yang diunggah oleh pengguna.
-   **REST API:** Open-Meteo Historical Weather API.
-   **Web scraping:** `requests` dan `BeautifulSoup` untuk mengambil
    informasi destinasi dari Indonesia.Travel.

## Dataset yang Digunakan

  ------------------------------------------------------------------------
  Kode             Isi dataset               Cakupan yang Format
                                         disebutkan dalam 
                                                 notebook 
  ---------------- ---------------- --------------------- ----------------
  D1               Historis                    1969--2025 CSV
                   wisatawan                              
                   mancanegara                            
                   Indonesia dan                          
                   Bali                                   

  D2               Wisatawan                         2026 CSV
                   mancanegara                            
                   berdasarkan                            
                   pintu masuk                            

  D3               Wisatawan                   2019--2024 CSV
                   berdasarkan                            
                   kebangsaan                             

  D4               Rata-rata lama                    2026 CSV
                   menginap di                            
                   hotel                                  

  D5               Kunjungan objek             2016--2025 Excel
                   dan daya tarik                         
                   wisata (Table                          
                   17)                                    
  ------------------------------------------------------------------------

Cakupan tahun di atas mengikuti deskripsi struktur sumber pada notebook.
Ketersediaan nilai dapat berbeda antarbulan atau antartahun.

## Sumber Data Tambahan

### Open-Meteo

-   Endpoint: `https://archive-api.open-meteo.com/v1/archive`
-   Lokasi: Denpasar, Bali
-   Koordinat: lintang `-8.65`, bujur `115.2167`
-   Periode permintaan: mulai 1 Januari 2026 hingga tanggal terakhir
    yang tersedia (notebook menetapkan tanggal akhir lima hari sebelum
    tanggal berjalan).
-   Variabel cuaca: mengikuti daftar `VAR_HARIAN` pada notebook.

### Indonesia.Travel

Halaman destinasi Bali digunakan sebagai sumber web scraping. Notebook
memeriksa `robots.txt` sebelum mengambil halaman. Jika scraping tidak
diizinkan atau statusnya tidak jelas, proses dihentikan dengan aman.
Data destinasi tidak dibuat secara manual untuk menggantikan hasil
scraping.

## Alur Pengolahan

1.  **Konfigurasi dan persiapan folder** --- menyiapkan direktori
    `data/raw`, `data/processed`, `data/final`, dan `metadata`.
2.  **Penyimpanan data mentah** --- menyalin lima dataset ke `data/raw`
    tanpa mengubah file asli, lalu mencatat hash MD5.
3.  **Identifikasi struktur dataset** --- memeriksa header bertingkat,
    blok tahun, subtotal, sel gabungan, dan rumus Excel.
4.  **Estimasi kebutuhan storage** --- menggunakan rumus
    `(ukuran file × 12) × 1,3`, dengan asumsi satu berkas per bulan dan
    cadangan 30%.
5.  **Pemeriksaan etika dan lisensi** --- membuat checklist sembilan
    butir; lisensi yang belum diverifikasi tidak diasumsikan atau
    dikarang.
6.  **Akuisisi API** --- mengambil data cuaca harian dari Open-Meteo.
7.  **Web scraping** --- memeriksa `robots.txt`, mengambil HTML jika
    diizinkan, menyimpan data mentah, lalu mengekstraksi informasi
    dengan BeautifulSoup.
8.  **Cleaning dan normalisasi** --- merapikan teks, menyeragamkan
    kolom, mengubah nilai numerik, dan menyiapkan kunci integrasi.
9.  **Integrasi data** --- menggabungkan dua pasangan sumber yang
    relevan.
10. **Validasi kualitas data** --- memeriksa jumlah baris, keunikan
    kunci, indikator hasil merge, dan validasi kardinalitas.
11. **Analisis dan visualisasi** --- membuat ringkasan serta grafik dari
    data hasil pengolahan.
12. **Dokumentasi** --- menyimpan metadata, provenance, dan checklist
    etika.
13. **Kesimpulan** --- merangkum status proses, temuan eksploratif,
    keterbatasan, dan daftar file hasil.

## Integrasi Data

### Integrasi A Objek Wisata + Web Scraping

Data kunjungan objek dan daya tarik wisata dari Excel (D5) digabungkan
dengan informasi destinasi hasil scraping Indonesia.Travel. Kunci
penggabungan adalah nama objek yang telah dinormalisasi
(`kunci_gabung`).

Notebook menggunakan `validate="many_to_one"`. Fuzzy matching dengan
`difflib` hanya menghasilkan kandidat untuk ditinjau manual; kandidat
tidak otomatis dianggap cocok.

### Integrasi B Pintu Masuk + Cuaca

Data wisatawan mancanegara berdasarkan pintu masuk (D2) digabungkan
dengan data cuaca bulanan. Kunci penggabungan adalah pasangan **tahun
dan bulan** (`tahun`, `bulan_num`), dengan `validate="many_to_one"`.

## Analisis dan Visualisasi

Notebook menyiapkan analisis eksploratif berikut:

1.  Tren wisatawan mancanegara Bali dan Indonesia, 1969--2025.
2.  Sepuluh negara asal wisatawan mancanegara terbesar.
3.  Jumlah wisatawan berdasarkan pintu masuk pada periode data 2026.
4.  Perbandingan kunjungan wisatawan nusantara dan mancanegara pada
    objek wisata.
5.  Sepuluh objek wisata dengan jumlah kunjungan tertinggi.
6.  Rata-rata lama menginap berdasarkan kelas hotel.
7.  Perubahan rata-rata lama menginap setiap bulan.
8.  Eksplorasi hubungan jumlah wisatawan dengan curah hujan bulanan
    menggunakan korelasi Spearman.

Semua grafik dan ringkasan dibuat dari data hasil pengolahan. Analisis
bersifat eksploratif dan **tidak membuktikan hubungan sebab-akibat**.

## Aturan Cleaning dan Validasi

-   Nilai `-`, `–`, `—`, dan sel kosong diperlakukan sebagai `NaN`,
    bukan nol.
-   Nilai nol yang memang tercatat pada sumber tetap dipertahankan.
-   Data mentah tidak dimodifikasi; hasil pembersihan disimpan terpisah.
-   Jumlah baris sebelum dan sesudah merge diperiksa.
-   Kunci pada sisi kanan penggabungan diperiksa keunikannya.
-   `validate="many_to_one"` digunakan untuk memeriksa kardinalitas
    merge.
-   Duplikasi tidak dihapus hanya untuk membuat hasil terlihat rapi;
    penyebabnya perlu ditinjau.

## Struktur Folder Hasil

Notebook menyiapkan struktur berikut (file dihasilkan setelah notebook
dijalankan):

``` text
Bali_Tourism_Data_Project/
├── data/
│   ├── raw/                 # Lima dataset asli dan data mentah akuisisi
│   ├── processed/           # Dataset hasil cleaning/normalisasi
│   └── final/               # Hasil integrasi dan metadata final
├── metadata/                # Artefak metadata
├── provenance.csv            # Catatan sumber dan jejak file
├── data_ethics_checklist.csv # Checklist etika data
└── Bali_Tourism_Project.ipynb
```

Nama dan jumlah file aktual di setiap folder mengikuti hasil eksekusi
notebook.

## Cara Menjalankan

### Opsi 1 Google Colab

1.  Unggah `Bali_Tourism_Project.ipynb` ke Google Colab.
2.  Isi identitas pada bagian konfigurasi:
    -   `EMAIL_KAMPUS`
    -   `NAMA_MAHASISWA`
    -   `NIM`
3.  Jalankan notebook dari atas ke bawah menggunakan **Runtime → Run
    all**.
4.  Saat diminta, unggah lima file dataset sesuai kata kunci nama file
    yang diperiksa notebook:
    -   `bali_dan_indonesia`
    -   `pintu_masuk`
    -   `kebangsaan`
    -   `lama_menginap`
    -   `table-17`
5.  Periksa output setiap bagian, terutama status API, status scraping,
    hasil integrasi, validasi, grafik, dan ringkasan akhir.

### Opsi 2 Menjalankan secara Lokal

Notebook mendukung lingkungan non-Colab. Siapkan Python dan dependensi
berikut:

``` bash
pip install pandas numpy requests openpyxl matplotlib seaborn beautifulsoup4 jupyter
```

Atur lokasi folder proyek dan folder unggahan jika diperlukan:

``` bash
# Linux/macOS
export BALI_ROOT="./Bali_Tourism_Data_Project"
export BALI_UPLOAD="./upload"
```

``` powershell
# Windows PowerShell
$env:BALI_ROOT = ".\Bali_Tourism_Data_Project"
$env:BALI_UPLOAD = ".\upload"
```

Tempatkan lima dataset pada folder unggahan yang ditentukan, lalu
jalankan notebook dari atas ke bawah.

## Etika, Lisensi, dan Penggunaan Data

-   Periksa ketentuan penggunaan dan lisensi dari masing-masing sumber
    sebelum mendistribusikan ulang data.
-   Notebook tidak mengarang lisensi; sumber yang belum diperiksa
    ditandai **Belum terverifikasi**.
-   `robots.txt` diperiksa sebelum scraping dilakukan.
-   Request HTTP menggunakan timeout, User-Agent, jeda antar-request,
    dan retry untuk kondisi tertentu.
-   Jangan mengunggah data yang memiliki batasan distribusi atau
    informasi pribadi ke repositori publik tanpa memastikan izin
    penggunaannya.
-   Cantumkan sumber data dan tanggal akses saat memublikasikan hasil.

## Keterbatasan dan Asumsi

-   D2 dan D4 hanya memiliki data untuk bulan yang tersedia pada 2026;
    bulan yang ditandai `-` bukan berarti nol.
-   D3 memiliki kekosongan data untuk tahun 2021; notebook juga mencatat
    India 2023 kosong.
-   D5 tidak memisahkan kategori wisatawan nusantara dan mancanegara
    untuk 2016--2017; notebook memberi kategori `total_gabungan`
    berdasarkan asumsi penjumlahan.
-   D4 tidak memisahkan tamu asing dan domestik.
-   Cakupan objek pada D5 dapat berbeda antarperiode.
-   Data cuaca hanya mewakili satu titik koordinat di Denpasar, bukan
    seluruh wilayah Bali.
-   Kategori destinasi hasil scraping diturunkan dari kata kunci dan
    merupakan inferensi, bukan kategori resmi dari situs.
-   Lisensi yang belum diisi pada konfigurasi tetap berstatus belum
    terverifikasi.
-   Analisis korelasi cuaca dan kunjungan hanya eksploratif, dengan
    keterbatasan jumlah observasi, dan tidak dapat ditafsirkan sebagai
    hubungan kausal.

## Output Dokumentasi

Setelah notebook berhasil dijalankan, hasil dokumentasi yang disiapkan
mencakup:

-   `metadata.json` --- identitas proyek, lingkungan, ringkasan tabel,
    parameter API/scraping, hasil integrasi, asumsi, dan status proses.
-   `provenance.csv` --- sumber, URL, tanggal akses, lisensi/ketentuan,
    jenis akuisisi, nama file, hash MD5, dan keterangan.
-   `data_ethics_checklist.csv` --- status pemeriksaan etika data.
-   File hasil cleaning, normalisasi, dan integrasi di `data/processed`
    dan `data/final`.

## Teknologi

-   Python
-   pandas
-   NumPy
-   Requests
-   OpenPyXL
-   BeautifulSoup
-   Matplotlib
-   Seaborn
-   Jupyter Notebook / Google Colab
-   Open-Meteo Historical Weather API

## Penulis

Lengkapi identitas penulis pada notebook dan bagian ini sebelum
publikasi:

-   **Nama:** I Made Bagus Abiyoga Prawira
-   **NIM:** 2501010089
-   **Kelas:** E
-   **Mata kuliah:** Data Acquisition & Management

------------------------------------------------------------------------

*README ini menjelaskan metode dan alur yang tertulis di notebook.
Masukkan angka dan temuan aktual ke bagian hasil setelah notebook
dijalankan dan output-nya diverifikasi.*
