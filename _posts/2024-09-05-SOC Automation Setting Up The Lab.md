---
title: "SOC Automation Setting Up The Lab"
date: 2024-09-05 20:00:00 +0300
categories: [Projects, SOC Automation, Setting up the Lab]
# tags: [soc, automation, cybersecurity, SOAR, SIEM, Wazuh, TheHive]
---

In this blog post, I'll walk through setting up a Security Operations Center (SOC) automation lab environment. This setup will include spinning up both a Windows 11 VM and an Ubuntu 24.04 VM, installing Wazuh, configuring Sysmon on the Windows endpoint, and setting up TheHive for incident response. I opted for a local setup due to limited resources and to avoid cloud costs.

---

## VM Setup

### Windows 11 VM - Endpoint

To begin, I spun up a Windows 11 VM using Oracle VirtualBox, which will serve as the endpoint in this project. This VM will generate events and forward them to Wazuh for analysis. 

### Ubuntu 24.04 VM - Wazuh and TheHive Host

I also spun up an Ubuntu 24.04 VM, which will host both Wazuh (for XDR and SIEM) and TheHive (for Incident Response). Due to limited resources, I opted to install both services on the same VM rather than using separate instances or a cloud provider.

---

## Sysmon Configuration on Windows 11

For enhanced event logging, I installed **Sysmon** on the Windows 11 VM and configured it using the following Sysmon configuration file from Olaf Hartong's repository: [sysmonconfig.xml](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml).

```bash
sysmon -accepteula -i sysmonconfig.xml
```

This configuration file helps capture detailed and relevant system events that will be sent to Wazuh for monitoring.

---

## Installing Wazuh on Ubuntu 24.04

To install Wazuh, I used the following command to download and execute the official Wazuh installation script:

```bash
curl -sO https://packages.wazuh.com/4.8/wazuh-install.sh && chmod +x ./wazuh-install.sh && sudo ./wazuh-install.sh -a -o -i
```

---

## Installing TheHive

The next step was installing TheHive, an Incident Response Platform, alongside its dependencies: Java, Cassandra, and Elasticsearch.

### Installing Java
```bash
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" | sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

### Installing Cassandra
```bash
wget -qO - https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```
> I created a a jvm.options file under /etc/elasticsearch/jvm.options.d to limit to the JVM Xms and Xms to 2G
{: .prompt-info }

### Installing Elasticsearch
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

### Installing TheHive
```bash
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive
```

I opted to install Java 11 from Amazon Corretto, Cassandra, and Elasticsearch to meet TheHive's prerequisites before proceeding with its installation.

---

## Next Steps

In the upcoming posts, I’ll be covering the configuration of the Wazuh manager to receive events from the Windows 11 endpoint and enrich those events through TheHive for Incident Response. Stay tuned for more detailed instructions on how the pieces fit together!

---

_This post is part of Jad's Cybersecurity Blog._
