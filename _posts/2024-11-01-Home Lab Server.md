---
title: "Home Lab Server"
date: 2024-11-16 08:00:00 +0300
categories: [Projects, Home Lab]
---

In this blog post, I’m thrilled to introduce my new home lab server setup. The server is built on a strong foundation with an **i7 12700K** processor, offering **12 cores and 20 threads**, paired with **32GB of DDR5 RAM** running at **5600 MHz**. This configuration is perfect for running multiple virtual machines (VMs) and testing various cybersecurity tools.

### Why This Build?

I chose the **LGA 1700 socket** for its future upgrade potential. This socket type supports up to the **14th Gen Intel i9 processors**, providing an opportunity to increase the core count and improve performance as my project needs grow. Additionally, the server has extra RAM slots, allowing me to expand the memory up to **256GB**, and additional storage slots for SSDs and HDDs, making it a scalable setup.

- **Processor**: Intel i7 12700K (12 cores, 20 threads)
- **Memory**: 32GB DDR5 RAM (5600 MHz)
- **Storage**: 500GB SSD (with room for future expansion)
- **Virtualization Platform**: Proxmox VE
- **Firewall**: pfSense

This setup is designed with flexibility in mind, enabling me to expand as I go, whether it’s adding more VMs, increasing storage, or upgrading the CPU.

---

## Home Lab Network Topology

Below is the network diagram of my home lab environment, showcasing the VLAN setup, Proxmox hypervisor, and pfSense firewall configuration:

![Home Lab Diagram](/assets/img/Home%20Lab/homelab-diagram.png)

The diagram illustrates how the network is segmented using VLANs and highlights the core components of the setup.

---

## Network Segmentation and VLAN Configuration

To ensure efficient traffic management and enhance security, I’ve segmented the network using VLANs. Here’s a detailed overview of the VLANs I’ve configured:

1. **VLAN 2020 (Main Tools VLAN)**: 
   - Subnet: `192.168.20.0/24`
   - Hosts: Kali Linux, The Hive, Cortex, Wazuh, and Nessus
   - Usage: Main tools for security analysis and incident response.

2. **VLAN 2121 (Active Directory VLAN)**:
   - Subnet: `192.168.30.0/24`
   - Hosts: Active Directory, Windows 10, and Windows 11 VMs
   - Usage: Dedicated for Active Directory and testing Windows environments.

3. **VLAN 2222 (Honeypot VLAN)**:
   - Subnet: `192.168.40.0/24`
   - Hosts: Honeypot VM
   - Usage: Isolated VLAN for honeypots, with internet access only, to capture potential attack traffic.

4. **VLAN 2323 (Vulnerable Machines VLAN)**:
   - Subnet: `192.168.50.0/24`
   - Hosts: Metasploitable, Vulnhub VMs
   - Usage: For penetration testing exercises with intentionally vulnerable VMs.

5. **VLAN 2424 (Containerization VLAN)**:
   - Subnet: `192.168.60.0/24`
   - Hosts: Portainer.io for managing Docker containers
   - Usage: Containerized applications and orchestration.

---

## Proxmox and pfSense Setup

The server uses **Proxmox VE** for virtualization, with pfSense serving as the primary firewall. 
I will go over the configuration in seperate blogs.

---

## Future Plans and Expansion

With the current setup i am planning a versatile set of future projects.

### Upcoming Projects (not limited to):
1. Setting up an advanced honeypot with automated attack analysis.
2. Configuring a secure OpenVPN server for remote access.
3. Deploying a comprehensive SIEM solution using Wazuh and Elastic Stack.
4. Implementing containerized services for automations and development projects

Stay tuned as I document these projects in detail in future blog posts.

---

_This post is part of Jad's Cybersecurity Blog._

