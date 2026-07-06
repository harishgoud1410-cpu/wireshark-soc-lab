# Network Traffic Analysis for SOC Operations: A Practical Case Study

## Executive Summary

This document presents an enhanced case study on network traffic analysis using Wireshark, reframed for Security Operations Center (SOC) professionals and aspiring incident responders. The project demonstrates practical skills in deep packet inspection, protocol analysis, and identifying suspicious network behaviors within a controlled virtualized environment. By analyzing ICMP, TCP, DNS, and HTTP traffic between Kali Linux and Windows systems, this study highlights critical insights for threat detection, incident investigation, and proactive network security. The enhancements focus on aligning technical observations with SOC use cases, mapping to the MITRE ATT&CK framework, and providing actionable recommendations for improving network visibility and security posture.

## 1. Introduction

Network traffic analysis is a foundational skill for cybersecurity professionals, particularly within a Security Operations Center (SOC) environment. Wireshark, an open-source network protocol analyzer, serves as an indispensable tool for capturing and interactively examining network traffic. Its capabilities enable detailed inspection of individual packets, facilitating the understanding of communication flows, troubleshooting network issues, and identifying potential security threats. This project leverages Wireshark in a controlled laboratory setting, involving a Windows host and a Kali Linux virtual machine, to analyze various network protocols and demonstrate their significance in cybersecurity investigations.

## 2. Problem Statement

Organizations frequently encounter challenges in maintaining comprehensive visibility into their internal network communications, which can hinder the timely detection of malicious activities and effective incident response. Key issues include: the difficulty in monitoring communication between systems in real-time, understanding network behavior at the protocol level, identifying insecure or unencrypted traffic, troubleshooting communication anomalies, and generally improving network visibility. This project addresses these challenges by providing a practical demonstration of how Wireshark can be utilized to gain granular insight into network traffic, thereby enhancing an organization's ability to detect, investigate, and respond to network-based threats. The initial setup involved a VirtualBox network configuration with a host-only adapter to simulate a segregated network segment for analysis.




## 3. Lab Architecture

The experimental environment for this network traffic analysis project was constructed using VirtualBox, a popular open-source virtualization software. This setup allowed for the creation of an isolated network segment, ensuring that all captured traffic was contained within the lab and did not interfere with external networks. The architecture comprised two primary virtual machines and a host-only network adapter, as depicted in the diagram below:

![Lab Architecture Diagram](/home/ubuntu/lab_architecture.png)

**Components:**

*   **Windows Host:** A virtual machine running a Windows operating system, serving as a client for generating various network activities, including web browsing (HTTP), DNS queries, and basic network connectivity tests (ICMP).
*   **Kali Linux VM:** A virtual machine running Kali Linux, a Debian-derived Linux distribution designed for digital forensics and penetration testing. This VM was used to initiate network communications and simulate potential attacker or target behaviors.
*   **Host-Only Network Adapter:** Configured within VirtualBox, this adapter created a private network segment that allowed the Windows Host and Kali Linux VM to communicate with each other while isolating them from the host machine's external network. This setup is crucial for controlled traffic capture and analysis.
*   **Wireshark:** The primary analysis tool, installed on the Windows Host, was used to capture and inspect all network traffic traversing the host-only adapter. This strategic placement ensured comprehensive visibility into the communication between the two virtual machines.

This architecture provided a robust and controlled environment for observing, capturing, and analyzing network protocols, forming the basis for the detailed investigations presented in this study.


## 4. Methodology

The methodology employed in this project involved a systematic approach to network traffic capture, analysis, and interpretation within the established virtual lab environment. The process was designed to simulate common network communication scenarios and then meticulously examine the resulting traffic using Wireshark. The key steps undertaken were:

