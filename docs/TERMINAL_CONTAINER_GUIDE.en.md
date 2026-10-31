# Virtual Terminal & Docker CLI User Mode Guide (IndoTech Management)

This document outlines the operational mechanics and technical architecture of the **Virtual Terminal** and **Docker Container / CLI Tools** across diverse server environments, specifically VPS and cPanel Shared Hosting.

---

## 1. Web-Based Virtual Terminal

The Virtual Terminal in IndoTech Management empowers administrators to execute system commands directly within their web browser, eliminating the need for standalone SSH clients (e.g., PuTTY or local terminal sessions).

### Dual-Engine Architecture
The terminal is built on two execution engines:

1. **Native PTY / Interactive Bash (Standard Mode)**:
   * Leverages the Linux PTY (Pseudo-Terminal) subsystem whenever permitted by the host OS.
   * Full interactive support, ANSI color rendering, and process control signals (`Ctrl+C`).
   * Available on: VPS instances, Dedicated Servers, or Shared Hosting accounts with unrestricted SSH access.

2. **Restricted Shell Simulator (Fallback Mode)**:
   * Autonomously activated when raw PTY allocation is blocked by host security policies (such as PHP `proc_open` restrictions or strict `disable_functions`).
   * **Supported Built-in Commands**:
     * Filesystem & Navigation: `cd`, `pwd`, `ls`, `cat`, `mkdir`, `rm`
     * Git Operations: `git status`, `git pull`, `git log`, `git checkout`
     * PHP & Laravel: `php artisan ...`, `php -v`, `composer ...`
     * Node.js & NPM: `node -v`, `npm -v`, `npm run build`
     * Cloud & Remote: `rclone ...`
     * Terminal State: `history`, `clear`, `help`

---

## 2. Docker Containers & CLI Tools: Root vs User Mode

### Intelligent Installation Pipeline
When an Administrator triggers the Docker CLI installation from the *Tools* menu, the system executes a cascading verification pipeline:

1. **Stage 1 (Root Mode / `su`)**:
   * Inspects availability of `su` or `sudo` binaries.
   * If root authorization is granted, Docker CLI is installed globally to `/usr/local/bin/docker` or `/usr/bin/docker`.
2. **Stage 2 (User Mode / Non-Root Fallback)**:
   * If `su` privileges are denied (standard on cPanel shared hosting accounts), installation redirects automatically to the local user directory:
     `~/.local/bin/docker`
   * The binary is downloaded directly from official static Docker binary releases without root escalation.

---

## 3. Explaining: *"Docker CLI not found or restricted by server policy"*

If you encounter this status message while managing containers, here is why it occurs:

### Primary Causes on Shared Hosting
1. **Docker CLI Binary Exists, but Docker Daemon is Missing**:
   * On shared cPanel hosts, individual users do not have access to the system-level Docker daemon (`dockerd`) or `/var/run/docker.sock` socket, because managing kernel cgroups requires root access.
2. **Jailed Shells & CloudLinux CageFS**:
   * CloudLinux shared hosting sandboxes user binaries inside virtual jail environments (*CageFS*), isolating them from root sockets.

### Recommended Resolutions
* **For Shared Hosting Tenants**:
  * You can still leverage Docker CLI for inspecting local archive images, and rely on developer tools that run 100% in User Mode (such as **Rclone**, **Composer**, and **Node.js**).
  * Full container orchestration (Start, Stop, Expose Ports) should be performed on a VPS or Dedicated Server.
* **For VPS / Dedicated Server Administrators**:
  * Verify that the Docker service is active:
    ```bash
    sudo systemctl enable --now docker
    sudo usermod -aG docker $USER
    ```

---

## 4. Auxiliary CLI Developer Tools

IndoTech Management provides supervised management for core developer binaries:

* **Composer**: Maintained at `~/.local/bin/composer` for PHP project dependencies.
* **Node.js & NPM**: Monitored dynamically for frontend asset compilation.
* **GitHub CLI (`gh`)**: Enables automated interaction with private repositories and releases.
* **Rclone**: Cloud storage sync engine running completely in User Mode.
