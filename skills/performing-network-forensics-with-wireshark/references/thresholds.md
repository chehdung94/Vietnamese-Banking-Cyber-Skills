# Bang nguong va chi bao - performing-network-forensics-with-wireshark

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi mo hinh mang, thiet bi bi theo doi hoac quy trinh -> cham lai va ghi ngay thay doi o cuoi file.

## Bang nguong phan tich luu luong

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| File pcap duoc cung cap | 1 file .pcap/.pcapng | Khong phan tich khi chua co file capture | Yeu cau dau vao cua skill |
| Hash SHA-256 cua file capture | 1 file | Khong bao cao ket qua khi khong xac dinh duoc file goc | Quy trinh bao toan bang chung noi bo |
| Baseline luu luong chuan | 1 baseline theo tuong tai phuong | Khong ket luan "bat thuong" khi chua co baseline de doi chieu | `../../_shared/off-hours-and-baseline.md` |

## Bang chi bao C2 va exfiltration

Moi chi bao phai co bang chung tu pcap. Khong co pcap thi ghi `NOT_AVAILABLE`.

| Chi bao | Dieu kien phat hien | Hanh dong | Nguon |
|---|---|---|---|
| Beaconing HTTP(S) | Cung mot `ip.dst`/`http.host` lap lai nhieu lan voi khoang thoi gian deu | Doi chieu voi proxy log | Khong co bang so nguong duy nhat trong repo - can baseline |
| DNS nhieu lan | Cung mot `dns.qry.name` bi query lap lai trong mot khoang thoi gian | Doi chieu voi DNS log | Can baseline |
| DNS tunneling | `dns.qry.name` dai bat thuong, nhieu subdomain, hoac query size lon | Doi chieu voi DNS log va ten mien | Can baseline |
| Tung HTTP sang domain la | `http.host` la domain chua nam trong danh sach dich vu hop le | Doi chieu voi proxy/allowlist | Can danh sach dich vu hop le - `NOT_AVAILABLE` neu chua co |
| User-agent khac thuong | `http.user_agent` khong khop danh sach UA chuan cua he thong | Doi chieu voi tai khoan dung UA | Can danh sach UA chuan - `NOT_AVAILABLE` neu chua co |
| Luong du lieu lon ra ngoai | Tong byte tu host noi trong sang host ngoai lon bat thuong so voi baseline | Doi chieu voi proxy/DLP | Can baseline |
| SMB/ RDP ra ngoai | `tcp.port` tuong ung tro ra ngoai viet trong ngoai | Doi chieu voi danh sach cho phep | Can danh sach cho phep - `NOT_AVAILABLE` neu chua co |

## Phan cap cho tung chi bao

Phan cap lay tu `../../_shared/severity-and-escalation.md` (nguon duy nhat).

| Chi bao | Dieu kien phan cap |
|---|---|
| Da rut du lieu khach hang ra ngoai, co bang chung | P1 |
| C2 da xac nhan | P1 |
| Lateral movement / truy cap khong duoc phep | P2 |
| Chi bao chua xac nhan | P3 hoac P4 theo muc thap nhat, khong tu nang |

## Ghi chu calibration

- Cong 80/443/8080/53/22 la port giao thuc pho bien, KHONG phai nguong phat hien. Chi la manh
  hoi duong dan phai diem tra.
- Capture chi phan anh thoi diem ghi. Khong co capture thi khong co bang chung mang nao.
- Moi gioi han ve dung luong pcap phai duoc ghi ro trong bao cao.
