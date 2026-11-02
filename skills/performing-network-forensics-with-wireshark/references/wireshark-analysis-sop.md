# SOP phan tich pcap - performing-network-forensics-with-wireshark

Command tshark va quy trinh chi tiet. SKILL.md giu lai cac buoc 1-6; file nay giu command.

## Step 1 - Danh gia ban dau

**File Properties:**
```bash
# Get pcap statistics without opening GUI
tshark -r capture.pcap -q -z io,phs

# Check file type and size
ls -lh capture.pcap
file capture.pcap
```

## Step 2 - Xac dinh endpoint

```bash
# List all unique IP addresses
tshark -r capture.pcap -Y "ip" -T fields -e ip.src | sort -u

# List all conversations
tshark -r capture.pcap -q -z conv,ip
```

## Step 3 - Phan tich DNS

```bash
# Extract DNS queries
tshark -r capture.pcap -Y "dns" -T fields -e ip.src -e dns.qry.name

# Look for suspicious domains
tshark -r capture.pcap -Y "dns" | grep -E "(darknet|tor|exfil)"
```

## Step 4 - HTTP/HTTPS

```bash
# Extract HTTP URLs
tshark -r capture.pcap -Y "http.request.method == GET" -T fields -e ip.src -e http.host -e http.request.uri

# Export objects from pcap
$and = "http"
for ($i=0; $i -lt 10; $i++) {
    Export-Objects -Path . -Index $i -Type $and
}
```

## Step 5 - Phat hien chi bao C2

```bash
# Detect beaconing (regular intervals)
tshark -r capture.pcap -Y "http" -T fields -e ip.src -e ip.dst -e frame.time_delta | sort | uniq -c | sort -rn

# Look for suspicious user agents
tshark -r capture.pcap -Y "http.user_agent" -T fields -e http.user_agent | sort | uniq -c | sort -rn
```

## Step 6 - Tai khoi su kien

```bash
# Follow TCP stream
tshark -r capture.pcap -Y "ip.addr == [IP] and tcp.port == 443" -z "follow,tcp,ascii,0"

# Extract files from HTTP
tshark -r capture.pcap -Y "http.file_data" --export-objects "http,exported_files"
```

## Cong cu phu tro

| Tool | Purpose |
|------|---------|
| Wireshark | GUI packet analysis |
| tshark | Command-line packet analysis |
| CapLoader | Large pcap rapid analysis |
| NetworkMiner | Automatic artifact extraction |

## Quy tac phan tich

1. Capture chi phan anh mot khoang thoi gian. Luon ghi ro khoang thoi gian capture trong bao cao.
2. Moi ket luan phai tro ve mot goi tin hoac stream cu the (chi tiet filter dang dung).
3. So sanh voi baseline truoc khi goi luu luong la "bat thuong"; xem
   `../../_shared/off-hours-and-baseline.md` cho cach lay baseline.
4. Khong ket luan exfiltration tu chi phi byte - can bang chung ve noi dung hoac pham vi du lieu.
5. File tai tu pcap (exported objects) phai duoc hash SHA-256 truoc khi dua vao danh sach IOC.
