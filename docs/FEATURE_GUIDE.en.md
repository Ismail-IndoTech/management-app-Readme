# Comprehensive Feature & Architecture Guide - IndoTech Server Management

This document provides an in-depth breakdown of the technical architecture, operating mechanics, and real-world features of **IndoTech Server Management & Panel Gateway**.

---

## 1. System Architecture & Philosophy

This application functions as an autonomous **Master Control Gateway** designed to operate across diverse server architectures without requiring root privileges or core operating system modifications:

* **Hosting Panel Bypass**: Delivers an all-in-one administrative interface for routine server tasks (domain management, databases, cron jobs, file manipulation, terminal consoles) without requiring continuous logins to sluggish or restricted hosting panels (cPanel, aaPanel, Webmin, Plesk, etc.).
* **Stateless & Zero-Credential Policy**: Third-party credentials are never hardcoded or persistently tracked in version control. When moved to a fresh host, the system guides users through automated re-integration (cPanel API, Cloudflare API, GitHub deploy keys) to preserve credentials integrity.
* **Self-Adapting Runtime**: Dynamically detects the host environment—Baremetal Dedicated Server, KVM VPS, or CloudLinux Shared Hosting—and selectively adjusts available operations based on server permissions.

---

## 2. Universal Hosting Panel Bypass

The backend service `App\Services\ServerEnvironmentService` dynamically inspects server signatures to identify the active hosting panel:

| Hosting Control Panel | System Detection Signatures | Bypass Capabilities |
|---|---|---|
| **cPanel / WHM** | `/usr/local/cpanel`, binary `/bin/uapi` | Domains, Subdomains, MySQL DBs, Cron Jobs, Email Routing, File Explorer |
| **CyberPanel** | `/usr/local/CyberCP`, port 8090 | DNS Zones, Website vhosts, SSL Management, DBs |
| **aaPanel** | `/www/server/panel`, `/www/server` | Site deployment, MySQL administration, Cron scheduler |
| **Webmin / Virtualmin** | `/etc/webmin`, `/usr/share/webmin` | Server modules, System status, Virtual servers |
| **Plesk** | `/usr/local/psa` | Domain subscriptions, DB instances, Virtual hosts |
| **DirectAdmin** | `/usr/local/directadmin` | Users, Domain pointers, DNS admin |
| **HestiaCP** | `/usr/local/hestia` | Web domains, Mail accounts, Cron tasks |
| **CloudPanel** | `/home/clp`, binary `clpctl` | Sites, Database engines, Security policies |
| **Native Linux Server** | No external hosting panel present | Direct native system utilities & systemd services |

Sidebar navigation labels and badges dynamically reflect the detected host engine (e.g., `cPanel Controll [cPanel]`, `CyberPanel Controll [CyberPanel]`, or `Server Controll [Native Server]`).

---

## 3. Dual-Layer Server Resource Profiler

On shared hosting accounts, standard system commands (e.g. `free -m`, `lscpu`) report the cumulative capacity of the **entire baremetal server**, which does not reflect the isolated quota limits assigned to your user account.

IndoTech Server Management splits resource telemetry into **Two Independent Layers**:

1. **Hardware Server (Full Physical Server)**:
   * **Total CPU Cores & Processor Model**: Read directly from `/proc/cpuinfo`.
   * **Total System RAM & Hardware Swap**: Parsed from `/proc/meminfo`.
   * **Physical Disk Allocation**: Read via root partition statistics `df -h /`.
   * **System Load Average**: Standard 1-min, 5-min, and 15-min load averages.
2. **Shared Account Quotas (Tenant / LVE Account)**:
   * **CloudLinux LVE / cPanel Quota**: Specific disk space allocated to your user (MB/GB used vs account cap).
   * **Inode Limits**: Number of files/inodes consumed versus maximum provider limit.
   * **Monthly Account Bandwidth**: Live bandwidth estimates transmitted across the tenant account.

---

## 4. Real-Time Multi-Framework Project Scanner & APM

The automated scanner (`App\Services\ProjectScannerService`) recursively traverses web root directories (`public_html/`, `/var/www/`, `/home/user/apps/`) to identify hosted software stacks:

### Framework Identification & Versioning
* **Laravel**: Discovers exact framework release from `composer.lock` or `vendor/laravel/framework`.
* **Node.js & TypeScript**: Analyzes `package.json`, Node runtime version, and application frameworks (Express.js, Nest.js, Next.js, Nuxt.js, Vite).
* **Python**: Identifies `requirements.txt`, `Pipfile`, virtual environments, and frameworks (Django, Flask, FastAPI).
* **CodeIgniter**: Identifies CI3 (`system/core/CodeIgniter.php`) and CI4 (`app/Config/App.php`) architectures.
* **WordPress & PHP Native**: Locates `wp-config.php` and retrieves WordPress core versions.

### Per-Project Telemetry & Threat Monitoring
The project detail view provides granular APM data:
* **HTTP Request Metrics**: Request throughput measured per minute and per hour.
* **Bandwidth & Transfer Rates**: Real-time ingress and egress transfer rates.
* **Proactive Security Alerts**:
  * **Brute Force Detection**: Flags recurring bursts of 401/403 status codes targeting administrative or login routes.
  * **DDoS / HTTP Flood Spike**: Alerts administrators when traffic spikes 5x above baseline averages.
* **Log Analyzer**: Real-time inspection of HTTP access logs and error logs directly from the web interface.

---

## 5. Web-Based Virtual Terminal

The virtual terminal component (`App\Livewire\Terminal\Index`) delivers command-line access inside the browser:

