# Network Traffic Analysis for SOC Operations: A Practical Case Study

![License](https://img.shields.io/badge/License-MIT-blue.svg)
![Topic](https://img.shields.io/badge/Topic-Cybersecurity-red.svg)
![Tool](https://img.shields.io/badge/Tool-Wireshark-green.svg)

## Overview

This repository contains an enhanced case study on network traffic analysis using Wireshark, specifically reframed for **Security Operations Center (SOC)** professionals and aspiring incident responders. The project demonstrates practical skills in deep packet inspection, protocol analysis, and identifying suspicious network behaviors within a controlled virtualized environment.

## Project Objectives

*   **Demonstrate Deep Packet Inspection:** Analyze ICMP, TCP, DNS, and HTTP traffic at the packet level.
*   **Identify Security Implications:** Explore how unencrypted traffic and protocol misuse can lead to security risks.
*   **Map to SOC Use Cases:** Align technical observations with real-world SOC functions like threat hunting and incident response.
*   **Leverage Industry Frameworks:** Map findings to the **MITRE ATT&CK** framework for standardized threat categorization.

## Repository Structure

*   `docs/`: Contains the full project report in Markdown format.
*   `assets/`: Includes diagrams and screenshots used in the project.
*   `filters/`: A library of Wireshark display filters used for analysis and triage.

## Key Findings

| Protocol | SOC Relevance | Key Observation |
| :--- | :--- | :--- |
| **ICMP** | Reconnaissance detection | Successful echo request/reply capture; connectivity verified. |
| **TCP** | Handshake & Session analysis | Observed 3-way handshake; verified reliable connection establishment. |
| **DNS** | C2 & Exfiltration hunting | Correlated queries and responses; identified potential for tunneling. |
| **HTTP** | Data exposure & Malware delivery | Plaintext visibility of URLs and payloads; identified data risks. |

## Getting Started

1.  **Read the Report:** Navigate to `docs/report.md` for the comprehensive case study.
2.  **Explore Filters:** Check `filters/display_filters.txt` for a library of useful Wireshark filters.
3.  **View Architecture:** See `assets/lab_architecture.png` for the virtual lab setup.

## Author

*   **Hari** - *Cybersecurity Aspirant*

## License

This project is licensed under the MIT License - see the LICENSE file for details.
