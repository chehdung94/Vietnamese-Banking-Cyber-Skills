# Checklist bang chung - playbook-after-hours-internal-access-investigation

Agent: trinh bay checklist nay cho nguoi dung / SOC. Khong phan tich khi chua co du
bang chung bat buoc.

## 1. Bang chung ket noi RDP (uu tien cao nhat)

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **Windows Event 4624 (may dich - User B)** | RDP logon thanh cong | May cua User B | Pending |
| [ ] | **Windows Event 4625 (may dich - User B)** | RDP logon that bai | May cua User B | Pending |
| [ ] | **Windows Event 4648 (may nguon - User A)** | Su dung explicit credential | May cua User A | Pending |
| [ ] | **Windows Event 4778 / 4779** | Session reconnect / disconnect | May cua User B | Pending |
| [ ] | **TerminalServices-RemoteConnectionManager** | Chi tiet ket noi RDP | May cua User B | Pending |
| [ ] | **Network connection logs** | Luu luong cong RDP | Firewall, EDR | Pending |

## 2. Bang chung nguoi dung A (nguon)

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **User A - login history** | User A xac thuc bang cach nao | Azure AD, AD | Pending |
| [ ] | **User A - location** | User A dang nhap tu dau | VPN logs, Azure AD | Pending |
| [ ] | **User A - device** | User A dung thiet bi nao | Azure AD, EDR | Pending |
| [ ] | **User A - hoat dong truoc do** | User A lam gi truoc khi RDP | Varonis, EDR | Pending |
| [ ] | **User A - email/chat** | Lien he ve viec nay | Exchange, Teams | Pending |

## 3. Bang chung nguoi dung B (dich)

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **May User B - event logs** | Chuyen biet sau khi RDP | May cua User B | Pending |
| [ ] | **May User B - truy cap tep** | Tep nao duoc truy cap | Varonis, Windows Events | Pending |
| [ ] | **May User B - process execution** | Chuong trinh nao chay | EDR, Sysmon | Pending |
| [ ] | **May User B - network activity** | Co ket noi ra ngoai khong | EDR, Firewall | Pending |
| [ ] | **May User B - USB activity** | Co copy ra USB khong | Registry, EDR | Pending |
| [ ] | **Phong van User B** | User B biet gi | HR / Security | Pending |

## 4. Bang chung lateral movement

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **RDP Bitmap Cache** | Anh chup cua phien RDP | May cua User A | Pending |
| [ ] | **RDP Clipboard History** | Copy/paste giua cac may | Registry, Memory | Pending |
| [ ] | **Jump Lists** | Un dung mo trong RDP | May cua User B | Pending |
| [ ] | **Prefetch Files** | Un dung chay trong RDP | May cua User B | Pending |
| [ ] | **Network shares accessed** | Shares nao duoc truy cap | Windows Events | Pending |

## 5. Bang chung boi canh

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **IT support ticket** | Co ticket ho tro khong | ITSM | Pending |
| [ ] | **Manager approval** | Quan ly co biet khong | Email, phong van | Pending |
| [ ] | **User B - permission** | User B co cho phep khong | Phong van | Pending |
| [ ] | **Business justification** | Ly do chinh dang | Phong van, Email | Pending |
| [ ] | **HR records** | Mau thuan giua A va B | HR | Pending |

## 6. Truong du lieu can biet

| Nguon | Truong can co | Ghi chu |
|---|---|---|
| Windows Security | TimeCreated, Id, Message | Chu ky thoi gian va quy tac doi timezone: `../_shared/off-hours-and-baseline.md` |
| Azure AD sign-in | CreatedDateTime, IPAddress, Location, DeviceDetail, Status | Log o UTC: xem `../_shared/off-hours-and-baseline.md` |
| Varonis / file | timestamp, user, path, action, size | - |
| EDR | timestamp, process, user, hash | - |

Neu thieu truong bat buoc, tra `insufficient_evidence` va liet ke artefact con thieu.