* **Native PTY Mode (Interactive Bash)**: Engaged when pseudo-terminals are supported (VPS, Dedicated servers, or SSH-enabled shared hosting).
* **Restricted Shell Simulator**: Smart fallback when PTY or shell spawning is restricted by cPanel host policies (`disable_functions` such as `proc_open`). Safely executes administrative binaries:
  * `git status`, `git pull`
  * `php artisan ...`
  * `composer ...`
  * `npm run build`
  * `rclone ...`
* Includes directory navigation (`cd`), command history (`history`), and screen clearing (`clear`).

---

## 6. Multi-Cloud & External Storage Hub

Powered by a headless **Rclone CLI** binary operating in *User Mode* (`~/.local/bin/rclone`):

* **Supported Storage Providers**:
  1. Google Drive
  2. MEGA
  3. Dropbox
  4. Microsoft OneDrive
  5. Apple iCloud (via WebDAV)
  6. Amazon S3, Cloudflare R2, MinIO
  7. Remote Servers via SFTP & FTP
  8. Removable USB Drives & Local Mounts
* **Core Capabilities**:
  * Real-time connectivity and storage quota probing.
  * **Interactive Cloud File Browser**: Navigate directories, create folders, and delete files directly from the UI.
  * **One-Click Backup Sync**: Synchronize database dumps or project folders to cloud destinations automatically or on demand.

---

## 7. CLI Ecosystem & Docker Container Engine

The system tool manager (`App\Services\SystemToolManagerService`) supervises developer tools:

* **Docker-CLI with Graceful Fallback**:
  1. Stage 1: Attempts installation using `su` (Root) privilege.
  2. Stage 2: If root access is denied (common on shared cPanel accounts), automatically installs Docker-CLI in unprivileged *User Mode*.
* **Docker Daemon Diagnostic Guidance**:
  * If the Docker daemon socket is blocked by host policies, the UI provides clear diagnostic steps without failing or blocking the application.
* **Auxiliary CLI Tools**:
  * Supervise and install `composer`, `node`/`npm`, `gh` (GitHub CLI), and `rclone`.

---

## 8. Dynamic Zero-Credential Installer Wizard & Virtual Terminal Setup

The deployment wizard features an intelligent **Deployment-Aware Lifecycle**:

1. **Automatic Activation Post-Git Clone**:
   * As soon as the repository is cloned to a new server, the `EnsureInstalledMiddleware` checks installation status via `ServerEnvironmentService::isInstalledOnCurrentServer()`.
   * If the host server is unconfigured (missing `.env` or missing hardware fingerprint lock), **all web traffic is seamlessly routed to `/install` without requiring prior login**.
2. **5-Step Interactive Setup Wizard**:
   * **Step 1: Auto-Read Server Integration**:
     * Automatically identifies host Web Server (Apache, Nginx, LiteSpeed, cPanel Host).
     * Inspects operating system architecture and execution privileges (Root mode vs Restricted User mode).
     * Verifies critical PHP extensions (`pdo`, `openssl`, `mbstring`, `fileinfo`, `zip`, `curl`).
     * Scans for external CLI runtime binaries (`git`, `composer`, `node`, `npm`, `docker`, `rclone`).
   * **Step 2: Application Identity & URL**:
     * Configure application name (`APP_NAME`).
     * Auto-detects server URL (`APP_URL`) from the incoming request protocol and host headers.
     * Select deployment environment (`production`, `local`, `staging`) and debug mode.
   * **Step 3: Multi-Driver Database Setup & Real-Time Connection Testing**:
     * Supports 4 database engines:
       * **SQLite**: Zero-configuration default; database file is generated automatically if missing.
       * **MySQL / MariaDB**: Host, Port, Database Name, Username, and Password configuration with automated database creation fallback when permissions allow.
       * **PostgreSQL**: Full configuration for PostgreSQL engines.
       * **SQL Server (sqlsrv)**: For Microsoft SQL Server environments on Windows or Linux.
     * **Real-Time Live Connection Testing**: Dedicated test button runs a live PDO handshake before proceeding, returning connection status, latency (ms), and exact PDO error diagnostics if failed.
     * Executes table schema migrations (`php artisan migrate --force`) via Virtual Terminal.
   * **Step 4: Administrator Account Setup**:
     * Setup the primary server Administrator account (Full Name, Email Address, and Password).
     * This Administrator account possesses full authority to govern all server resources and features.
   * **Step 5: Virtual Terminal Deployment Modal**:
     * In-browser Linux bash terminal simulator (`indotech-installer@server: ~`).
     * Executes the full deployment pipeline with live streaming log output:
       1. Creating public storage symlink (`php artisan storage:link`).
       2. Optimizing configuration & route caches (`optimize:clear`).
       3. Writing installation lock (`storage/framework/indotech_installed.lock`) tied to server hardware fingerprint.
     * Real-time progress bar with glowing cyan-blue gradient from 0% to 100%.
3. **Permanent Lock Post-Installation**:
   * Once finalized, the Installer Wizard **is permanently locked and hidden from navigation menus**.
   * Public requests to `/install` are redirected straight to the main Dashboard.

---

## 9. Security & Role-Based Access Control (RBAC)

* **Server Administrator Authority**:
  * The primary Administrator account holds complete operational control over server monitoring, web projects, databases, external rclone storage, and the virtual terminal.
  * Multi-layer authentication security and protected session tokens.
* **Audit Trails**:
  * Every destructive or administrative operation is logged with timestamp, user ID, IP address, and payload parameters (`security_logs`).
