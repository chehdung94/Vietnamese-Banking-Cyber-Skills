# Quy trinh phan tich chi tiet - analyzing-email-audit-logs

Tai lieu nay giu phan SOP dai, bang mau va vi du. SKILL.md chi giu phan dac biet va tro toi file nay.

## 1. Nguon log va pham vi thu thap

| Nguon | Loai log | Thoi gian luu tru |
|---|---|---|
| Exchange Online | MessageTrace, MailboxAudit, AuditLog | 90 ngay |
| Exchange On-Premises | Transport log, Message Tracking | 30 ngay (tuy chinh) |
| Mail gateway | SMTP log, filter log | Thay doi theo thiet bi |
| Egress monitor | DLP alert, transport rule | 90 ngay |

> Thoi gian luu tru la dac tinh cua nen tang, dung khi nguoi dung xac nhan. Khong tu gan nguong moi.

## 2. Phan loai huong email (bat buoc truoc khi danh gia rui ro)

| Huong | Dinh nghia | Boi canh rui ro | Vi du |
|---|---|---|---|
| INBOUND | Ngoai -> cong ty | Tu chuyen tiep, staging; KHONG phai exfiltration | Gmail ca nhan -> hop thu cong ty |
| OUTBOUND | Cong ty -> ngoai | **Khong lo trung roi du lieu** | Hop thu cong ty -> Gmail ca nhan |
| MIXED | Thread co ca hai huong | Tuy thuoc; phan tich tung email trong thread | Thread vua co internal vua co external |
| OUTBOUND CC | Cong ty -> ngoai o truong CC | Rui ro thap hon khi nguoi nhan chinh la internal | CC Gmail ca nhan trong email cong viec |

## 3. Phan loai domain (whitelist do nguoi dung xac nhan)

| Nhom | Vi du | Cach xu ly |
|---|---|---|
| Noi bo | domain cong ty | Loai khoi danh sach canh bao |
| Doi tac ngan hang | cac ngan hang, thanh toan lien mach | Loai khoi danh sach canh bao |
| Nha cung cap da duyet | Microsoft, Amazon, nha cung cap dich vu | Loai khoi danh sach canh bao |
| Domain ca nhan | freemail (gmail, yahoo, outlook, icloud, proton) | **Gan nhan, can review** |
| Khong ro | Domain ngoai khac | Gan nhan de review |

> Danh sach domain cu the tu nguoi dung xac nhan. Khong tu gan domain vao whitelist.
> Khong co danh sach whitelist -> ghi `NOT_AVAILABLE` va ghi ro muc do tin cay cua phan loai domain.

## 4. Quy trinh phat hien email gui doc lap (standalone)

```
1. LOC: tat ca email co dia chi ca nhan (freemail) trong nguoi nhan
2. KIEM TRA: email co dia chi noi bo hoac doi tac trong To/CC/BCC khong?
3. PHAN LOAI:
   - CO  -> "Contextual" (co nguoi canh cong viec, rui ro thap hon)
   - KHONG -> "STANDALONE" (chi gui den dia chi ca nhan, can uu tien dieu tra)
4. HUONG:
   - OUTBOUND standalone -> kha nang exfiltration
   - INBOUND standalone -> tu chuyen tiep
5. LIEN-NGUOI: neu pham vi co nhieu nguoi dung, kiem tra dia chi ca nhan cua nguoi dung A
   co xuat hien trong nguoi nhan cua nguoi dung B khong
```

Bieu mau tong hop:

| Nguoi dung | Tong so email | Standalone | OUTBOUND | INBOUND | Lien-nguoi |
|---|---|---|---|---|---|
| (ghi ten theo mau chuan) | (so) | (so) | (so) | (so) | (dia chi hoac "-") |

## 5. Phan tich lien-mailbox (nghi thao tac phoi hop)

1. Trich xuat tat ca email ngoai cua tung nguoi dung.
2. Tim dia chi ca nhan cua nguoi dung A xuat hien trong truong nhan cua nguoi dung B.
3. Ghi nhan khi phat hien giao tiep doc lap xuyen hop thu.
4. Doi chieu timeline de xac dinh hoat dong phoi hop.

