# Cyberfortress Labs - Intelligent SOC Ecosystem

> An Intelligent SOC Ecosystem for Monitoring, Detection, and Response to Cyber Attacks

![SIEM Architecture](img/KLTN_SOC-Ecosystem-LogicFlow.drawio.png)

## Overview

A unified intelligent SOC ecosystem where SIEM, SOAR, OpenXDR, Threat Intelligence, and AI/ML/LLM platforms are integrated into a cohesive operational pipeline. The system enables end-to-end security event processing: from log collection and normalization to analysis and automated incident response. SmartXDR serves as the intelligent fusion layer, providing semantic analysis, event enrichment, and risk-based triage prioritization.

**View the project:** [https://cyberfortress-labs.github.io/](https://cyberfortress-labs.github.io/)

## Key Features

- **Real-time log monitoring & alerting**
- **AI/ML-based log classification**
- **CTI enrichment (MISP, IntelOwl)**
- **Automated incident response (SOAR)**
- **MITRE ATT&CK mapping**
- **Human-in-the-loop AI support**
- **Customizable dashboards**
- **Integration with SIEM**
- **Open Source Project**

## Technologies & Tools

### Elastic Stack (ELK)
Complete search and analytics platform:
- Elasticsearch
- Logstash
- Kibana
- Beats & Elastic Agent
- Elastic Fleet

### Network Security
- pfSense
- Suricata IDS/IPS
- Zeek NSM
- Nginx

### SOAR & Automation
- n8n Workflows
- DFIR-IRIS
- ElastAlert2
- Playbooks

### Threat Intelligence
- MISP
- IntelOwl
- IOC Enrichment
- CTI Feeds

### AI/ML Engine
- SmartXDR
- Log Classification
- LLM RAG
- Anomaly Detection

## System Architecture

### Infrastructure & Sensors
Network Sensors (Suricata IDPS, Zeek NSM), Endpoint Sensors (Wazuh Agents), and Network Infrastructure (pfSense Firewall, Nginx Reverse Proxy, WAF) provide comprehensive visibility.

### Central Data
Logs Management (Logstash, Elastic Agents, Fleet) normalizes data to ECS format. Elasticsearch serves as the centralized Data Lake for fast retrieval and long-term Big Data storage.

### Application & Analytics
Kibana (SIEM Dashboard), Wazuh Manager (Security Management), Elastic Detection Rules (KQL/EQL), and ElastAlert2 provide real-time analysis and multi-layer alert generation.

### Orchestration & Response
n8n (Integration Hub), DFIR-IRIS (Incident Management), MISP & IntelOwl (CTI Platform), and SmartXDR (AI-powered analysis, automated reporting, human-in-the-loop decision support).

## System Processing Workflow

![SOC Pipeline](img/KLTN_SOC-Ecosystem-Pipeline.drawio.png)

1. **Log Collecting**: Elastic Agent and Fleet collect raw logs from Firewall (pfSense), Linux Router, IDPS (Suricata), NSM (Zeek), WAF (ModSecurity). Logs are parsed, normalized to ECS standard and stored for pipeline processing.
2. **Log Pre-processing**: SmartXDR Ingest Pipeline filters redundant fields, extracts contextual information, and generates the `ml_input` field - a structured, condensed log representation serving as input for ML classification.
3. **Classification and Enrichment**: SmartXDR Classification uses Bylastic (DistilBERT-based model by Byviz Analytics) to analyze `ml_input` semantically, assign severity labels, and generate `prediction_probability` scores stored in `ml.prediction.*` fields.
4. **Alert Generation and Correlation**: Elastic Detection Rules query ML fields for anomaly detection. ElastAlert2 monitors alerts index and forwards matched alerts with IoCs, timestamps, and Kibana links to DFIR-IRIS for case management.
5. **Contextual Enrichment and Incident Response**: Analysts review alerts in DFIR-IRIS, convert to Incident Cases. IntelOwl performs IoC pre-analysis via MISP and VirusTotal. SmartXDR Analysis uses RAG for intelligent interpretation and risk assessment.
6. **Automated Response and Reporting**: n8n workflows orchestrate Wazuh Active Response for endpoint isolation, pfSense/Suricata rule updates for network blocking. SmartXDR Reporting generates DOCX reports, uploads to cloud storage, and sends Telegram notifications.

## SmartXDR

![SmartXDR Architecture](img/KLTN_SOC-Ecosystem-SmartXDR.drawio.png)

SmartXDR is the intelligent SOC ecosystem core designed as the central brain orchestrating the entire SOC operational pipeline.

- **SmartXDR Core**: The central Control Plane responsible for orchestrating all modules, routing data, managing background tasks, and providing AI/LLM/RAG services.
- **Ingest Pipeline**: Handles data enrichment before storage. Filters noise, extracts contextual information, and generates the `ml_input` field.
- **Classification**: Uses Bylastic (DistilBERT model) to automatically classify logs into INFO, WARNING, and ERROR severity levels.
- **Analysis**: Deep analysis layer performing semantic analysis on Logs and IoCs with Risk Score calculation. Integrates LLM+RAG for CTI-style reports.
- **Reporting**: Extracts and aggregates classified log events to generate administrative reports, distributed via Email/Telegram and Webhook.
- **Assistant**: AI Security Assistant combining LLM with Advanced RAG using Two-Stage Retrieval (Bi-encoder & Cross-encoder).

## Project Team

- **Lai Quan Thien** - [WanThinnn](https://github.com/WanThinnn)
- **Ho Diep Huy** - [hohuyy](https://github.com/hohuyy)