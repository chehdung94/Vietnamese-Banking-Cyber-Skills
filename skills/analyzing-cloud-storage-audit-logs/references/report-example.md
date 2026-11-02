# Vi du bao cao - analyzing-cloud-storage-audit-logs

Tai lieu nay la **VI DU MAU** de hieu dinh dang. Cac gia tri chi la du lieu gia lap.
Khi phan tich that, chi ghi nhung gia tri co trong bang chung duoc cung cap; truong khong co
du lieu ghi `[NOT_AVAILABLE]` va them vao `missing_artifacts` (Rules dong 1-3).
Muc P1-P4 lay tu `../_shared/severity-and-escalation.md`.

---

## 1. Bao cao markdown (vi du)

```markdown
# Bao cao phan tich cloud storage audit - tai khoan [EMAIL]

## Tom tat
- Pham vi: M365 Unified Audit, SharePointFileOperation (gia tri la VI DU)
- Nen tang: OneDrive / SharePoint
- Ket luan chinh: co bang chung dong bo tep du lieu khach hang sang OneDrive ca nhan

## Bang chi tiet
| Su kien | Thoi diem | Hanh dong | Tep | Kich thuoc | Ghi chu |
|---|---|---|---|---|---|
| ... | ... | upload | customer_database.xlsx | ... | dong bo sang tai khoan ca nhan |

## Phan cap
- Muc: P1 (theo bang phan cap tai ../_shared/severity-and-escalation.md)
- Dieu kien da chung minh: du lieu bi rut co bang chung (audit log + tep + tai khoan dich)

## IOC
- Tai khoan dich: [EMAIL]
- Tep: customer_database.xlsx
- Loai link chia se: anyone with link

## Hanh dong de xuat (chi de xuat)
1. Revoke session va go bo chia se ngoai (can xac nhan)
2. Bao P.KSTT/IT, luu bang chung
3. Danh gia muc thong bao theo ../_shared/compliance-vietnam.md
```

---

## 2. JSON (vi du)

```json
{
  "skill": "analyzing-cloud-storage-audit-logs",
  "status": "ok",
  "scope": {
    "source": "M365 Unified Audit (SharePointFileOperation)",
    "time_range": "2025-01-08/2025-01-15",
    "users": ["[EMAIL]"]
  },
  "summary": {
    "files_accessed": 0,
    "files_downloaded": 0,
    "files_uploaded": 0,
    "external_shares": 0,
    "total_size_mb": 0
  },
  "findings": [
    {
      "id": "F-01",
      "type": "PersonalCloudUpload",
      "direction": "upload",
      "destination": "[EMAIL]",
      "destination_class": "personal_cloud",
      "files": ["customer_database.xlsx"],
      "size_mb": 0,
      "method": "Sync to personal account",
      "evidence": "SharePointFileOperation audit entry",
      "confidence": "high"
    }
  ],
  "indicators": [
    {"type": "email", "value": "[EMAIL]"},
    {"type": "file_name", "value": "customer_database.xlsx"}
  ],
  "severity": "P1",
  "severity_evidence": [
    "Du lieu bi rut co bang chung - dieu kien P1 theo ../_shared/severity-and-escalation.md"
  ],
  "recommended_actions": [
    "De xuat revoke session va go bo chia se ngoai (can xac nhan)",
    "De xuat bao P.KSTT/IT va luu bang chung",
    "De xuat danh gia muc thong bao theo ../_shared/compliance-vietnam.md"
  ],
  "missing_artifacts": []
}
```

---

## 3. Truong hop `insufficient_evidence`

```json
{
  "skill": "analyzing-cloud-storage-audit-logs",
  "status": "insufficient_evidence",
  "scope": {
    "source": "M365 Unified Audit (SharePointFileOperation)",
    "time_range": "NOT_AVAILABLE",
    "users": []
  },
  "summary": {
    "files_accessed": 0,
    "files_downloaded": 0,
    "files_uploaded": 0,
    "external_shares": 0,
    "total_size_mb": 0
  },
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [
    "Danh sach dich vu cloud duoc duyet cua to chuc (chua duoc cung cap)",
    "Log identity (sign-in / token) de doi chieu tai khoan bi chiem doat"
  ]
}
```
