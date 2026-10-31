# Panduan Komprehensif Fitur & Arsitektur IndoTech Server Management

Dokumen ini menjelaskan secara mendalam fungsi, mekanisme kerja, dan arsitektur teknis dari seluruh fitur nyata yang ada di dalam aplikasi **IndoTech Server Management & Panel Gateway**.

---

## 1. Arsitektur & Filosofi Sistem

Aplikasi ini dirancang sebagai **Gerbang Kendali Master (Master Control Gateway)** yang mampu berjalan di atas berbagai varian infrastruktur server tanpa memodifikasi kernel atau membutuhkan hak akses root secara mutlak:

* **Bypass Hosting Panel**: Memberikan antarmuka manajemen langsung untuk tugas-tugas server (domain, database, cron job, file, console) tanpa harus login ke antarmuka cPanel, aaPanel, Webmin, Plesk, dll. yang sering lambat atau dibatasi.
* **Stateless & Zero-Credential**: Aplikasi tidak menyimpan kredensial pihak ketiga secara hardcoded di dalam repositori. Saat dideploy di server baru, sistem secara otomatis meminta otorisasi ulang (cPanel API, Cloudflare API, GitHub Deploy Key) agar keamanan data server tetap terlindungi.
* **Self-Adapting Runtime**: Secara cerdas mendeteksi lingkungan hosting tempat aplikasi berjalan—apakah Baremetal Dedicated Server, VPS KVM, atau Akun Shared Hosting CloudLinux—dan menyesuaikan fitur yang dapat dieksekusi secara otomatis.

---

## 2. Universal Hosting Panel Bypass

Layanan backend `App\Services\ServerEnvironmentService` secara dinamis memindai signature server untuk mengenali panel yang aktif:

| Panel Hosting | Signature Deteksi Sistem | Fitur Bypass |
|---|---|---|
| **cPanel / WHM** | `/usr/local/cpanel`, binary `/bin/uapi` | Domain, Subdomain, Database, Cron Job, Email Routing, File Manager |
| **CyberPanel** | `/usr/local/CyberCP`, port 8090 | DNS, Website vhost, SSL issuing, Database |
| **aaPanel** | `/www/server/panel`, `/www/server` | Website management, MySQL manager, Cron |
| **Webmin / Virtualmin** | `/etc/webmin`, `/usr/share/webmin` | Server modules, System status, Virtual servers |
| **Plesk** | `/usr/local/psa` | Subscription management, Domains, DB |
| **DirectAdmin** | `/usr/local/directadmin` | Users, Domains, DNS admin |
| **HestiaCP** | `/usr/local/hestia` | Web domains, Mail accounts, Cron |
| **CloudPanel** | `/home/clp`, binary `clpctl` | Sites, Database, Security |
| **Native Linux Server** | Tidak ada hosting panel eksternal | Operasi langsung melalui Linux system utilities |

Sidebar navigasi aplikasi secara otomatis mengubah label dan badge sesuai panel yang terdeteksi, misalnya: `cPanel Controll [cPanel]`, `CyberPanel Controll [CyberPanel]`, atau `Server Controll [Native Server]`.

---

## 3. Profiler Resource Server Dual-Layer

Pada lingkungan shared hosting (seperti cPanel), sumber daya yang ditampilkan oleh perintah Linux standar (seperti `free -m` atau `lscpu`) seringkali merupakan sumber daya milik **seluruh mesin fisik server**, bukan batas kuota akun shared hosting Anda.

IndoTech Server Management membagi telemetri menjadi **2 lapisan (Dual-Layer)**:

1. **Hardware Server (Full Server)**:
   * **Total CPU Cores & Model Processor**: Dibaca langsung dari `/proc/cpuinfo`.
   * **Total RAM & Swap Hardware**: Dibaca dari `/proc/meminfo`.
   * **Total Kapasitas Harddisk Mesin**: Partisi fisik server via `df -h /`.
   * **System Load Average**: Rata-rata beban server (1 min, 5 min, 15 min).
2. **Akun Pengguna Shared (Tenant / LVE Account)**:
   * **CloudLinux LVE / cPanel Quota**: Penggunaan kuota disk spesifik milik user (MB/GB terpakai dari batas kuota akun Anda).
   * **Inode Limits**: Jumlah berkas/file terpakai dibandingkan batas maksimal inodes yang diizinkan hosting provider.
   * **Bandwidth Bulanan Akun**: Estimasi lalu lintas data yang melewati akun Anda.

