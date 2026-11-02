# Mẫu báo cáo và JSON - analyzing-azure-ad-signin-logs

Mẫu tham khảo. Giá trị trong ví dụ là giá trị minh hoạ, khi chạy thật phải thay bằng
dữ liệu quan sát được trong log (Rules dòng 1 và 3).

## Mẫu JSON đầy đủ

```json
{
  "analysis_id": "AZAD-SIGNIN-YYYYMMDD-001",
  "skill": "analyzing-azure-ad-signin-logs",
  "status": "ok",
  "scope": {
    "users": ["[EMAIL]"],
    "time_range": {"start": "YYYY-MM-DDTHH:MM:SSZ", "end": "YYYY-MM-DDTHH:MM:SSZ"},
    "log_types": ["interactive", "non-interactive"],
    "baseline_available": true
  },
  "summary": {
    "total_signins": 0,
    "failed_signins": 0,
    "unique_locations": 0,
    "unique_ips": 0,
    "risky_signins": 0
  },
  "findings": {
    "compromised_indicators": {
      "impossible_travel": false,
      "off_hours": false,
      "new_location": false,
      "failed_brute_force": false
    },
    "overall_assessment": "",
    "scenario": "AccountCompromised|SuspiciousActivity|NoFinding",
    "detail": [
      {
        "type": "",
        "evidence": "",
        "threshold_applied": "",
        "conclusion": "evidence|assumption|need_more_data"
      }
    ]
  },
  "timeline": [
    {
      "time": "YYYY-MM-DDTHH:MM:SSZ",
      "location": "",
      "ip": "",
      "device": "",
      "status": "",
      "note": ""
    }
  ],
  "indicators": {
    "ips": [],
    "locations": [],
    "devices": [],
    "users": []
  },
  "severity": "P3",
  "severity_evidence": [
    "Điều kiện nào đã chứng minh, theo ../../_shared/severity-and-escalation.md"
  ],
  "recommended_actions": [
    {
      "action": "",
      "owner": "",
      "note": "chi de xuat, khong tu thuc thi"
    }
  ],
  "missing_artifacts": []
}
```

## Mẫu báo cáo markdown

```markdown
# Bao cao phan tich Entra ID sign-in - [USER]

## 1. Tom tat
- Pham vi: [user / khoang thoi gian / loai log]
- Ket luan: [1-3 dong]
- Phan cap: [P1-P4] - dieu kien da chung minh: [...]

## 2. Tong quan du lieu
| Chi so | Gia tri | Ghi chu |
|---|---|---|
| Tong so sign-in | | |
| Sign-in that bai | | |
| So dia diem / IP | | |
| Risky sign-in | | |

## 3. Phat hien chi tiet
| # | Loai | Bang chung | Nguong ap dung | Ket luan |
|---|---|---|---|---|

## 4. Timeline
| Thoi gian | Su kien | Dia diem | IP | Thiet bi | Nhan xet |
|---|---|---|---|---|---|

## 5. IOC
| Loai | Gia tri | Nguon | |
|---|---|---|---|

## 6. Hanh dong de xuat
| Hanh dong | Chu the | Chu tri | |
|---|---|---|---|

## 7. Gioi han va du lieu con thieu
- [artefact chua co]
```

## Ghi chu

- Mốc thời gian pháp lý lấy từ `../../_shared/compliance-vietnam.md`; không tự viết số giờ/ngày.
- Phân cấp lấy từ `../../_shared/severity-and-escalation.md`.
- Không dùng trường "risk_score" tự chế. Điểm rủi ro mang tính định tính chỉ dùng để mô tả trong
  báo cáo; mức P1-P4 mới là thang chuẩn.
