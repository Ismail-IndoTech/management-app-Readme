> 📚 **Dokumentasi:** [Indeks lengkap ID/EN](DOCS.md) · [English documentation index](DOCS.en.md)

<div align="center">

<img src="docs/Logo.png" alt="IndoTech Server Management" width="128" height="128">

# IndoTech Server Management & Panel Gateway


### Gerbang Kendali Server Terpusat, Bypass Hosting Panel, Multi-Cloud Storage & Auto-Discovery Proyek.

**Solusi master control modern untuk mengelola infrastruktur server, membypass kontrol hosting panel (cPanel, CyberPanel, aaPanel, Webmin, Plesk, DirectAdmin, HestiaCP, CloudPanel, atau Native Linux Host), memonitor telemetri resource dual-layer, integrasi cloud storage, dan menginspeksi seluruh framework aplikasi secara real-time.**

[![Versi](https://img.shields.io/badge/versi-1.0.0-FF2D20?style=flat-square)](#)
[![Lisensi: MIT](https://img.shields.io/badge/lisensi-MIT-0EA5E9?style=flat-square)](LICENSE)
[![PHP](https://img.shields.io/badge/PHP-8.2%2B-777BB4?style=flat-square&logo=php)](#)
[![Laravel](https://img.shields.io/badge/Laravel-12.x-FF2D20?style=flat-square&logo=laravel)](#)
[![Livewire](https://img.shields.io/badge/Livewire-3.x-FB70A9?style=flat-square&logo=livewire)](#)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.x-38B2AC?style=flat-square&logo=tailwind-css)](#)
[![Buatan Indonesia](https://img.shields.io/badge/buatan%20di%20Indonesia-FF2D20?style=flat-square)](https://github.com/Ismail-IndoTech)

**Tanpa ketergantungan manual. Manajemen server, container, penyimpanan eksternal, dan terminal langsung dari antarmuka Web modern.**

<div align="center">

**[Bahasa Indonesia](README.md)** · <a href="README.en.md">English</a>

</div>

</div>

---

## Yang Tersedia di Dalam Aplikasi Ini

<div align="center">

| | | | | |
|:---:|:---:|:---:|:---:|:---:|
| 🎛️<br>**Universal Panel Bypass**<br>cPanel, aaPanel, Webmin | 📊<br>**Dual-Layer Resource**<br>Server Penuh vs Shared Akun | 🔍<br>**Multi-Framework Scan**<br>Laravel, Node, Python, CI | ☁️<br>**Multi-Cloud Storage**<br>Drive, Dropbox, MEGA, S3 | 💻<br>**Web Terminal**<br>PTY & Restricted Simulator |
| 🐳<br>**Docker & CLI Tools**<br>Root & User Mode | 🛡️<br>**APM & Security Telemetry**<br>Deteksi DDoS & BruteForce | 🚀<br>**Zero-Credential Wizard**<br>Installer Auto-Lock | 🌐<br>**Cloudflare & GitHub**<br>Re-integrasi Dinamis | 👥<br>**Proteksi Administrator**<br>RBAC Berlapis & Aman |

</div>

---

## 📖 Sekilas Aplikasi & Arsitektur Utama

**IndoTech Server Management & Panel Gateway** dibangun untuk menjawab tantangan tata kelola server modern dan batasan akun *shared hosting*. Ketika aplikasi dideploy pada lingkungan dengan hak akses terbatas (seperti cPanel shared hosting tanpa akses root), aplikasi ini secara cerdas menyesuaikan kapabilitasnya:

1. **Pusat Kendali Tunggal (Single Pane of Glass)**: Bertindak sebagai gerbang terpadu untuk mengeksekusi operasi server, mengelola domain/DNS, database, cron job, dan logs tanpa harus terus-menerus login ke cPanel, Webmin, aaPanel, atau console hosting yang lambat.
2. **Arsitektur Stateless & Zero-Credential**: Tidak ada kredensial sensitif pihak ketiga yang disimpan permanen di codebase. Saat dideploy ke server baru (Dedicated, VPS, atau Shared Hosting), sistem otomatis memandu proses inisialisasi dan menghubungkan ulang layanan eksternal (Cloudflare, GitHub, Cloud Storage) secara aman.
3. **Pemisahan Resource Dual-Layer**: Menampilkan data beban mesin fisik sebenarnya (*Hardware Server*, Core CPU, Total RAM, Total Disk) sekaligus membaca batasan kuota riil akun Anda (*cPanel CloudLinux LVE, user inode limits, user disk quota*).
4. **Deteksi Proyek & Pemantauan Agnostik Framework**: Menelusuri seluruh direktori server secara rekursif untuk mengenali framework yang berjalan (Laravel, NodeJS, TypeScript, Python Django/Flask, CodeIgniter, WordPress) lengkap dengan telemetri request, transfer rate, dan deteksi anomali serangan (DDoS / Brute Force).

---

## 🚀 Fitur Unggulan Real Aplikasi

### 1. 🎛️ Universal Hosting Panel Bypass
* Mendeteksi control panel server secara otomatis: **cPanel / WHM**, **CyberPanel**, **aaPanel**, **Webmin / Virtualmin**, **Plesk**, **DirectAdmin**, **HestiaCP**, **CloudPanel**, atau **Native Linux Server**.
* Nama menu sidebar dan badge secara dinamis berganti menyesuaikan panel yang aktif pada host.
* Membuka akses cepat (*quick launch console*) dan bypass manajemen domain, file, database, dan cron job langsung dari dashboard.

### 2. 📊 Profiler Resource Dual-Layer (Server Hardware vs Akun Shared)
* **Full Server Metrics**: Membaca penggunaan CPU Cores, Total Memori RAM, Physical Disk Usage, Uptime, dan Load Average mesin host secara transparan.
* **Shared Account Quota**: Jika berjalan di Shared Hosting / CloudLinux, sistem mendeteksi kuota akun isolasi pengguna (kuota disk MB/GB terpakai dan limit inodes file).

### 3. 🔍 Auto-Discovery Proyek & Deteksi Multi-Framework Real-Time
* Mengidentifikasi proyek di server tanpa konfigurasi manual:
  * **Laravel**: Membaca versi framework dari `composer.lock` / `composer.json`.
  * **Node.js & TypeScript**: Mendeteksi engine dan framework (Express, Nest, Next.js, Nuxt, Vite).
  * **Python**: Mendeteksi Django, Flask, FastAPI, dan versi Python interpreter.
  * **CodeIgniter**: Mendeteksi CI3 / CI4.
* **Per-Project Telemetry**: Menghitung volume request per menit/jam, estimasi konsumsi bandwidth, pemantauan error logs, serta pendeteksi dini lonjakan trafik mencurigakan (DDoS & Brute Force attempt).

### 4. ☁️ Multi-Cloud & External Storage Hub (Mesin Rclone)
* Didukung oleh engine universal **Rclone CLI** yang terpasang otomatis dalam *User Mode* (berjalan lancar di akun shared tanpa memerlukan akses root).
* Mendukung provider cloud terkemuka:
  * **Google Drive**
  * **MEGA**
  * **Dropbox**
  * **Microsoft OneDrive**
  * **iCloud** (via WebDAV)
  * **Object Storage S3 / Cloudflare R2 / MinIO**
  * **Server Eksternal via SFTP & FTP**
  * **Perangkat Fisik USB / Flashdisk & Local Mounts**
* Dilengkapi penjelajah file interaktif (*Cloud File Explorer*), pengujian koneksi & kuota instan, serta fitur sinkronisasi backup database dan proyek sekali klik (*One-Click Backup Sync*).

### 5. 💻 Terminal Virtual Web-Based Dual-Engine
* **Native PTY / Interactive Bash**: Berjalan saat hak akses terminal PTY tersedia pada server VPS / Dedicated.
* **Restricted Shell Simulator**: Fallback cerdas saat terminal server dinonaktifkan oleh kebijakan cPanel / hoster. Mengizinkan eksekusi perintah aman (Composer, Git, Artisan, NPM, Rclone) dengan riwayat perintah dan direktori aktif.

### 6. 🐳 Docker Container & CLI Tools Manager
* **Docker CLI Otomatis**: Urutan instalasi cerdas menggunakan mode `su` (Root), dan jika tidak didukung oleh kebijakan server (seperti pada cPanel shared hosting), secara otomatis fallback terpasang pada mode pengguna (*User Mode*).
* **CLI Ecosystem**: Instalasi dan monitoring status alat server: `docker`, `composer`, `node` / `npm`, `gh` (GitHub CLI), dan `rclone`.
* Mengelola status container (Run, Stop, Restart, Inspect, View Logs).

### 7. 🚀 Dynamic Zero-Credential Installer Wizard
* **Siklus Hidup Deployment Nyata**: Mode Installer hanya aktif jika aplikasi baru pertama kali dideploy di server (belum ada `.env`, direktori `vendor/`, aset manifest `build/`, atau *App Key* belum terkonfigurasi).
* Begitu sistem selesai dideploy dan terpasang di server, **Installer otomatis terkunci dan dihilangkan dari menu UI**, dan rute `/install` dialihkan langsung ke dashboard.
* Jika sewaktu-waktu aplikasi dipindahkan ke server baru, wizard akan memandu re-integrasi ulang dari awal.

### 8. 🛡️ Keamanan Ketat & Otoritas Administrator (RBAC)
* Manajemen otorisasi server terstruktur dengan hak akses berjenjang untuk menjaga integritas server.
* Proteksi mutlak kredensial administratif serta audit logging komprehensif.
* Dilengkapi *Activity Audit Log* komprehensif untuk melacak setiap aksi mutasi sistem.

### 9. 🎨 Tema Konsisten & Multi-Bahasa Dinamis
* **Konsistensi Tema**: Pilihan tema Dark Mode, Light Mode, dan System Auto yang tersimpan persisten tanpa flickering.
* **Dual Language**: Tombol pengubah bahasa instan (Bahasa Indonesia 🇮🇩 dan English 🇬🇧) dengan tombol gambar bendera negara yang elegan.

---

## 🛠️ Persyaratan Sistem & Instalasi Cepat

### Persyaratan Minimum
* **OS**: Linux (Ubuntu, Debian, AlmaLinux, RockyLinux, CloudLinux) atau macOS
* **Web Server**: Nginx, Apache, atau LiteSpeed
* **PHP**: 8.2 atau lebih tinggi (disarankan PHP 8.3 / 8.4)
* **Ekstensi PHP**: `bcmath`, `curl`, `fileinfo`, `mbstring`, `openssl`, `pdo_mysql` / `pdo_sqlite`, `tokenizer`, `xml`, `zip`
* **Composer**: v2.x
* **Node.js**: v18+ & NPM (untuk kompilasi aset frontend)

### Langkah Instalasi di Server Baru (Automated Setup Wizard)

```bash
# 1. Clone repositori ke server Anda

git clone https://github.com/Ismail-IndoTech/IndoTech_APP.git
cd IndoTech_APP/Management-APP

# 2. Instal dependensi backend & build frontend

composer install --no-dev --optimize-autoloader
npm install && npm run build
```

Setelah langkah di atas, **cukup buka domain atau IP server Anda di browser**:
1. Sistem akan secara otomatis mendeteksi status belum terpasang dan mengarahkan langsung ke **Installer Wizard (`/install`)**.
2. **Langkah 1: Deteksi Sistem & Web Server**: Sistem mendeteksi otomatis Web Server (Apache, Nginx, LiteSpeed, cPanel), OS, hak akses (Root/User mode), dan kebutuhan PHP. Klik tombol Next untuk menjalankan inisialisasi lingkungan `.env` dan dependensi via Virtual Terminal.
3. **Langkah 2: Konfigurasi Identitas Aplikasi**: Masukkan `APP_NAME`, `APP_URL` (terdeteksi otomatis dari host server), environment mode (`production`/`local`), dan bahasa default sistem. Disimpan via Virtual Terminal.
4. **Langkah 3: Setup Database Multi-Driver**: Pilih SQLite (Zero-config default), MySQL/MariaDB, PostgreSQL, atau SQL Server dengan fitur **Uji Koneksi Database Real-Time**. Klik Next untuk mengeksekusi migrasi skema tabel database via Virtual Terminal.
5. **Langkah 4: Konfigurasi Akun Administrator**: Buat akun Administrator server (Nama Lengkap, Email, dan Kata Sandi) untuk login dan memanajemen seluruh fitur server.
6. **Langkah 5: Finalisasi & Peluncuran Sistem**: Klik *Mulai Peluncuran Server* untuk menyaksikan Virtual Terminal menyelesaikan instalasi (symlink storage, optimasi cache views & config, penguncian installer dengan hardware fingerprint).
7. Setelah selesai, installer otomatis dikunci permanen dan tombol *Masuk ke Dashboard Sistem* siap digunakan!

---

## 📚 Dokumentasi Rinci

Panduan komprehensif untuk setiap modul telah disediakan di direktori [`docs/`](docs/):

* 📖 **[Panduan Lengkap Fitur & Arsitektur (Bahasa Indonesia)](docs/PANDUAN_FITUR.id.md)**
* 📖 **[Comprehensive Feature & Architecture Guide (English)](docs/FEATURE_GUIDE.en.md)**
* ☁️ **[Panduan Integrasi Multi-Cloud & Storage Eksternal (Bahasa Indonesia)](docs/PANDUAN_STORAGE_EXTERNAL.id.md)**
* ☁️ **[Multi-Cloud & External Storage Guide (English)](docs/EXTERNAL_STORAGE_GUIDE.en.md)**
* 💻 **[Panduan Virtual Terminal & Docker CLI User Mode (Bahasa Indonesia)](docs/PANDUAN_TERMINAL_CONTAINER.id.md)**
* 💻 **[Virtual Terminal & Docker CLI User Mode Guide (English)](docs/TERMINAL_CONTAINER_GUIDE.en.md)**

---

## 👥 Sistem Hak Akses (RBAC)

Aplikasi ini menerapkan pemisahan peran yang tegas dan aman:

- **Administrator Server**:
  - Akun primer server untuk mengelola pemantauan server, inspeksi proyek, operasi external storage, container, dan operasional harian dengan proteksi keamanan tinggi.
  - Memiliki akses penuh untuk konfigurasi server, manajemen domain, backup, dan pengaturan jaringan.
- **Operator / Viewer**:
  - Akses baca (*Read-Only*) untuk pemantauan metrik server, status proyek, dan riwayat log.

---

## Akses & Lisensi

Repositori ini adalah **karya saya sendiri**, **Mochamad Ismail Soleh** (Ismail-IndoTech), dan dilisensikan secara khusus di bawah **[MIT License](LICENSE)**.

Untuk memperoleh akses ke **repositori sumber (privat)** dari proyek ini, silakan hubungi saya sebagai **Creator Repo** melalui WhatsApp di nomor yang terdaftar pada akun DANA saya: **08121600058** — [wa.me/628121600058](https://wa.me/628121600058). Akses diberikan sesuai kesepakatan.
## 💖 Kontribusi & Donasi

Jika aplikasi ini mempermudah operasional IT, DevOps, dan pengelolaan server di perusahaan atau proyek pribadi Anda, dukung pengembangannya dengan menjadi kontributor atau **Donatur Spesial**.  
*(Donatur dan Sponsor resmi akan kami cantumkan logo perusahaannya di repositori ini).*

Dukung pemeliharaan berkelanjutan, riset modul bypass panel baru, dan pembaruan sistem dengan berdonasi melalui:

<div align="center">

**DANA — 08121600058** (a.n. Mochamad Ismail Soleh)

<img src="docs/qris.jpg" alt="QRIS DANA IndoTech" width="280">

*Pindai dengan aplikasi DANA, BCA, Mandiri, GoPay, OVO, atau aplikasi pembayaran QRIS apa pun di Indonesia.*

</div>

Semua dana dialokasikan untuk biaya infrastruktur riset server, lisensi alat pengujian, dan pengembangan modul open-source IndoTech. Jika Anda atau institusi Anda ingin menjadi mitra resmi atau sponsor, silakan hubungi kami. Terima kasih atas dukungan Anda!

---

## 📄 Lisensi

Proyek ini dirilis di bawah lisensi terbuka [MIT License](LICENSE).  
Hak cipta © 2026 **Mochamad Ismail Soleh (Ismail.IndoTech)**.

<div align="center">

Dibuat dengan ❤️ oleh [**Ismail.IndoTech**](https://it-app.id) · [it-app.id](https://it-app.id)

</div>