1.  **Environment Setup:** Configuration of VirtualBox to host a Windows operating system and a Kali Linux virtual machine, interconnected via a host-only network adapter. This ensured an isolated and controlled environment for traffic generation and capture.
2.  **Traffic Generation:** Deliberate generation of various types of network traffic between the Windows host and the Kali Linux VM. This included:
    *   **ICMP Traffic:** Initiating `ping` commands from one system to another to generate Internet Control Message Protocol (ICMP) echo requests and replies, primarily for connectivity testing.
    *   **TCP Traffic:** Establishing TCP connections to observe the three-way handshake (SYN, SYN-ACK, ACK) and data transfer, simulating typical application-layer communication.
    *   **DNS Traffic:** Performing domain name lookups to generate Domain Name System (DNS) queries and responses, demonstrating how hostnames are resolved to IP addresses.
    *   **HTTP Traffic:** Accessing web resources over HTTP to capture unencrypted web traffic, including GET requests and server responses, highlighting the visibility of data in plaintext.
3.  **Packet Capture with Wireshark:** Wireshark was configured to capture all traffic on the host-only network adapter. This allowed for comprehensive collection of packets exchanged between the Windows and Kali Linux systems during the traffic generation phase.
4.  **Deep Packet Inspection and Analysis:** The captured `.pcap` files were then subjected to deep packet inspection using Wireshark. This involved:
    *   **Protocol Identification:** Identifying the various protocols present in the traffic stream.
    *   **Filter Application:** Utilizing Wireshark's display filters (e.g., `icmp`, `tcp`, `dns`, `http`) to isolate and focus on specific protocols or communication flows.
    *   **Conversation Analysis:** Examining network conversations to understand source/destination IP addresses, ports, and data volumes.
    *   **Payload Examination:** Inspecting the payload of packets, particularly for HTTP traffic, to identify application-layer data and potential security implications.
5.  **Documentation and Interpretation:** Observations from the packet analysis were documented, focusing on key features, behaviors, and security insights. This included correlating network events with protocol specifications and identifying deviations or points of interest relevant to SOC operations.

This systematic methodology ensured a thorough investigation of network traffic, providing a practical foundation for understanding how Wireshark can be effectively used in cybersecurity analysis.


## 5. Detailed Findings

### 5.1 ICMP Traffic Analysis

**Traffic Generated:** Internet Control Message Protocol (ICMP) traffic was generated by initiating `ping` requests between the Kali Linux virtual machine and the Windows host. This fundamental network utility is used to test reachability of a host on an Internet Protocol (IP) network.

**Wireshark Filter:** `icmp`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **Echo Request/Reply** | Successful capture of ICMP Echo Request (Type 8, Code 0) and Echo Reply (Type 0, Code 0) packets. |
| **Connectivity Verified** | The presence of corresponding echo replies confirmed successful network connectivity and reachability between the Kali Linux and Windows systems. |
| **Packet Structure** | Inspection of ICMP packet headers revealed source and destination IP addresses, sequence numbers, and identifiers, which are crucial for correlating requests with replies. |

**Security Implications & SOC Relevance:**

While ICMP is essential for network diagnostics, it can also be leveraged by attackers for reconnaissance and covert communication. For SOC analysts, understanding ICMP traffic is vital for:

*   **Network Reconnaissance:** Attackers often use `ping` sweeps to identify active hosts on a network. Unusual patterns of ICMP requests originating from or directed towards specific segments can indicate reconnaissance activities.
*   **Firewall Evasion:** Misconfigured firewalls might permit all ICMP traffic, potentially allowing attackers to use ICMP for tunneling data (ICMP tunneling) or for command and control (C2) communications, although this was not observed in this basic lab setup.
*   **Troubleshooting:** Legitimate ICMP traffic helps in diagnosing network connectivity issues, path MTU discovery, and identifying routing problems. Anomalies in expected ICMP behavior can signal underlying network infrastructure problems or malicious interference.
*   **Baselining:** Establishing a baseline of normal ICMP traffic patterns is crucial for detecting deviations that could indicate suspicious activity.


### 5.2 TCP Communication Analysis

**Traffic Generated:** Transmission Control Protocol (TCP) communication was established between the Kali Linux virtual machine and the Windows host to observe the fundamental three-way handshake process and subsequent data transfer. This simulates typical client-server interactions that underpin most internet applications.

