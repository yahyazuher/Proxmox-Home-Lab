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

<img width="872" height="558" alt="truenas-apps" src="https://github.com/user-attachments/assets/47950446-6150-47b7-8d90-5dbf78f3a930" />


---

## Hybrid Ingress: Remote Automation vs. Strict Local Access

### 1. Remote Access Layer (Cloudflare Tunnels)
I use **cloudflared** to create a secure outbound bridge to my custom domain, but **only** for managing my automation and download services from outside my house. This allows me to safely add new media or check download statuses on the go without opening risky router ports to the public internet:
*   **Exposed Remotely:** Radarr, Sonarr, and qBittorrent interfaces.

### 2. Local-Only Network Layer (High Security)
For everything else, services are entirely locked behind my home router. To access them, a device must be physically connected to my local Wi-Fi or hooked via an Ethernet cable into my **TP-Link 5-Port Gigabit Desktop Switch**. Access is granted strictly using the internal static IP address and the specific port assigned to each application.

*   **Example Local Format:** `http://192.168.0.10:PORT_NUMBER`

---

## Deployed Services & Core Functions

Here is the exact breakdown of the applications running on this node as shown in my TrueNAS dashboard, explaining their individual roles and network accessibility:

#### 🔹 cloudflared
*   **Role:** The Cloudflare Zero Trust tunnel daemon.
*   **Function:** Establishes the secure, encrypted connection to Cloudflare's edge, handling the remote traffic routing specifically for my allowed automation apps.

#### 🔹 radarr
*   **Role:** Automated Movie Manager.
*   **Function:** Monitors and tracks movie releases. It automatically communicates with indexers to find files, pushes them to the download client, and moves them to the permanent ZFS media storage once done.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote requests `example1.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 sonarr
*   **Role:** Automated TV Show Manager.
*   **Function:** Functions exactly like Radarr but is custom-tailored for episodic content, automatically tracking, fetching, and archiving TV series seasons.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote requests `example2.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 qbittorrent
*   **Role:** High-Performance Download Client.
*   **Function:** Receives `torrent` configurations and download commands from Radarr/Sonarr. It handles the raw file downloading and writes temporary files into an isolated dataset.
*   **Access:** Dual-accessible (Securely via Cloudflare Tunnel for remote monitoring `example3.yahyaz.dev`, and via Local IP/Port at home).

#### 🔹 prowlarr
*   **Role:** Indexer Manager.
*   **Function:** Integrates with Radarr and Sonarr to centralize and sync all torrent indexers and trackers. It ensures that whenever I look for media, all search requests across indexers are synchronized in one clean hub.
*   **Access:** **Local Only** (`192.168.0.10:PORT`) – Kept strictly inside the home LAN for backend sync security.

#### 🔹 jellyfin
*   **Role:** Home Media Server.
*   **Function:** Streams my organized movies and shows directly from my TrueNAS ZFS pools to any device (TV, phone, or laptop) with hardware-accelerated transcoding.
*   **Access:** **Local Only** (`192.168.0.10:PORT`) – Streamed privately over the local Wi-Fi and Ethernet switch for maximum bandwidth and security.
*   
---
## Core Architecture & Network Storage Implementation

I deployed TrueNAS SCALE inside a customized virtual environment to act as the central nervous system for my local data and media assets.

### 1. Unified Private Cloud & Smooth Data Storage
I configured optimized, secure ZFS pools to centralize all my critical digital assets. This setup acts as my resilient private cloud, enabling seamless file managment, media streaming, and robust storage expansion. 

### 2. High-Speed Local Network Connectivity (LAN & WLAN)
To ensure zero-latency data transfers and high-performance cross-device accessibility, the storage environment is tightly integrated with my local network hardware:
*   **Wired High-Speed Access (Ethernet):** My main developer workstation is connected via an explicit Ethernet cable directly to my **TP-Link 5-Port Gigabit Desktop Switch**. This guarantees maximum data throughput and ultra-low latency when transferring large coding assets, ISOs, or running heavy system backups to TrueNAS.
*   **Wireless Remote Mobility (Wi-Fi):** Devices like smartphones, tablets, or laptops access the TrueNAS datasets smoothly over the local Wi-Fi network, allowing on-the-go file management and effortless internal media consumption.

### 3. Permission Automation & Multi-App Syncing
*   **ACL & Dataset Mapping:** Deployed strict POSIX/ACL recursive permission inheritance models across specific storage datasets. This successfully fixed complex multi-user and containerized application synchronization conflicts, allowing different services to read/write data seamlessly without breaking underlying security barriers.
*   **Backup and Automation:** Designed specialized directories for secure personal backups and daily system snapshots, creating a safe vault for my programming projects and personal records.