---

## 4. Dynamic Multi-Framework Project Scanner & APM

Fitur pemindai proyek (`App\Services\ProjectScannerService`) bekerja secara rekursif menelusuri folder web di server (`public_html/`, `/var/www/`, `/home/user/apps/`) untuk mendeteksi aplikasi apa pun yang terpasang:

### Deteksi Framework & Versi
* **Laravel**: Membaca versi framework persis dari `composer.lock` atau `vendor/laravel/framework`.
* **Node.js & TypeScript**: Mengidentifikasi package.json, versi runtime Node, dan framework (Express.js, Nest.js, Next.js, Nuxt.js, Vite).
* **Python**: Mendeteksi `requirements.txt`, `Pipfile`, virtualenv, dan framework (Django, Flask, FastAPI).
* **CodeIgniter**: Mendeteksi struktur `system/core/CodeIgniter.php` untuk CI3 atau `app/Config/App.php` untuk CI4.
* **WordPress & PHP Native**: Mendeteksi `wp-config.php` beserta versi WordPress inti.

### Metrik & Telemetri Per Proyek
Di halaman detail per-proyek, pengguna disajikan statistik performa:
* **Volume HTTP Request**: Total request per menit/jam yang diarahkan ke vhost proyek tersebut.
* **Transfer Rates & Bandwidth**: Kecepatan transfer data masuk dan keluar secara live.
* **Pendeteksi Serangan & Anomali**:
  * **Brute Force Detection**: Menganalisis pola error 401/403 yang berulang dalam rentang waktu singkat pada endpoint login atau sensitif.
  * **DDoS / HTTP Flood Spike**: Memicu peringatan jika lonjakan request melonjak 5x di atas rata-rata trafik normal.
* **Log Analyzer**: Membaca log akses dan error log langsung dari antarmuka web tanpa membuka SSH atau cPanel File Manager.

---

## 5. Web-Based Virtual Terminal

Terminal virtual (`App\Livewire\Terminal\Index`) menyediakan akses baris perintah langsung melalui browser:

* **Mode Interaktif PTY (Native Bash)**: Digunakan jika server mengizinkan pembukaan pseudo-terminal (tersedia pada VPS, Dedicated Server, atau Shared Hosting dengan SSH aktif).
* **Mode Restricted Shell Simulator**: Solusi cerdas jika terminal server dinonaktifkan oleh kebijakan cPanel (`disable_functions` pada PHP seperti `proc_open` atau tidak ada akses PTY). Memungkinkan eksekusi perintah aman seperti:
  * `git status`, `git pull`
  * `php artisan ...`
  * `composer ...`
  * `npm run build`
  * `rclone ...`
* Dilengkapi navigasi direktori (`cd`), riwayat perintah (`history`), dan pembersihan layar (`clear`).

---

## 6. Multi-Cloud & External Storage Hub

Didukung oleh mesin headless **Rclone CLI** yang terpasang dalam *User Mode* (`~/.local/bin/rclone`):

* **Provider yang Didukung**:
  1. Google Drive
  2. MEGA
  3. Dropbox
  4. Microsoft OneDrive
  5. Apple iCloud (via WebDAV)
  6. Amazon S3, Cloudflare R2, MinIO
  7. Remote Server via SFTP & FTP
  8. Flashdisk / USB External & Local Mount Point
* **Fitur Utama**:
  * Pengujian koneksi & kapasitas sisa secara instan.
  * **Interactive Cloud File Browser**: Jelajahi berkas, buat folder, dan hapus berkas langsung dari UI.
  * **One-Click Backup Sync**: Sinkronisasi cadangan basis data atau folder proyek ke cloud storage pilihan secara terjadwal atau sekali klik.

---

## 7. Ekosistem Alat CLI & Docker Engine

Aplikasi memiliki pengelola perkakas sistem bawaan (`App\Services\SystemToolManagerService`):

* **Docker-CLI dengan Fallback Cerdas**:
  1. Tahap 1: Mencoba instalasi dengan hak akses `su` (Root).
  2. Tahap 2: Jika server menolak perintah `su` (biasanya pada cPanel Shared Hosting), sistem otomatis beralih memasang Docker-CLI dalam *User Mode*.
* **Status Docker Daemon**:
  * Jika daemon Docker tidak berjalan karena kebijakan server, UI memberikan panduan diagnostik yang transparan tanpa merusak kestabilan aplikasi.
