# Vi du bao cao - analyzing-email-audit-logs

Tai lieu nay la **VI DU MAU** de hieu dinh dang. Cac gia tri chi la du lieu gia lap.
Khi phan tich that, chi ghi nhung gia tri co trong bang chung duoc cung cap; truong khong co
du lieu ghi `[NOT_AVAILABLE]` va them vao `missing_artifacts` (Rules dong 1-3).
Muc P1-P4 lay tu `../_shared/severity-and-escalation.md`.

---

## 1. Bao cao markdown (vi du)

```markdown
# Bao cao phan tich email audit - hop thu [EMAIL]

## Tom tat
- Pham vi: Exchange Online message trace + mailbox audit (gia tri la VI DU)
- Nguoi dung: hop thu bi chuyen tiep sang dia chi ca nhan
- Ket luan chinh: co bang chung email gui doc lap ra dia chi ca nhan kem theo dinh kem du lieu khach hang

## Bang chi tiet
| Email | Huong | Nguoi nhan | Loai tep | Kich thuoc | Ghi chu |
|---|---|---|---|---|---|
| ... | OUTBOUND | dia chi ca nhan | customer_list.xlsx | ... | gui doc lap, khong co nguoi nhan noi bo |

## Phan cap
- Muc: P1 (theo bang phan cap tai ../_shared/severity-and-escalation.md)
- Dieu kien da chung minh: du lieu bi rut co bang chung (email + dinh kem) -> dieu kien P1

## IOC
- Dia chi nhan ngoai: [EMAIL]
- Tep dinh kem: customer_list.xlsx
- Quy tac chuyen tiep: <ten rule> (dang hoat dong)

## Hanh dong de xuat (chi de xuat)
1. Chan forwarding va tat inbox rule (can xac nhan nguoi dung)
2. Thu hoi email da gui (can xac nhan)
3. Bao P.KSTT/IT, luu bang chung
4. Danh gia muc thong bao theo ../_shared/compliance-vietnam.md
```

---

## 2. JSON (vi du)

```json
{
  "skill": "analyzing-email-audit-logs",
  "status": "ok",
  "scope": {
    "source": "Exchange Online message trace + mailbox audit",
    "time_range": "2025-01-01/2025-01-07",
    "users": ["[EMAIL]"]
  },
  "summary": {
    "emails_analyzed": 0,
    "outbound_external": 0,
    "standalone_outbound": 0,
    "forwarding_rules_found": 0
  },
  "findings": [
    {
      "id": "F-01",
      "type": "StandaloneOutbound",
      "direction": "OUTBOUND",
      "recipient_class": "personal_domain",
      "recipient": "[EMAIL]",
      "attachments": ["customer_list.xlsx"],
      "size_mb": 0,
      "evidence": "message trace + audit log entry",
      "confidence": "high"
    }
  ],
  "indicators": [
    {"type": "email", "value": "[EMAIL]"},
    {"type": "file_name", "value": "customer_list.xlsx"},
    {"type": "domain", "value": "gmail.com"}
  ],
  "severity": "P1",
  "severity_evidence": [
    "Du lieu bi rut co bang chung (email + dinh kem) - dieu kien P1 theo ../_shared/severity-and-escalation.md"
  ],
  "recommended_actions": [
    "De xuat chan forwarding va xoa inbox rule (can xac nhan nguoi dung)",
    "De xuat thu hoi email da gui (can xac nhan)",
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
  "skill": "analyzing-email-audit-logs",
  "status": "insufficient_evidence",
  "scope": {
    "source": "Exchange Online message trace",
    "time_range": "NOT_AVAILABLE",
    "users": []
  },
  "summary": {
    "emails_analyzed": 0,
    "outbound_external": 0,
    "standalone_outbound": 0,
    "forwarding_rules_found": 0
  },
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [
    "Baseline hanh vi gui mail cua nguoi dung (khong co file baseline)",
    "Danh sach whitelist domain noi bo/doi tac (chua duoc xac nhan)"
  ]
}
```
