# README — UTS Dam pariwisata Bali

## 1. Identitas Proyek

| Keterangan | Informasi |
|---|---|
| Nama proyek | **Bali Tourism Intelligence: Wisman per Pintu Masuk, Lama Menginap Hotel Bintang (2026), dan Tren Wisman 1969–2025** |
| Nama mahasiswa | I Made Bagus Abiyoga Prawira |
| NIM | 2501010089 |
| Mata kuliah | Data Acquisition & Management (DAM) |
| Jenis tugas | Ujian Tengah Semester (UTS) |
| Tools utama | Google Colab, Python, Google Drive |
| Wilayah kajian | Provinsi Bali; data historis pembanding juga mencakup Indonesia |

## 2. Konsep Proyek

Proyek **Bali Tourism Intelligence** menggabungkan beberapa sumber data untuk memberikan gambaran mengenai perkembangan pariwisata Bali. Fokus utamanya adalah jumlah wisatawan mancanegara (wisman) berdasarkan pintu masuk, rata-rata lama menginap pada hotel berbintang, tren kunjungan wisman jangka panjang, serta kondisi cuaca di Denpasar.

Proyek menggunakan tiga dataset terbuka dari Badan Pusat Statistik (BPS) Provinsi Bali, data cuaca historis dari API Open-Meteo, dan data listing daya tarik wisata dari situs Dinas Pariwisata Provinsi Bali (Disparda Bali) melalui web scraping.

### Tujuan

1. Mengumpulkan data pariwisata dari sumber terbuka, API, dan web scraping.
2. Membersihkan serta memvalidasi data sebelum dianalisis.
3. Menggabungkan data wisman, lama menginap, dan cuaca berdasarkan tahun dan bulan.
4. Mengamati tren kunjungan wisman Bali dan Indonesia dalam jangka panjang.
5. Menyajikan hasil analisis dalam bentuk tabel dan visualisasi.
6. Mendokumentasikan sumber, proses akuisisi, provenance, serta pemeriksaan integritas data.

### Dataset yang digunakan

| Kode | Dataset | Sumber | Cakupan dan isi |
|---|---|---|---|
| D1 | Jumlah wisman bulanan ke Bali menurut pintu masuk | BPS Provinsi Bali | Bandara Ngurah Rai, Pelabuhan Laut, dan jumlah; Januari–September 2026 tersedia pada file yang diproses |
| D2 | Rata-rata lama menginap tamu asing dan domestik pada hotel bintang menurut kelas dan bulan | BPS Provinsi Bali | Hotel bintang 1–5 dan seluruh kelas; Januari–September 2026 tersedia |
| D3 | Banyaknya wisman ke Bali dan Indonesia | BPS Provinsi Bali | Data tahunan 1969–2025 |
| D4 | Cuaca harian historis Denpasar | Open-Meteo Archive API | Suhu rata-rata, curah hujan, dan kecepatan angin maksimum; diagregasi menjadi data bulanan |
| D5 | Listing daya tarik wisata kabupaten/kota di Bali | Situs Disparda Bali | Nama daya tarik wisata, URL, tanggal posting (jika tersedia), kabupaten/kota, dan halaman sumber |

**Catatan cakupan:** Data D1 dan D2 yang tersedia dalam notebook hanya mencakup Januari–September 2026. Data D2 merupakan rata-rata lama menginap tamu asing **dan domestik**, bukan khusus wisman. Data cuaca hanya mewakili satu titik koordinat di Denpasar, sehingga tidak menggambarkan seluruh kondisi cuaca di Bali.

## 3. Sumber Data

Bagian ini mencantumkan sumber yang digunakan dalam notebook. URL BPS di bawah adalah URL portal/tabel statistik yang tercatat pada konfigurasi notebook; notebook belum mencatat URL halaman tabel individual untuk masing-masing CSV. Karena itu, tautan tersebut tidak boleh dianggap sebagai tautan unduhan langsung setiap dataset.

### A. Dataset BPS Provinsi Bali

Portal statistik BPS Provinsi Bali:
- https://bali.bps.go.id/id/statistics-table?subject=561
- Situs utama: https://bali.bps.go.id/

Judul dataset sesuai dokumentasi notebook:
1. **Jumlah Wisatawan Mancanegara Bulanan ke Bali Menurut Pintu Masuk, 2026**.
2. **Rata-Rata Lama Menginap Tamu Asing dan Domestik pada Hotel Bintang Menurut Kelas dan Bulan, 2026**.
3. **Banyaknya Wisatawan Mancanegara ke Bali dan Indonesia, 1969–2025**.

