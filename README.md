# Automated Hybrid Home Lab Infrastructure

Welcome to my Home Lab environment repository! This project documents the complete architecture, deployment, hardware specification, and security configurations of my self-hosted private cloud and production web-hosting platform.

---

## Core Architecture Overview

My environment is engineered around a single bare-metal host running a Type-1 hypervisor, which orchestrates the continuous delivery of internal storage clusters and public-facing production applications.

* **Virtualization Foundation:** Proxmox VE managing resource allocation and system sandboxing.
* **Storage & Data Vault Layer:** TrueNAS SCALE handling automated backups and localized streaming arrays.
* **Web Production Layer:** Isolated Linux (Ubuntu Server) instances powering full-stack live systems.
* **Ingress Network Layer:** Secure Cloudflare Zero Trust Tunnels, routing traffic seamlessly through encrypted outbound connections to bypass provider network blocks.

---

## Repository Navigation & System Documentation

To maintain clean documentation principles, the infrastructure details are modularly segmented. The table below uses explicit formatting to ensure clear navigation across all devices:

<table>
  <thead>
    <tr>
      <th align="left">System Document</th>
      <th align="left">Architecture Component</th>
      <th align="left">Document Purpose & Scope</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap><a href="./hardware-and-systems/My-Server-PC.md"><b>My-Server-PC.md</b></a></td>
      <td nowrap><b>Bare-Metal Hardware & Hypervisor</b></td>
      <td>Outlines the physical hardware specs (CPU/GPU/RAM), internal 1Gbit network switching routing, and the deployment of Proxmox VE.</td>
    </tr>
    <tr>
      <td nowrap><a href="./hardware-and-systems/My-TrueNAS-SCALE.md"><b>My-TrueNAS-SCALE.md</b></a></td>
      <td nowrap><b>Network Attached Storage & Media Automation</b></td>
      <td>Details ZFS storage pools, local high-speed file sharing (SMB/NFS), permission mappings (ACLs), and the containerized automation stack.</td>
    </tr>
    <tr>
      <td nowrap><a href="./hardware-and-systems/My-Ubuntu-Server.md"><b>My-Ubuntu-Server.md</b></a></td>
      <td nowrap><b>Production Environment & Web Apps</b></td>
      <td>Documents the sandboxed headless Linux host serving public web portfolio apps and integrated machine learning models.</td>
    </tr>
  </tbody>
</table>

---

## Hybrid Ingress Network Architecture

This lab implements a dual-layer security model to ensure that production services are accessible globally, while sensitive infrastructure remains strictly private.

### Public Access Layer (Zero Trust)
*Services routed through encrypted **Cloudflare Tunnels** to bypass CGNAT and hide the Home IP.*

* **Production Gate:** [me.yahyaz.dev](https://me.yahyaz.dev) → **Ubuntu Server VM**
* **Medical AI Node:** [hepatiq.site](https://hepatiq.site) → **Ubuntu Server VM**
* **Management Pipeline:** Remote access to **Radarr, Sonarr, and qBittorrent** for on-the-go orchestration.

### Private Infrastructure Layer (LAN Only)
*Strictly isolated services with NO external exposure. Access requires a physical connection to the internal network.*

* **The Backbone:** Accessible only via Internal IP (`192.168.0.10:PORT`).
* **Connection Method:** Requires connection to the **TP-Link 1Gbit Switch** or the local secure Wi-Fi.
* **Secured Services:** * **TrueNAS Dashboard:** Storage management and ZFS administration.
    * **Jellyfin:** High-bandwidth local media streaming.

---
## System Architecture & Virtualization Mapping
```mermaid
graph TD
    %% Title Section
    title[<b>Infrastructure Hierarchy & Service Mapping</b>]
    style title fill:none,stroke:none,color:#e67e22,font-size:20px

    %% Nodes Definition
    Hardware["Bare-Metal Server PC<br/>(Ryzen 5 2600 | 16GB RAM | GTX 1650)"]
    Proxmox["PROXMOX VE<br/>"]
    
    subgraph VMs [Virtual Machines Layer]
        VM1["VM 1: TrueNAS SCALE"]
        VM2["VM 2: Ubuntu Server"]
    end

    subgraph Goals1 [TrueNAS Objectives]
        G1["• ZFS Storage Management<br/>• Private Cloud<br/>• Media Automation"]
    end

    subgraph Goals2 [Ubuntu Objectives]
        G2["• Production Web Hosting<br/>• AI Inference Execution<br/>• Live Deployment"]
    end

    subgraph Domains [Network Access]
        Local["Local Only Access<br/>IP: 192.168.0.xxx"]
        Public["Global Public Access<br/>me.yahyaz.dev | hepatiq.site"]
    end

    %% Connections
    Hardware ==> Proxmox
    Proxmox --> VM1
    Proxmox --> VM2
    
    VM1 --- G1
    VM2 --- G2
    
    G1 --> Local
    G2 --> Public

    %% Styling for clarity
    style Hardware fill:#2d3436,stroke:#dfe6e9,color:#fff
    style Proxmox fill:#e67e22,stroke:#d35400,color:#fff
    style VM1 fill:#2980b9,stroke:#2471a3,color:#fff
    style VM2 fill:#8e44ad,stroke:#713d84,color:#fff
    style Domains fill:#27ae60,stroke:#1e8449,color:#fff
