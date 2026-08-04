# Automated-SOC-Pipeline-Using-Wazuh-Shuffle-and-TheHive
![Status](https://img.shields.io/badge/Project-Complete-brightgreen)
![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-0266C8?logo=wazuh&logoColor=white)
![TheHive](https://img.shields.io/badge/TheHive-Case%20Management-F9A825?logo=thehive&logoColor=white)
![Shuffle](https://img.shields.io/badge/Shuffle-SOAR-7B61FF?logo=shuffle&logoColor=white)

*Built an end-to-end detection, enrichment, and incident response workflow using Sysmon, Wazuh, Shuffle, VirusTotal, and TheHive.*

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