Berkas mentah yang digunakan:
- `wisman_pintu_masuk_bulanan_2026.csv`
- `lama_menginap_hotel_bintang_2026.csv`
- `wisman_indonesia_bali_1969_2025.csv`

**Penting:** Lengkapi provenance dengan URL halaman tabel BPS yang spesifik untuk setiap dataset apabila URL individual tersedia. Di notebook, ketiga dataset menggunakan URL portal BPS yang sama sebagai URL sumber. Status lisensi BPS pada notebook ditandai perlu diverifikasi; cantumkan atribusi BPS dan periksa ketentuan penggunaan yang berlaku sebelum redistribusi data.

### B. API Cuaca — Open-Meteo

- Dokumentasi Historical Weather API: https://open-meteo.com/en/docs/historical-weather-api
- Endpoint Archive API: https://archive-api.open-meteo.com/v1/archive
- Informasi lisensi: https://open-meteo.com/en/license

Parameter yang digunakan:
- Latitude: `-8.65`
- Longitude: `115.22`
- Lokasi representatif: Denpasar, Bali
- Periode: 1 Januari 2026 sampai akhir bulan terakhir yang tersedia pada D1 (September 2026 pada hasil notebook)
- Timezone: `Asia/Makassar`
- Variabel harian: `temperature_2m_mean`, `precipitation_sum`, `wind_speed_10m_max`

Data API dinormalisasi menjadi tabel harian, lalu diagregasi per bulan. Notebook mencatat Open-Meteo sebagai CC BY 4.0, tetapi tetap meminta verifikasi ketentuan lisensi pada halaman resmi.

### C. Web Scraping — Dinas Pariwisata Provinsi Bali

- Situs utama: https://disparda.baliprov.go.id/
- `robots.txt`: https://disparda.baliprov.go.id/robots.txt

Halaman kategori kabupaten/kota yang menjadi target:
- https://disparda.baliprov.go.id/badung/
- https://disparda.baliprov.go.id/buleleng/
- https://disparda.baliprov.go.id/bangli/
- https://disparda.baliprov.go.id/denpasar/
- https://disparda.baliprov.go.id/gianyar/
- https://disparda.baliprov.go.id/jembrana/
- https://disparda.baliprov.go.id/karangasem/
- https://disparda.baliprov.go.id/klungkung/
- https://disparda.baliprov.go.id/tabanan/

Scraping mengambil halaman pertama setiap kategori (maksimal satu halaman per kategori) dan mengekstrak judul artikel/listing, tautan, tanggal posting jika tersedia, nama kabupaten/kota, serta URL halaman sumber. Notebook menyimpan HTML mentah dan memeriksa `robots.txt`. Pemeriksaan `robots.txt` bukan pengganti pemeriksaan Terms of Service; ketentuan penggunaan situs tetap perlu dibaca dan dipastikan.

## 4. Struktur dan Alur Kode

Notebook dijalankan di **Google Colab** dari atas ke bawah (*Runtime → Run all*). Alur utamanya:

`Persiapan → Akuisisi CSV → Provenance → Intip data → Pembersihan → Validasi → API → Scraping → Merge → Analisis → Visualisasi → Penyimpanan → Verifikasi`

### BAB 0 — Persiapan lingkungan
- Mengimpor pustaka Python: `pandas`, `numpy`, `requests`, `BeautifulSoup`, `matplotlib`, dan pustaka pendukung.
- Mengatur identitas, direktori proyek, User-Agent, timeout, serta jeda request.
- Menyiapkan folder `data/raw`, `data/interim`, `data/processed`, dan `docs`.

### BAB 1 — Akuisisi data mentah dan provenance
- Mengidentifikasi dan mengunggah tiga CSV BPS.
- Menyimpan salinan mentah tanpa menimpa file yang sudah ada.
- Mencatat URL sumber, waktu pencatatan UTC, lisensi, metode akuisisi, ukuran file, dan checksum MD5 ke `docs/provenance.json`.
- Mengintip isi CSV sebelum proses parsing.

