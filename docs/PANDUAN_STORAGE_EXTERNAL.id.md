# Panduan Integrasi Multi-Cloud & Storage Eksternal (IndoTech Management)

Modul **External Storage** pada IndoTech Management memungkinkan integrasi berbagai penyedia penyimpanan awan (*Cloud Storage*), server jarak jauh (SFTP/FTP), dan perangkat fisik USB untuk penambahan kapasitas penyimpanan serta otomatisasi backup server.

Modul ini ditenagai oleh mesin **Rclone CLI** yang berjalan dalam *User Mode* (`~/.local/bin/rclone`), sehingga dapat beroperasi di server VPS maupun Shared Hosting cPanel tanpa memerlukan hak akses root.

---

## 1. Provider Penyimpanan yang Didukung

| Provider | Tipe Rclone | Parameter Konfigurasi Kunci |
|---|---|---|
| **Google Drive** | `drive` | Client ID, Client Secret, OAuth Token / Refresh Token, Root Folder ID |
| **MEGA** | `mega` | Username (Email) dan Password Akun MEGA |
| **Dropbox** | `dropbox` | App Key, App Secret, OAuth Token |
| **Microsoft OneDrive** | `onedrive` | Client ID, Client Secret, Tenant ID / Drive ID |
| **Apple iCloud** | `webdav` | Apple ID, App-Specific Password, iCloud WebDAV URL |
| **S3 / Cloudflare R2 / MinIO** | `s3` | Access Key, Secret Key, S3 Endpoint, Bucket, Region |
| **Remote Server (SFTP / FTP)**| `sftp` / `ftp`| Host, Port, Username, SSH Private Key / Password |
| **Perangkat USB & Local Disk** | `local` | Path Mount (misal: `/media/usb`, `/mnt/backup`, `/home/...`) |

---

## 2. Langkah Konfigurasi Per Provider