**Wireshark Filter:** `tcp`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **Three-Way Handshake** | Successfully identified the SYN, SYN-ACK, and ACK packet sequence, confirming reliable connection establishment. |
| **Connection Establishment** | The handshake demonstrated the successful initiation of a TCP session between the two systems, a prerequisite for most application-layer protocols. |
| **Port Numbers** | Observed source and destination port numbers, indicating the specific services involved in the communication (e.g., ephemeral ports for clients, well-known ports for servers). |
| **Sequence/Acknowledgment Numbers** | Tracked the incrementing sequence and acknowledgment numbers, which ensure ordered and reliable delivery of data segments. |

**Security Implications & SOC Relevance:**

TCP is the backbone of many critical network services, making its analysis crucial for SOC operations. Understanding TCP communication patterns helps in:

*   **SYN Floods and DoS Attacks:** Anomalous patterns of SYN packets without corresponding SYN-ACKs or ACKs can indicate a SYN flood attack, a common Denial of Service (DoS) technique. SOC analysts monitor for such imbalances to detect and mitigate these attacks.
*   **Port Scanning:** Attackers often perform TCP port scans (e.g., SYN scans, Connect scans) to discover open ports and services on target systems. Wireshark analysis can reveal these scanning attempts by identifying numerous connection attempts to different ports from a single source.
*   **Session Hijacking:** While more complex to detect solely via packet capture, understanding normal TCP session behavior is a prerequisite for identifying deviations that might suggest session hijacking attempts.
*   **Application Layer Protocol Foundation:** TCP provides the reliable transport for protocols like HTTP, FTP, and SSH. Analyzing TCP streams helps in understanding the underlying communication issues or malicious activities at the application layer.
*   **Firewall Rule Validation:** Observing TCP connections helps validate whether firewall rules are correctly permitting or denying traffic as intended, preventing unintended exposure or blocking legitimate services.


### 5.3 DNS Traffic Analysis

**Traffic Generated:** Domain Name System (DNS) traffic was generated by performing domain name lookups from the Kali Linux virtual machine and the Windows host. DNS is a hierarchical and decentralized naming system for computers, services, or other resources connected to the Internet or a private network. It translates human-readable domain names into numerical IP addresses required for locating and identifying computer services and devices with the underlying network protocols.

**Wireshark Filter:** `dns`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **DNS Queries** | Captured standard DNS queries (Type A, AAAA, PTR) initiated by the client systems to resolve domain names to IP addresses. |
| **DNS Responses** | Observed corresponding DNS responses from the DNS server, providing the requested IP addresses or indicating resolution failures. |
| **Query/Response Correlation** | Successfully correlated DNS queries with their respective responses, demonstrating the full cycle of domain name resolution. |
| **Protocol Details** | Inspection of DNS packet details revealed query types, response codes, and resolved IP addresses, along with the DNS server involved. |

**Security Implications & SOC Relevance:**

DNS is a critical service, and its traffic is a rich source of information for SOC analysts, but also a common vector for attacks. Analyzing DNS traffic helps in:

*   **Malware Command and Control (C2):** Attackers frequently use DNS for C2 communications, often through techniques like DNS tunneling. This involves encoding malicious data within DNS queries and responses to bypass firewalls and intrusion detection systems. Unusual query patterns, long or random-looking subdomains, or queries to suspicious Top-Level Domains (TLDs) can be indicators of compromise.
*   **Data Exfiltration:** DNS can be used to exfiltrate small amounts of data by encoding it into subdomains of legitimate-looking queries. Monitoring for unusually large DNS query sizes or frequent queries to external, unknown domains is crucial.
*   **Phishing and Malicious Domains:** DNS queries to newly registered domains, domains with low reputation, or domains associated with known phishing campaigns can indicate user compromise or an impending attack. Threat intelligence feeds often include lists of malicious domains that can be cross-referenced with DNS logs.
*   **Reconnaissance:** Attackers may perform DNS enumeration to gather information about a target network, such as subdomains or internal hostnames. A high volume of diverse DNS queries from a single source could be a sign of reconnaissance.
*   **Network Troubleshooting:** Legitimate DNS traffic analysis helps in diagnosing connectivity issues, slow website loading, or misconfigured DNS servers. Anomalies can point to network infrastructure problems.
*   **MITRE ATT&CK T1071.004 (Application Layer Protocol: DNS):** This technique highlights how adversaries abuse DNS for C2. SOC analysts should be proficient in identifying indicators of this technique, such as frequent, unusual DNS requests or TXT/A record misuse for data transfer.


