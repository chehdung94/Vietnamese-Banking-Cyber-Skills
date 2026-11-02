---
name: performing-network-forensics-with-wireshark
description: "Analyze PCAP captures with tshark to reconstruct sessions, extract transferred objects, and identify C2 or exfiltration traffic. Use when a .pcap or .pcapng file is provided."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.0.0
domain: cybersecurity
subdomain: network-forensics
tags:
  - [network-forensics
  - wireshark
  - packet-analysis
  - traffic-analysis
  - DFIR]
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Performing Network Forensics with Wireshark

## Overview

Network forensics is critical for understanding attacker methodology, identifying C2 communications, and reconstructing events when disk/memory evidence is unavailable. Wireshark and tshark provide comprehensive packet capture analysis capabilities.

## When to Use

- Investigating network-based attacks
- Identifying C2 communications
- Reconstructing attacker movements
- Validating incident timelines
- Detecting data exfiltration
- Analyzing protocol anomalies

## Prerequisites

- Packet capture files (.pcap, .pcapng)
- Network diagrams and logs
- Timeline of incident
- Understanding of normal baseline traffic

## Tools

| Tool | Purpose |
|------|---------|
| Wireshark | GUI packet analysis |
| tshark | Command-line packet analysis |
| CapLoader | Large pcap rapid analysis |
| NetworkMiner | Automatic artifact extraction |

## Steps

### Step 1: Initial Assessment

**File Properties:**
```bash
# Get pcap statistics without opening GUI
tshark -r capture.pcap -q -z io,phs

# Check file type and size
ls -lh capture.pcap
file capture.pcap
```

### Step 2: Identify Endpoints

```bash
# List all unique IP addresses
tshark -r capture.pcap -Y "ip" -T fields -e ip.src | sort -u

# List all conversations
tshark -r capture.pcap -q -z conv,ip
```

### Step 3: Analyze DNS Traffic

```bash
# Extract DNS queries
tshark -r capture.pcap -Y "dns" -T fields -e ip.src -e dns.qry.name

# Look for suspicious domains
tshark -r capture.pcap -Y "dns" | grep -E "(darknet|tor|exfil)"
```

### Step 4: Investigate HTTP/HTTPS

```bash
# Extract HTTP URLs
tshark -r capture.pcap -Y "http.request.method == GET" -T fields -e ip.src -e http.host -e http.request.uri

# Export objects from pcap
$and = "http"
for ($i=0; $i -lt 10; $i++) { 
    Export-Objects -Path . -Index $i -Type $and 
}
```

### Step 5: Detect C2 Indicators

**Common C2 Protocols:**
| Protocol | Indicators | Ports |
|----------|-----------|-------|
| HTTP/S | Long URI, uncommon headers | 80, 443, 8080 |
| DNS | High frequency, large responses | 53 |
| ICMP | Data exfiltration | N/A |
| SSH | Unusual times, large volumes | 22 |

**tshark C2 Detection:**
```bash
# Detect beaconing (regular intervals)
tshark -r capture.pcap -Y "http" -T fields -e ip.src -e ip.dst -e frame.time_delta | sort | uniq -c | sort -rn

# Look for suspicious user agents
tshark -r capture.pcap -Y "http.user_agent" -T fields -e http.user_agent | sort | uniq -c | sort -rn
```

### Step 6: Reconstruct Events

```bash
# Follow TCP stream
tshark -r capture.pcap -Y "ip.addr == 192.168.1.100 and tcp.port == 443" -z "follow,tcp,ascii,0"

# Extract files from HTTP
tshark -r capture.pcap -Y "http.file_data" --export-objects "http,exported_files"
```

## Output Format

```json
{
  "pcap_analysis": {
    "file": "capture.pcap",
    "duration": "HH:MM:SS",
    "total_packets": 0,
    "total_bytes": 0
  },
  "endpoints": {
    "internal": [],
    "external": []
  },
  "conversations": {
    "total": 0,
    "high_volume": []
  },
  "dns_findings": {
    "suspicious_domains": [],
    "high_frequency_queries": []
  },
  "http_findings": {
    "suspicious_urls": [],
    "malicious_user_agents": []
  },
  "c2_indicators": [],
  "ioc_list": {
    "ips": [],
    "domains": [],
    "urls": []
  },
  "reconstructed_timeline": []
}
```

## Key Findings for Banking Investigations

| Finding | Concern | Action |
|---------|---------|--------|
| Connection to Tor exit node | C2, anonymization | Block, escalate |
| DNS tunneling pattern | Data exfiltration | Block, investigate |
| Unusual HTTPS to foreign IP | C2, data theft | Block, IR |
| Large upload to external IP | Possible exfiltration | Block, assess data |
| SMB to external IP | Lateral movement | Block, incident |

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.

## References

- Wireshark User Guide
- SANS FOR572 (Network Forensics)
- Malware Analysis Cookbook
- MITRE ATT&CK Network Techniques
