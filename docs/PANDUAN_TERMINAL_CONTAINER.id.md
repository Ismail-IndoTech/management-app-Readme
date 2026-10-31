# Panduan Virtual Terminal & Docker CLI User Mode (IndoTech Management)

Dokumen ini menjelaskan pengoperasian dan arsitektur teknis dari fitur **Virtual Terminal** dan **Docker Container / CLI Tools** pada lingkungan server heterogen, khususnya server VPS dan cPanel Shared Hosting.

---

## 1. Virtual Terminal Web-Based

Virtual Terminal pada IndoTech Management memungkinkan administrator menjalankan perintah sistem langsung dari antarmuka peramban tanpa harus membuka aplikasi SSH terpisah (seperti PuTTY atau Terminal lokal).

### Arsitektur Dual-Engine
Terminal didesain dengan dua mesin eksekusi:

1. **Native PTY / Interactive Bash (Mode Standar)**:
   * Menggunakan modul PTY (Pseudo-Terminal) Linux jika diizinkan oleh sistem operasi.
   * Mendukung input interaktif penuh, pemanggilan teks warna ANSI, serta sinyal kontrol proses (`Ctrl+C`).
   * Tersedia pada: VPS (KVM/OpenVZ), Dedicated Server, atau Shared Hosting yang memiliki izin SSH terbuka.

2. **Restricted Shell Simulator (Mode Fallback)**:
   * Diaktifkan otomatis ketika terminal diblokir oleh kebijakan keamanan server atau hosting provider (misalnya fitur `proc_open` dibatasi atau PHP berjalan dalam `disable_functions` yang ketat).
   * **Daftar Perintah yang Didukung**:
     * Navigasi & File: `cd`, `pwd`, `ls`, `cat`, `mkdir`, `rm`
     * Git: `git status`, `git pull`, `git log`, `git checkout`
     * PHP & Laravel: `php artisan ...`, `php -v`, `composer ...`
     * Node.js & NPM: `node -v`, `npm -v`, `npm run build`
     * Cloud & Remote: `rclone ...`
     * Terminal State: `history`, `clear`, `help`

---

## 2. Docker Container & CLI Tools: Root vs User Mode

### Alur Instalasi Cerdas
Saat Administrator mengklik tombol instalasi Docker CLI pada menu *Perkakas*, sistem menjalankan logika bertingkat:

1. **Urutan 1 (Root Mode / `su`)**:
   * Sistem mendeteksi ketersediaan utilitas `su` atau `sudo`.
   * Jika kata sandi root atau hak istimewa sudo tersedia, Docker CLI dipasang langsung ke path global sistem (`/usr/local/bin/docker` atau `/usr/bin/docker`).
2. **Urutan 2 (User Mode / Fallback Non-Root)**:
   * Jika server menolak perintah `su` (kasus umum pada akun shared hosting cPanel), instalasi otomatis beralih ke direktori pengguna lokal:
     `~/.local/bin/docker`
   * Biner diekstrak langsung dari rilis statis resmi Docker tanpa memerlukan akses administratif root.

---

## 3. Penjelasan Pesan: *"Docker CLI not found or restricted by server policy"*

Jika Anda melihat pesan ini saat mengelola kontainer di server, berikut penjelasan penyebab dan cara kerjanya:

### Penyebab Utama pada Akun Shared Hosting
1. **Docker CLI Terpasang, Namun Daemon Tidak Ada**:
   * Pada akun cPanel shared hosting, pengguna tidak memiliki akses ke Docker daemon (`dockerd`) atau socket `/var/run/docker.sock` karena daemon membutuhkan hak akses root di tingkat kernel host.
2. **Jailed Shell / CloudLinux CageFS**:
   * Lingkungan CloudLinux sering mengisolasi eksekusi biner pengguna di dalam sangkar virtual (*CageFS*), sehingga soket sistem internal di luar sangkar tidak dapat diakses.

### Solusi & Rekomendasi
* **Untuk Pengguna Shared Hosting**:
  * Anda tetap dapat menggunakan Docker CLI untuk memeriksa arsitektur, membaca file image lokal jika diizinkan, atau menggunakan alat CLI lain yang berjalan 100% di User Mode (seperti **Rclone**, **Composer**, dan **Node.js**).
  * Manajemen kontainer penuh (Start, Stop, Expose Port) disarankan dijalankan pada VPS atau Dedicated Server.
* **Untuk Pengguna VPS / Dedicated Server**:
  * Pastikan Docker daemon berjalan dengan perintah:
    ```bash
    sudo systemctl enable --now docker
    sudo usermod -aG docker $USER
    ```

---

## 4. Ekosistem Perkakas CLI Lainnya

IndoTech Management mendukung pengelolaan perkakas pengembang tambahan:

* **Composer**: Terpasang di `~/.local/bin/composer` untuk pengelolaan dependensi PHP proyek.
* **Node.js & NPM**: Terdeteksi secara dinamis untuk menjalankan tugas kompilasi aset frontend.
* **GitHub CLI (`gh`)**: Memfasilitasi clone repositori privat dan sinkronisasi rilis kode langsung.
* **Rclone**: Mesin penggerak cloud storage yang berjalan independen di User Mode.
