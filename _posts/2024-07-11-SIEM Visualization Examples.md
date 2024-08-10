---
title: "SIEM Visualization Examples"
date: 2024-07-11 18:00:00 +0300
categories: [HTB SOC Analyst, Security Monitoring & SIEM Fundamentals, Alert Triaging]
# tags: [siem, soc, cybersecurity, elastic stack, kibana]
---

# SIEM Visualization Examples

In this guide, we explored various SIEM visualizations using Kibana, focusing on creating dashboards that monitor critical security events. Each section provided a detailed walkthrough for setting up a specific visualization.

## SIEM Visualization Example 1: Failed Logon Attempts (All Users)

This visualization tracks failed logon attempts across all users within the network. It is essential for identifying potential brute-force attacks or unauthorized access attempts.

### Steps:

1. **Create a New Dashboard**: Start by creating a new dashboard in Kibana.
2. **Set Up the Visualization**:
   - Select the "Table" visualization type.
   - Apply a filter to include only event IDs matching 4625 (Failed logon attempts).
   - Use the `windows*` index pattern to capture relevant data.
   - Configure the "Rows" to display the `user.name.keyword`, `host.hostname.keyword`, and the count of failed attempts.

3. **Refine the Visualization**:
   - Exclude unnecessary computer accounts.
   - Sort and refine columns based on the SOC manager’s suggestions.
   - Save and name the visualization.

### Screenshot:
![Failed Logon Attempts Visualization](/assets/img/SIEM%20VIsualization/failed-logon-attempts.png)

---

## SIEM Visualization Example 2: Failed Logon Attempts (Disabled Users)

This visualization monitors failed logon attempts specifically against disabled user accounts, highlighting potential security breaches where attackers attempt to use disabled accounts.

### Steps:

1. **Create a New Visualization**:
   - Filter the data to include only event IDs matching 4625, and apply the `SubStatus` field filter to `0xC0000072` (logon with disabled user).
   - Use the `windows*` index pattern.
   - Configure the "Rows" to display the `user.name.keyword`, `host.hostname.keyword`, and the count of failed attempts for disabled users.

2. **Enhance the Table**:
   - Add a column to show the specific machine where the failed attempt occurred.
   - Save the visualization.

### Screenshot:
![Failed Logon Attempts for Disabled Users Visualization](/assets/img/SIEM%20VIsualization/failed-logon-disabled-users.png)

---

## SIEM Visualization Example 3: Successful RDP Logon Related to Service Accounts

This visualization focuses on monitoring successful RDP logons associated with service accounts, which typically should not be used for RDP access in corporate environments.

### Steps:

1. **Set Up the Visualization**:
   - Use event ID 4624 (successful logon) and filter by `winlog.logon.type` for RemoteInteractive logons.
   - Apply a filter for service accounts (all accounts starting with `svc-`).
   - Configure the "Rows" to display the service account, the machine where the logon occurred, the initiating IP, and the count of successful logons.

2. **Final Adjustments**:
   - Ensure the visualization is capturing all relevant service account logons.
   - Save the visualization and add it to the dashboard.

### Screenshot:
![Successful RDP Logon for Service Accounts Visualization](/assets/img/SIEM%20VIsualization/successful-rdp-logon-service-accounts.png)

---

## SIEM Visualization Example 4: Users Added or Removed from a Local Group

This visualization monitors user additions or removals from the local "Administrators" group, focusing on a specific timeframe from March 5th, 2023 to the present.

### Steps:

1. **Create the Visualization**:
   - Filter by event IDs 4732 (user added) and 4733 (user removed) with additional filtering for the "Administrators" group.
   - Use the `windows*` index pattern.
   - Configure "Rows" to include the user added/removed, the group name, the action performed, and the host where the action occurred.

2. **Timeframe Configuration**:
   - Narrow the scope of the visualization to the specified timeframe.
   - Save the changes and add the visualization to the dashboard.

### Screenshot:
![User Group Changes Visualization](/assets/img/SIEM%20VIsualization/user-group-changes.png)

## Key Points

- **Data Filtering and Indexing**: Gained expertise in filtering event data using specific indices and fields, crucial for narrowing down large datasets to focus on relevant security events.

- **Visualization Customization**: Learned how to customize visualizations to monitor specific security events, such as failed logon attempts and service account usage, by configuring table rows, applying filters, and refining the visual output.

- **Use of SIEM Tools**: Developed a strong understanding of how to use Kibana’s visualization tools within SIEM systems to provide real-time insights into network activity.

- **Critical Thinking and Problem-Solving**: Enhanced problem-solving skills by refining visualizations based on specific criteria, such as excluding certain accounts or focusing on a specific timeframe.

## Conclusion

These exercises provided valuable hands-on experience in developing SIEM visualizations using Kibana. I learned how to effectively filter and present data to ensure that critical security events are clearly highlighted for analysis. The skills acquired through this process, including data filtering, visualization customization, and the use of SIEM tools, are essential for effective security monitoring in a SOC environment. This experience has significantly strengthened my ability to interpret and respond to security data in real-time.

## Next Steps

Next up : Alert Triaging and the section skills assessment
---

_This post is part of Jad's Cybersecurity Blog._