### A. Google Drive
1. **Buat Project & Kredensial OAuth di Google Cloud**:
   * Masuk ke **Google Cloud Console** (https://console.cloud.google.com).
   * Aktifkan **Google Drive API** di menu *APIs & Services* -> *Library*.
   * Di menu *OAuth Consent Screen*, pilih *External*, masukkan nama aplikasi, dan tambahkan scope `.../auth/drive`.
   * Di menu *Credentials*, buat **OAuth 2.0 Client ID** (tipe *Web Application*).
   * Pada bagian **Authorized redirect URIs**, tambahkan alamat callback server Anda:
     `https://indotech-management.it-app.id/storage/oauth/google/callback`
   * Salin **Client ID** (`xxx.apps.googleusercontent.com`) dan **Client Secret** (`your-client-secret`).

2. **Perbedaan Penting (Penyebab Error: "token expired and there's no refresh token")**:
   > ⚠️ **Catatan Kritis**: File JSON yang diunduh dari Google Cloud Console (`client_secret_xxx.json`) adalah file kredensial Client ID & Secret, **bukan token otorisasi**. Token otorisasi Google hanya didapatkan setelah Anda login memberikan izin (*consent*). Jika hanya Client Secret JSON yang ditempel ke field token, Rclone tidak akan menemukan `refresh_token` dan gagal melakukan koneksi.

3. **Cara Menghubungkan Google Drive di IndoTech (3 Metode)**:
   * **Metode 1: Otorisasi Otomatis 1-Klik (Sangat Disarankan)**:
     1. Masukkan Client ID dan Client Secret, lalu simpan.
     2. Klik tombol **🔐 Login & Otorisasi Google Drive**.
     3. Anda akan diarahkan ke Google, klik "Izinkan/Allow".
     4. Google otomatis mengalihkan kembali ke server Anda dan token (beserta `refresh_token`) terpasang secara otomatis!
   * **Metode 2: Tukarkan Authorization Code Manual**:
     Jika menggunakan akun Google berbeda di browser lain, buka link otorisasi, salin kode otorisasi Google (`4/0A...`), tempel ke kolom *"Tukarkan Authorization Code"* lalu klik tombolnya.
   * **Metode 3: Google Service Account (Bebas Kedaluwarsa - Rekomendasi Server)**:
     Di Google Cloud Console, buat *Service Account*, unduh JSON Key, tempelkan isi JSON ke kolom Token di IndoTech, lalu bagikan (*Share*) folder Google Drive target ke email Service Account tersebut. Metode ini tidak membutuhkan interaksi browser dan tidak pernah kedaluwarsa.

### B. MEGA
1. Cukup gunakan kredensial akun MEGA Anda (Email dan Password).
2. Mesin Rclone akan mengenkripsi password secara aman ke dalam file konfigurasi lokal (`~/.config/rclone/rclone.conf`).
3. Kuota gratis 20GB+ dari MEGA dapat langsung dimanfaatkan untuk cadangan database offsite.

### C. Dropbox
1. Buka **Dropbox App Console** (https://www.dropbox.com/developers/apps).
2. Buat aplikasi baru dengan akses *Full Dropbox* atau *App Folder*.
3. Generate Access Token / Refresh Token.
4. Masukkan **App Key**, **App Secret**, dan **Token** ke form IndoTech Storage.

### D. Microsoft OneDrive
1. Daftarkan aplikasi di portal **Azure Active Directory / Microsoft Entra ID**.
2. Berikan izin *Files.ReadWrite.All* dan *offline_access*.
3. Dapatkan **Client ID** dan buat **Client Secret**.
4. Masukkan kredensial ke IndoTech Storage untuk menghubungkan OneDrive Personal atau OneDrive for Business.

### E. Apple iCloud (via WebDAV)
1. Masuk ke https://appleid.apple.com dengan Apple ID Anda.
2. Di bagian *Security*, buat **App-Specific Password** (Kata Sandi Khusus Aplikasi).
3. Masukkan Apple ID sebagai username dan App-Specific Password sebagai kata sandi.

### F. Object Storage S3 / Cloudflare R2 / MinIO
1. **Cloudflare R2**:
   * Endpoint: `https://<account_id>.r2.cloudflarestorage.com`
   * Masukkan **R2 Access Key ID** dan **Secret Access Key**.
   * Tentukan nama **Bucket**.
2. **Amazon AWS S3**:
   * Masukkan AWS Access Key, Secret Key, Region (misal: `ap-southeast-1`), dan nama Bucket.
3. **MinIO / Self-Hosted S3**:
   * Masukkan URL endpoint server MinIO Anda beserta kredensial bucket.

### G. Remote Server (SFTP / SSH)
1. Masukkan Host IP / Domain server tujuan dan Port (default `22`).
2. Masukkan Username server.
3. Pilih metode autentikasi: Password atau SSH Private Key.
4. Sangat cocok untuk sinkronisasi antar VPS atau transfer backup ke storage VPS terpisah.

### H. USB Device & Local Storage
1. Sistem secara otomatis mendeteksi perangkat penyimpanan yang terpasang pada port USB server atau partisi yang dimount di `/media`, `/mnt`, atau `/run/media`.
2. Klik tombol **Deteksi USB** untuk memindai partisi yang terhubung.
3. Anda dapat memilih partisi tersebut untuk dijadikan target penyimpanan offline instan.

---

## 3. Fitur Utama Pengelolaan & Dedicated Storage

### 1. Dedicated Cloud Storage Pool (Ekstensi Kapasitas Server)
* Anda dapat menetapkan akun cloud storage (misal: **Google Drive 5TB** atau **MEGA**) menjadi **Dedicated Storage** dengan menandai opsi `Jadikan Penyimpanan Dedicated`.
* Tersedia 4 pilihan peran (role) penyimpanan:
  1. **Dedicated Backup Vault**: Menjadi gudang utama pencadangan database, snapshot proyek, dan arsip sistem secara terisolasi.
  2. **App Media & Storage Expansion**: Berfungsi sebagai ekspansi kapasitas penyimpanan file publik/media aplikasi.
  3. **Project Codebase Archive**: Khusus untuk snapshot repository dan source code proyek production.
  4. **Dedicated Storage Pool**: Kapasitas cloud digabungkan ke telemetri sistem server (Hybrid Storage Pool).
* **Telemetri Dashboard Hibrida**: Pada dashboard beranda (**Tier 3: Dedicated Cloud & Extended Storage Pool**), kapasitas cloud storage yang terhubung secara otomatis dihitung dan digabungkan dengan disk lokal server (`Total Kapasitas Hibrida`).

### 2. HUD Animasi Progress Real-Time & Live Log Stream
* Setiap proses transfer data (Backup Database, Backup Proyek, Sinkronisasi Direktori, maupun Upload Berkas) kini dilengkapi modal **Real-Time Transfer HUD**.
* **Fitur HUD:**
  * Indikator status pulsing (*Running*, *Completed*, *Failed*).
  * Glowing progress bar dengan persentase real-time (`wire:poll.750ms`).
  * Telemetri berkas aktif: Menampilkan nama file yang sedang diproses dan ukuran total data yang ditransfer.
  * **Live Activity Terminal Drawer**: Stream log proses Rclone secara langsung baris demi baris, sehingga Anda dapat memantau proses transfer tanpa menebak-nebak statusnya.

### 3. Operasi 1-Klik (+ DB Backup & + Proyek)
* **+ DB Backup**: Cukup satu klik tombol pada kartu storage, sistem akan mendeteksi database server (SQLite atau MySQL), melakukan dump otomatis, mengompresnya dengan format `.sql.gz`, dan mengunggahnya ke folder `backups/database/` di cloud storage Anda.
* **+ Proyek (Project Backup)**: Memilih aplikasi atau proyek server yang ingin dicadangkan. Sistem akan membuat arsip `.tar.gz` secara cerdas (otomatis mengecualikan folder berukuran raksasa seperti `node_modules`, `vendor`, `.git`, dan cache logs), kemudian mengunggahnya ke cloud target dengan live progress tracker.
* **Integrasi di Menu Project Detail**: Pada halaman detail aplikasi (`/projects/{id}`), tersedia tombol **Backup ke Cloud** di toolbar dan kartu tindakan cepat, memungkinkan backup instan proyek production langsung ke Google Drive / Dedicated Cloud.

### 4. Interactive Cloud File Browser, Upload & Remote Editor
* **Jelajahi File**: Menjelajah struktur direktori di dalam Google Drive atau cloud storage lainnya secara cepat.
* **Upload File Langsung dari Browser**: Unggah berkas dokumen, zip, atau skrip langsung dari komputer Anda ke folder tujuan di remote storage.
* **Remote File Editor (+ File Baru & Edit)**: Buat file baru atau edit skrip/konfigurasi secara langsung di cloud tanpa perlu mengunduh dan mengunggah ulang.
* **Download & Hapus Berkas**: Unduh file langsung ke browser atau hapus direktori/berkas yang sudah usang.

---

## 4. Keamanan & Enkripsi Kredensial

* Semua token, kata sandi, dan secret key yang dimasukkan disimpan dalam database menggunakan enkripsi kuat **AES-256-CBC** melalui fitur *Encrypted Casts* Laravel (`$casts = ['credentials' => 'encrypted:json']`).
* File konfigurasi Rclone dihasilkan secara dinamis di direktori terlindungi (`~/.config/rclone/rclone.conf`) dengan izin berkas ketat (`chmod 600`) sehingga tidak dapat dibaca oleh pengguna lain di server shared hosting.
* Password pada remote seperti MEGA atau SFTP di-obscure menggunakan algoritma Rclone sebelum disimpan ke konfigurasi sistem.
