---
title: "SOC Automation Project"
date: 2024-09-01 20:00:00 +0300
categories: [Projects, SOC Automation, Intro and Architecture]
# tags: [soc, automation, cybersecurity, SOAR, SIEM]
---

# SOC Automation Project: Gaining Hands-On Experience

In this blog series, I'll be documenting my journey in gaining hands-on experience with Security Operations Center (SOC) automation. The focus will be on integrating various cybersecurity tools to streamline alert management and response actions. The key components of this project include the XDR and SIEM Wazuh for extended detection and response, Shuffle as the SOAR (Security Orchestration, Automation, and Response) tool, and The Hive as the Incident Response Platform.

---

## Architecture Overview

### Flow of Operations

1. **Event Generation**: The process begins with the Win 11 VM generating events, which are sent to the Wazuh Manager.

2. **Event Reception**: The Wazuh Manager receives these events and processes them.

3. **Alerting**: Once the Wazuh Manager processes the events, alerts are sent to Shuffle for further action.

4. **IOC Enrichment**: Shuffle then enriches these alerts with Indicators of Compromise (IOCs).

5. **Alert Forwarding**: The enriched alerts are forwarded to The Hive for logging and further investigation.

6. **Email Notifications**: Both Shuffle and The Hive are configured to send email notifications, ensuring that SOC analysts are kept informed of any critical alerts.

7. **SOC Analyst Interaction**: The SOC analyst, equipped with this information, can send and receive emails and interact with the system for further analysis and response.

8. **Response Actions**: Depending on the severity and nature of the alert, the system is designed to send response actions back to Wazuh Manager or directly to affected systems, closing the loop of the automation process.

---

## Flowchart

Below is the flowchart illustrating the architecture of the SOC automation process:

![SOC Automation Flowchart](/assets/img/SOC%20Automation/flowchart.png)

This flowchart provides a visual representation of how the different components in the system interact with each other to automate the SOC operations.

---

## Next Steps

In the upcoming posts, I'll dive deeper into the configuration of each component, showing how they are set up and how they interact to achieve a fully automated SOC environment. Stay tuned!

---

_This post is part of Jad's Cybersecurity Blog._