### BAB 2 — Pembersihan, validasi, dan identifikasi dataset
- Membuat fungsi normalisasi angka, teks, dan nama bulan.
- Mengubah D1 menjadi data panjang berdasarkan bulan dan pintu masuk.
- Mengubah D2 menjadi data panjang berdasarkan bulan dan kelas hotel.
- Menggabungkan dua panel D3 menjadi satu tabel tahunan 1969–2025.
- Melakukan pemeriksaan nilai kosong, kunci duplikat, rentang nilai, konsistensi jumlah, dan konsistensi periode.
- Menyimpan dataset hasil pembersihan dan katalog identifikasi dataset.

### BAB 3 — Lisensi, ketentuan situs, dan etika data
- Mengambil halaman bukti lisensi/ketentuan dari sumber terkait.
- Memeriksa indikasi PII pada file CSV.
- Membuat Data Ethics Checklist yang mencakup sumber, lisensi, robots.txt, PII, minimalisasi data, etika request, penyimpanan, dan keterbatasan.

### BAB 4 — Akuisisi API Open-Meteo
- Mengambil data cuaca historis harian untuk Denpasar.
- Menyimpan respons JSON mentah.
- Mengubah JSON menjadi tabel harian.
- Mengagregasi suhu rata-rata, total curah hujan, jumlah hari hujan, dan angin maksimum menjadi data bulanan.

### BAB 5 — Web scraping Disparda Bali
- Memeriksa `robots.txt`.
- Mengakses halaman kategori kabupaten/kota dengan jeda dan timeout.
- Menyimpan HTML mentah.
- Menggunakan `BeautifulSoup` untuk mengekstrak listing daya tarik wisata.
- Menghapus listing duplikat berdasarkan URL dan menyimpan hasilnya.

### BAB 6 — Merge, analisis, visualisasi, dan penyimpanan
- Mengubah data wisman dan lama menginap ke bentuk pivot.
- Menggabungkan data D1, D2, dan cuaca dengan kunci `tahun` dan `bulan`.
- Menggunakan `validate="one_to_one"` serta assertion untuk memastikan tidak ada duplikasi atau baris tanpa pasangan.
- Menghitung indikator bulanan dan analisis tren tahunan.
- Menghitung CAGR dan korelasi Spearman secara eksploratif.
- Membuat grafik dan menyimpan tabel hasil analisis.

### BAB 7 — Verifikasi akhir
- Memeriksa checksum MD5 berkas mentah.
- Menampilkan checklist akhir dan ringkasan jumlah data.
- Mengemas folder proyek menjadi ZIP untuk diunduh.

## 5. Struktur Folder Output

Struktur folder yang dibangun notebook:

```text
UTS_DAM_Pariwisata_Bali_2501010089/
├── data/
│   ├── raw/
│   │   ├── wisman_pintu_masuk_bulanan_2026.csv
│   │   ├── lama_menginap_hotel_bintang_2026.csv
│   │   ├── wisman_indonesia_bali_1969_2025.csv
│   │   ├── openmeteo_denpasar_harian_2026.json
│   │   ├── disparda_dtw_*.html
│   │   └── bukti_kepatuhan/
│   ├── interim/
│   │   ├── cuaca_harian.csv
│   │   ├── cuaca_bulanan.csv
│   │   └── scrape_daya_tarik_wisata.csv
│   └── processed/
│       ├── bali_wisman_menginap_cuaca_2026.csv
│       ├── asosiasi_spearman_2026.csv
│       ├── analisis_tahunan_1969_2025.csv
│       ├── cagr_wisman.csv
│       ├── metadata_hasil_gabungan.json
│       └── ...hasil validasi dan pembersihan lainnya
└── docs/
    ├── provenance.json
    ├── identifikasi_dataset.csv
    ├── bukti_lisensi_tos.csv
    ├── bukti_robots.csv
    ├── pii_scan_csv.csv
    └── gambar/
        ├── tren_wisman_1969_2025.png
        ├── pangsa_bali.png
        ├── wisman_bulanan_pintu_masuk_2026.png
        ├── lama_menginap_per_kelas_2026.png
        └── hujan_vs_wisman_2026.png
```

Nama file dan isi folder aktual dapat berbeda sesuai hasil eksekusi notebook.

## 6. Ringkasan Hasil Eksekusi Notebook

Berdasarkan output yang tersimpan pada notebook:

| Komponen | Hasil |
|---|---:|
| Dataset BPS D1 setelah dibersihkan | 27 baris |
| Dataset BPS D2 setelah dibersihkan | 54 baris |
| Dataset BPS D3 setelah dibersihkan | 57 baris |
| Data cuaca Open-Meteo | 273 baris harian |
| Data cuaca setelah agregasi | 9 baris bulanan |
| Hasil scraping Disparda Bali | 50 listing |
| Provenance yang tercatat | 16 sumber |
| Hasil merge D1 + D2 + cuaca | 9 baris |
| Kunci duplikat pada hasil merge | 0 |
| Bulan tanpa pasangan pada hasil merge | 0 |

