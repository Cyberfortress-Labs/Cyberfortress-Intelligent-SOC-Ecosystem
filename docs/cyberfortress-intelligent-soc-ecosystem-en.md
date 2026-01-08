# Intelligent SOC Ecosystem - Cyberfortress Labs

> Thesis Summary: Intelligent SOC Ecosystem for Monitoring, Detection, and Response to Cyber Attacks

![SIEM Architecture](../img/KLTN_SOC-Ecosystem-LogicFlow.drawio.png)



## Report Summary

This thesis presents the research, design, and implementation of a comprehensive **Intelligent Security Operations Center (SOC) Ecosystem**. The core focus is addressing the inherent challenges of traditional SOC models, including alert overload, lack of analytical context, and dependence on manual processes.

The proposed solution is a unified architecture integrating **SIEM, SOAR, OpenXDR, and Threat Intelligence (CTI)** platforms, enhanced by **Artificial Intelligence (AI/ML/LLM)** technologies to fully automate the monitoring, detection, and incident response lifecycle.

The breakthrough component of the system is **SmartXDR**, an intelligent orchestration layer developed by the research team. SmartXDR does not replace existing tools but serves as the central "brain," performing semantic analysis, data enrichment, risk scoring, and decision support, thereby reducing false alerts and optimizing efficiency for security analysts.

This architecture is fully implemented using **open-source technologies** (Elastic Stack, Wazuh, DFIR-IRIS, Suricata, n8n) in a simulated enterprise environment, demonstrating both cost-effectiveness and technical feasibility.

The ecosystem's effectiveness is validated through **five real-world attack scenarios** mapped to the **MITRE ATT&CK** framework. Experimental results show that the system accurately detects threats while significantly reducing **Mean Time to Detect (MTTD)** and **Mean Time to Respond (MTTR)**.


## 1. Context and Core Problems

### 1.1. Modern Information Security Landscape

The explosion of digital transformation, cloud computing, and IoT has significantly increased organizations' attack surfaces. Cyber threats are becoming increasingly sophisticated, with Advanced Persistent Threats (APT), ransomware, and zero-day exploits becoming prevalent, requiring continuous and timely monitoring and response capabilities.

### 1.2. Challenges of Traditional SOC Models

Traditional SOC centers face several critical limitations:

| Challenge                         | Description                                                                                                                                              |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Alert Fatigue**                 | SIEM and IDS/IPS systems often generate a large volume of alerts, including many false positives, overwhelming analysts and risking missed real threats. |
| **Lack of Analytical Context**    | Data from disparate, scattered tools makes event correlation and understanding the complete picture of an attack difficult.                              |
| **Manual Processes**              | Heavy reliance on manual operations extends response time, lacks consistency, and is prone to errors.                                                    |
| **High Costs and Vendor Lock-in** | Commercial XDR solutions, while powerful, often have high investment costs and limited flexible integration with diverse enterprise infrastructure.      |

> [!IMPORTANT]
> Given these challenges, building an intelligent, integrated, and automated SOC model is an urgent necessity.


## 2. Proposed Intelligent SOC Ecosystem Architecture

To address the above issues, the thesis proposes a unified architecture that transforms individual tools into a seamlessly operating ecosystem.

### 2.1. Design Philosophy

The system is built on the principle of combining core components of a modern SOC:

- **SIEM**: Platform for centralized log collection, normalization, and storage
- **OpenXDR**: Extending multi-layer monitoring from network to endpoint
- **SOAR**: Automating response processes and incident management
- **CTI**: Enriching alert data with threat intelligence
- **AI/ML**: Intelligent layer supporting analysis and decision-making

### 2.2. Open Source Technologies Used

The system leverages the power of open-source solutions to optimize costs and ensure flexibility.

| Function Group                 | Technology Solution                                    | Main Role                                                                                              |
| ------------------------------ | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| Log Management & SIEM          | Elastic Stack (Elasticsearch, Logstash, Kibana, Fleet) | Centralized log storage and normalization (ECS standard), data visualization, and rule-based detection |
| Endpoint Security (XDR)        | Wazuh                                                  | File Integrity Monitoring (FIM), threat detection, and proactive response on endpoints                 |
| Network Monitoring (NSM/IDPS)  | Suricata & Zeek                                        | Network intrusion detection and prevention (IDPS); deep network protocol analysis for investigation    |
| Firewall & WAF                 | pfSense & ModSecurity                                  | Network traffic control and filtering; web application protection from common attacks                  |
| Automation & Response (SOAR)   | DFIR-IRIS, n8n, IntelOwl                               | Incident handling process management, workflow automation, and intelligence enrichment                 |
| Threat Intelligence (CTI)      | MISP                                                   | Platform for storing, managing, and sharing Indicators of Compromise (IoCs)                            |
| Intelligent Orchestration Core | SmartXDR (Self-developed)                              | Central "brain" integrating AI/ML for semantic analysis, risk ranking, and full system orchestration   |



