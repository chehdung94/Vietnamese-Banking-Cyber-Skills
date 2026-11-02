# Quy trinh dieu tra ky thuat - playbook-after-hours-internal-access-investigation

Cac lenh PowerShell duoi day la **mau tham khao de phan tich**. Lenh kham phuc
(vo hieu hoa tai khoan, cach ly may) chi duoc DE XUAT, khong chay tu dong.

## 1. Phase 1 - Phan tich ket noi RDP

### 1.1 Lay su kien RDP tren may dich (User B)

```powershell
# Event 4624: account logged on successfully - Logon Type 10 = RemoteInteractive (RDP)
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4624
    StartTime = (Get-Date).AddDays(-1)
} | Where-Object { $_.Message -like "*Logon Type:*10*" } |
Select-Object TimeCreated, Id, LevelDisplayName, Message

# Event 4778: session reconnected
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4778
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Message

# Event 4779: session disconnected
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4779
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Message
```

Thong tin can trich xuat:

- Source IP (may cua User A).
- Logon time.
- Logoff time (neu co).
- Thoi luong phien.
- Ten workstation nguon.

### 1.2 Lay su kien explicit credential tren may nguon (User A)

```powershell
# Event 4648: logon attempted using explicit credentials
# Cho thay User A co tinh dung credential de ket noi den may cua User B
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4648
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Message
```

Thong tin can trich xuat:

- Thoi diem su dung credential.
- Target server (may cua User B).
- Ten tien trinh (mstsc.exe = RDP client).
- Dia chi IP cua dich.

### 1.3 Timeline phien RDP

```
RDP Session Timeline:
[Time]          [Event]                           [Source]
[User A login tai workstation]     [Azure AD]
[Event 4648 - explicit credential] [May cua User A]
[Event 4624 Type 10 - RDP success] [May cua User B]
[Session established]              [May cua User B]
[File access]                      [Varonis]
[Event 4779 - session disconnect]  [May cua User B]
[User A logout]                    [Azure AD]

Session duration: [duration]
Files accessed: [list]
Data transferred: [volume]
```

## 2. Phase 2 - Dieu tra nguon (User A)

### 2.1 Phan tich xac thuc cua User A

```powershell
# Cach User A xac thuc truoc khi RDP
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '[UPN]'" |
    Where-Object { $_.CreatedDateTime -gt (Get-Date).AddHours(-2) } |
    Select-Object CreatedDateTime, IPAddress, Location, DeviceDetail, Status
```

Cau hoi can tra loi:

- User A dang nhap tu dau (van phong, VPN, ben ngoai)?
- User A co mat tai van phong khong (badge logs)?
- User A dung thiet bi nao (cong ty hay ca nhan)?
- User A co dau hieu bi chiem (impossible travel, thiet bi la)?

### 2.2 Phan tich hanh vi cua User A

| Check | Binh thuong | Quan sat | Rui ro |
|---|---|---|---|
| Login location | Van phong / VPN | IP ben ngoai | Cao |
| Login time | Trong gio lam viec | Ngoai gio lam viec | Cao |
| Device | May cong ty | Thiet bi la | Cao |
| Hoat dong truoc RDP | Cong viec binh thuong | Hoat dong nghi | Cao |
| Pattern RDP | Chi IT support | Nguoi dung ngau nhien truy cap | Critical |

Muc phan hoi theo gio lam viec lay tu `../_shared/off-hours-and-baseline.md`.

**Leo thang:** neu User A co dau hieu bi chiem -> phan cap theo
`../_shared/severity-and-escalation.md`, cach ly User A.

## 3. Phase 3 - Dieu tra dich (User B)

### 3.1 Phan tich hoat dong sau RDP

```powershell
# Event 4663: attempt to access an object
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4663
    StartTime = (Get-Date).AddHours(-2)
} | Select-Object TimeCreated, Message

# Process execution (Sysmon)
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Sysmon/Operational'
    ID = 1
    StartTime = (Get-Date).AddHours(-2)
} | Select-Object TimeCreated, Message
```

Hoat dong can xac dinh:

- Tep nao duoc truy cap (co du lieu nhay khong).
- Un dung nao duoc mo (email, browser, file manager).
- Ket noi mang (upload cloud).
- Thiet bi USB da ket noi.
- Lenh da chay (PowerShell, CMD).

### 3.2 Mau ket qua phan tich (vi du)

```json
{
  "user_b_computer_analysis": {
    "rdp_session": {
      "start_time": "[timestamp]",
      "end_time": "[timestamp]",
      "duration_minutes": "[duration]",
      "source_user": "[user]"
    },
    "files_accessed": [
      {
        "path": "[duong dan tep]",
        "time": "[timestamp]",
        "action": "Read",
        "sensitivity": "[High|Critical]"
      }
    ],
    "applications_opened": ["[ten chuong trinh]"],
    "network_connections": [
      {
        "destination": "[dich]",
        "port": "[port]",
        "bytes_sent": "[so byte]"
      }
    ]
  }
}
```

## 4. Phase 4 - Pham vi lateral movement

### 4.1 Kiem tra cac dich khac

```powershell
# Kiem tra User A co RDP den nhieu may khong
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4648
    StartTime = (Get-Date).AddDays(-1)
} | Where-Object { $_.Message -like "*mstsc.exe*" } |
Group-Object { $_.Message -match "Target Server: (.*)" } |
Select-Object Name, Count
```

Danh gia pham vi:

- User A chi RDP den User B hay nhieu may?
- Co dau hieu lan rong nhu worm khong?

### 4.2 Phan tich RDP cache

```powershell
# RDP Bitmap Cache location
# C:\Users\<user>\AppData\Local\Microsoft\Terminal Server Client\Cache

Get-ChildItem -Path "C:\Users\<user>\AppData\Local\Microsoft\Terminal Server Client\Cache" -Recurse

# Hash cho bang chung
Get-FileHash -Path "C:\Users\<user>\AppData\Local\Microsoft\Terminal Server Client\Cache\*" -Algorithm SHA256
```

RDP cache co the chua anh chup cua phien, can thu thap cho forensic.

## 5. Skill Tier 3 duoc goi

| Skill | Vai tro |
|---|---|
| `../analyzing-azure-ad-signin-logs/SKILL.md` | Xac thuc cua User A, dia diem, thiet bi |
| `../analyzing-ueba-alerts-varonis/SKILL.md` | Phan tich truy cap tep cua User B |
| `../collecting-volatile-evidence-from-compromised-host/SKILL.md` | Memory, tien trinh, ket noi mang |
| `../performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md` | Phan tich RDP cache, Jump List, Prefetch |
| `../analyzing-windows-registry-for-artifacts/SKILL.md` | Registry: USB, clipboard, persistence |
