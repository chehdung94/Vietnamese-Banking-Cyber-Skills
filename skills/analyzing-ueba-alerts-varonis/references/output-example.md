# Mẫu báo cáo và JSON - analyzing-ueba-alerts-varonis

Mẫu tham khảo. Giá trị trong ví dụ là minh hoạ; khi chạy thật phải thay bằng dữ liệu quan
sát được trong dữ liệu cảnh báo (Rules dòng 1 và 3).

## Mẫu JSON đầy đủ

```json
{
  "investigation_id": "INV-YYYYMMDD-XXX",
  "skill": "analyzing-ueba-alerts-varonis",
  "status": "ok",
  "scope": {
    "users": [],
    "departments": [],
    "time_range": {"start": "YYYY-MM-DD", "end": "YYYY-MM-DD"},
    "alert_source": ""
  },
  "alert_summary": {
    "total_alerts": 0,
    "high_risk": 0,
    "medium_risk": 0,
    "low_risk": 0
  },
  "user_analysis": [
    {
      "user": "",
      "email": "",
      "department": "",
      "risk_score": 0,
      "risk_score_components": {
        "severity": 0,
        "behavioral_deviation": 0,
        "asset_sensitivity": 0,
        "historical_context": 0
      },
      "alert_count": 0,
      "distinct_alert_types": 0,
      "jev_ranking": "",
      "primary_concern": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "findings": [
    {
      "type": "",
      "user": "",
      "evidence": "",
      "threshold_applied": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "timeline": [
    {"timestamp": "YYYY-MM-DD HH:MM:SS", "event": "", "alert_id": ""}
  ],
  "indicators": {
    "ips": [],
    "files": [],
    "users": [],
    "assets": []
  },
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [
    {"action": "", "owner": "", "note": "chi de xuat, khong tu thuc thi"}
  ],
  "missing_artifacts": []
}
```

## Mẫu báo cáo markdown

```markdown
# Bao cao phan tich UEBA Varonis - [PHAM VI]

## 1. Tom tat
- Pham vi: [user / nhom / khoang thoi gian]
- Ket luan: [1-3 dong]
- Phan cap: [P1-P4] - dieu kien da chung minh: [...]

## 2. Tom tat canh bao
| Loai canh bao | So luong | MITRE | Uu tien |
|---|---|---|---|

## 3. Xep hang user rui ro
| User | Phong ban | Diem rui ro | Loai canh bao | Xep hang Jev | Van de chinh |
|---|---|---|---|---|---|

## 4. Phat hien chi tiet
| # | Loai | Bang chung | Nguong ap dung | Ket luan |
|---|---|---|---|---|

## 5. Timeline
| Thoi gian | Su kien | AlertID |
|---|---|---|

## 6. IOC
| Loai | Gia tri | AlertID | Ghi chu |
|---|---|---|---|

## 7. Hanh dong de xuat
| Hanh dong | Chu tri | Uu tien | Ghi chu |
|---|---|---|---|

## 8. Gioi han va du lieu con thieu
- [artefact chua co]
```

## Ghi chu

- Điểm RiskScore là thang tương đối để xếp hạng; phân cấp P1-P4 lấy từ `../../_shared/severity-and-escalation.md`.
- Xếp hạng Jev chỉ thể hiện thứ tự ưu tiên, không thay đổi phân cấp (xem `schemas/jev_contract.json`).
- Mốc thời gian pháp lý lấy từ `../../_shared/compliance-vietnam.md`.