## 6. So sanh baseline

| Chi so | Gia tri baseline mau | Gia tri nguoi dung | Delta | Bat thuong? |
|---|---|---|---|---|
| Email ra ngoai moi ngay | < 10 | (so) | (so %) | (co/khong) |
| Kich thuoc dinh kem moi ngay (MB) | < 20 | (so) | (so %) | (co/khong) |
| So nguoi nhan ngoai trong tuan | < 20 | (so) | (so) | (co/khong) |
| Email ngoai gio | Hiem | (so) | - | (co/khong) |

Gia tri mau lay tu `references/thresholds.md`. Quy tac baseline, khung gio lam viec va dieu kien dung
mu + sigma lay tu `../_shared/off-hours-and-baseline.md`.

## 7. Phan tich chuoi email (email chain)

| Kiem tra | Muc dich |
|---|---|
| Chuoi reply | Day co phai la tra loi cho cuoc tro chuyen hop le khong? |
| Thay doi CC/BCC | Co them nguoi nhan khong co ly do khong? |
| Thay doi tep dinh kem | Them tep khong co trong ban goc khong? |
| Thay doi chu de | Bo tu khoa canh bao khong? |

## 8. Trich xuat IOC

```json
{
  "analysis_id": "EMAIL-AUDIT-YYYYMMDD-001",
  "user": "[EMAIL]",
  "time_range": "NOT_AVAILABLE",
  "findings": {
    "total_external_emails": 0,
    "total_attachment_mb": 0,
    "unique_external_recipients": 0,
    "suspicious_patterns": [
      {
        "type": "LargeDataTransfer",
        "date": "NOT_AVAILABLE",
        "recipients": [],
        "size_mb": 0,
        "files": [],
        "risk": "NOT_AVAILABLE"
      },
      {
        "type": "AutoForwardCreated",
        "date": "NOT_AVAILABLE",
        "forward_to": "",
        "status": "StillActive",
        "risk": "NOT_AVAILABLE"
      }
    ]
  },
  "iocs": {
    "external_emails": [],
    "attachments": [],
    "keywords_found": []
  }
}
```

> Moi truong ghi `[NOT_AVAILABLE]` khi khong co du lieu. Khong bia so lieu (Rules dong 1-3).

## 9. Tu khoa nghi ngo (phat hien theo noi dung)

| Nhom | Tu khoa mau |
|---|---|
| Tai chinh | invoice, payment, bank transfer, SWIFT |
| PII | salary, password, SSN, passport |
| Nhay cam | confidential, secret, internal, not for sharing |
| Luu tru | backup, archive, old |

Tu khoa chi la **chi bao de review**, khong phai bang chung vi pham. Kiem tra lai bang chung
thi dua (vi du: tep co that khong) truoc khi dung vao bao cao.

## 10. Hanh dong de xuat (chi DE XUAT, khong chay tu dong)

| Hanh dong | Cach thuc | Ghi chu |
|---|---|---|
| Chan chuyen tiep ra ngoai | `Set-Mailbox -Identity "user" -ForwardingSmtpAddress $null` | Can xac nhan cua nguoi dung truoc khi chay |
| Xoa inbox rule | `Remove-InboxRule -Identity "<rule>" -Mailbox "<user>"` | Luu bang chung rule truoc khi xoa |
| Thu hoi email da gui | `Search-Mailbox -SearchQuery "..." -TargetMailbox "<user>" -DeleteContent` | Chi khi co xac nhan va co phep xoa |
| Vo hieu tai khoan | `Set-AzureADUser -AccountEnabled $false` | Can doi chieu HR va muc do su co |
| Lien he nguoi nhan ngoai de xoa | Lien he thu cong | De xuat, khong tu gui |

## 11. Ma leo thang

Dung ma leo thang trong `../_shared/severity-and-escalation.md`. Moc thoi gian phap ly lay tu
`../_shared/compliance-vietnam.md` (khong ghi so gio/ngay cuc the o day).
