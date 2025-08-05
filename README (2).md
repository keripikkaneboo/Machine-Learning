# Analisis Klasifikasi Otomatis Aktivitas Gunung Api Berdasarkan Citra CCTV Dengan Menggunakan Kecerdasan Buatan
Aplikasi web ini merupakan sistem lengkap untuk melakukan analisis citra aktivitas gunung api secara otomatis. Sistem ini menggunakan dua pendekatan utama kecerdasan buatan: Convolutional Neural Network (CNN) untuk klasifikasi kondisi dan Fully Convolutional Network (FCN) untuk segmentasi gambar. Aplikasi ini dibangun menggunakan framework Flask untuk antarmuka web dan menyediakan fitur untuk prediksi, melihat riwayat, serta melatih ulang model AI dengan data baru.

# Bagaimana Cara Kerjanya
Arsitektur sistem ini dirancang secara modular untuk memisahkan antara antarmuka pengguna, logika prediksi, dan proses pelatihan model. Inti dari sistem ini adalah aplikasi web yang dibangun menggunakan Flask (app.py), yang berfungsi sebagai pusat kendali untuk semua operasi. Ketika pengguna mengunggah sebuah gambar untuk dianalisis, aplikasi pertama-tama akan memeriksa database riwayat (history.db) untuk melihat apakah gambar tersebut pernah diproses sebelumnya, sehingga dapat memberikan hasil secara instan dan efisien.  Jika gambar tersebut baru, maka akan dianalisis oleh dua pipeline kecerdasan buatan utama yang bekerja secara bersamaan. Pipeline pertama, yang menggunakan serangkaian Convolutional Neural Network (CNN) dari pipeline.py, melakukan klasifikasi bertingkat untuk memahami konteks gambar secara mendalam. Proses ini menentukan berbagai aspek seperti waktu pengambilan gambar (siang atau malam), kondisi cuaca, hingga akhirnya menyimpulkan tingkat aktivitas vulkanik yang teramati, misalnya tenang, hembusan, atau letusan. Secara simultan, pipeline kedua yang menggunakan Fully Convolutional Network (FCN) dari fcn_pipeline.py melakukan tugas segmentasi. Pipeline ini menganalisis gambar untuk mengidentifikasi dan membedakan secara visual berbagai objek di dalamnya, seperti badan gunung api, awan, dan asap, dengan cara melapisi warna pada area-area tersebut. Gabungan hasil dari kedua pipeline ini—berupa kesimpulan teks dari klasifikasi CNN dan perbandingan visual antara gambar asli dengan gambar hasil segmentasi FCN—kemudian disajikan kembali kepada pengguna melalui antarmuka web yang interaktif. 

### Alur Prediksi
Ketika pengguna mengunggah gambar untuk dianalisis:
1. Permintaan Diterima: Gambar diunggah melalui antarmuka di `predict.html` dan diterima oleh `app.py`.
2. Pengecekan Riwayat: Aplikasi pertama-tama memeriksa apakah gambar tersebut (berdasarkan timestamp pada nama filenya) sudah ada di dalam `history.db`. Jika ya, hasil yang tersimpan akan langsung ditampilkan.
3. Pipeline Klasifikasi CNN: Jika gambar baru, ia akan diproses oleh `CNNPipeline` secara bertahap:
   * Tier 1 (Waktu): Model pertama menentukan apakah kondisi gambar Siang atau Malam. Jika Malam, proses berhenti di sini.
   * Tier 2 (Cuaca): Jika Siang, model kedua mengklasifikasikan cuaca menjadi Berawan atau Tidak Berawan.
   * Tier 3 (Kondisi): Berdasarkan hasil cuaca, model yang sesuai digunakan untuk memprediksi kondisi gunung api: Hembusan, Letusan, atau Tenang.
4. Pipeline Segmentasi FCN: Secara paralel, gambar asli juga diproses oleh `fcn_pipeline.py` untuk menghasilkan gambar baru dengan overlay warna yang menandai hasil segmentasi.
5. Hasil Ditampilkan: Hasil klasifikasi (teks) dan kedua gambar (asli dan hasil segmentasi) disimpan, dicatat di database, dan dikirim kembali ke browser pengguna untuk ditampilkan dalam jendela modal yang interaktif.

