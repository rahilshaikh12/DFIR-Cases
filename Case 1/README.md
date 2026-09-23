# DFIR Incident Report: Lockdown Lab

## 1. Executive Summary
An attacker performed targeted enumeration against the HTTP service on the IIS server. Following this reconnaissance, the attacker initiated an SMB connection and successfully planted a web-accessible payload. A misconfigured SMB shares folder allowed the attacker to write an `.aspx` file containing the code to contact a Command and Control (C2) server. The server subsequently executed the code and downloaded the final payload.

## 2. Timeline of Events
| Timestamp (UTC) | Event Description |
| :--- | :--- |
| 2024-09-10 05:47:33 | First tree connect request to `10.0.2.15\IPC$` |
| 2024-09-10 05:47:33 | Second tree connect request to `10.0.2.15\Documents` |
| 2024-09-10 05:48:52 | Payload (`shell.aspx`) Create Request initiated |
| 2024-09-10 05:48:52 | Create Response observed |
| 2024-09-10 05:48:53 | Payload successfully written to server |
| 2024-09-10 05:49:51 | Payload executes and contacts C2 |

## 3. Indicators of Compromise (IoCs)
| Indicator Type | Value | Context |
| :--- | :--- | :--- |
| **IPv4** | `[Attacker IP]` | Source of SMB connection / Enumeration |
| **IPv4** | `[C2 IP]` | Destination for payload callback |
| **File** | `shell.aspx` | Initial web-accessible payload |
| **Hash (SHA256)**| `[Insert Hash]` | SHA256 hash of `shell.aspx` |

## 4. MITRE ATT&CK Mapping
| Tactic | Technique | ID | Application to Case |
| :--- | :--- | :--- | :--- |
| **Reconnaissance** | Active Scanning | T1595 | Enumeration against the HTTP service on the IIS server. |
| **Initial Access** | Exploit Public-Facing Application | T1190 | Exploitation via misconfigured SMB share to upload payload. |
| **Execution** | Command and Scripting Interpreter | T1059 | Execution of the `.aspx` script on the IIS server. |
| **Command and Control** | Application Layer Protocol | T1071 | Payload contacts the C2 server to download subsequent stages. |

## 5. Methodology & Tools Used
*   **Wireshark:** Analyzed the PCAP file to trace the SMB connection and payload delivery.
*   **Volatility 3:** Conducted memory forensics to identify malicious processes.
*   **PEStudio:** Performed static malware analysis on the extracted payload to identify strings and capabilities.
*   **VirusTotal:** Verified file hashes against known malware signatures.

## 6. Detailed Findings
The investigation revealed that malware was pushed onto the device utilizing an open or authenticated SMB connection, exploiting the misconfigured document share to achieve code execution via the IIS web directory.

*(Screenshot Example below - remove this text and uncomment the image link when ready)*

*Figure 1: Wireshark capture showing the SMB tree connect request and payload transfer.*

## 7. Personal Reflection & Skills Development
Based on this investigation, I have identified the following areas for continuous improvement and study:
*   **Process Identification:** Deepen my understanding of normal vs. anomalous child-parent process relationships in Windows environments.
*   **Web Shells:** Study the structure and execution flow of `.aspx` files and how IIS handles them during an attack.
*   **Wireshark Profiling:** Customize Wireshark packet color rules to identify malicious SMB and C2 traffic more efficiently in future PCAPs.
