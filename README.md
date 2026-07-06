# 🔵 Wireshark Network Traffic Analysis Lab

> **Blue Team | SOC Analyst | Incident Response | Threat Hunting**  
> A hands-on lab demonstrating practical network traffic analysis skills using Wireshark, focused on SOC operations, incident response, and threat intelligence.

---

## 📌 Project Summary

This project provides a comprehensive case study on network traffic analysis using Wireshark within a controlled virtualized environment. It showcases the ability to perform deep packet inspection, identify protocol-level insights, and correlate observations with cybersecurity threats relevant to a Security Operations Center (SOC). The analysis covers ICMP, TCP, DNS, and HTTP protocols, highlighting their security implications and mapping findings to the MITRE ATT&CK framework.

| Component | Role |
|---|---|
| Windows Host VM | Traffic generation source/destination |
| Kali Linux VM | Traffic generation source/destination |
| VirtualBox | Virtualization platform |
| Wireshark | Network protocol analyzer |

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────┐
│           VirtualBox            │
│           Environment           │
│                                 │
│  ┌──────────────────────────┐   │
│  │      Windows Host        │   │
│  └──────────┬───────────────┘   │
│             │                   │
│  ┌──────────▼───────────────┐   │
│  │      Kali Linux VM       │   │
│  └──────────┬───────────────┘   │
│             │                   │
│  ┌──────────▼───────────────┐   │
│  │    Host-Only Network     │   │
│  │        Adapter           │   │
│  └──────────┬───────────────┘   │
│             │                   │
│  ┌──────────▼───────────────┐   │
│  │        Wireshark         │   │
│  │   (on Windows Host)      │   │
│  └───────────────────────────┘   │
└─────────────────────────────────┘
```

The experimental environment for this network traffic analysis project was constructed using VirtualBox, a popular open-source virtualization software. This setup allowed for the creation of an isolated network segment, ensuring that all captured traffic was contained within the lab and did not interfere with external networks. The architecture comprised two primary virtual machines and a host-only network adapter, as depicted in the diagram above.

**Components:**

*   **Windows Host:** A virtual machine running a Windows operating system, serving as a client for generating various network activities, including web browsing (HTTP), DNS queries, and basic network connectivity tests (ICMP).
*   **Kali Linux VM:** A virtual machine running Kali Linux, a Debian-derived Linux distribution designed for digital forensics and penetration testing. This VM was used to initiate network communications and simulate potential attacker or target behaviors.
*   **Host-Only Network Adapter:** Configured within VirtualBox, this adapter created a private network segment that allowed the Windows Host and Kali Linux VM to communicate with each other while isolating them from the host machine's external network. This setup is crucial for controlled traffic capture and analysis.
*   **Wireshark:** The primary analysis tool, installed on the Windows Host, was used to capture and inspect all network traffic traversing the host-only adapter. This strategic placement ensured comprehensive visibility into the communication between the two virtual machines.

This architecture provided a robust and controlled environment for observing, capturing, and analyzing network protocols, forming the basis for the detailed investigations presented in this study.

---

## 🎯 Analysis Use Cases

All analysis use cases are mapped to the [MITRE ATT&CK Framework](https://attack.mitre.org/).

| # | Protocol | SOC Relevance | MITRE Technique | Severity |
|---|---|---|---|---|
| 1 | [ICMP](#uc1) | Reconnaissance detection | T1595.001, T1595.002 | 🟠 Medium |
| 2 | [TCP](#uc2) | Handshake & Session analysis | T1498.001 | 🟠 Medium |
| 3 | [DNS](#uc3) | C2 & Exfiltration hunting | T1071.004 | 🔴 High |
| 4 | [HTTP](#uc4) | Data exposure & Malware delivery | T1071.001 | 🔴 Critical |

---

## 📂 Repository Structure

```
wireshark-soc-lab/
├── README.md                          ← This file
├── LICENSE                            ← Project license
├── lab_architecture.png               ← Lab architecture diagram (visual)
├── display_filters.txt                ← Collection of useful Wireshark display filters
├── BusinessCase_WiresharkinCyberSecurity.pdf ← Original project document
```

---

<a name="uc1"></a>
## 🔍 Analysis Use Case 1 — ICMP Traffic Analysis

**Objective:** Understand ICMP traffic patterns for network reconnaissance and basic connectivity troubleshooting.

**Security Relevance:** While essential for diagnostics, ICMP can be abused for reconnaissance (ping sweeps) and covert communication (ICMP tunneling). SOC analysts must monitor ICMP for anomalous patterns.

**Traffic Generated:** Internet Control Message Protocol (ICMP) traffic was generated by initiating `ping` requests between the Kali Linux virtual machine and the Windows host.

**Wireshark Filter:** `icmp`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **Echo Request/Reply** | Successful capture of ICMP Echo Request (Type 8, Code 0) and Echo Reply (Type 0, Code 0) packets. |
| **Connectivity Verified** | The presence of corresponding echo replies confirmed successful network connectivity and reachability between the Kali Linux and Windows systems. |
| **Packet Structure** | Inspection of ICMP packet headers revealed source and destination IP addresses, sequence numbers, and identifiers, which are crucial for correlating requests with replies. |

**MITRE ATT&CK:**
- [T1595.001 — Active Scanning: Scanning IP Blocks/Ranges](https://attack.mitre.org/techniques/T1595/001/)
- [T1595.002 — Active Scanning: Vulnerability Scanning](https://attack.mitre.org/techniques/T1595/002/)

**Investigation Findings:**
- Normal ICMP traffic indicates network reachability.
- Unusual volumes or patterns of ICMP can signal reconnaissance.
- Misconfigured firewalls might allow ICMP for covert channels.

---

<a name="uc2"></a>
## 🔍 Analysis Use Case 2 — TCP Communication Analysis

**Objective:** Observe TCP three-way handshake and session establishment for connection integrity and anomaly detection.

**Security Relevance:** TCP is fundamental for most network services. Its analysis helps detect SYN floods, port scanning, and understand application-layer communication issues or malicious activities.

**Traffic Generated:** Transmission Control Protocol (TCP) communication was established between the Kali Linux virtual machine and the Windows host.

**Wireshark Filter:** `tcp`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **Three-Way Handshake** | Successfully identified the SYN, SYN-ACK, and ACK packet sequence, confirming reliable connection establishment. |
| **Connection Establishment** | The handshake demonstrated the successful initiation of a TCP session. |
| **Port Numbers** | Observed source and destination port numbers, indicating specific services. |
| **Sequence/Acknowledgment Numbers** | Tracked incrementing sequence and acknowledgment numbers for reliable data delivery. |

**MITRE ATT&CK:**
- [T1498.001 — Denial of Service: Direct Network Attack](https://attack.mitre.org/techniques/T1498/001/)

**Investigation Findings:**
- Proper TCP handshake confirms legitimate connection attempts.
- Anomalous SYN packets (without replies) can indicate SYN flood attacks.
- Numerous connection attempts to different ports from a single source suggest port scanning.

---

<a name="uc3"></a>
## 🔍 Analysis Use Case 3 — DNS Traffic Analysis

**Objective:** Analyze DNS queries and responses to identify potential C2, data exfiltration, or reconnaissance activities.

**Security Relevance:** DNS is a critical service and a common vector for attacks. It's frequently abused for C2 (DNS tunneling) and data exfiltration. Monitoring DNS traffic is vital for detecting these stealthy threats.

**Traffic Generated:** Domain Name System (DNS) traffic was generated by performing domain name lookups from the Kali Linux virtual machine and the Windows host.

**Wireshark Filter:** `dns`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **DNS Queries** | Captured standard DNS queries (Type A, AAAA, PTR). |
| **DNS Responses** | Observed corresponding DNS responses from the DNS server. |
| **Query/Response Correlation** | Successfully correlated DNS queries with their respective responses. |
| **Protocol Details** | Inspection revealed query types, response codes, and resolved IP addresses. |

**MITRE ATT&CK:**
- [T1071.004 — Application Layer Protocol: DNS](https://attack.mitre.org/techniques/T1071/004/)

**Investigation Findings:**
- Unusual query patterns, long/random subdomains, or queries to suspicious TLDs can indicate DNS tunneling (C2).
- Large DNS query sizes or frequent queries to external, unknown domains can suggest data exfiltration.
- DNS queries to malicious domains indicate potential phishing or compromise.

---

<a name="uc4"></a>
## 🔍 Analysis Use Case 4 — HTTP Traffic Analysis

**Objective:** Examine HTTP traffic for data exposure, malware delivery, and C2 communications.

**Security Relevance:** Unencrypted HTTP traffic exposes sensitive information and is a common protocol for malware delivery and C2. Its analysis is crucial for identifying data breaches, malicious downloads, and web application attacks.

**Traffic Generated:** Hypertext Transfer Protocol (HTTP) traffic was generated by accessing web resources from the Windows host.

**Wireshark Filter:** `http`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **HTTP GET Requests** | Captured HTTP GET requests for web resources. |
| **HTTP Responses** | Observed corresponding HTTP responses, including status codes and content. |
| **Plaintext Communication** | URLs, headers, and data payloads were visible in plaintext. |
| **User-Agent Strings** | Identified User-Agent strings providing client information. |

**MITRE ATT&CK:**
- [T1071.001 — Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)

**Investigation Findings:**
- Plaintext HTTP exposes sensitive data to eavesdropping.
- Suspicious file downloads (executables) via HTTP can indicate malware delivery.
- Frequent, unusual HTTP requests to suspicious domains or non-standard methods suggest C2 activity.
- HTTP traffic to known malicious domains indicates phishing or compromised websites.

---

## 🛠️ Technical Skills Demonstrated

**Network Traffic Analysis**
- Deep Packet Inspection (DPI) using Wireshark
- Protocol analysis (ICMP, TCP, DNS, HTTP)
- Wireshark display and capture filter application
- Session reconstruction and stream analysis

**Cybersecurity Operations**
- Incident Response (IR) evidence collection
- Threat hunting for network-based indicators
- Correlation of network events with security implications
- Understanding of Command and Control (C2) mechanisms

**Security Concepts**
- MITRE ATT&CK framework mapping
- Network reconnaissance detection
- Data exfiltration identification
- Denial of Service (DoS) attack indicators

---

## 🔗 References

- [CyberDefenders: The Ultimate Guide to Network Traffic Analysis for SOC Analysts](https://cyberdefenders.org/blog/the-ultimate-guide-to-network-traffic-analysis-for-soc-analysts/)
- [Medium: SOC Analyst Lab — Network Analysis (Malware) using Wireshark](https://medium.com/@gboyegaemmanuel0/soc-analyst-lab-network-analysis-malware-using-wireshark-d8a5129ed9eb)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

---

## 👤 Author

**Harish Goud**  
Cybersecurity Aspirant — SOC Operations | Incident Response | Threat Intelligence

[![GitHub](https://img.shields.io/badge/GitHub-harishgoud1410--cpu-181717?style=flat&logo=github)](https://github.com/harishgoud1410-cpu)

---

> ⚠️ **Disclaimer:** This project was developed in a controlled lab environment for educational and cybersecurity training purposes only. All analysis was performed on isolated virtual machines with no connection to production systems. The findings are based on simulated traffic and may not fully represent the complexity of real-world attacks.