## 3. Breakthrough Component: SmartXDR

![SmartXDR Architecture](../img/KLTN_SOC-Ecosystem-SmartXDR.drawio.png)

**SmartXDR** is the core of the ecosystem, designed as an intelligent middleware layer that enhances the capabilities of the entire system rather than replacing existing components.

### Role

SmartXDR is not another SIEM or SOAR, but an **intelligent orchestration and analysis layer** that connects technology "islands" together.

### Architecture

Built on the **Python Flask** framework, SmartXDR includes specialized functional modules:

| Module                                        | Function                                                                                                                                                                                                                                                                                              |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SmartXDR Ingest Pipeline & Classification** | Receives normalized logs, then uses the Bylastic Machine Learning model (based on DistilBERT) to automatically classify logs into three severity levels: INFO, WARNING, ERROR. This mechanism filters noise and prioritizes important events from input.                                              |
| **SmartXDR Analysis**                         | The deep analysis core, using Large Language Models (LLM) combined with advanced Retrieval-Augmented Generation (RAG) techniques. It can "understand" system context, synthesize information from multiple sources, assess risk (Risk Score), and provide investigation recommendations for analysts. |
| **SmartXDR Reporting**                        | Automatically generates detailed incident reports or periodic overview reports, then distributes via email, Telegram, and stores on OneDrive.                                                                                                                                                         |
| **SmartXDR Assistant**                        | A cybersecurity virtual assistant interacting via Telegram chatbot. It allows analysts to query data using natural language, request incident summaries, and execute response actions directly on the chat interface, realizing the ChatOps operational model.                                        |



## 4. End-to-End Incident Processing Workflow

![SOC Pipeline](../img/KLTN_SOC-Ecosystem-Pipeline.drawio.png)

The system operates through a closed **6-phase pipeline**, ensuring data is processed logically and efficiently.

### Phase 1: Log Collecting
Elastic Agent collects multi-source logs (network, endpoint, application), then normalizes them to the **Elastic Common Schema (ECS)** format.

### Phase 2: Log Pre-processing
Logs pass through SmartXDR Ingest Pipeline to filter noise and create input data fields (`ml_input`) for the ML model.

### Phase 3: Classification & Enrichment
The SmartXDR Classification module uses the **Bylastic** model to assign severity labels (INFO, WARNING, ERROR) and confidence scores to each log record.

### Phase 4: Alert Generation & Correlation
Elastic Detection Rules, enhanced by ML classification results, trigger alerts when abnormal behavior is detected. **ElastAlert2** then forwards these alerts to the SOAR platform.

### Phase 5: Contextual Enrichment & Incident Response
Alerts are automatically created as an incident case on **DFIR-IRIS**. The system automatically analyzes IoCs using IntelOwl/MISP and triggers SmartXDR Analysis to provide deeper insights.

### Phase 6: Automated Response & Reporting
Based on analysis, analysts can trigger automated response scenarios via **n8n** (e.g., blocking IP on pfSense, isolating servers via Wazuh). Simultaneously, SmartXDR Reporting creates incident reports and SmartXDR Assistant sends summary notifications via Telegram.



## 5. Experimentation and Evaluation

### 5.1. Environment Setup and Attack Scenarios

The system was tested in a virtualized lab environment simulating an enterprise network with separate zones (LAN, GUEST, SOC). Five representative attack scenarios were executed, mapped to the **MITRE ATT&CK** framework:

| Scenario                  | Description                                                                                        | MITRE Technique                           |
| ------------------------- | -------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| 1. C2 Connection          | Malware hidden in a fake PDF file, establishing connection to command and control server           | T1071.001 (Application Layer Protocol)    |
| 2. SQL Injection Attack   | Attacker exploits SQLi vulnerability on web application (DVWA) to access database                  | T1190 (Exploit Public-Facing Application) |
| 3. SMB Brute-force Attack | Continuous password guessing against Windows Server SMB service                                    | T1110.003 (Password Spraying)             |
| 4. Malware Execution      | User tricked into downloading and running file containing malware, Wazuh Agent detects and removes | T1204 (User Execution)                    |
| 5. ChatOps Operations     | Analyst interacts with SmartXDR Assistant via Telegram for investigation and response              | -                                         |

### 5.2. Evaluation Results

Experimental results show the system operates effectively and meets the stated objectives.

**Average performance measurement results in the experimental environment:**

