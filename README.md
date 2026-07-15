# 🧵 Grading Inspeksi Web

[![Python](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python&logoColor=white)](https://www.python.org)
[![Flask](https://img.shields.io/badge/Flask-3.0%2B-black?logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![JavaScript](https://img.shields.io/badge/JavaScript-ES6-yellow?logo=javascript&logoColor=black)](https://developer.mozilla.org)
[![GitLab CI](https://img.shields.io/badge/GitLab%20CI-Pipeline-orange?logo=gitlab&logoColor=white)](https://docs.gitlab.com/ee/ci/)

Aplikasi web untuk proses **grading / quality control (QC) kain** di lini inspeksi produksi. Operator menggunakan sistem ini untuk memindai barcode potongan kain, mencatat posisi meter serta bobot point defect, dan mengirimkan akumulasi data langsung ke backend sistem BTA.

Aplikasi ini dibangun menggunakan **Flask** yang berfungsi sebagai web server sekaligus **proxy server** untuk mengatasi kendala CORS (Cross-Origin Resource Sharing) antara frontend browser dan backend upstream berbasis CodeIgniter 3.

---

## ⚡ Fitur Utama

### 1. 🔑 Otentikasi dan Identifikasi
* **Login Operator via Barcode**: Pemilihan nama operator dilakukan via pintasan keyboard, disusul dengan identifikasi material melalui pemindaian barcode kain.
* **Status Ciri Kain**: Menyediakan sakelar toggle untuk menandai karakteristik khusus pada kain secara cepat.

### 2. 🧪 Validasi dan Antrean Data Defect
* **Auto-lookup Kode Cacat**: Pengisian kode defect 3 digit akan memicu pencarian nama cacat dari master data secara otomatis. Kode yang tidak terdaftar akan ditolak oleh sistem.
* **Validasi Point Ketat**: Input point dibatasi hanya untuk rentang angka 1 hingga 4. Jika operator memasukkan nilai di luar rentang, sistem otomatis mengonversinya menjadi 4.
* **Antrean Data Lokal**: Beberapa temuan cacat dapat ditampung ke dalam antrean in-memory terlebih dahulu sebelum dikirim secara kolektif ke server.

### 3. 🖥️ Manajemen Log dan Antarmuka Modul
* **Input Panjang Akhir**: Operator wajib memasukkan total meteran akhir kain melalui jendela popup sebelum melakukan finalisasi.
* **Operasi Berbasis Keyboard**: Seluruh alur kerja dioptimalkan tanpa menggunakan mouse, dirancang untuk terminal kasir produksi atau tablet industri.
* **Jendela Modal Terintegrasi**:
  * **Modal Detail Log**: Memeriksa antrean defect aktif dalam bentuk tabel.
  * **Modal Inputan Terakhir**: Menampilkan rekap inspeksi terakhir yang sukses tersimpan di database.
  * **Modal Inputan Hari Ini**: Menampilkan metrik pencapaian total kain yang berhasil diproses pada hari berjalan.

### 4. 🛠️ Sistem Pendukung
* **Toast Notification**: Memberikan umpan balik visual instan setelah aksi penyimpanan berhasil dilakukan.
* **Dokumentasi API** otomatis menggunakan Swagger UI yang dapat diakses melalui endpoint `/api/docs`.
* **Integrasi CI/CD Pipeline (GitLab CI)** untuk menjalankan proses linting via Flake8 dan pengujian otomatis via Pytest.

---

## 💻 Spesifikasi Teknologi

| Komponen | Teknologi |
| --- | --- |
| Backend / Proxy Server | Python 3.11, Flask Framework |
| Frontend Core | HTML5, Vanilla CSS, Vanilla JavaScript (ES6) |
| HTTP Client (Proxy Link) | Python Requests Library |
| Aset Visual & Tipografi | Inter Font (Google Fonts), Font Awesome 6 Icons |
| API Engine Documentation | OpenAPI 3.0 Standard, Swagger UI |
| Quality Assurance Tools | Flake8 (Linting), Pytest (Testing Framework) |
| DevOps / Pipeline | GitLab CI |
| Backend Upstream | CodeIgniter 3 (External Enterprise Server) |

---

## 🚀 Panduan Instalasi dan Menjalankan Berkas

### 📋 Prasyarat Sistem
* Python versi 3.11 atau yang lebih baru
* Package Installer untuk Python (pip)

### ⚙️ Langkah Eksplorasi

1. Clone repositori ini dan masuk ke dalam direktori utama proyek:
   ```bash
   git clone <url-repository>
   cd grading-inspeksi

```

2. Buat dan aktifkan modul virtual environment:
```bash
python -m venv .venv

# Untuk Windows (Command Prompt)
.venv\Scripts\activate

# Untuk Linux / macOS
source .venv/bin/activate

```


3. Pasang semua dependensi library yang dibutuhkan aplikasi:
```bash
pip install -r requirements.txt

```


4. Jalankan server lokal Flask:
```bash
python app.py

```


5. Akses antarmuka aplikasi melalui peramban:
```text
[http://127.0.0.1:5000](http://127.0.0.1:5000)

```



> ⚠️ **Catatan Jaringan:** Aplikasi ini membutuhkan koneksi aktif ke server upstream CodeIgniter 3 di alamat `https://testingbas.ddns.net/bta-testing`. Pastikan proxy server lokal Anda memiliki izin akses ke jaringan eksternal tersebut.

---

## 📁 Struktur Direktori

```text
grading-inspeksi/
├── app.py                  # Server entry point: Konfigurasi routing & proxy API
├── requirements.txt        # Daftar manifestasi dependensi library Python
├── .gitlab-ci.yml          # Pipeline konfigurasi CI/CD GitLab (lint + test)
├── .gitignore
├── templates/
│   ├── index.html          # Layout antarmuka utama frontend
│   └── swagger.html        # Render view dokumentasi Swagger UI
└── static/
    ├── css/
    │   └── style.css       # Berkas stylesheet utama
    ├── js/
    │   └── app.js          # Logika frontend, state management, & keyboard event listener
    └── swagger.yaml        # Dokumen definisi spesifikasi OpenAPI 3.0

```

---

## 🔌 Dokumentasi Endpoint API

Semua request dialokasikan melalui Flask. Endpoint berawalan `/api/` bertindak sebagai reverse-proxy yang meneruskan request menuju arsitektur backend CodeIgniter 3.

### 🌐 Rute Halaman Aplikasi

| Method | Path | Fungsi / Deskripsi |
| --- | --- | --- |
| `GET` | `/` | Memuat halaman utama operasional |
| `GET` | `/api/docs` | Memuat halaman interaktif Swagger UI |

### 💾 Manajemen Data Lokal (In-Memory Server)

| Method | Path | Fungsi / Deskripsi |
| --- | --- | --- |
| `POST` | `/simpan` | Menyimpan record defect ke memori lokal server sementara |
| `GET` | `/detail` | Mengambil daftar antrean defect yang ada di memori lokal |
| `POST` | `/reset` | Mengosongkan atau menghapus seluruh antrean defect di memori lokal |

### 🔄 Komunikasi Proxy Upstream (CodeIgniter 3)

| Method | Path | Fungsi / Deskripsi |
| --- | --- | --- |
| `GET` | `/api/cacat` | Mengambil master data jenis cacat (Diurutkan berdasarkan kode_cacat secara ascending) |
| `GET` | `/api/inspeksi` | Mengambil daftar nama operator produksi yang aktif |
| `POST` | `/api/potongan` | Memvalidasi dan mencari data material kain berdasarkan input barcode |
| `POST` | `/api/simpan-cacat` | Mengirimkan paket data final inspeksi kain ke database pusat |
| `GET` | `/api/detail-terakhir` | Memuat log data QC terakhir beserta rincian defect-nya |
| `GET` | `/api/rekap-hari-ini` | Mengambil seluruh riwayat transaksi grading pada hari berjalan |

---

## ⌨️ Panduan Navigasi Shortcut Keyboard

Gunakan kombinasi tombol pintasan berikut sesuai dengan layar modal atau halaman yang sedang aktif untuk efisiensi input.

### 🔏 Layar Login Operator

* `<kbd>↑</kbd>` / `<kbd>↓</kbd>` : Berpindah pilihan nama operator ke atas atau ke bawah.
* `<kbd>*</kbd>` : Menghidupkan atau mematikan switch status Ciri Kain.
* `<kbd>+</kbd>` : Membuka modal ringkasan Inputan Hari Ini.
* `<kbd>-</kbd>` : Membuka modal ringkasan Inputan Terakhir.
* `<kbd>Enter</kbd>` : Dipicu pada kolom input untuk scan barcode dan beralih ke halaman kerja berikutnya.

### 📝 Layar Utama Input Cacat

* `<kbd>+</kbd>` : Memindahkan fokus input ke kolom berikutnya (Meter ke Defect, lalu ke Point).
* `<kbd>-</kbd>` : Mengembangkan fokus input ke kolom sebelumnya sekaligus menghapus nilai di dalam kolom tersebut.
* `<kbd>*</kbd>` : Memasukkan data defect terisi ke dalam antrean tabel lokal.
* `<kbd>/</kbd>` : Membuka atau menyembunyikan tampilan jendela Modal Detail Log antrean.
* `<kbd>Enter</kbd>` : Membuka jendela Modal Input Panjang Akhir (Hanya dapat diakses jika antrean tidak kosong).
* `<kbd>I</kbd>` atau `<kbd>.</kbd>` : Membatalkan seluruh sesi inspeksi aktif dan kembali ke Halaman Login.

### 📦 Modul Layar Jendela (Modal)

* **Modal Panjang Akhir:**
* `<kbd>Enter</kbd>` : Mengirimkan data inspeksi teragregasi langsung ke backend.
* `<kbd>.</kbd>` : Menutup modal dan kembali melanjutkan input cacat.


* **Modal Inputan Terakhir:**
* `<kbd>-</kbd>` atau `<kbd>.</kbd>` : Keluar dan menutup modal.


* **Modal Inputan Hari Ini:**
* `<kbd>+</kbd>` atau `<kbd>.</kbd>` : Keluar dan menutup modal.



---

## 📊 Flowchart Alur Operasional Sistem

```text
+---------------------------------------------------------+
|                 HALAMAN LOGIN OPERATOR                  |
|  - Tentukan operator via tombol panah ↑ / ↓             |
|  - (Opsional) Tekan tombol * untuk toggle Ciri Kain     |
|  - Pindai barcode potongan kain, lalu tekan Enter       |
+---------------------------+-----------------------------+
                            |
                            | [Kondisi: Barcode Terverifikasi]
                            v
+---------------------------------------------------------+
|                  HALAMAN INPUT CACAT                    |
|  Siklus input data kerusakan material:                   |
|  1. Tentukan posisi defect pada kolom Meter             |
|  2. Tekan tombol + untuk pindah ke Defect, isi kode      |
|     -> Deskripsi kerusakan akan termuat otomatis         |
|  3. Tekan tombol + untuk pindah ke Point, isi skala 1-4 |
|  4. Tekan tombol * untuk menyimpan data ke antrean lokal |
|  5. Ulangi langkah 1 s.d 4 untuk tipe defect lainnya    |
|                                                         |
|  (Gunakan tombol / untuk meninjau status antrean)       |
+---------------------------+-----------------------------+
                            |
                            | [Kondisi: Selesai Inspeksi -> Tekan Enter]
                            v
+---------------------------------------------------------+
|                MODAL INPUT PANJANG AKHIR                |
|  - Masukkan total panjang akhir rol kain (meter)        |
|  - Tekan Enter untuk mengirim komparasi data ke backend |
+---------------------------+-----------------------------+
                            |
                            | [Kondisi: Pengiriman Sukses]
                            v
+---------------------------------------------------------+
|     Notifikasi Toast Muncul -> Reset ke Layar Login     |
|       (Sistem siap memproses gulungan kain baru)        |
+---------------------------------------------------------+

```

---

## 👷 Author dan Maintainer

* **Status Proyek:** Produksi / Operasional Intern
* Dikembangkan sebagai sistem pemosisian data berbasis web berkecepatan tinggi untuk lini Quality Control / Grading Inspection.

```

```
