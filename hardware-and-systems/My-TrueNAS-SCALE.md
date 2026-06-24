# TrueNAS SCALE (Hosted on Proxmox)

This file documents the network-attached storage (NAS) architecture and automated data management workflows implemented inside a dedicated Virtual Machine (VM) on my Proxmox cluster.

---

## What is TrueNAS SCALE?
**TrueNAS SCALE** is an enterprise-grade, open-source Network Attached Storage (NAS) operating system based on Debian Linux. It provides unified storage management, high-performance file sharing, and robust data protection. It is widely recognized for its ability to turn standard hardware or virtual machines into powerful, scalable storage appliances.

### How it Works (The Power of ZFS)
TrueNAS SCALE relies on the advanced **ZFS (Zettabyte File System)**. ZFS is not just a filesystem; it is a logical volume manager designed to provide high data integrity. It achieves this through features like:
*   **Copy-on-Write (CoW):** Protects data from corruption during sudden power losses by writing data to new blocks before updating references.
*   **Self-Healing:** Continuously verifies data against cryptographic checksums to automatically detect and repair silent data corruption (bit rot).
*   **Advanced Snapshots:** Allows near-instantaneous point-in-time backups without affecting system performance.

---
---

## Application Architecture & Local Networking

To keep my lab secure, I implement a strict hybrid access model. Only my automation stack is exposed via Cloudflare for remote management, while the rest of my core applications remain completely private, accessible only within my home network.

### TrueNAS Applications Overview
Here is a snapshot of my active ecosystem running seamlessly inside TrueNAS SCALE:

<img width="697" height="722" alt="image" src="https://github.com/user-attachments/assets/5a0fcaf2-a889-45c6-9369-fef3f207e164" />

---

## Hybrid Ingress: Remote Automation vs. Strict Local Access

### 1. Remote Access Layer (Cloudflare Tunnels)
I use **cloudflared** to create a secure outbound bridge to my custom domain, but **only** for managing my automation and download services from outside my house. This allows me to safely add new media or check download statuses on the go without opening risky router ports to the public internet:
*   **Exposed Remotely:** Radarr, Sonarr, qBittorrent, Uptime Kuma, Vaultwarden, and Immich.

### 2. Local-Only Network Layer (High Security)
For everything else, services are entirely locked behind my home router. To access them, a device must be physically connected to my local Wi-Fi or hooked via an Ethernet cable into my **TP-Link 5-Port Gigabit Desktop Switch**. Access is granted strictly using the internal static IP address and the specific port assigned to each application.

*   **Example Local Format:** `http://192.168.0.10:PORT_NUMBER`

---

## Deployed Services & Core Functions

Here is the exact breakdown of the applications running on this node as shown in my TrueNAS dashboard, explaining their individual roles and network accessibility:

#### 🔹 affine
*   **Role:** Collaborative Workspace & Knowledge Base.
*   **Function:** A self-hosted workspace for organizing notes, documentation, and personal project management.
*   **Access:** **Local Only** (`192.168.0.10:PORT`) – Kept strictly inside the home LAN.

#### 🔹 bazarr
*   **Role:** Automated Subtitle Manager.
*   **Function:** Integrates seamlessly with Radarr and Sonarr to automatically search, download, and manage subtitles in preferred languages for all media.
*   **Access:** **Local Only** (`192.168.0.10:PORT`).

#### 🔹 cloudflared
*   **Role:** Cloudflare Zero Trust Tunnel Daemon.
*   **Function:** Establishes the secure, encrypted connection to Cloudflare's edge, handling the remote traffic routing specifically for my allowed automation apps and dashboards.

#### 🔹 flaresolverr
*   **Role:** Proxy Server.
*   **Function:** Operates in the background alongside the "Arr" suite of applications to bypass Cloudflare anti-bot protection when interacting with trackers.
*   **Access:** **Local Only** (Backend system).

#### 🔹 homepage
*   **Role:** Centralized Application Dashboard.
*   **Function:** A highly customizable hub organized via a `services.yaml` file that displays icons, active statuses, and links for all self-hosted home lab services.
*   **Access:** **Local Only** (`192.168.0.10:PORT`).

