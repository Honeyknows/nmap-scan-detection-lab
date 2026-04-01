# nmap-scan-detection-lab

> **Red Team Attacks | Blue Team Detects**  
> A hands-on home lab documenting 19 Nmap scan types — every scan performed, captured in Wireshark, and analyzed from both attacker and defender perspectives.

---

## Overview

This lab explores how Nmap works at the packet level and how each scan type appears to a blue team analyst monitoring network traffic. Every scan was executed on a controlled two-VM home lab environment (Kali Linux attacker vs. Kali Linux victim) running on VirtualBox with a Host-Only network adapter.

The goal is not just to run scans — it is to understand what each scan leaves behind, how it gets detected, and why that matters for a SOC analyst.

---

## Lab Environment

| Component | Details |
|---|---|
| Attacker | Kali Linux — 192.168.29.14 |
| Victim | Kali Linux — 192.168.29.115 |
| Network | VirtualBox Host-Only Adapter |
| Capture Tool | Wireshark (running on victim before every scan) |
| Services on Victim | Apache2 (HTTP/80), vsftpd (FTP/21), OpenSSH (SSH/22) |

---

## Scans Covered

### Basic Scans
| # | Scan | Command |
|---|---|---|
| 1 | Ping Sweep | `nmap -sn 192.168.29.0/24` |
| 2 | SYN Scan | `nmap -sS 192.168.29.115` |
| 3 | NULL Scan | `nmap -sN 192.168.29.115` |
| 4 | FIN Scan | `nmap -sF 192.168.29.115` |

### Advanced Scans
| # | Scan | Command |
|---|---|---|
| 5 | UDP Scan | `nmap -sU --top-ports 100 192.168.29.115` |
| 6 | ARP Discovery | `nmap -PR -sn 192.168.29.0/24` |
| 7 | Version Detection | `nmap -sV 192.168.29.115` |
| 8 | OS Detection | `nmap -O 192.168.29.115` |
| 9 | Aggressive Scan | `nmap -A 192.168.29.115` |

### Evasion Techniques
| # | Scan | Command |
|---|---|---|
| 10 | Fragmentation | `nmap -sS -f 192.168.29.115` |
| 11 | Decoy Scan | `nmap -sS -D RND:5 192.168.29.115` |
| 20 | Combined Evasion | `nmap -f -T1 -n -Pn --data-length 200 -D RND:5 192.168.29.115` |

### Timing Templates
| # | Scan | Command |
|---|---|---|
| 12 | T5 Insane | `nmap -sS -T5 192.168.29.115` |
| 13 | T1 Sneaky | `nmap -sS -T1 192.168.29.115` |
| 14 | Rate Limited | `nmap -sS --max-rate 10 192.168.29.115` |

### NSE Scripts
| # | Scan | Command |
|---|---|---|
| 15 | Default Scripts | `nmap -sC 192.168.29.115` |
| 16 | Banner Grab | `nmap --script=banner 192.168.29.115` |
| 17 | HTTP Title | `nmap --script=http-title 192.168.29.115` |
| 18 | SMB Enumeration | `nmap --script smb-enum-shares 192.168.29.115` |
| 19 | Vulnerability Scan | `nmap --script=vuln 192.168.29.115` |

---

## Repository Structure

```
nmap-scan-detection-lab/
│
├── README.md
│
├── nmap_guide.pdf              # Full lab guide with commands, outputs, and analysis
│
├── pcaps/                      # Wireshark capture files, one per scan types
│
└── screenshots/                # Wireshark and terminal screenshots per scan
    
```

---

## Key Takeaways

- **SYN scan** never completes the handshake — RST after every SYN/ACK is the detection signature
- **NULL and FIN scans** produce zero-flag packets that never appear in real application traffic — one packet is enough to trigger an alert
- **T1 timing** bypasses real-time IDS entirely — catching it requires a SIEM with a 24-hour correlation window
- **Decoy scans** are detected by timing analysis — real distributed traffic never synchronises this precisely
- **Combined evasion** (fragmentation + decoys + T1 + padding) still leaves behavioral traces that a SIEM catches
- **Behavioral detection always beats IP blocking** — the underlying pattern survives even when every visible characteristic is changed

---

## Tools Used

- **Nmap 7.9x** — scanning and scripting
- **Wireshark** — packet capture and analysis
- **VirtualBox** — virtualization
- **Kali Linux** — both attacker and victim VMs

---

## Author

**Honey**  
Final-year CSE student | Cybersecurity enthusiast | Targeting SOC Analyst roles  
[GitHub](https://github.com/Honeyknows) · [LinkedIn](https://linkedin.com/in/honeyknows)
