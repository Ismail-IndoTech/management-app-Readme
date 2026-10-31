# Multi-Cloud & External Storage Integration Guide (IndoTech Management)

The **External Storage** module in IndoTech Management allows seamless integration of multi-cloud storage providers, remote servers (SFTP/FTP), and physical USB devices to expand server storage capacity and automate off-site backup workflows.

This module is driven by a headless **Rclone CLI** binary operating in *User Mode* (`~/.local/bin/rclone`), ensuring reliable operation across both VPS instances and unprivileged cPanel Shared Hosting accounts.

---

## 1. Supported Storage Providers

| Provider | Rclone Type | Key Configuration Parameters |
|---|---|---|
| **Google Drive** | `drive` | Client ID, Client Secret, OAuth Token / Refresh Token, Root Folder ID |
| **MEGA** | `mega` | Username (Email) and Password |
| **Dropbox** | `dropbox` | App Key, App Secret, OAuth Token |
| **Microsoft OneDrive** | `onedrive` | Client ID, Client Secret, Tenant ID / Drive ID |
| **Apple iCloud** | `webdav` | Apple ID, App-Specific Password, iCloud WebDAV URL |
| **S3 / Cloudflare R2 / MinIO** | `s3` | Access Key, Secret Key, S3 Endpoint, Bucket, Region |
| **Remote Server (SFTP / FTP)**| `sftp` / `ftp`| Host, Port, Username, SSH Private Key / Password |
| **USB Devices & Local Disks** | `local` | Mount Path (e.g. `/media/usb`, `/mnt/backup`, `/home/...`) |

---

## 2. Configuration Walkthrough by Provider