Hasil merge mencakup Januari–September 2026. Notebook melaporkan total kunjungan wisman sebesar **5.237.128** untuk Januari–September 2026. Bulan dengan jumlah tertinggi pada periode tersebut adalah Juli (697.809), sedangkan yang terendah adalah Maret (472.070).

Untuk tren tahunan, notebook melaporkan jumlah wisman Bali tahun 2025 sebesar **6.948.754**, atau 110,7% dari angka tahun 2019 (6.275.210). Angka dan interpretasi ini mengikuti hasil eksekusi notebook.

## 7. Metode Analisis dan Visualisasi

Analisis yang dilakukan meliputi:
- Tren jumlah wisman Bali dan Indonesia dari 1969 sampai 2025.
- Pangsa wisman Bali terhadap total Indonesia (diberi catatan sebagai indikatif).
- Pertumbuhan tahunan dan Compound Annual Growth Rate (CAGR) pada beberapa periode.
- Jumlah wisman bulanan menurut pintu masuk pada 2026.
- Rata-rata lama menginap menurut kelas hotel bintang.
- Hubungan statistik Spearman antara jumlah wisman dan variabel lama menginap/cuaca.

Visualisasi disimpan dalam format PNG di `docs/gambar/`.

**Batas interpretasi:** Analisis hubungan bulanan menggunakan hanya sembilan observasi (Januari–September 2026), sehingga bersifat eksploratif. Korelasi tidak membuktikan sebab-akibat. Cuaca hanya berasal dari satu titik di Denpasar. Perbandingan sembilan bulan 2026 dengan dua belas bulan 2025 bukan perbandingan pertumbuhan tahunan yang setara.

## 8. Teknologi dan Pustaka

- **Python** — bahasa pemrograman.
- **Google Colab** — lingkungan pengembangan notebook.
- **Pandas** — membaca, membersihkan, menggabungkan, dan menganalisis data.
- **NumPy** — operasi numerik.
- **Requests** — komunikasi HTTP dan pengambilan data API/halaman web.
- **BeautifulSoup4** — parsing HTML untuk web scraping.
- **Matplotlib** — visualisasi data.
- **SciPy** — perhitungan korelasi Spearman.
- **Google Drive** — penyimpanan hasil proyek.

## 9. Cara Menjalankan

1. Buka file notebook `.ipynb` melalui Google Colab.
2. Pastikan tiga file CSV BPS tersedia saat notebook meminta unggahan.
3. Pada bagian konfigurasi, periksa nama, NIM, dan isi email kampus yang digunakan pada User-Agent.
4. Periksa kembali URL sumber BPS individual dan ketentuan lisensi/ToS.
5. Jalankan notebook dari sel pertama sampai terakhir menggunakan **Runtime → Run all**.
6. Periksa output validasi, `docs/provenance.json`, hasil scraping, hasil merge, dan grafik.
7. Jika seluruh proses selesai, notebook dapat membuat arsip ZIP folder proyek.

## 10. Catatan Transparansi dan Keterbatasan

- URL BPS yang tercatat di konfigurasi notebook adalah URL portal statistik, bukan URL individual masing-masing tabel. README ini tidak mengarang URL unduhan langsung yang tidak tercatat.
- Lisensi BPS dan ketentuan penggunaan situs Disparda perlu diverifikasi pada halaman resmi. Status scraping pada notebook mencatat bahwa `robots.txt` diperiksa; hal ini tidak otomatis berarti seluruh ketentuan penggunaan telah terpenuhi.
- D1 dan D2 baru memiliki data Januari–September 2026 pada file yang diproses.
- D2 mencakup tamu asing dan domestik.
- Data cuaca mewakili satu titik di Denpasar.
- Korelasi dengan sembilan bulan data hanya eksploratif dan tidak dapat digunakan untuk menyimpulkan hubungan sebab-akibat.
- Perbedaan angka growth antara data sumber dan hasil hitung ulang dicatat, bukan dikoreksi diam-diam.

---
README disusun berdasarkan struktur kode dan output pada notebook `UTS_DAM_Pariwisata_Bali_2501010089(1).ipynb`.
