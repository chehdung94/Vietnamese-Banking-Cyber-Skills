# Mau bao cao - Daily Watchlist Monitoring

Mau bao cao va mau JSON tach tu `SKILL.md` (ban v2.0). Noi dung ky thuat giu nguyen.

## 1. Ho so hoat dong theo user

Tao mot ho so hop nhat cho moi nhan vien watchlist:

| Chi so | AD | Email | Fileserver | Tong |
|---|---|---|---|---|
| Tong su kien | X | X | X | TONG |
| Su kien ngoai gio | X | X | X | TONG |
| Ngay hoat dong | X | X | X | HOP |
| So canh bao | X | X | X | TONG |

## 2. Cau truc bao cao ngay

```markdown
# Daily Watchlist Monitoring Report
Date: YYYY-MM-DD
Analyst: [Ten]

## Executive Summary
- Tong so nhan vien trong watchlist: X
- So nhan vien co hoat dong trong ngay: X
- Alert muc cao nhat: X
- So canh bao muc cao: X
- So nhan vien can leo thang: X

## Critical Findings
[Danh sach nhan vien voi alert muc cao nhat]

## High-Risk Users
[Danh sach nhan vien voi alert muc cao]

## Activity Summary by Source
| Source | Total Events | Filtered Events | Alerts |
|--------|-------------|-----------------|--------|
| AD | X | X | X |
| Email | X | X | X |
| Fileserver | X | X | X |

## Top 10 Users by Activity
| User | AD | Email | File | Total | Risk Level |
|------|-----|-------|------|-------|------------|

## Escalation Recommendations
[Danh sach nhan vien can dieu tra chuyen sau kem ly do]
```

> Muc phan cap P1-P4 trong bao cao lay theo `../_shared/severity-and-escalation.md`.

## 3. Mau JSON alert

```json
{
  "alert_id": "WL-YYYYMMDD-XXX",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "user": {
    "name": "Account name",
    "watchlist_reason": "Resignation/Disciplinary/Suspicious"
  },
  "alert_type": "MASS_FILE_DELETE|EXTERNAL_EMAIL|...",
  "severity": "P1|P2|P3|P4",
  "sources": ["AD", "Email", "Fileserver"],
  "details": {
    "description": "Mo ta chi tiet",
    "count": 0,
    "threshold": 0
  },
  "correlation": {
    "multi_source": true,
    "related_alerts": []
  },
  "recommended_action": "Escalate|Investigate|Monitor"
}
```

## 4. Moc phan hoi

Moc phan cap va cua so phan hoi lay theo `../_shared/severity-and-escalation.md`.
Cac moc bao cao noi bo thao tac duoc mo ta trong `references/thresholds.md`.

## 5. Tham chieu chung

- Nguong: `references/thresholds.md`.
- Quy trinh tien xu ly: `references/log-preprocessing.md`.
- Leo thang va checklist phap ly: `references/escalation-and-compliance.md`.