# Setup:
### Environment Setup:
#### Conda:
Download environment.yml dan simpan ke folder user C:/Users/(user acc).<br/>
Kemudian jalankan perintah dengan Anaconda Prompt:
```bash
conda env create -f environment-pvmbg.yml
```
atau:
```bash
conda env create -f "path-file.yml"
```
Jalankan environment:
```bash
conda activate klasifikasi-pvmbg
```
# Panduan Penggunaan Aplikasi
Setelah menyelesaikan langkah-langkah pada bagian Setup di atas, ikuti panduan ini untuk menjalankan aplikasi web.
1. Aktifkan Environment Conda <br/>
   buka Anaconda Prompt atau terminal Anda dan aktifkan environment yang sudah dibuat:
   ```bash
    conda activate klasifikasi-pvmbg
   ```
2. Siapkan Model <br/>
   Aplikasi ini membutuhkan file model yang sudah dilatih. Pastikan Anda memiliki file-file berikut dan letakkan di dalam folder `models/` di direktori utama proyek:
   * tier1_waktu_vgg16.h5
   * tier2_cuaca_vgg16.h5
   * tier3_kondisi_berawan_vgg16.h5
   * tier3_kondisi_tidakberawan_vgg16.h5
   * fcn_model.pth
3. Jalankan Aplikasi Web <br/>
   Dari direktori utama proyek (di mana file app.py berada), jalankan perintah berikut:
   ```bash
   python app.py
   ```
5. Akses Aplikasi <br/>
   Setelah server berjalan, Anda akan melihat output alamat web di terminal. Buka browser web Anda (misalnya Chrome, Firefox) dan kunjungi alamat yang diberikan untuk mulai menggunakan aplikasi.
   
# Laporan Kegiatan KP
| Timeline | Kegiatan | Catatan |
|----------|----------|----------|
| 9 Juli 2025 | Tahap awal konstruksi menggunakan CNN dan ANN|  - |
| 10 Juli 2025 | Melanjutkan Progress Programing menggunakan CNN tahap 1 bagian klasifikasi siang dan malam dan 2 bagian berawan dan tidak berawan | - |
|11 Juli 2025 | Melanjutkan progress konstruksi menggunakan CNN tahap 2 (perbaikan Klasifikasi berawan dan tidak berawan) dan tahap 3 bagian klasifikasi kondisi gunung seperti meletus, tidak meletus, dan tenang. | terdapat problem dalam mengklasifikasikan perbedaan antara hembusan dan berawan |
| 14 Juli 2025 | Testing model CNN menggunakan pytest. Melakukan klasifikasi segmentasi gambar untuk mencoba pada model FCN | - |
| 15 Juli 2025 | Melanjutkan segmentasi gambar dan mencoba untuk menambahkan klasifikasi gambar untuk data training | penambahan gambar ditambahkan agar model daapat memisahkan antara awan, hembusan, dan letusan lebih baik |
| 16 Juli 2025 | Melanjutkan segmentasi gambar dan menambahkan data gambar untuk klasifikasi yang lebih akurat | terdapat sedikit kesulitan dalam menemukan gambar untuk kelas tenang_tidak berawan_letusan |
| 17 Juli 2025 | Melanjutkan segmentasi gambar dan menambahkan data gambar untuk klasifikasi yang lebih akurat | perbaikan program segmentasi dan penambahan dataset untuk klasifikasi (ongoing) dan pembuatan dataset untuk segmentasi (ongoing) |
| 18 Juli 2025 | Melanjutkan segmentasi gambar dan menambahkan data gambar untuk klasifikasi yang lebih akurat | Mencoba menambahkan kelas gambar gunung meletus tidak berawan ke dataset menggunakan edit dari photoshop dikarenakan dari dataset cctv 2025 yang diperoleh atau dalam tanggal terjadinya letusan tidak dapat ditemukan di dataset. |
| 21 Juli 2025 | Mempersiapka model segmentasi FCN untuk | - |
| 22 Juli 2025 | Mempersiapkan presentasi progress | - |
| 23 Juli 2025 | Presentasi Progress ke tim | - |
| 24 Juli 2025 | Melanjutkan konstruksi program | mengolah video gunung menjadi gambar untuk klasifikasi kelas tidak berawan meletus |
| 25 Juli 2025 | Melanjutkan konstruksi program | Melanjutkan perbaikan program model sesuai saran dan masukan saat presentasi |
| 28 Juli 2025 | Melanjutkan konstruksi program | Memperbaharui program training cnn |
| 29 Juli 2025 | Melanjutkan konstruksi program | - |
| 30 Juli 2025 | Melanjutkan konstruksi program | Membuat enviroment |
| 31 Juli 2025 | Melanjutkan konstruksi program | Menambahkan fitur upload dataset pada aplikasi web |

# Screnshoot

