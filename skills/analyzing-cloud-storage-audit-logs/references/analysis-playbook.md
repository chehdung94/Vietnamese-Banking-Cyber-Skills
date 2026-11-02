# Quy trinh phan tich chi tiet - analyzing-cloud-storage-audit-logs

Tai lieu nay giu phan SOP dai, bang mau va vi du. SKILL.md chi giu phan dac biet va tro toi file nay.

## 1. Nen tang va nguon log

| Nen tang | Nguon log | Cach phan tich |
|---|---|---|
| Microsoft OneDrive / SharePoint | M365 Compliance Center, Purview | Graph API, PowerShell |
| Microsoft Azure Blob | Azure Storage Analytics, Log Analytics | Azure Portal, PowerShell |
| AWS S3 | CloudTrail, S3 Access Logs | AWS Console, CLI |
| Google Drive | Google Workspace Admin, Cloud Audit | Admin Console, API |
| Dropbox / Box | CASB, native audit log | CASB Dashboard |

## 2. Tong hop hoat dong theo nguoi dung / tai khoan

```json
{
  "user": "[EMAIL]",
  "analysis_period": "NOT_AVAILABLE",
  "onedrive_summary": {
    "total_files_accessed": 0,
    "total_download_mb": 0,
    "total_upload_mb": 0,
    "external_shares": 0,
    "sharing_breakdown": {
      "internal": 0,
      "external": 0
    }
  },
  "sharepoint_summary": {
    "sites_accessed": 0,
    "files_accessed": 0,
    "files_modified": 0,
    "sensitive_files_accessed": []
  }
}
```

> Chi ghi gia tri co trong bang chung. Truong khong co du lieu ghi `[NOT_AVAILABLE]` (Rules dong 1-3).

## 3. Moi hinh phat hien ro ri qua cloud

| Mo hinh | Cach phat hien | Loai ro ri |
|---|---|---|
| Zip roi tai ve | Tai ve hang loat ngay sau khi tao tep | Ro ri theo lich |
| Dong bo cloud ca nhan | Upload sang storage ca nhan | Tu dong hoa |
| Lam dung link chia se | Chia se mass ra ngoai | Thuy cong |
| Loc doi API | Truy cap lap trinh qua API | Script hoa |

Doi chieu hanh vi voi mapping MITRE tai `../_shared/mitre-nist-mapping.md`:
- Doc du lieu cloud storage: T1530.
- Upload/ro ri sang cloud storage: T1567.002.
- Gom du lieu tam tren may: T1074.
- Tai khoan hop le bi chiem doat: T1078.

## 4. Phan loai du lieu nhay cam theo duong dan tep

| Loai du lieu | Mau duong dan | Muc do rui ro tham chieu |
|---|---|---|
| PII | customer, employee, personal | Cao |
| Tai chinh | invoice, payment, transaction | Rat cao |
| Thong tin dang nhap | password, credential, secret | Rat cao |
| Nhan su | salary, performance, contract | Cao |

> Mucluong do rui ro tham chieu khong phai muc P1-P4. Phan cap lay tu `../_shared/severity-and-escalation.md`.

## 5. Phat hien truy cap tu dia diem la

| Chi bao | Binh thuong | Dang ngo |
|---|---|---|
| Dang IP | Van phong / VPN | ISP ca nhan, VPN khac |
| Quoc gia | Viet Nam | Quoc gia moi xuat hien |
| Thiet bi | Quan tri | Thiet bi khong quan tri |
| Thoi diem | Trong gio lam viec | Ngoai gio |

Khung gio lam viec va quy tac ngoai gio: `../_shared/off-hours-and-baseline.md`.

## 6. Trich xuat IOC

```json
{
  "analysis_id": "CLOUD-AUDIT-YYYYMMDD-001",
  "user": "[EMAIL]",
  "time_range": "NOT_AVAILABLE",
  "findings": {
    "upload_activities": [
      {
        "timestamp": "NOT_AVAILABLE",
        "destination": "personal OneDrive ([EMAIL])",
        "files": [],
        "size_mb": 0,
        "method": "Sync to personal account",
        "risk": "NOT_AVAILABLE"
      }
    ],
    "download_activities": [
      {
        "timestamp": "NOT_AVAILABLE",
        "files": [],
        "size_mb": 0,
        "risk": "NOT_AVAILABLE"
      }
    ],
    "external_shares": [
      {
        "timestamp": "NOT_AVAILABLE",
        "file": "Strategic_Plan.pptx",
        "link_type": "Anyone with link",
        "recipient": "[EMAIL]",
        "risk": "NOT_AVAILABLE"
      }
    ]
  },
  "iocs": {
    "personal_accounts": [],
    "files_exfiltrated": [],
    "total_size_mb": 0
  }
}
```

## 7. Hanh dong de xuat (chi DE XUAT, khong chay tu dong)

| Hanh dong | Cach thuc | Ghi chu |
|---|---|---|
| Revoke session nguoi dung | `Revoke-AzureADUserAllRefreshToken` | Can xac nhan nguoi dung truoc khi chay |
| Tat dong bo OneDrive | `Set-SPOTenant -DisablePersonalFeatures` | Anh huong to bang, can xac nhan |
| Go bo chia se ngoai | `Remove-SPOUser -Site <site-url>` | Luu bang chung share truoc khi go bo |
| Chan cloud khong duyet | CASB policy | Can xac nhan |
| Xoa access key bi lo | `aws iam delete-access-key --access-key-id <key> --user-name <user>` | Chi khi co xac nhan, luu bang chung truoc |
| Cap lai key / vô hieu SAS | Rotate storage key / regenerate SAS token | Can xac nhan |

> Chi ghi de xuat. Khong chay lenh tren tenant neu chua co xac nhan ro rang cua nguoi dung (Rules dong 6).

## 8. Ma leo thang

Dung ma leo thang trong `../_shared/severity-and-escalation.md`. Moc thoi gian phap ly lay tu
`../_shared/compliance-vietnam.md` (khong ghi so gio/ngay cuc the o day).