### 5.4 HTTP Traffic Analysis

**Traffic Generated:** Hypertext Transfer Protocol (HTTP) traffic was generated by accessing web resources from the Windows host. HTTP is the foundation of data communication for the World Wide Web, and in its unencrypted form, it allows for direct observation of web requests and responses.

**Wireshark Filter:** `http`

**Key Observations:**

| Observation Detail | Description |
| :----------------- | :---------- |
| **HTTP GET Requests** | Captured HTTP GET requests, indicating the client (Windows host) requesting resources (e.g., web pages, images) from a web server. |
| **HTTP Responses** | Observed corresponding HTTP responses from the server, including status codes (e.g., 200 OK) and the content of the requested resources. |
| **Plaintext Communication** | The entire communication, including URLs, headers, and data payloads, was visible in plaintext, demonstrating the lack of confidentiality in unencrypted HTTP. |
| **User-Agent Strings** | Identified User-Agent strings in HTTP headers, providing information about the client's browser and operating system. |

**Security Implications & SOC Relevance:**

Unencrypted HTTP traffic presents significant security risks and is a prime target for attackers. For SOC analysts, analyzing HTTP traffic is crucial for:

*   **Data Exposure:** The most critical implication of HTTP is the exposure of sensitive information (e.g., login credentials, personal data, session tokens) in plaintext. Attackers can easily intercept and read this data using tools like Wireshark, leading to compromise.
*   **Malware Delivery:** HTTP is a common protocol for delivering malware. SOC analysts can identify suspicious file downloads (e.g., executables, scripts) by inspecting HTTP requests and responses, especially if they originate from untrusted sources or involve unusual file types.
*   **Command and Control (C2):** Attackers often use HTTP for C2 communications, blending malicious traffic with legitimate web browsing. Indicators include frequent, unusual HTTP requests to suspicious domains, non-standard HTTP methods, or large amounts of data being uploaded (exfiltration) or downloaded.
*   **Phishing and Malicious Websites:** HTTP traffic to known malicious domains or IP addresses can indicate a successful phishing attempt or a user browsing to a compromised website. Correlating HTTP requests with threat intelligence feeds is essential.
*   **Web Application Attacks:** Analysis of HTTP requests can reveal attempts at web application attacks such as SQL injection, cross-site scripting (XSS), or directory traversal, by examining parameters and URL structures for malicious payloads.
*   **Policy Violations:** Monitoring HTTP traffic can help enforce organizational policies, such as preventing access to prohibited websites or detecting unauthorized data transfers.
*   **Transition to HTTPS:** The prevalence of HTTPS (HTTP Secure) has significantly reduced plaintext HTTP traffic. However, understanding HTTP remains vital for analyzing legacy systems, misconfigured applications, or when attackers intentionally downgrade connections.


## 6. SOC Use Cases

Network traffic analysis with Wireshark is an indispensable capability for Security Operations Center (SOC) analysts. The insights gained from deep packet inspection directly support various critical SOC functions, enabling proactive defense, efficient incident response, and continuous security posture improvement. This project demonstrates how Wireshark can be applied to the following key SOC use cases:

### 6.1 Network Baselining and Anomaly Detection

*   **Description:** Establishing a baseline of normal network behavior (e.g., typical protocol usage, traffic volumes, common communication patterns) is fundamental for identifying deviations. Wireshark allows for granular inspection of traffic to understand what constitutes "normal" in a specific environment.
*   **Application:** By capturing traffic during periods of normal operation, analysts can document expected ICMP, TCP, DNS, and HTTP behaviors. Any significant departure from this baseline—such as unusual port activity, unexpected protocol usage, or communication with rare external destinations—can trigger alerts for further investigation.

