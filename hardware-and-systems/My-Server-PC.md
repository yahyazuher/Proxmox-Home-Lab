# Bare-Metal Server & Hardware Specifications (My Server PC)

This file documents the physical hardware, networking infrastructure, and the core hypervisor powering my entire Home Lab environment.

## Hardware Specifications
*   **CPU:** AMD Ryzen 5 2600 (6 Cores, 12 Threads) – Handling heavy virtualization workloads, system orchestration, and concurrent processes.
*   **GPU:** NVIDIA GeForce GTX 1650 – Providing hardware acceleration.
*   **RAM:** 16 GB – Allocated dynamically across multiple virtual environments.
*   **Power Supply:** 500W PSU.

## Networking Infrastructure
To ensure stable, high-speed local data transfer between my main workstation and the server nodes, I integrated a dedicated hardware switch:
*   **Network Switch:** TP-Link 5-Port Gigabit(1Gbit) Desktop Switch.
*   **Purpose:** Handles high-bandwidth internal LAN traffic, ensuring seamless local media streaming and zero-latency SSH/Terminal access to internal nodes.

---

## The Core Hypervisor: Proxmox VE

### What is Proxmox VE?
**Proxmox Virtual Environment (PVE)** is a complete, open-source enterprise virtualization platform. It is a Type-1 (bare-metal) hypervisor based on Debian GNU/Linux. It integrates both **KVM** (Kernel-based Virtual Machine) for full hardware virtualization (VMs) and **LXC** (Linux Containers) for lightweight, isolated container environments, all managed through a powerful web-based user interface.

### Deployment in My Lab
To maximize this hardware's potential, I installed Proxmox VE directly on the bare metal. It acts as the virtualization foundation of my entire lab, splitting physical resources into isolated, high-performance environments:
1.  **TrueNAS SCALE VM:** Dedicated to secure network storage and media orchestration.
2.  **Ubuntu Server VM:** Dedicated to web application deployment and hosting production code.
