# Automated SOC Pipeline Using Wazuh, Shuffle, and TheHive
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

## Section

The jist of this lab is to set up the architecture to have an endpoint that can have enriched logs aggregated to a SIEM for advanced rule detection that is passed over to a SOAR for OSINT intel and threat mapping, which will be used to generate an alert within a case management platform. I've done this by dividing the share of the workload onto differing hosts that communicate with one another, as in an actual SOC. The result of this project is to create a platform for automated detection and alert creation. In this case, I have a barebones rule that detects the deployment of a specific malicious binary on a host. Eventually, I would like to delve into behavioral-based rule detection that mirrors true SOC work with multiple rule facets and more complex topics.

<p align="center">
  <img src="images/automation-diagram.png"/>
  <br/>
  <em>jist of picture</em>
</p>

I started this lab by downloading Sysmon & its Olaf configuration onto a Windows VM. I will have 3 total machines running in my local network: 1. The Windows VM that will be configured with a Wazuh Agent that transfers Sysmon logs to the Wazuh Manager, 2. The Wazuh Manager Instance running in a Linux VM, and 3. An instance of TheHive running also on a Linux VM.

I proceeded by downloading a Debian Linux server, installing the Wazuh Manager. I downloaded an additional Debian Linux server, and installed TheHive and its dependencies. I configured Cassandra and Elasticsearch to communicate locally, and, after enabling port 9000 on the TheHive VM, the service web application is reachable.

<p align="center">
  <img src="images/image29.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image18.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image23.png"/>
  <br/>
  <em>jist of picture</em>
</p>

Now, I've configured Sysmon telemetry to reach the Wazuh Manager, and created a custom detection rule looking for Mimikatz activity. I've added Sysmon logs to the Windows Wazuh Agent, and Sysmon logs are now ingested into our SIEM!

We will be using Mimikatz, so the firewall will need to be disabled. I continue preparation of the the telemetry phase by deploying the Wazuh Agent onto the Sysmon machine, and the .SVC has started successfully! I Opened ports 1514/1515 for the Wazuh Manager VM to communicate with the Agent and restarted service. Upon checking the web application, our Agent is registered in Wazuh.

<p align="center">
  <img src="images/image14.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image21.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image22.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image2.png"/>
  <br/>
  <em>jist of picture</em>
</p>

Now that the endpoint telemetry has been established, I will move onto the detection section. On our Wazuh Agent, we can now run Mimikatz. Wazuh does not currently detect our malicious activity on our endpoint, and we must now make our own custom rules to adjust our security posture. I altered the configuration file to capture all logs now. After that, I will create a new index pattern aggregated by the time field “timestamp”. The logs for Mimikatz are now being captured under the new index.

<p align="center">
  <img src="images/image32.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image4.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image25.png"/>
  <br/>
  <em>jist of picture</em>
</p>

I created a simple rule to detect Mimikatz usage. This rule effectively detects standard Mimikatz usage in a lab environment by identifying the executable name through Sysmon. But, in a SOC environment, stronger detections would rely on behavioral indicators since filenames can be changed to bypass detection. And now the rule has triggered the generation of an alert.

<p align="center">
  <img src="images/image6.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image11.png"/>
  <br/>
  <em>jist of picture</em>
</p>

The next step will be to tie everything in with an automated workflow. I am using Shuffler.io to create a new workflow for this. I’ve pointed Wazuh toward my Shuffler integration using the hook URI, and the workflow is able to receive the alert.

Here is the workflow: The webhook will use its URI to receive alerts generated from Sysmon via our Wazuh Agent, sent to our Wazuh Manager and finally to our Shuffler webhook. Next, the hash is taken, and regex is performed to pull that SHA256, which is sent to the VirusTotal API, and given a hash assessment. The information is passed to TheHive and an alert is created!

I realized I made all of my machines communicate locally and Shuffle cannot trace my Hive IP, so I made a temporary tunnel via CloudFlair. Okay, now the alert is generated in TheHive! Finally, we will send an email to our analyst to inspect the alert.

Here is the final workflow, and email sent.

<p align="center">
  <img src="images/image1.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image15.png"/>
  <br/>
  <em>jist of picture</em>
</p>

<p align="center">
  <img src="images/image17.png"/>
  <br/>
  <em>jist of picture</em>
</p>

>The automated enrichment and alerting workflow completed in approximately 3.1 seconds, including SHA256 extraction, VirusTotal enrichment, TheHive alert creation, and analyst email notification. 

The whole of this process lasted just shy of 3.1 seconds. When real security incidents are involved, the time for systems to detect and alert its analysts is absolutely critical to managing threats. I just wanted to share the information of the event times here:

Event
Timestamp
Elapsed
Workflow started
19:08:01.223
0.000 s
SHA256 extracted
19:08:01.690
0.467 s
VirusTotal response received
19:08:02.632
1.409 s
TheHive response received
19:08:03.788
2.565 s
Email response received
19:08:04.128
2.905 s
Workflow finished
19:08:04.285
3.062 s


