### 6.2 Incident Response and Forensic Investigation

*   **Description:** During an active incident or post-incident forensic analysis, Wireshark is crucial for understanding the attacker's actions, identifying the scope of compromise, and collecting evidence.
*   **Application:** If a system is suspected of compromise, capturing its network traffic can reveal Command and Control (C2) communications, data exfiltration attempts, or lateral movement. For instance, observing plaintext HTTP traffic might expose credentials, while unusual DNS queries could point to DNS tunneling. Wireshark allows for the reconstruction of communication flows (`Follow TCP Stream`) and extraction of embedded files, providing critical forensic artifacts.

### 6.3 Threat Hunting

*   **Description:** Threat hunting involves proactively searching for undetected threats within a network. This often relies on hypotheses about attacker tactics, techniques, and procedures (TTPs) and leveraging network visibility to find subtle indicators of compromise (IOCs).
*   **Application:** Using Wireshark, analysts can hunt for specific patterns that might evade automated detection systems. Examples include searching for specific User-Agent strings associated with known malware, identifying beaconing behavior (regular, small communications to external IPs), or looking for protocol anomalies (e.g., HTTP traffic on non-standard ports, DNS queries with unusually long subdomains).

### 6.4 Detection Engineering and Rule Validation

*   **Description:** SOC teams continuously develop and refine detection rules for their Security Information and Event Management (SIEM) systems or Intrusion Detection/Prevention Systems (IDS/IPS). Wireshark plays a vital role in validating these rules.
*   **Application:** After creating a new detection signature (e.g., for a specific C2 pattern), Wireshark can be used to generate and capture traffic that should trigger the rule. By analyzing the captured packets, engineers can confirm that the rule correctly identifies the malicious traffic without generating false positives, ensuring the effectiveness of the detection mechanism.

### 6.5 Network Troubleshooting and Performance Monitoring

*   **Description:** While primarily a security tool, Wireshark's ability to provide deep insight into network communications makes it invaluable for diagnosing network performance issues and connectivity problems.
*   **Application:** Analyzing TCP retransmissions, high latency in DNS queries, or excessive ICMP redirects can help pinpoint network bottlenecks or misconfigurations. This directly contributes to maintaining the availability and reliability of critical systems, which is also a security concern as network outages can be exploited by attackers.


## 7. MITRE ATT&CK Mapping

The MITRE ATT&CK framework provides a comprehensive knowledge base of adversary tactics and techniques based on real-world observations. Mapping network traffic analysis findings to this framework helps SOC analysts understand adversary behavior, develop more effective detection strategies, and communicate security risks in a standardized manner. This project's analysis of ICMP, TCP, DNS, and HTTP traffic directly relates to several ATT&CK techniques:

### 7.1 Reconnaissance (TA0043)

*   **Technique:** T1595.001 (Active Scanning: Scanning IP Blocks/Ranges) & T1595.002 (Active Scanning: Vulnerability Scanning)
*   **Relevance:** ICMP `ping` sweeps and TCP port scans (e.g., SYN scans) are common reconnaissance activities. Wireshark can detect these by identifying numerous ICMP echo requests or SYN packets directed at multiple hosts or ports, indicating an adversary attempting to discover active systems and open services.

### 7.2 Command and Control (TA0011)

*   **Technique:** T1071 (Application Layer Protocol) - Specifically T1071.001 (Web Protocols) and T1071.004 (DNS)
*   **Relevance:**
    *   **HTTP (T1071.001):** Adversaries frequently use HTTP for C2 communications, blending malicious traffic with legitimate web browsing. Wireshark analysis can reveal unusual HTTP requests, non-standard User-Agent strings, or frequent communications to suspicious domains, which may indicate C2 activity. The plaintext nature of HTTP also exposes potential C2 channels.
    *   **DNS (T1071.004):** As highlighted in the DNS analysis, adversaries can abuse DNS for C2 through techniques like DNS tunneling. This involves encoding malicious data within DNS queries and responses. Wireshark can identify indicators such as unusually long or random-looking subdomains, high-frequency queries to specific domains, or the use of non-standard DNS record types (e.g., TXT records for data transfer).

