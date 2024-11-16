---
title: "Setting Up Proxmox"
date: 2024-11-01 10:00:00 +0300
categories: [Projects, Home Lab, Proxmox]
---

In this blog post, I'll provide a comprehensive step-by-step guide to installing Proxmox on a dedicated home server and configuring it for use as a hypervisor. This includes creating a bootable USB, installing Proxmox on the host, configuring network bridges, setting up isolated WAN/LAN interfaces for pfSense, and preparing iptables rules for VPN and internet access.

---

## Creating a Bootable USB for Proxmox Installation

To begin, I needed to create a bootable USB drive with the Proxmox ISO:

1. **Download the Proxmox ISO**:
   - I visited the [Proxmox Downloads page](https://www.proxmox.com/en/downloads) and downloaded the latest Proxmox VE ISO.

2. **Flash the ISO to a USB Drive**:
   - I used **Rufus** on Windows to create a bootable USB:
     - Open Rufus and select the USB drive.
     - Click **Select** and choose the Proxmox ISO file.
     - Set the **Partition Scheme** to **MBR** (for legacy BIOS) or **GPT** (for UEFI).
     - Click **Start** to begin the flashing process.

3. **Verify the USB Drive**:
   - After flashing, I safely removed the USB and connected it to my home server for installation.

---

## Installing Proxmox on the Host Server

With the bootable USB ready, I proceeded with the installation on my home server.

### Step-by-Step Installation:

1. **Boot from USB**:
   - I powered on the server and entered the BIOS/UEFI menu to set the USB drive as the first boot device.
   - After saving the changes, the system booted into the Proxmox installer.

2. **Select Install Proxmox VE**:
   - I chose the default install option and proceeded to the disk selection screen.

3. **Select Target Disk and Filesystem**:
   - I selected the SSD as the target disk.
   - Chose the **ext4 filesystem** for simplicity and better performance with virtualization.

4. **Configure the System**:
   - Set the root password and email address for administrative notifications.
   - Configured the network settings:
     - **Hostname**: `prxmx.local`
     - **Management IP**: `170.10.100.21/24`
     - **Gateway**: `170.10.100.214`
     - **DNS Server**: `8.8.8.8`

5. **Complete Installation and Reboot**:
   - The installer completed successfully, and I removed the USB drive before rebooting the server.

---

## Configuring Network Bridges in Proxmox

In my setup, I created three network bridges:
1. **vmbr0**: Bridged to the physical network interface (`eth0`), used for Proxmox management and internet access.
2. **vmbr1**: A virtual bridge for the pfSense LAN interface, not bridged to any physical network.
3. **vmbr2**: A virtual bridge for the pfSense WAN interface, also not bridged to any physical network.

This configuration allows for isolated VLANs and network segmentation.

### Configuring Network Bridges

In the Proxmox web interface:
1. Go to **Datacenter > Node > Network**.
2. Added the following bridge configurations:

```bash
auto vmbr0
iface vmbr0 inet static
    address 170.10.100.21/24
    gateway 170.10.100.214
    bridge-ports eth0
    bridge-stp off
    bridge-fd 0

auto vmbr1
iface vmbr1 inet manual
    bridge-ports none
    bridge-stp off
    bridge-fd 0

auto vmbr2
iface vmbr2 inet manual
    bridge-ports none
    bridge-stp off
    bridge-fd 0
```

This setup ensures that **vmbr1** and **vmbr2** are isolated and can be used for pfSense firewall configurations.

---

## Configuring iptables for VPN and Internet Access

I set up iptables rules on Proxmox to handle traffic routing and NAT for the VPN server and ensure internet access for the VMs.

### iptables Configuration

```bash
# Allow incoming VPN traffic on vmbr0 (public interface) on port 39990
-A INPUT -i vmbr0 -p udp -m udp --dport 39990 -j ACCEPT

# Allow traffic forwarding from vmbr2 (WAN interface) to vmbr0
-A FORWARD -i vmbr2 -o vmbr0 -j ACCEPT

# Allow established and related connections from vmbr0 to vmbr2
-A FORWARD -i vmbr0 -o vmbr2 -m state --state RELATED,ESTABLISHED -j ACCEPT

# Allow VPN traffic forwarding from vmbr0 to vmbr2 on port 39990
-A FORWARD -i vmbr0 -o vmbr2 -p udp -m udp --dport 39990 -j ACCEPT

# Forward VPN traffic to pfSense WAN (10.0.0.1:39990)
-A PREROUTING -i vmbr0 -p udp -m udp --dport 39990 -j DNAT --to-destination 10.0.0.1:39990

# Enable NAT for outgoing traffic on vmbr0 (internet access)
-A POSTROUTING -o vmbr0 -j MASQUERADE

# Enable NAT for outgoing traffic on vmbr2 (pfSense WAN interface)
-A POSTROUTING -o vmbr2 -j MASQUERADE
```

### Saving iptables Rules

I installed `iptables-persistent` to ensure these rules persist across reboots:

```bash
apt update
apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
```

This configuration prepares the Proxmox server for VPN traffic and handles NAT for internet access.

---

## Creating the First Virtual Machine

With Proxmox and the network setup complete, I created my first VM:

1. Clicked **Create VM** in the Proxmox web interface.
2. Configured the VM with 4GB RAM, 2 CPU cores, and 20GB disk space.
3. Attached the network interface to **vmbr1** (LAN interface for pfSense).

This VM setup will be used for further configurations, including setting up pfSense.

## Next Steps
Now that I have Proxmox setup I will be creating a new VM to setup the pfsense firewall.

---

_This post is part of Jad's Cybersecurity Blog._

