# Automated SOC Pipeline Using Wazuh, Shuffle, and TheHive
![Status](https://img.shields.io/badge/Project-Complete-brightgreen)
![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-0266C8?logo=wazuh&logoColor=white)
![TheHive](https://img.shields.io/badge/TheHive-Case%20Management-F9A825?logo=thehive&logoColor=white)
![Shuffle](https://img.shields.io/badge/Shuffle-SOAR-7B61FF?logo=shuffle&logoColor=white)

*A SOC-focused security lab demonstrating automated threat detection, intelligence enrichment, and incident response orchestration.*

<sub>by: Brandon Chaney</sub>

## Overview

Modern Security Operations Centers (SOCs) rely on both detection and automation to rapidly identify and respond to security threats. This project demonstrates the design and implementation of an end-to-end detection and response pipeline that combines endpoint telemetry, custom threat detection, automated enrichment, and incident management.

> Using Sysmon, Wazuh, Shuffle, VirusTotal, and TheHive, in this lab, I've simulated a Mimikatz attack that generates endpoint telemetry which is collected and analyzed against a custom detection rule, enriched with external threat intelligence, converted into an investigation case, and automatically delivered to a security analyst for review.

## Objectives
- [x] Build an end-to-end SOC detection and response pipeline.
- [x] Collect endpoint telemetry using Sysmon and Wazuh.
- [x] Detect Mimikatz activity with a custom Wazuh rule.
- [x] Automate alert enrichment using VirusTotal.
- [x] Create investigation cases in TheHive.
- [x] Notify analysts through an automated Shuffle workflow.

## Environment Architecture

### Lab Components

| Component | Concept | Description |
|-----------|---------|-------------|
| Windows Endpoint | ![Endpoint](https://img.shields.io/badge/Attack_Target-0078D4?style=for-the-badge&logo=windows&logoColor=white) | Simulated credential dumping with Mimikatz. |
| Sysmon | ![Sysmon](https://img.shields.io/badge/Telemetry-blue?style=for-the-badge) | Captured detailed endpoint telemetry. |
| Wazuh Agent | ![Agent](https://img.shields.io/badge/Log_Collection-green?style=for-the-badge) | Forwarded Sysmon logs to the Wazuh Manager. |
| Wazuh Manager | ![Wazuh](https://img.shields.io/badge/SIEM-026AA7?style=for-the-badge) | Processed logs and generated alerts. |
| Shuffle | ![SOAR](https://img.shields.io/badge/SOAR-orange?style=for-the-badge) | Automated enrichment and response workflows. |
| VirusTotal | ![Threat Intel](https://img.shields.io/badge/Threat_Intelligence-red?style=for-the-badge) | Enriched alerts with threat intelligence. |
| TheHive | ![Case Management](https://img.shields.io/badge/Case_Management-purple?style=for-the-badge) | Created cases for analyst investigations. |
| Email Service | ![Notification](https://img.shields.io/badge/Alerting-yellow?style=for-the-badge&logoColor=black) | Sent automated analyst notifications. |
### Data Flow
1. Collect Endpoint Telemetry – Sysmon captures Windows endpoint activity and the Wazuh Agent securely forwards the telemetry to the Wazuh Manager.
2. Detect Malicious Activity – Wazuh analyzes incoming events against custom detection rules and generates a security alert upon identifying suspicious activity.
3. Enrich Security Alert – Shuffle automates the workflow by enriching the alert with VirusTotal intelligence and creating an investigation case in TheHive.
4. Notify Security Analyst – An automated email delivers the enriched alert to the analyst, enabling immediate investigation and response.

<p align="center">
  <img src="images/automation-diagram.png"/>
  <br/>
  <em>Security Data Flow: Detection and Incident Automation</em>
</p>

## Methodology

### Environment Setup

#### Windows Endpoint Deployment
The lab environment begins with the deployment of a Windows virtual machine configured as the target endpoint. Sysmon is installed to generate detailed endpoint telemetry, providing the process creation, network, and system events required for security monitoring and detection.

<p align="center">
  <img src="images/image29.png"/>
  <br/>
  <em>Endpoint Telemetry Setup: Confirming Sysmon Service Status on Windows VM</em>
</p>

#### Wazuh Manager Deployment
A Debian Linux virtual machine is provisioned to host the Wazuh Manager. After installation and initial configuration, the manager serves as the central SIEM platform, receiving and analyzing telemetry collected from the monitored endpoint.

<p align="center">
  <img src="images/image18.png"/>
  <br/>
  <em>Wazuh Manager Setup: Verifying SIEM Installation and Dashboard Connectivity</em>
</p>

#### TheHive Deployment
A second Debian Linux virtual machine is deployed to host TheHive. After installing TheHive and its dependencies, Cassandra and Elasticsearch are configured for local communication. Port 9000 is then opened, allowing successful access to the TheHive web interface and completing the incident management component of the lab.

<p align="center">
  <img src="images/image23.png"/>
  <br/>
  <em>TheHive Setup: Verifying Incident Response Platform Installation and Dashboard Connectivity</em>
</p>

### Telemetry Collection

#### System Integration
To enhance endpoint visibility, Sysmon telemetry is configured to be collected by the Wazuh Agent and forwarded to the Wazuh Manager. Sysmon event logs are successfully ingested into the SIEM, providing the detailed process, network, and system activity needed for detection engineering.

<p align="center">
  <img src="images/image14.png"/>
  <br/>
  <em>Sysmon Telemetry Integration: Confirming Endpoint Events Are Ingested into Wazuh SIEM</em>
</p>

#### Disabling Endpoint Firewall
Because Mimikatz is used to simulate credential-dumping activity, Windows Defender Firewall needs disabled to prevent the tool from being blocked during testing.

<p align="center">
  <img src="images/image21.png"/>
  <br/>
  <em>Security Control Adjustment: Temporarily Disabling Endpoint Protection for Detection Testing</em>
</p>

#### Wazuh Agent Deployment
To complete the telemetry pipeline, the Wazuh Agent is deployed to the Windows endpoint hosting Sysmon. After confirming the agent service is running, I opened ports 1514 and 1515 to allow communication with the Wazuh Manager. Once the service has been restarted, the endpoint successfully registers with Wazuh, confirming telemetry is being forwarded to the SIEM.

<p align="center">
  <img src="images/image2.png"/>
  <br/>
  <em>Wazuh Agent Integration: Confirming Endpoint Communication with the Wazuh Manager</em>
</p>

### Detection Engineering

#### Transition to Detection
With endpoint telemetry successfully flowing into Wazuh, the next phase is building custom detections. To generate relevant security events, Mimikatz is executed on the monitored endpoint, simulating credential-dumping activity that should be identified by the SIEM.

<p align="center">
  <img src="images/image32.png"/>
  <br/>
  <em>Adversary Simulation: Executing Credential Access Techniques on the Windows Host</em>
</p>

#### Need for Custom Rules
By default, Wazuh does not generate an alert for this simulated attack. To improve the security posture, I created a simple rule to detect Mimikatz usage. This rule effectively detects standard Mimikatz usage in a lab environment by identifying the executable name through Sysmon. But, in a SOC environment, stronger detections would rely on behavioral indicators since filenames can be changed to bypass detection. And now the rule has triggered the generation of an alert.

<p align="center">
  <img src="images/image6.png"/>
  <br/>
  <em>Wazuh Rule Engineering: Developing a Custom Detection for Malicious Binary Execution</em>
</p>

#### Capturing the Required Telemetry
To ensure the necessary events are available for detection development, the Wazuh configuration is updated to collect all relevant logs. A new index pattern is then created using the timestamp field, allowing the captured Mimikatz telemetry to be searched, visualized, and analyzed within the Wazuh dashboard.

<p align="center">
  <img src="images/image25.png"/>
  <br/>
  <em>SIEM Alert Generation: Confirming Custom Rule Detection of Mimikatz Execution</em>
</p>

### SOAR Automation

The next step will be to tie everything in with an automated incident response process, a SOAR workflow is created in Shuffle. Wazuh is configured to forward alerts to the Shuffle webhook, where the incoming event is parsed and the SHA256 hash is extracted using regular expressions (REGEX). The hash is then submitted to the VirusTotal API for threat intelligence enrichment before the enriched alert is forwarded to TheHive for case creation. Because TheHive was hosted on an isolated private network, a temporary Cloudflare Tunnel was configured to enable secure communication between Shuffle and TheHive.

<p align="center">
  <img src="images/image1.png"/>
  <br/>
  <em>SOAR Automation Pipeline: Integrating Wazuh, VirusTotal, and TheHive for Alert Response</em>
</p>

### Validation & Results

The completed workflow successfully automates the end-to-end response process. A Mimikatz detection generated by Sysmon is forwarded through Wazuh to Shuffle, enriched with VirusTotal intelligence, and automatically creates an alert in TheHive. The alert is mapped to the MITRE ATT&CK T1003 – OS Credential Dumping technique, and an email notification is sent to the security analyst, demonstrating an automated detection and response pipeline from initial telemetry to analyst notification.

<p align="center">
  <img src="images/image17.png"/>
  <br/>
  <em>Automated Case Creation: Forwarding Enriched Alerts into TheHive</em>
</p>

<p align="center">
  <img src="images/image15.png"/>
  <br/>
  <em>Incident Alerting: Confirming Email Delivery After Automated Detection</em>
</p>

## Performance Metrics

The automated enrichment and alerting workflow completed in approximately 3.1 seconds, including SHA256 extraction, VirusTotal enrichment, TheHive alert creation, and analyst email notification. 

>While this timing reflects a controlled lab environment, it demonstrates how security automation reduces the time between detection and analyst awareness. By automatically enriching alerts, creating investigation cases, and sending notifications, the workflow provides analysts with actionable context almost immediately, enabling faster triage and supporting a lower Mean Time to Respond (MTTR).


| Event | Timestamp | Elapsed |
|-------|-----------|---------|
| Workflow started | 19:08:01.223 | 0.000 s |
| SHA256 extracted | 19:08:01.690 | 0.467 s |
| VirusTotal response received | 19:08:02.632 | 1.409 s |
| TheHive response received | 19:08:03.788 | 2.565 s |
| Email response received | 19:08:04.128 | 2.905 s |
| Workflow finished | 19:08:04.285 | 3.062 s |

## Key Findings

- Endpoint telemetry can be centralized and monitored in near real time using Wazuh.
- Custom detection rules enable identification of attack techniques beyond default detections.
- SOAR automation significantly reduces manual incident response tasks through enrichment and case creation.
- Threat intelligence integration improves alert context and investigation efficiency.
- The end-to-end detection and response workflow completed in approximately **3.1 seconds**.

## Skills Demonstrated

- Wazuh SIEM administration
- Sysmon endpoint monitoring
- Custom detection engineering
- SOAR workflow automation
- API and webhook integration
- Threat intelligence enrichment (VirusTotal)
- TheHive case management
- Security event analysis
- MITRE ATT&CK mapping

## Future Improvements

- Develop behavioral detections for credential dumping.
- Correlate multiple events to improve detection accuracy.
- Automate endpoint containment actions.
- Expand coverage to additional attack techniques.
- Perform false-positive tuning and performance validation.