### 7.3 Exfiltration (TA0010)

*   **Technique:** T1041 (Exfiltration Over C2 Channel) & T1048 (Exfiltration Over Alternative Protocol)
*   **Relevance:** While not explicitly demonstrated as malicious in this basic lab, the ability to observe data within HTTP traffic (due to its unencrypted nature) and the potential for DNS tunneling illustrate how data could be exfiltrated. Large outbound HTTP POST requests or unusual DNS traffic volumes could indicate data leaving the network via C2 channels or alternative protocols.

### 7.4 Impact (TA0040)

*   **Technique:** T1498 (Denial of Service) - Specifically T1498.001 (Direct Network Attack)
*   **Relevance:** While not directly simulated, the analysis of TCP communication provides the foundation for understanding SYN flood attacks. Wireshark can identify a high volume of SYN packets without corresponding SYN-ACKs, which is a classic indicator of a Denial of Service attempt targeting network services.

By understanding these mappings, SOC analysts can leverage Wireshark to not only detect suspicious network activities but also to categorize them within a recognized framework, facilitating better threat intelligence sharing and defensive strategy development.


## 8. Recommendations

Based on the network traffic analysis conducted in this project and the identified security implications, the following recommendations are provided to enhance network visibility, improve threat detection, and strengthen incident response capabilities within a SOC environment:

1.  **Implement Network Segmentation:** Segment networks to limit the blast radius of potential breaches. This restricts lateral movement of attackers and allows for more focused monitoring of critical assets.
2.  **Deploy Network Intrusion Detection/Prevention Systems (NIDS/NIPS):** Utilize NIDS/NIPS solutions (e.g., Snort, Suricata) at strategic network chokepoints to detect and, where possible, prevent known attack patterns and suspicious activities based on signatures and behavioral analysis.
3.  **Centralize Log Management and SIEM Integration:** Aggregate network logs (e.g., NetFlow, firewall logs, DNS logs) and packet capture metadata into a Security Information and Event Management (SIEM) system. This enables correlation of events across different data sources, facilitating faster detection and more comprehensive incident investigations.
4.  **Enforce HTTPS Everywhere:** Mandate the use of HTTPS for all web traffic within the organization and encourage its use for external communications. This encrypts data in transit, protecting sensitive information from eavesdropping and tampering. For internal traffic, consider SSL/TLS decryption solutions where legally and ethically permissible to maintain visibility for security monitoring.
5.  **Implement DNS Security Best Practices:**
    *   **Monitor DNS Traffic:** Continuously monitor DNS queries for anomalies such as unusually long subdomains, high query volumes to suspicious domains, or the use of non-standard record types, which can indicate DNS tunneling or C2 activity.
    *   **Use DNS Filtering:** Deploy DNS filtering solutions to block access to known malicious domains and prevent resolution of suspicious domains.
    *   **Internal DNS Servers:** Ensure internal DNS servers are properly secured and configured to prevent unauthorized zone transfers and cache poisoning.
6.  **Regularly Baseline Network Traffic:** Periodically capture and analyze network traffic during normal operations to establish and update baselines. This helps in quickly identifying deviations that could signify new threats or changes in attacker TTPs.
7.  **Conduct Regular Security Awareness Training:** Educate users about common attack vectors, such as phishing and malicious links, to reduce the likelihood of initial compromise that often leads to network-based attacks.
8.  **Develop and Test Incident Response Playbooks:** Create and regularly update incident response playbooks specifically for network-based incidents (e.g., DoS attacks, C2 communications, data exfiltration). Conduct tabletop exercises and simulations to ensure the SOC team can effectively respond to these threats.
9.  **Leverage Threat Intelligence:** Integrate threat intelligence feeds into security tools (SIEM, NIDS/NIPS) to automatically detect and block communications with known malicious IP addresses, domains, and other indicators of compromise.
10. **Automate Packet Capture and Analysis:** For high-volume networks, explore automated packet capture solutions and tools that can extract metadata or alert on suspicious patterns, reducing the manual burden on analysts while still providing deep visibility when needed.

