---
name: performing-network-forensics-with-wireshark
description: "Analyze PCAP captures with tshark to reconstruct sessions, extract transferred objects and identify C2 or exfiltration traffic. Use when a .pcap or .pcapng file is provided for a DFIR case. Not for firewall or proxy log analysis and not for live traffic interception."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: network-forensics
mitre_attack:
  - T1071
  - T1048
  - T1021.001
  - T1110
nist_csf:
  - DE.CM-1
  - DE.CM-3
  - DE.AE-1
  - DE.AE-3
tags:
  - network-forensics
  - wireshark
  - packet-analysis
  - traffic-analysis
  - DFIR
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Performing Network Forensics with Wireshark

## Overview

Phan tich goi tin cho phep tai khoi hanh dong cua ke tu, xac nhan C2 va phat hien dau hieu
du lieu bi rut ra ngoai khi bang chung dia va RAM khong con. Skill nay dung tshark va Wireshark
de phan tich file capture.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## When to Use

- Dieu tra cuoc tan cong dua tren mang.
- Xac nhan giao tiep voi may chay C2.
- Tai khoi duong dan di chuyen cua ke tu.
- Doi chieu va xac nhan timeline su co.
- Phat hien du lieu bi rut ra ngoai.
- Phan tich bat thuong giao thuc.

**Khong dung cho skill nay:** phan tich log firewall/proxy/DNS (dung skill phu hop dang co),
phap tich capture truc tiep tren duong truyen (khong thuoc pham vi skill nay), hoac phan tich
dia/RAM (dung `../performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md` va
`../conducting-memory-forensics-with-volatility/SKILL.md`).

## Prerequisites

- File capture: `.pcap` hoac `.pcapng`, kem hash SHA-256.
- tshark (Wireshark) va tshark filters can dung.
- So do mang va danh sach dia chi IP/trang thuoc noi bo trong pham vi.
- Moc thoi gian su co va khoang thoi gian capture bao phu.
- Baseline luu luong chuan de so sanh (tham chieu `../_shared/off-hours-and-baseline.md`).
- Danh sach dich vu/dich vu hop le va danh sach cho phep de doi chieu IOC.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu khong co file capture, hoac khong biet capture bao phu khoang thoi gian nao, dung - ket luan
ve hanh dong se khong co gia tri.

## Execution Steps

### Step 1: Danh gia file capture

Ghi ten file, kich thuoc, hash SHA-256, va khoang thoi gian capture. Chay `tshark -q -z io,phs`
de lay thong ke protocol va so goi tin.

### Step 2: Xac dinh endpoint va conversation

Liet ke dia chi IP duy nhat va bang conversation (`-z conv,ip`). Phan tach host noi bo va host
ngoai theo so do mang. Ket qua nay la danh sach endpoint de doi chieu.

### Step 3: Phan tich DNS

Trich xuat `dns.qry.name` kem `ip.src`. So sanh voi danh sach dich vu hop le. Truy van lai
lap lai va ten subdomain la doi tuong can dieu tra.

### Step 4: Phan tich HTTP/HTTPS

Trich xuat host, URI, user-agent va tai doi giao thuc. Export object trong HTTP de lay file
da truyen, hash SHA-256 moi file truoc khi dua vao danh sach IOC.

### Step 5: Phat hien chi bao C2 va exfiltration

Ap dung bang chi bao tai `references/thresholds.md`: beaconing, DNS tunneling, user-agent khac
thuong, luong du lieu lon ra ngoai, giao thuc lao ra ngoai. Moi chi bao phai kem bang chung
filter cu the.

### Step 6: Tai khoi timeline va phan cap

Dung `follow,tcp` va `follow,udp` de doc noi dung stream, dua vao timeline su co. Chuan hoa moc
thoi gian theo `../_shared/off-hours-and-baseline.md`. Phan cap theo
`../_shared/severity-and-escalation.md`, chi de xuat hanh dong khac phuc (Rules dong 6).

Chi tiet command: `references/wireshark-analysis-sop.md`.

## Expected Output

1. **Bao cao** (markdown): thong tin file capture, danh sach endpoint, ket qua DNS/HTTP, bang chi
   bao C2 va exfiltration, danh sach IOC (IP, domain, URL, hash), phan cap P1-P4, hanh dong de
   xuat, gioi han phan tich.
2. **JSON** (mau):

```json
{
  "skill": "performing-network-forensics-with-wireshark",
  "status": "ok",
  "scope": {
    "capture_file": "",
    "sha256": "",
    "capture_window": "",
    "total_packets": 0
  },
  "summary": {},
  "findings": [
    {
      "indicator": "",
      "filter_used": "",
      "assessment": "",
      "evidence": "",
      "hypothesis": ""
    }
  ],
  "indicators": {
    "ips": [],
    "domains": [],
    "urls": [],
    "hashes": []
  },
  "severity": "P2",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

## References

- `references/thresholds.md` - bang nguong phan tich va bang chi bao C2/exfiltration.
- `references/wireshark-analysis-sop.md` - command tshark theo buoc va quy tac phan tich.
- `../_shared/mitre-nist-mapping.md` - danh sach ID MITRE ATT&CK v18 va NIST CSF 2.0 duy nhat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly.
- `../_shared/off-hours-and-baseline.md` - chuan hoa moc thoi gian va baseline luu luong.
- Wireshark User Guide: https://www.wireshark.org/docs/wsug_html_chunked/
- SANS FOR572 - Network Forensics.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
