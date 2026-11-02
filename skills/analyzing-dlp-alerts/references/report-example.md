# Vi du bao cao - analyzing-dlp-alerts

Tai lieu nay la **VI DU MAU** de hieu dinh dang. Cac gia tri chi la du lieu gia lap.
Khi phan tich that, chi ghi nhung gia tri co trong bang chung duoc cung cap; truong khong co
du lieu ghi `[NOT_AVAILABLE]` va them vao `missing_artifacts` (Rules dong 1-3).
Muc P1-P4 lay tu `../_shared/severity-and-escalation.md`.

---

## 1. Bao cao markdown (vi du)

```markdown
# Bao cao phan tich DLP - canh bao PURVIEW-2024-001234

## Tom tat
- Pham vi: 1 canh bao DLP tu Microsoft Purview (gia tri la VI DU)
- Phan loai: true positive
- Ket luan chinh: co bang chung du lieu PII gui ra ngoai qua email

## Bang chi tiet
| Truong | Gia tri |
|---|---|
| Nguoi dung | [EMAIL] |
| Loai canh bao | EmailAttachment |
| Policy vi pham | Block PII External Email |
| Loai du lieu | PII, so tai khoan |
| Duong ro ri | Email ngoai |
| Hanh dong DLP | Blocked |

## Phan cap
- Muc: P1 (theo bang phan cap tai ../_shared/severity-and-escalation.md)
- Dieu kien da chung minh: du lieu bi rut co bang chung (canh bao DLP + message trace)

## IOC
- Nguoi dung: [EMAIL]
- Tep: customer_list.xlsx
- Domain nguoi nhan: gmail.com

## Hanh dong de xuat (chi de xuat)
1. Thu hoi email da gui (can xac nhan)
2. Chan domain nguoi nhan trong policy DLP (can xac nhan)
3. Bao P.KSTT/IT va DPO, luu bang chung
4. Danh gia muc thong bao theo ../_shared/compliance-vietnam.md
```

---

## 2. JSON (vi du)

```json
{
  "skill": "analyzing-dlp-alerts",
  "status": "ok",
  "scope": {
    "source": "Microsoft Purview DLP alert export",
    "time_range": "2025-01-01/2025-01-31",
    "alerts_analyzed": 1
  },
  "summary": {
    "true_positive": 1,
    "false_positive": 0,
    "intentional": 0,
    "policy_error": 0
  },
  "findings": [
    {
      "id": "F-01",
      "alert_id": "PURVIEW-2024-001234",
      "classification": "true_positive",
      "user": "[EMAIL]",
      "alert_type": "EmailAttachment",
      "policy_violated": "Block PII External Email",
      "data_types": ["PII"],
      "record_count": 0,
      "exfiltration_route": "External Email",
      "recipients": ["[EMAIL]"],
      "files": ["customer_list.xlsx"],
      "action_taken_by_dlp": "Blocked",
      "pattern": "FirstOccurrence",
      "evidence": "Purview alert + Exchange message trace"
    }
  ],
  "indicators": [
    {"type": "email", "value": "[EMAIL]"},
    {"type": "file_name", "value": "customer_list.xlsx"},
    {"type": "domain", "value": "gmail.com"}
  ],
  "severity": "P1",
  "severity_evidence": [
    "Du lieu bi rut co bang chung - dieu kien P1 theo ../_shared/severity-and-escalation.md"
  ],
  "recommended_actions": [
    "De xuat thu hoi email da gui (can xac nhan)",
    "De xuat chan domain nguoi nhan trong policy DLP (can xac nhan)",
    "De xuat bao P.KSTT/IT va DPO, luu bang chung",
    "De xuat danh gia muc thong bao theo ../_shared/compliance-vietnam.md"
  ],
  "missing_artifacts": []
}
```

---

## 3. Truong hop `insufficient_evidence`

```json
{
  "skill": "analyzing-dlp-alerts",
  "status": "insufficient_evidence",
  "scope": {
    "source": "Microsoft Purview DLP alert export",
    "time_range": "NOT_AVAILABLE",
    "alerts_analyzed": 0
  },
  "summary": {
    "true_positive": 0,
    "false_positive": 0,
    "intentional": 0,
    "policy_error": 0
  },
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [
    "Danh muc policy DLP dang ap dung (chua duoc cung cap)",
    "Message trace tuong ung canh bao de xac nhan du lieu bi rut"
  ]
}
```
