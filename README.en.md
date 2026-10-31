> 📚 **Documentation:** [Full ID/EN index](DOCS.md) · [English index](DOCS.en.md)

<div align="center">

<img src="docs/Logo.png" alt="IndoTech Server Management" width="128" height="128">

# IndoTech Server Management & Panel Gateway


### Centralized Server Control Gateway, Hosting Panel Bypass, Multi-Cloud Storage & Dynamic Project Discovery.

**A modern master control solution for managing server infrastructure, bypassing traditional hosting panels (cPanel, CyberPanel, aaPanel, Webmin, Plesk, DirectAdmin, HestiaCP, CloudPanel, or Native Linux Hosts), monitoring dual-layer telemetry, integrating external cloud storage, and inspecting multi-framework applications in real-time.**

[![Version](https://img.shields.io/badge/version-1.0.0-FF2D20?style=flat-square)](#)
[![License: MIT](https://img.shields.io/badge/license-MIT-0EA5E9?style=flat-square)](LICENSE)
[![PHP](https://img.shields.io/badge/PHP-8.2%2B-777BB4?style=flat-square&logo=php)](#)
[![Laravel](https://img.shields.io/badge/Laravel-12.x-FF2D20?style=flat-square&logo=laravel)](#)
[![Livewire](https://img.shields.io/badge/Livewire-3.x-FB70A9?style=flat-square&logo=livewire)](#)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.x-38B2AC?style=flat-square&logo=tailwind-css)](#)
[![Made in Indonesia](https://img.shields.io/badge/made%20in%20Indonesia-FF2D20?style=flat-square)](https://github.com/Ismail-IndoTech)

**No manual CLI hassle. Autonomous server management, containers, multi-cloud storage, and virtual terminals directly through a modern Web UI.**

<div align="center">

<a href="README.md">Bahasa Indonesia</a> · **[English](README.en.md)**

</div>

</div>

---

## What's Included Inside

<div align="center">

| | | | | |
|:---:|:---:|:---:|:---:|:---:|
| 🎛️<br>**Universal Panel Bypass**<br>cPanel, aaPanel, Webmin | 📊<br>**Dual-Layer Resources**<br>Full Server vs Shared Account | 🔍<br>**Multi-Framework Scan**<br>Laravel, Node, Python, CI | ☁️<br>**Multi-Cloud Storage**<br>Drive, Dropbox, MEGA, S3 | 💻<br>**Web Terminal**<br>PTY & Restricted Simulator |
| 🐳<br>**Docker & CLI Tools**<br>Root & User Mode Support | 🛡️<br>**APM & Security Metrics**<br>DDoS & BruteForce Probe | 🚀<br>**Zero-Credential Wizard**<br>Deployment Auto-Lock | 🌐<br>**Cloudflare & GitHub**<br>Dynamic Re-Integration | 👥<br>**Administrator Protection**<br>Layered RBAC Security |

</div>

---

## 📖 System Overview & Architecture

**IndoTech Server Management & Panel Gateway** was engineered to solve the operational friction of heterogeneous server environments, particularly the severe constraints imposed by shared hosting accounts. When deployed in restricted environments (such as shared cPanel hosting without root privileges), the platform autonomously adapts:

1. **Single Pane of Glass Control**: Acts as an all-in-one gateway to perform server-level operations, DNS routing, domain management, cron tasks, and log aggregation without repeatedly navigating sluggish legacy hosting panels.
2. **Stateless & Zero-Credential Architecture**: Sensitive credentials are never hardcoded or stored persistently in code repositories. When deployed across new servers (Dedicated, VPS, or Shared Hosting), the system dynamically orchestrates initial setup and enables seamless re-integration with Cloudflare, GitHub, and Cloud Storage.
3. **Dual-Layer Resource Profiling**: Transparantly reports both actual physical hardware metrics (Total Host Cores, System RAM, Physical Block Storage) and isolated shared account resource caps (CloudLinux LVE quotas, account inode limits, user disk consumption).
4. **Framework-Agnostic Project Discovery**: Recursively scans server directories to identify active frameworks (Laravel, Node.js, TypeScript, Python Django/Flask, CodeIgniter, WordPress) while gathering granular HTTP request metrics, bandwidth transfer rates, and abnormal traffic spikes (DDoS / Brute Force).

---

## 🚀 Key Real-World Features

### 1. 🎛️ Universal Hosting Panel Bypass
* Autonomously detects the underlying host control panel: **cPanel / WHM**, **CyberPanel**, **aaPanel**, **Webmin / Virtualmin**, **Plesk**, **DirectAdmin**, **HestiaCP**, **CloudPanel**, or **Native Linux Server**.
* Dynamic sidebar navigation title and badge update automatically to match the detected control panel.
* Direct bypass management for domains, subdomains, email routing, databases, and cron jobs directly from the dashboard.

### 2. 📊 Dual-Layer Resource Telemetry (Hardware vs Shared Account)
* **Full Server Metrics**: Real-time readouts of host CPU core usage, total RAM allocation, physical storage volumes, server uptime, and system load averages.
* **Shared Account Quota**: On shared hosting / CloudLinux systems, accurately identifies the specific tenant's disk allocation (MB/GB used vs quota) and file inode consumption.

### 3. 🔍 Real-Time Multi-Framework Discovery & APM
* Automatically identifies hosted projects without manual registration:
  * **Laravel**: Detects framework release versions from `composer.lock` / `composer.json`.
  * **Node.js & TypeScript**: Recognizes runtime versions and frameworks (Express, Nest, Next.js, Nuxt, Vite).
  * **Python**: Detects Django, Flask, FastAPI stacks, and Python runtime versions.
  * **CodeIgniter**: Recognizes CI3 and CI4 architectures.
* **Per-Project Telemetry**: Tracks request counts, transfer rates, bandwidth utilization, real-time error logs, and proactive anomaly alerts (DDoS surges and Brute Force attempts).

### 4. ☁️ Multi-Cloud & External Storage Hub (Rclone Powered)
* Driven by a headless **Rclone CLI** engine installed in *User Mode* (runs out-of-the-box on shared hosting without requiring root access).
* Supports leading cloud storage providers:
  * **Google Drive**
  * **MEGA**
  * **Dropbox**
  * **Microsoft OneDrive**
  * **iCloud** (via WebDAV)
  * **Object Storage S3 / Cloudflare R2 / MinIO**
  * **Remote Servers via SFTP & FTP**
  * **Physical USB / Removable Media & Local Mounts**
* Includes an interactive Cloud File Explorer, live quota testing, and one-click database & project backup synchronization.

### 5. 💻 Web-Based Virtual Terminal Dual-Engine
* **Native PTY / Interactive Bash**: Utilizes native Linux pseudo-terminals when shell execution permissions are available on VPS/Dedicated environments.
* **Restricted Shell Simulator**: Smart fallback when terminal access is disabled by cPanel or hosting policies. Safely executes common administrative binaries (Composer, Git, Artisan, NPM, Rclone) with persistent command histories and working directory navigation.

### 6. 🐳 Docker Container & CLI Tools Manager
* **Intelligent Docker-CLI Installation**: Prioritizes `su` (Root) mode, and gracefully falls back to unprivileged *User Mode* on restrictive shared hosts.
* **CLI Ecosystem**: On-demand installation and monitoring for: `docker`, `composer`, `node` / `npm`, `gh` (GitHub CLI), and `rclone`.
* Manage Docker container lifecycles (Run, Stop, Restart, Inspect, View Logs).

### 7. 🚀 Dynamic Zero-Credential Installer Wizard
* **True Deployment-Aware Lifecycle**: Installer wizard activates only when the application is freshly deployed (missing `.env`, `/vendor`, build manifests, or application encryption keys).
* Once deployed and verified on the server, **the installer automatically locks and vanishes from the UI navigation**, redirecting `/install` straight to the main dashboard.
* If migrated to a new server, the wizard automatically re-engages for zero-downtime reconfiguration.

### 8. 🛡️ Strict RBAC & Administrator Account Security
* Structured multi-level access control to preserve server integrity and operational safety.
* Strong protection of administrative credentials and extensive audit logging.
* Complete Audit Log trails document every mutating administrative action.

### 9. 🎨 Consistent UI Themes & Dual-Language
* **Persistent Themes**: Seamless toggle between Dark Mode, Light Mode, and System Default without visual flash.
* **Dual Language**: Instant switcher for Bahasa Indonesia 🇮🇩 and English 🇬🇧 with clean national flag buttons.

---

## 🛠️ System Requirements & Quick Start

### Minimum Requirements
* **OS**: Linux (Ubuntu, Debian, AlmaLinux, RockyLinux, CloudLinux) or macOS
* **Web Server**: Nginx, Apache, or LiteSpeed
* **PHP**: 8.2 or higher (PHP 8.3 / 8.4 recommended)
* **PHP Extensions**: `bcmath`, `curl`, `fileinfo`, `mbstring`, `openssl`, `pdo_mysql` / `pdo_sqlite`, `tokenizer`, `xml`, `zip`
* **Composer**: v2.x
* **Node.js**: v18+ & NPM (for asset compilation)

### Deployment Steps on a Fresh Server (Automated Setup Wizard)

```bash
# 1. Clone repository to your server

git clone https://github.com/Ismail-IndoTech/IndoTech_APP.git
cd IndoTech_APP/Management-APP

# 2. Install backend dependencies & build frontend assets

composer install --no-dev --optimize-autoloader
npm install && npm run build
```

Following the commands above, **simply open your domain or server IP in your browser**:
1. The platform automatically detects the uninstalled status and redirects directly to the **Installer Wizard (`/install`)**.
2. **Step 1: System & Web Server Detection**: Automatically detects host Web Server (Apache, Nginx, LiteSpeed, cPanel), OS architecture, privileges (Root vs User mode), and PHP requirements. Click Next to launch the Virtual Terminal for initializing `.env` and dependencies.
3. **Step 2: App Identity Configuration**: Configure `APP_NAME`, `APP_URL` (auto-detected from your server host), environment mode (`production`/`local`), and default language. Saved via Virtual Terminal.
4. **Step 3: Multi-Driver Database Setup**: Choose SQLite (Zero-config default), MySQL/MariaDB, PostgreSQL, or SQL Server with **Real-Time Live Database Connection Testing**. Click Next to execute table schema migrations via Virtual Terminal.
5. **Step 4: Administrator Account Setup**: Configure the server Administrator account (Full Name, Email Address, and Password) used to log in and manage all server resources.
6. **Step 5: System Deployment & Finalization**: Click *Start Server Deployment* to watch the Virtual Terminal finalize deployment (storage symlinks, optimizing caches, and locking the installer with the hardware server fingerprint).
7. Upon completion, the installer locks permanently and the *Enter Management Dashboard* button is ready to use!

---

## 📚 Comprehensive Documentation

Detailed documentation guides for specific subsystems are available in the [`docs/`](docs/) directory:

* 📖 **[Comprehensive Feature & Architecture Guide (English)](docs/FEATURE_GUIDE.en.md)**
* 📖 **[Panduan Lengkap Fitur & Arsitektur (Bahasa Indonesia)](docs/PANDUAN_FITUR.id.md)**
* ☁️ **[Multi-Cloud & External Storage Guide (English)](docs/EXTERNAL_STORAGE_GUIDE.en.md)**
* ☁️ **[Panduan Integrasi Multi-Cloud & Storage Eksternal (Bahasa Indonesia)](docs/PANDUAN_STORAGE_EXTERNAL.id.md)**
* 💻 **[Virtual Terminal & Docker CLI User Mode Guide (English)](docs/TERMINAL_CONTAINER_GUIDE.en.md)**
* 💻 **[Panduan Virtual Terminal & Docker CLI User Mode (Bahasa Indonesia)](docs/PANDUAN_TERMINAL_CONTAINER.id.md)**

---

## 👥 Role-Based Access Control (RBAC)

This platform provides strict and secure role boundaries:

- **Server Administrator**:
  - Primary server management identity governing server telemetry, project inspection, external cloud storage, containers, and daily operations.
  - Full authority to configure server domains, backups, and network parameters.
- **Operator / Viewer**:
  - Read-only access for infrastructure monitoring, project health status, and audit trails.

---

## Access & License

This repository is **my own work**, **Mochamad Ismail Soleh** (Ismail-IndoTech), and is specifically licensed under the **[MIT License](LICENSE)**.

To obtain access to the **private source repository** for this project, contact me as the **Repository Creator** via WhatsApp using the number registered to my DANA account: **08121600058** — [wa.me/628121600058](https://wa.me/628121600058). Access is granted by agreement.
## 💖 Contribution & Sponsorship

If this platform enhances your IT operations, DevOps workflows, or server management efficiency, consider supporting the project as an official contributor or **Special Sponsor**.  
*(Official sponsors will have their company logos prominently featured in this repository).*

Support continuous maintenance, research into hosting bypass vectors, and open-source module development by donating via:

<div align="center">

**DANA — 08121600058** (a.n. Mochamad Ismail Soleh)

<img src="docs/qris.jpg" alt="QRIS DANA IndoTech" width="280">

*Scan with any DANA, BCA, Mandiri, GoPay, OVO, or standard QRIS mobile banking application.*

</div>

All contributions directly fund server research infrastructure, testing licenses, and open-source tool development by IndoTech. If you or your organization would like to discuss partnership or corporate sponsorship, please reach out to us. Thank you for your support!

---

## 📄 License

This software is licensed under the open-source [MIT License](LICENSE).  
Copyright © 2026 **Mochamad Ismail Soleh (Ismail.IndoTech)**.

<div align="center">

Made with ❤️ by [**Ismail.IndoTech**](https://it-app.id) · [it-app.id](https://it-app.id)

</div>