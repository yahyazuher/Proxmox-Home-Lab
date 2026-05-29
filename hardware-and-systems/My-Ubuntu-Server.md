# Ubuntu Server (Hosted on Proxmox)

This file documents my high-performance Linux production environment, running as an isolated Virtual Machine (VM) inside my Proxmox VE cluster. This node is dedicated to hosting live, production-ready applications and showcasing my deployed projects.

---

## Environment Purpose
I utilize **Ubuntu Server** for its renowned stability and resource efficiency. Running headlessly (without a GUI), this VM is optimized to provide maximum CPU and RAM overhead for hosting web services and running AI inference models.

By separating this node from the storage layer (TrueNAS), I ensure a secure, sandboxed environment for all public-facing applications.

---

## Deployed Production Projects

This server is the live host for my professional web presence and specialized software solutions:

### 1. Personal Portfolio & Resume
* **Domain:** [me.yahyaz.dev](https://me.yahyaz.dev)
* **Description:** My central professional hub, hosting my digital CV, contact information, and a showcase of my technical journey. It is served directly from this Ubuntu node and secured via Cloudflare.

### 2. HepatiQ: AI Liver Disease Diagnosis System
* **Domain:** [hepatiq.site](https://hepatiq.site)
* **Project Documentation:** [AI-Liver-Disease-Diagnosis-System](https://github.com/yahyazuher/AI-Liver-Disease-Diagnosis-System)
* **Description:** My medical graduation project. This is a sophisticated AI-driven platform designed to assist in diagnosing liver diseases using advanced machine learning algorithms. 
* **Implementation:** The Ubuntu server handles the web backend and the execution of Python-based predictive models, making the tool accessible to users through a clean, web-based interface.

---

## Networking & Secure Access

To make these projects accessible to the world while keeping my home network invisible, I implemented the following:

### Cloudflare Zero Trust Integration
Since my home network is behind a CGNAT (Carrier Grade Network Address Translation), I use **Cloudflare Tunnels (cloudflared)**. 
* **Function:** It creates a secure, encrypted outbound tunnel that maps `me.yahyaz.dev` and `hepatiq.site` directly to the internal ports on this Ubuntu VM.
* **Security:** This method allows me to host my projects publicly without opening any ports on my router, effectively hiding my home IP address and protecting the server from external attacks.

### Local Management
* **Maintenance:** System administration is performed via SSH over the local **TP-Link 1Gbit Switch**, ensuring ultra-fast and secure terminal access for updates and deployments.