| KPI Metric                      | Recorded Value                                           | Notes                                                                   |
| ------------------------------- | -------------------------------------------------------- | ----------------------------------------------------------------------- |
| **MTTD** (Mean Time to Detect)  | 5-15 seconds (network attacks), 15-30 seconds (endpoint) | Near real-time detection thanks to continuous processing pipeline       |
| **MTTR** (Mean Time to Respond) | 1-3 minutes (with automated scenarios)                   | Automation significantly reduces threat containment time                |
| **False Alert Reduction**       | ~25-30% reduction after applying ML classification       | ML model helps prioritize high-risk events, reducing noise for analysts |
| **Detection Accuracy**          | ~92% of attack events detected with correct context      | Detection rule quality and input data are key factors                   |

> [!TIP]
> Compared to traditional SOC models, the proposed system excels through semantic analysis capabilities, response automation, and AI integration, shifting from reactive to proactive cybersecurity protection.



## 6. Conclusion and Future Development

### 6.1. Conclusion

The thesis successfully built a comprehensive, feasible, and effective **Intelligent SOC Ecosystem** based on open-source technologies. Introducing the SmartXDR architecture as an intelligent orchestration layer addressed the lack of context and alert overload problems of traditional SOCs.

This model not only demonstrates effectiveness through quantitative metrics (MTTD, MTTR) but also provides valuable reference documentation for organizations wanting to build modern cybersecurity monitoring capabilities at optimal cost.

### 6.2. Limitations

- The system was deployed in a lab environment, not fully reflecting the scale and complexity of production environments
- ML model effectiveness heavily depends on the quality and diversity of training data
- Integrating and operating multiple open-source tools requires high technical expertise

### 6.3. Future Development Directions

- **Enhance AI/ML Capabilities**: Expand training datasets with more complex attack scenarios (APT, evasion) and improve long event chain analysis models
- **Improve SOAR Automation**: Build more complex response playbooks, moving toward full automation for clearly identified threats
- **Optimize SIEM Performance**: Apply Index Lifecycle Management (ILM) techniques and optimize correlation rules to handle larger log volumes
- **Expand to Cloud-Hybrid Environments**: Test system deployment on cloud platforms to evaluate scalability, load capacity, and monitoring of hybrid environments


## 7. System Demo

> Watch our Intelligent SOC Ecosystem in action through real-world attack scenarios

### Scenario 1: C2 Outbound Connection

Simulates a user opening a disguised PDF file on a Windows 11 workstation (192.168.85.150). The executable, camouflaged with a PDF icon, launches a background process and establishes a beacon connection to an external Command & Control (C2) server.

**[Watch Demo](https://youtu.be/XWCobYtvlBE)**



### Scenario 2: SQL Injection Attack

Demonstrates SQL Injection exploitation against DVWA web application behind ModSecurity WAF (192.168.85.111). The attacker targets the database server (192.168.85.112) to expose sensitive data, manipulate records, or escalate privileges.

**[Watch Demo](https://youtu.be/ajynzGKljqE)**



### Scenario 3: NTLM over SMB Brute-force

An attacker from the GUEST network (192.168.95.100) uses automated tools to perform password brute-forcing against SMB service on Windows Server (192.168.85.115), attempting NTLM authentication bypass for lateral movement.
**[Watch Demo](https://youtu.be/9ECwOKesfUM)**


### Scenario 4: Malware Execution

Simulates social engineering attack where a user downloads a fake MediaPlayer installer (.zip) containing malware. Wazuh Agent detects the threat via VirusTotal integration, automatically removes the malicious file, and reports to the central system.

**[Watch Demo](https://youtu.be/_wuKYfO0x_4)**



### Scenario 5: SmartXDR ChatOps

Demonstrates ChatOps workflow via Telegram integration. Administrators interact bidirectionally with SmartXDR through the chatbot, requesting AI-powered incident analysis or executing response actions directly from the messaging platform.

**[Watch Demo](https://youtu.be/2qiM6oSsuOg)**


## 8. Resources

> Access demo videos and project documentation

| Resource                | Description                           | Link                                                                                               |
| ----------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **GitHub Organization** | Explore source code and repositories  | [Cyberfortress-Labs](https://github.com/Cyberfortress-Labs)                                        |
| **YouTube Playlist**    | Watch all demo videos in one playlist | [Watch Playlist](https://www.youtube.com/playlist?list=PLshy_4RvrXflyh76ppECu-9yAvTgE2h3d)         |
| **Google Drive**        | Access additional resources and files | [Open Drive](https://drive.google.com/drive/folders/1TnPI0jEuVOhzjaLv8Pt6tsFN6O2h-dR9?usp=sharing) |


## Project Team

- **Lai Quan Thien** - [WanThinnn](https://github.com/WanThinnn)
- **Ho Diep Huy** - [hohuyy](https://github.com/hohuyy)
