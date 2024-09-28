---
title: "SOC Automation - Configuration of Wazuh and TheHive"
date: 2024-09-10 20:00:00 +0300
categories: [Projects, SOC Automation, Setting up the Lab]
---


In this blog post, i'll walk through setting up a home lab for SOC (Security Operations Center) automation. This part focuses on configuring and getting both Hive (Incident Response Platform) and Wazuh (SIEM and XDR) servers up and running. 

---

## Important Note

>For this setup, I kept all configurations to `localhost` to simplify communication between the services. Additionally, I set the **network mode** in my Oracle VirtualBox VMs to **Bridged Adapter** so that they could get assigned an IP on the same subnet. This allowed me to run both Wazuh and TheHive on the same server without cloud deployment, and I configured UFW on the Linux server to allow communication from the Windows endpoint.
{: .prompt-info }

---

### UFW Configuration

I opened the necessary ports on the Linux server hosting Wazuh and TheHive so that they can communicate with the Windows endpoint. Here's how I allowed traffic from my Windows host to the Linux server:

```bash
# Allow access to Wazuh dashboard (port 443)
sudo ufw allow from 170.10.100.0/24 to any port 443 proto tcp

# Allow access to The Hive dashboard (port 9000)
sudo ufw allow from 170.10.100.0/24 to any port 9000 proto tcp

# Allow access to Wazuh manager (port 1514)
sudo ufw allow from 170.10.100.0/24 to any port 1514 proto tcp

# Allow access to Wazuh API (port 1515)
sudo ufw allow from 170.10.100.0/24 to any port 1515 proto tcp

# Allow Wazuh agent registration (port 50000)
sudo ufw allow from 170.10.100.0/24 to any port 50000 proto tcp
```

---

## Configuring Hive Server

### Cassandra Configuration

Hive relies on Cassandra as its database system. Here's a step-by-step process for configuring it:

1. **Access Cassandra Configuration**: Open Cassandra's configuration file by typing:
    ```bash
    nano /etc/cassandra/cassandra.yaml
    ```

2. **Edit Cluster Name**: Modify the cluster name from "Test Cluster" to something relevant, like "soc-automation".

3. **Set Listen and RPC Addresses**: Update the listen and RPC addresses with the public IP of your Hive instance (kept it as local host).

4. **Update Seed Address**: Replace the seed address from the default `127.0.0.1` to the public IP of Hive (kept it as is).

5. **Restart Cassandra**: Use the following commands to stop and start Cassandra:
    ```bash
    sudo systemctl stop cassandra
    sudo systemctl start cassandra
    ```

### Elasticsearch Configuration

Elasticsearch manages data indexing for Hive:

1. **Edit Elasticsearch Config**: Open the Elasticsearch configuration file:
    ```bash
    nano /etc/elasticsearch/elasticsearch.yml
    ```

2. **Modify Cluster Name**: Change the cluster name to "soc-automation" and adjust network settings to reflect the public IP. (kept it as localhost)

3. **Start Elasticsearch**: Start and enable the service with:
    ```bash
    sudo systemctl start elasticsearch
    sudo systemctl enable elasticsearch
    ```

### Setting Up The Hive

Before configuring The Hive:

1. **Ensure Directory Permissions**: Update the file permissions for The Hive's working directory:
    ```bash
    sudo chown -R hive:hive /opt/thehive
    ```

2. **Edit Application Configuration**: Open the Hive configuration file and update the database, index, and storage settings with your public IP (kept it as local host) and cluster name.

3. **Start The Hive**: Finally, start The Hive and enable the service:
    ```bash
    sudo systemctl start thehive
    sudo systemctl enable thehive
    ```

---

## Configuring Wazuh Server

### Accessing the Wazuh Dashboard

To begin Wazuh's configuration:

1. **Login to the Dashboard**: Use the administrative credentials from part 2 to access Wazuh.

### Adding a Windows Agent

To connect your Windows endpoint to Wazuh:

1. **Select Windows Agent**: In the dashboard, click on "Add agent" and select Windows.

2. **Enter Server Details**: Enter the Wazuh server's public IP (kept it as localhost) and assign a name to the agent (e.g., soc-automation).

3. **Copy and Run Command**: Copy the generated command and run it on your Windows machine in an administrative PowerShell window.


### End Result

![SOC Automation Wazuh Dashboard](/assets/img/SOCAutomation/wazuh-dashboard.png)
---


---

## Next Steps

Now that both Hive and Wazuh are up and running, the next step is to generate security events and configure alerts in Wazuh. 

---

_This post is part of Jad's Cybersecurity Blog._