#### 🔹 immich
*   **Role:** High-Performance Photo & Video Backup.
*   **Function:** A self-hosted alternative to cloud photo storage. Excellent for large-scale data migrations directly over the local IP to ensure maximum transfer speeds for thousands of media files.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel on `---.yahyaz.dev` for on-the-go backups, and via Local IP/Port at home).

#### 🔹 jellyfin
*   **Role:** Home Media Server.
*   **Function:** Streams my organized movies and shows directly from my TrueNAS ZFS pools to any device (TV, phone, or laptop) with hardware-accelerated transcoding.
*   **Access:** **Local Only** (`192.168.0.10:PORT`) – Streamed privately over the local Wi-Fi and Ethernet switch for maximum bandwidth and security.

#### 🔹 netdata
*   **Role:** Real-Time System Monitoring.
*   **Function:** Provides high-resolution metrics and visual dashboards to track TrueNAS and Proxmox hardware performance (CPU, RAM, Disk I/O, and Network).
*   **Access:** **Local Only** (`192.168.0.10:PORT`).

#### 🔹 prowlarr
*   **Role:** Indexer Manager.
*   **Function:** Integrates with Radarr and Sonarr to centralize and sync all torrent indexers and trackers. It ensures that whenever I look for media, all search requests across indexers are synchronized in one clean hub.
*   **Access:** **Local Only** (`192.168.0.10:PORT`) – Kept strictly inside the home LAN for backend sync security.

#### 🔹 qbittorrent
*   **Role:** High-Performance Download Client.
*   **Function:** Receives `torrent` configurations and download commands from Radarr/Sonarr. It handles the raw file downloading and writes temporary files into an isolated dataset.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote monitoring `---.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 radarr
*   **Role:** Automated Movie Manager.
*   **Function:** Monitors and tracks movie releases. It automatically communicates with indexers to find files, pushes them to the download client, and moves them to the permanent ZFS media storage once done.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote requests `---.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 sonarr
*   **Role:** Automated TV Show Manager.
*   **Function:** Functions exactly like Radarr but is custom-tailored for episodic content, automatically tracking, fetching, and archiving TV series seasons.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote requests `---.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 uptime-kuma
*   **Role:** Centralized Uptime Monitor.
*   **Function:** Continuously monitors all services exposed through Cloudflare Tunnels linked to my custom domain, alongside internal TrueNAS and Proxmox services. It is natively integrated with a custom Telegram bot to dispatch immediate push notifications, allowing me to effortlessly track the real-time up/down status of all my external domain addresses.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel on `----.yahyaz.dev`, and via Local IP/Port).
*   
#### 🔹 vaultwarden
*   **Role:** Encrypted Password Manager.
*   **Function:** A lightweight Bitwarden-compatible server providing secure credential synchronization and autofill capabilities across mobile apps and browser extensions.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel, and via Local IP/Port at home).

---

## Core Architecture & Network Storage Implementation

I deployed TrueNAS SCALE inside a customized virtual environment to act as the central nervous system for my local data and media assets.

### 1. Unified Private Cloud & Smooth Data Storage
I configured optimized, secure ZFS pools to centralize all my critical digital assets. This setup acts as my resilient private cloud, enabling seamless file management, media streaming, and robust storage expansion. 

### 2. High-Speed Local Network Connectivity (LAN & WLAN)
To ensure zero-latency data transfers and high-performance cross-device accessibility, the storage environment is tightly integrated with my local network hardware:
*   **Wired High-Speed Access (Ethernet):** My main developer workstation is connected via an explicit Ethernet cable directly to my **TP-Link 5-Port Gigabit Desktop Switch**. This guarantees maximum data throughput and ultra-low latency when transferring large coding assets, ISOs, or running heavy system backups to TrueNAS.
*   **Wireless Remote Mobility (Wi-Fi):** Devices like smartphones, tablets, or laptops access the TrueNAS datasets smoothly over the local Wi-Fi network, allowing on-the-go file management and effortless internal media consumption.

### 3. Permission Automation & Multi-App Syncing
*   **ACL & Dataset Mapping:** Deployed strict POSIX/ACL recursive permission inheritance models across specific storage datasets. This successfully fixed complex multi-user and containerized application synchronization conflicts, allowing different services to read/write data seamlessly without breaking underlying security barriers.
*   **Backup and Automation:** Designed specialized directories for secure personal backups and daily system snapshots, creating a safe vault for my programming projects and personal records.