These recommendations, when implemented collectively, will significantly enhance an organization's ability to detect, analyze, and respond to network-based cyber threats, moving towards a more proactive and resilient security posture.


## 9. Limitations and Future Work

This project provided a foundational understanding of network traffic analysis using Wireshark within a controlled virtual environment. However, several limitations exist in the current setup, which also present opportunities for future work and more advanced studies:

### 9.1 Limitations

*   **Controlled Environment:** The lab setup is highly controlled and does not fully replicate the complexity and scale of a real-world enterprise network. Traffic volume, diversity of protocols, and the presence of encrypted communications are significantly reduced.
*   **Lack of Encryption:** The primary focus on unencrypted HTTP traffic simplifies analysis but overlooks the challenges posed by encrypted traffic (e.g., HTTPS, VPNs), which constitutes a significant portion of modern network communications. Analyzing encrypted traffic often requires SSL/TLS decryption capabilities, which were not part of this project.
*   **Limited Malicious Activity:** The project primarily focused on generating benign traffic patterns. While security implications were discussed, actual malicious traffic generation and analysis (e.g., live malware execution, advanced persistent threats) were outside the scope of this basic setup.
*   **Manual Analysis:** The analysis was largely manual, which is not scalable for large networks. Real-world SOC operations rely heavily on automated tools and SIEM systems for initial triage and correlation.
*   **Static Capture:** The project involved analyzing static `.pcap` files. Continuous, real-time monitoring and analysis of network traffic, as performed in a live SOC, present different challenges and require different tools and approaches.

### 9.2 Future Work

To build upon the foundation established in this project, future work could include:

*   **Advanced Protocol Analysis:** Investigate more complex protocols and their security implications, such as SMB, RDP, FTP, and various VPN protocols.
*   **Encrypted Traffic Analysis:** Incorporate SSL/TLS decryption techniques (e.g., using a proxy or tapping into endpoints) to analyze encrypted traffic and identify hidden threats.
*   **Malware Traffic Analysis:** Introduce controlled malware samples into the lab environment to analyze their network communication patterns, including C2 beaconing, data exfiltration, and lateral movement attempts.
*   **Integration with Security Tools:** Integrate Wireshark analysis with other security tools such as Suricata/Snort (for IDS/IPS rule development), Zeek (for network security monitoring), and a basic SIEM (for log aggregation and correlation).
*   **Automation and Scripting:** Develop scripts (e.g., using Python with Scapy or Tshark) to automate repetitive analysis tasks, extract specific indicators of compromise (IOCs), and generate custom reports.
*   **Cloud Network Traffic Analysis:** Extend the analysis to cloud environments, understanding how tools like Wireshark can be used to inspect traffic in cloud-native network architectures.
*   **Threat Hunting Scenarios:** Design and execute specific threat hunting scenarios within the lab, focusing on identifying stealthy adversary techniques and validating detection hypotheses.

By addressing these limitations and pursuing the suggested future work, the practical skills in network traffic analysis can be further honed, providing a more comprehensive and realistic understanding of cybersecurity operations.
## References

[1] CyberDefenders. (2026, January 25). *The Ultimate Guide to Network Traffic Analysis for SOC Analysts: How to Detect, Investigate, and Respond to Network-Based Attacks*. [https://cyberdefenders.org/blog/the-ultimate-guide-to-network-traffic-analysis-for-soc-analysts/](https://cyberdefenders.org/blog/the-ultimate-guide-to-network-traffic-analysis-for-soc-analysts/)

[2] Emmanuel, G. (2025, July 12). *SOC Analyst Lab — Network Analysis (Malware) using Wireshark*. Medium. [https://medium.com/@gboyegaemmanuel0/soc-analyst-lab-network-analysis-malware-using-wireshark-d8a5129ed9eb](https://medium.com/@gboyegaemmanuel0/soc-analyst-lab-network-analysis-malware-using-wireshark-d8a5129ed9eb)

[3] MITRE. (n.d.). *ATT&CK® - Enterprise*. [https://attack.mitre.org/](https://attack.mitre.org/)