* **CLI Pendukung**:
  * Pemantau dan installer untuk `composer`, `node`/`npm`, `gh` (GitHub CLI), dan `rclone`.

---

## 8. Dynamic Zero-Credential Installer Wizard & Virtual Terminal Setup

Installer Wizard dirancang dengan siklus hidup otomatis yang sadar-lingkungan (*Deployment-Aware Lifecycle*):

1. **Aktivasi Otomatis Pasca Git Clone**:
   * Begitu repositori di-clone dari GitHub ke server baru, middleware `EnsureInstalledMiddleware` mendeteksi status instalasi server host melalui `ServerEnvironmentService::isInstalledOnCurrentServer()`.
   * Jika server belum terkonfigurasi (file `.env` belum ada / belum ter-lock dengan hardware fingerprint server saat ini), **seluruh lalu lintas web otomatis diarahkan ke rute `/install` tanpa memerlukan login**.
2. **5 Tahapan Interaktif Setup Wizard**:
   * **Langkah 1: Auto-Read Integrasi Server**:
     * Mendeteksi otomatis Web Server host (Apache, Nginx, LiteSpeed, cPanel Host).
     * Menganalisis arsitektur sistem operasi dan hak akses server (Root mode vs Restricted User mode).
     * Memeriksa kesiapan ekstensi PHP vital (`pdo`, `openssl`, `mbstring`, `fileinfo`, `zip`, `curl`).
     * Memindai ketersediaan runtime CLI eksternal (`git`, `composer`, `node`, `npm`, `docker`, `rclone`).
   * **Langkah 2: Identitas & URL Aplikasi**:
     * Konfigurasi nama aplikasi (`APP_NAME`).
     * Deteksi URL server otomatis (`APP_URL`) berdasarkan skema (HTTP/HTTPS) dan host header request saat ini.
     * Pilihan Environment (`production`, `local`, `staging`) dan switch Debug Mode.
   * **Langkah 3: Setup Database Multi-Driver & Uji Koneksi Real-Time**:
     * Mendukung 4 engine database utama:
       * **SQLite**: Zero-configuration default, file database dibuat otomatis jika belum ada.
       * **MySQL / MariaDB**: Konfigurasi Host, Port, Database, Username, Password. Mendeteksi dan otomatis mencoba membuat database jika hak akses mengizinkan.
       * **PostgreSQL**: Konfigurasi lengkap pgsql engine.
       * **SQL Server (sqlsrv)**: Untuk server Windows atau Linux berbasis MSSQL.
     * **Uji Koneksi Real-Time**: Tombol uji live koneksi PDO sebelum melanjutkan. Memberikan umpan balik instan: status koneksi, latensi (ms), dan pesan detail error PDO jika gagal.
     * Eksekusi migrasi skema tabel database (`php artisan migrate --force`) via Virtual Terminal.
   * **Langkah 4: Konfigurasi Akun Administrator Server**:
     * Pembuatan akun Administrator primer server (Nama Lengkap, Alamat Email, dan Kata Sandi).
     * Akun ini memegang otorisasi penuh untuk mengelola seluruh fungsi dan fitur panel server.
   * **Langkah 5: Virtual Terminal Deployment Modal**:
     * Menyediakan konsol bergaya bash terminal (`indotech-installer@server: ~`) interaktif di dalam browser.
     * Menjalankan pipeline instalasi lengkap secara streaming:
       1. Pembuatan storage symlink (`php artisan storage:link`).
       2. Optimasi konfigurasi & route cache (`optimize:clear`).
       3. Pembuatan file lock instalasi (`storage/framework/indotech_installed.lock`) dengan hardware fingerprint server.
     * Dilengkapi bar progress glowing gradient (0% - 100%) dan log streaming real-time.
3. **Penguncian Permanen Pasca Instalasi**:
   * Setelah instalasi berhasil difinalisasi, Installer Wizard **otomatis dikunci dan dihilangkan dari menu navigasi UI**.
   * Akses publik ke rute `/install` ditutup dan otomatis dialihkan ke Dashboard.

---

## 9. Keamanan & Role-Based Access Control (RBAC)

* **Otoritas Administrator Server**:
  * Akun Administrator primer memegang kendali penuh operasional server, situs web, database, rclone storage eksternal, dan terminal.
  * Proteksi keamanan berlapis pada otentikasi dan sesi login.
* **Audit Trail**:
  * Setiap tindakan penting (penghapusan storage, eksekusi terminal, perubahan konfigurasi) dicatat dalam tabel riwayat audit (`security_logs`).