### A. Google Drive
1. **Create Project & OAuth Credentials in Google Cloud**:
   * Navigate to **Google Cloud Console** (https://console.cloud.google.com).
   * Enable **Google Drive API** under *APIs & Services* -> *Library*.
   * In *OAuth Consent Screen*, choose *External*, enter app details, and add the `.../auth/drive` scope.
   * Under *Credentials*, create an **OAuth 2.0 Client ID** (type: *Web Application*).
   * In the **Authorized redirect URIs** section, add your server's callback URL:
     `https://indotech-management.it-app.id/storage/oauth/google/callback`
   * Copy the **Client ID** (`xxx.apps.googleusercontent.com`) and **Client Secret** (`your-client-secret`).

2. **Crucial Difference (Cause of: "token expired and there's no refresh token")**:
   > ⚠️ **Critical Notice**: The JSON downloaded from Google Cloud Console (`client_secret_xxx.json`) is the Client ID & Secret configuration file, **not an authorized OAuth token**. An OAuth token is only generated after an account owner consents to access. If only the Client Secret JSON is placed into the token field, Rclone cannot locate a `refresh_token` and fails to authenticate.

3. **Connecting Google Drive in IndoTech (3 Approaches)**:
   * **Approach 1: 1-Click Automated OAuth (Highly Recommended)**:
     1. Enter your Client ID and Client Secret, then save the storage.
     2. Click **🔐 Login & Authorize Google Drive**.
     3. Complete Google's authorization screen in your browser.
     4. Google redirects back to your server and installs the token (complete with `refresh_token`) automatically!
   * **Approach 2: Manual Authorization Code Exchange**:
     If authorizing from a different device, open the consent URL, copy the generated code (`4/0A...`), paste it into *"Exchange Authorization Code"*, and click the exchange button.
   * **Approach 3: Google Service Account (Permanent - Recommended for Servers)**:
     In Google Cloud Console, create a *Service Account*, download its JSON Key, paste the JSON into IndoTech's Token field, and share the target Google Drive directory with the Service Account email. No browser intervention or token refresh is required.

### B. MEGA
1. Simply enter your MEGA account credentials (Email and Password).
2. The Rclone engine encrypts the password securely into the local user configuration (`~/.config/rclone/rclone.conf`).
3. Take advantage of MEGA's 20GB+ free storage allocation for automated database backups.

### C. Dropbox
1. Navigate to the **Dropbox App Console** (https://www.dropbox.com/developers/apps).
2. Create an app with *Full Dropbox* or *App Folder* access scope.
3. Generate an Access Token / Refresh Token.
4. Input the **App Key**, **App Secret**, and **Token** into the IndoTech Storage modal.

### D. Microsoft OneDrive
1. Register an application in the **Azure Active Directory / Microsoft Entra ID** portal.
2. Grant *Files.ReadWrite.All* and *offline_access* API permissions.
3. Retrieve your **Client ID** and generate a **Client Secret**.
4. Save credentials in IndoTech Storage to connect OneDrive Personal or OneDrive for Business.

### E. Apple iCloud (via WebDAV)
1. Log in to https://appleid.apple.com with your Apple ID.
2. In the *Security* section, generate an **App-Specific Password**.
3. Supply your Apple ID as username and the app password as secret.

### F. Object Storage S3 / Cloudflare R2 / MinIO
1. **Cloudflare R2**:
   * Endpoint: `https://<account_id>.r2.cloudflarestorage.com`
   * Enter your **R2 Access Key ID** and **Secret Access Key**.
   * Specify the **Bucket** name.
2. **Amazon AWS S3**:
   * Enter AWS Access Key, Secret Key, Region (e.g., `ap-southeast-1`), and Bucket name.
3. **MinIO / Self-Hosted S3**:
   * Enter your custom MinIO S3 endpoint URL along with bucket credentials.

### G. Remote Server (SFTP / SSH)
1. Enter the target server's Host IP / Domain and Port (default `22`).
2. Specify the remote Username.
3. Select authentication method: Password or SSH Private Key.
4. Ideal for server-to-server offsite backup synchronization.

### H. USB Devices & Local Storage
1. The platform automatically monitors attached USB flash drives and block devices mounted at `/media`, `/mnt`, or `/run/media`.
2. Click **Detect USB Devices** to scan mounted drives.
3. Select an attached volume for instantaneous local backups.

---

## 3. Storage Management & Dedicated Cloud Features

### 1. Dedicated Cloud Storage Pool (Server Storage Expansion)
* You can designate any high-capacity cloud account (e.g., **Google Drive 5TB** or **MEGA**) as **Dedicated Storage** by checking `Set as Dedicated Storage`.
* 4 distinct dedicated storage roles are supported:
  1. **Dedicated Backup Vault**: Serves as the primary off-site repository for database dumps, project snapshots, and disaster recovery archives.
  2. **App Media & Storage Expansion**: Functions as an external storage pool for public application assets and media files.
  3. **Project Codebase Archive**: Dedicated vault specifically for production application archives and source code snapshots.
  4. **Dedicated Storage Pool**: Aggregates cloud capacity directly into server system metrics (Hybrid Storage Pool).
* **Hybrid Telemetry Dashboard**: On the central dashboard (**Tier 3: Dedicated Cloud & Extended Storage Pool**), connected dedicated cloud capacity is dynamically calculated and combined with the physical server disk (`Hybrid Total Storage Pool`).

### 2. Animated Real-Time Progress HUD & Live Log Stream
* All data operations (Database Dump, Project Tarballing, Directory Sync, or File Upload) feature an animated **Real-Time Progress HUD Modal**.
* **HUD Capabilities:**
  * Status indicator (*Running*, *Completed*, *Failed*).
  * Glowing animated progress bar updated in real time via Livewire polling (`wire:poll.750ms`).
  * Active File & Telemetry Ticker: Displays the currently processed archive name and transfer size.
  * **Live Activity Terminal Drawer**: Streams Rclone execution logs line-by-line so administrators can audit transfer progress in real time without guessing.

### 3. One-Click Operations (+ DB Backup & + Project)
* **+ DB Backup**: One-click trigger automatically dumps the local database (SQLite or MySQL), compresses it to `.sql.gz`, and uploads it to `backups/database/` on the remote cloud storage.
* **+ Project (Project Backup)**: Select any application or server project. The system generates a compressed `.tar.gz` archive (intelligently omitting bulky directories such as `node_modules`, `vendor`, `.git`, and runtime log files) and uploads it with full progress tracking.
* **Project Details Integration**: On the project management page (`/projects/{id}`), a dedicated **Backup to Cloud** action button is embedded directly into the header toolbar and quick actions panel.

### 4. Interactive Cloud File Browser, Upload & Remote File Editor
* **Browse Files**: Explore directories inside Google Drive or external remotes instantaneously.
* **Direct Browser File Upload**: Upload local documents, tarballs, or scripts directly to any remote folder.
* **Remote File Editor (+ New File & Edit)**: Create and edit text files, configurations, or shell scripts on remote drives without downloading and re-uploading.
* **Download & Delete**: Download files directly to your device or remove obsolete archives safely.

---

## 4. Security & Credential Encryption

* All tokens, secrets, and private keys are encrypted in the MySQL/SQLite database using **AES-256-CBC** via Laravel's native encrypted model casts (`$casts = ['credentials' => 'encrypted:json']`).
* The local Rclone configuration file (`~/.config/rclone/rclone.conf`) is generated with strict `chmod 600` permissions, preventing read access by other tenants on shared servers.
* Passwords for remote drivers such as MEGA or SFTP are obscured using Rclone's internal obscuring algorithm before writing to disk.
