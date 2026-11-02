# Kiem tra boi canh va quyet dinh - playbook-after-hours-internal-access-investigation

## 1. Checklist business justification

| Check | Cau hoi | Bang chung | Ket qua |
|---|---|---|---|
| [ ] | User A co phai IT support khong? | AD group, HR records | Yes/No |
| [ ] | Co ticket ho tro tu User B khong? | ITSM | Yes/No |
| [ ] | Ticket da duoc approve chua? | ITSM workflow | Yes/No |
| [ ] | User B co yeu cau ho tro khong? | Email, chat, phong van | Yes/No |
| [ ] | Quan ly co biet khong? | Email, phong van | Yes/No |
| [ ] | Co nam trong pham vi cong viec khong? | Job description | Yes/No |

## 2. Ma tran quyet dinh

| IT support | Ticket | User B biet | Quan ly biet | Ket luan | Phan cap |
|---|---|---|---|---|---|
| Yes | Yes | Yes | Yes | Hop le | Theo `../_shared/severity-and-escalation.md` |
| Yes | No | Yes | No | Vi pham chinh sach | Theo `../_shared/severity-and-escalation.md` |
| No | No | No | No | Truy cap khong duoc phep | Theo `../_shared/severity-and-escalation.md` |
| No | No | Yes | No | Ngh nho insider threat | Theo `../_shared/severity-and-escalation.md` |

Muc phan hoi va nguoi duoc bao lay tu `../_shared/severity-and-escalation.md`.

## 3. Kiem tra nhanh phan loai (quick assessment)

| Check | Tra loi Yes | Tra loi No |
|---|---|---|
| User A co quyen RDP den may User B khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |
| User A co phai IT support / helpdesk khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |
| Co ticket ho tro hop le khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |
| User B xac nhan cho phep khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |
| Co dau hieu rut du lieu khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |
| User A co dau hieu bi chiem khong? | theo `../_shared/severity-and-escalation.md` | theo `../_shared/severity-and-escalation.md` |

## 4. Phan loai tinh huong

| Tinh huong | Dau hieu | Hanh dong |
|---|---|---|
| **User A bi chiem** | Login tu dia diem la, sau do RDP den nhieu may | Cach ly User A, dieu tra pham vi |
| **Insider threat** | User A co history truy cap may User B, co mau thuan | Lien he HR + Legal |
| **Lam dung quyen** | User A co quyen admin, dung sai muc dich | Quan ly review, cap nhat chinh sach |
| **Hop le** | Co ticket, User B dong y, trong pham vi cong viec | Ghi nhan, dong su co |

## 5. Bao cao thong bao cho nguoi dung B (mau script)

```
"Chao [User B],

Chung toi phat hien co nguoi dung [User A] vua truy cap may tinh cua ban qua RDP
vao luc [thoi gian].

Ban co:
1. Yeu cau [User A] ho tro khong?
2. Biet ve viec nay khong?
3. Cho phep [User A] truy cap khong?

Thong tin nay dang duoc dieu tra. Vui long giu bi mat."
```

## 6. Bao dam bang chung phap ly

### 6.1 Thu thap theo uu tien

| Uu tien | Bang chung | Vi tri | Phuong thuc |
|---|---|---|---|
| 1 | Windows Event Logs (may User B) | Thu muc log he thong Windows | Export truc tiep |
| 2 | Windows Event Logs (may User A) | Thu muc log he thong Windows | Export truc tiep |
| 3 | RDP Bitmap Cache | May cua User A | Copy file |
| 4 | Memory dump (may User B) | RAM | WinPmem |
| 5 | Network logs | Firewall / Switch | Export log |
| 6 | EDR data | EDR console | Export API |

### 6.2 Chain of custody (mau)

```
┌─────────────────────────────────────────────────────────────┐
│         CHAIN OF CUSTODY - RDP LATERAL MOVEMENT             │
├─────────────────────────────────────────────────────────────┤
│ Case ID: RDP-AH-YYYYMMDD-XXX                                │
│ Incident: Truy cap RDP khong duoc phep tu User A den User B  │
│                                                             │
│ Evidence items:                                             │
│ 1. Security.evtx (may User B) - su kien RDP                │
│ 2. Security.evtx (may User A) - explicit credentials       │
│ 3. RDP Bitmap Cache (may User A)                            │
│ 4. Memory dump (may User B)                                 │
│ 5. Network logs (firewall)                                  │
│                                                             │
│ Collection:                                                 │
│ - Date/Time: [YYYY-MM-DD HH:MM]                            │
│ - Collected by: [analyst]                                   │
│ - Witness: [IT staff]                                       │
│                                                             │
│ SHA-256 hashes:                                             │
│ - [file 1]: [hash]                                          │
│ - [file 2]: [hash]                                          │
│                                                             │
│ Storage: [secure location]                                  │
│ Access: [restricted list]                                   │
└─────────────────────────────────────────────────────────────┘
```

### 6.3 Hanh dong ngan han (chi DE XUAT)

| Uu tien | Hanh dong | Phuong thuc | Chu quyen |
|---|---|---|---|
| Cao | Vo hieu hoa tai khoan User A | `Set-ADUser -Identity <user> -Enabled $false` | Identity team |
| Cao | Thu hoi session User A | `Revoke-AzureADUserAllRefreshToken` | Identity team |
| Cao | Cach ly may User B | Network isolation | IT Ops |
| Cao | Bao ton bang chung | Chain of custody | Security team |
| Trung binh | Kiem tra cac phien RDP khac | Query Event 4624 toan domain | SOC |
| Trung binh | Rà soat quyen cua User A | AD group audit | Identity team |

### 6.4 Hanh dong dai han

| Hanh dong | Muc dich | Khung thoi gian |
|---|---|---|
| Rà soat chinh sach RDP | Chi cho phep nguoi duoc phep | Theo ke hoach ngan hang |
| Just-in-time access | Trien khai PAM cho RDP | Theo ke hoach ngan hang |
| Ghi hinh session | Ghi lai toan bo phien RDP | Theo ke hoach ngan hang |
| Tuning rule EDR | Phat hien lateral movement | Theo ke hoach ngan hang |
| Dao tao awareness | Nang cao nhan thuc ve bao mat RDP | Theo ke hoach ngan hang |
