# SOP thu thap bang chung volatile - collecting-volatile-evidence-from-compromised-host

Command va quy trinh chi tiet. SKILL.md giu lai cac buoc 1-7; file nay giu command va mau bieu mau.

## Step 1 - Chuan bi thu thap

```bash
EVIDENCE_DIR="C:\Forensics\$(hostname)_$(Get-Date -Format yyyyMMdd_HHmmss)"
mkdir -p "$EVIDENCE_DIR"
cd "$EVIDENCE_DIR"
```

## Step 2 - Ghi nhan trang thai he thong

```bash
# System info
hostname > system_info.txt
ifconfig -a >> system_info.txt 2>/dev/null
ipconfig /all >> system_info.txt 2>$null # Windows

# Current user
whoami > current_user.txt
query user > logged_in_users.txt 2>$null # Windows
w > w_output.txt 2>/dev/null # Linux

# System date/time
date -u > system_time_utc.txt
wmic os get localdatetime > system_time.txt 2>$null # Windows
```

Ghi them moc thoi gian phap ly chua den han su kien vao bao cao: xem `../../_shared/compliance-vietnam.md`.

## Step 3 - Capture memory (quan trong nhat)

**Windows:**
```bash
# WinPmem
winpmem_mini_x64.exe memory.raw

# DumpIt
DumpIt.exe
# Creates .raw file in current directory
```

**Linux:**
```bash
# AVML
./avml output.lime

# LiME
insmod lime.ko "path=output.lime format=lime"
```

## Step 4 - Thu thap ket noi mang

```bash
# Windows
netstat -anob > network_connections.txt
netstat -ano > netstat_ano.txt

# Linux
ss -tunap > ss_output.txt
netstat -tunap > netstat_output.txt
cat /proc/net/tcp > proc_net_tcp.txt
```

## Step 5 - Capture thong tin tien trinh

```bash
# Windows
tasklist /v > tasklist.txt
wmic process list brief > process_list.txt

# Linux
ps auxwwf > ps_output.txt
cat /proc/*/cmdline 2>/dev/null | tr '\0' ' ' > proc_cmdline.txt

# DLLs loaded
listdlls.exe -accepteula > loaded_dlls.txt 2>$null # Windows
```

## Step 6 - Capture phien dang dang nhap

```bash
# Windows
query user > logged_in_users.txt
query session > active_sessions.txt
net session > net_sessions.txt 2>&1

# Linux
who > who_output.txt
last -50 > last_logins.txt
```

## Step 7 - Capture cau hinh he thong

```bash
# Scheduled tasks
schtasks /query /fo CSV /v > scheduled_tasks.csv 2>$null # Windows
crontab -l > crontab.txt 2>/dev/null # Linux

# Services
sc queryex type=service state=all > services.txt 2>$null # Windows
systemctl list-units --type=service --all > services.txt 2>$null # Linux

# Registry autoruns
reg export "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" autorun_hklm.reg /y 2>$null
```

Scheduled task moc keo theo T1053.005, Run key theo T1547.001, chay lenh shell theo T1059.001.

## Step 8 - Chain of custody

```bash
# Hash all evidence
sha256sum * > evidence_manifest.sha256

# Create chain of custody record
cat > chain_of_custody.txt << EOF
CHAIN OF CUSTODY RECORD
========================
Case ID: IR-YYYY-NNN
Collection Date: $(date -u)
Collected By: $(whoami)
System: $(hostname)
Collection Method: Live forensic collection

Evidence Items:
$(ls -la)

SHA256 Manifest: evidence_manifest.sha256
EOF
```

## Khai niem then chot

| Concept | Description |
|---------|-------------|
| Order of Volatility | Collect most volatile first |
| Live Forensics | Evidence from running system |
| Chain of Custody | Documentation of handling |
| Forensic Soundness | Not altering evidence |
| Trusted Tools | From external media |
| Evidence Integrity | SHA256 hashing |

## Cong cu thu thap

| Tool | Purpose |
|------|---------|
| WinPmem | Windows memory acquisition |
| AVML | Linux memory acquisition |
| KAPE | Automated artifact collection |
| Velociraptor | Remote forensic collection |
| CyLR | Cross-platform live response |
| Sysinternals | Process/handle analysis |
