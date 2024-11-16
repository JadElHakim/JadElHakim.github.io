---
title: "Setting Up OpenVPN on pfSense"
date: 2024-11-02 10:00:00 +0300
categories: [Projects, Home Lab, pfSense, OpenVPN]
---

In this blog post, I’ll provide a detailed walkthrough of setting up OpenVPN on pfSense for secure remote access to my home lab environment. This includes creating the Certificate Authority (CA), generating certificates, configuring the OpenVPN server, assigning the OpenVPN interface, and setting up the necessary firewall rules.

---

## Creating Certificates and Setting Up the Certificate Authority

### Step 1: Create the Certificate Authority (CA)
To establish a secure VPN connection, the first step is to create a Certificate Authority in pfSense:

1. Go to **System > Cert Manager > CAs**.
2. Click **Add** to create a new Certificate Authority.
   - **Descriptive Name**: `pfsense-ca`
   - **Method**: Create an internal Certificate Authority
   - **Key Length**: 2048 bits
   - **Digest Algorithm**: SHA256
   - **Lifetime**: 3650 days (10 years)
   - **Common Name**: `pfsense-ca`

3. Click **Save**.

> The Certificate Authority (`pfsense-ca`) is used to issue and validate the server and client certificates for OpenVPN.

### Step 2: Create the Server Certificate
1. Go to **System > Cert Manager > Certificates** and click **Add**.
2. Set the following parameters:
   - **Method**: Create an internal certificate
   - **Descriptive Name**: `ovpn-server-cert`
   - **Certificate Authority**: `pfsense-ca` (created in the previous step)
   - **Key Length**: 2048 bits
   - **Digest Algorithm**: SHA256
   - **Certificate Type**: Server Certificate
   - **Common Name**: `ovpn-server`

3. Click **Save**.

### Step 3: Create the Client Certificate
1. Go to **System > Cert Manager > Certificates** and click **Add**.
2. Set the following parameters:
   - **Method**: Create an internal certificate
   - **Descriptive Name**: `ovpn-client-cert`
   - **Certificate Authority**: `pfsense-ca`
   - **Key Length**: 2048 bits
   - **Digest Algorithm**: SHA256
   - **Certificate Type**: User Certificate
   - **Common Name**: `ovpn-client`

3. Click **Save**.

> With the server and client certificates created, I proceeded to configure the OpenVPN server.

---

## Configuring the OpenVPN Server

### Step 1: OpenVPN Server Setup
1. Go to **VPN > OpenVPN > Servers** and click **Add**.
2. Set the following parameters:
   - **Server Mode**: Remote Access (User Auth)
   - **Protocol**: UDP
   - **Device Mode**: tun
   - **Interface**: WAN (`10.0.0.1`)
   - **Local Port**: `39990`
   - **TLS Authentication**: Enable
   - **Peer Certificate Authority**: `pfsense-ca`
   - **Server Certificate**: `ovpn-server-cert`
   - **DH Parameter Length**: 2048 bits
   - **Encryption Algorithm**: AES-256-CBC
   - **Auth Digest Algorithm**: SHA256

3. **Tunnel Settings**:
   - **Tunnel Network**: `10.8.0.0/24` (IP range assigned to VPN clients)
   - **Local Network**: `192.168.1.0/24` (LAN network accessible to VPN clients)
   - **Concurrent Connections**: 10

4. Click **Save** and **Apply Changes**.

### Step 2: Assign OpenVPN Interface
1. Go to **Interfaces > Assignments**.
2. Click **Add** to create a new interface for OpenVPN.
   - **Interface**: `ovpns1` (OpenVPN server interface)
   - **Description**: `OPT1VPN`
3. Enable the interface and click **Save**.

> Assigning an interface allows us to create specific firewall rules for the OpenVPN traffic.

---

## Configuring Firewall Rules for OpenVPN

To allow VPN clients to access the LAN and internet, I set up the following firewall rules:

### WAN Interface Rule:
1. Go to **Firewall > Rules > WAN**.
2. Add a rule:
   - **Action**: Pass
   - **Protocol**: UDP
   - **Source**: Any
   - **Destination Port**: `39990`
   - **Description**: Allow OpenVPN traffic on port 39990
3. Click **Save** and **Apply Changes**.

### OpenVPN Interface Rule:
1. Go to **Firewall > Rules > OPT1VPN**.
2. Add a rule to allow traffic from VPN clients to the LAN network:
   - **Action**: Pass
   - **Protocol**: Any
   - **Source**: `10.8.0.0/24` (VPN client network)
   - **Destination**: `192.168.1.0/24` (LAN network)
   - **Description**: Allow VPN clients to access LAN
3. Click **Save** and **Apply Changes**.

---

## Exporting the OpenVPN Client Configuration

I used the **OpenVPN Client Export Utility** in pfSense to generate the client configuration file:

1. Go to **VPN > OpenVPN > Client Export**.
2. Select the **Remote Access Server** (`ovpn-server`).
3. Click **Export Configuration** to download the `.ovpn` file.

The `.ovpn` file contains all necessary settings, including the server address, port, and certificates.

---

## Testing the VPN Connection

I imported the `.ovpn` file into the OpenVPN client on my laptop:

1. Opened the OpenVPN client and imported the configuration file.
2. Connected to the VPN server:
   - Successfully received an IP from the VPN tunnel network (`10.8.0.0/24`).
   - Verified access to the LAN network (`192.168.1.0/24`) by pinging devices.

> The VPN connection was established successfully, providing secure remote access to my home lab.

---

## Conclusion

By setting up OpenVPN on pfSense with detailed certificate management, interface assignment, and firewall rule configuration, I achieved a secure remote access solution for my home lab. This configuration ensures that all traffic from VPN clients is encrypted and routed through pfSense, providing robust security and access control.

## Next Steps
Now that my home server is fully configured with Proxmox, pfSense, and OpenVPN, and the software-defined network (SDN) is set up properly, I’m ready to dive into new projects. This includes setting up vulnerable machines for testing, deploying honeypots, implementing automation tasks, creating a separate VLAN for containerized applications (e.g., running tools like Nessus in Docker containers), and integrating IDS/IPS solutions for enhanced network security.

---
_This post is part of Jad's Cybersecurity Blog._
