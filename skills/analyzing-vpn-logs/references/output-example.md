# Mẫu báo cáo và JSON - analyzing-vpn-logs

Mẫu tham khảo. Giá trị trong ví dụ là minh hoạ; khi chạy thật phải thay bằng dữ liệu quan
sát được trong log (Rules dòng 1 và 3).

## Mẫu JSON đầy đủ

```json
{
  "investigation_id": "VPN-YYYYMMDD-XXX",
  "skill": "analyzing-vpn-logs",
  "status": "ok",
  "scope": {
    "target_type": "ip|user|both",
    "ip": "",
    "user": "",
    "time_range": {"start": "YYYY-MM-DD", "end": "YYYY-MM-DD"},
    "log_source": "",
    "filters_applied": []
  },
  "log_summary": {
    "total_rows": 0,
    "unique_users": 0,
    "unique_remote_ips": 0,
    "unique_tunnel_ips": 0
  },
  "connection_events": {
    "tunnel_up": 0,
    "tunnel_down": 0,
    "auth_success": 0,
    "auth_failed": 0,
    "disconnect": 0
  },
  "user_analysis": [
    {
      "user": "",
      "total_connections": 0,
      "unique_remote_ips": 0,
      "first_connection": "YYYY-MM-DD HH:MM",
      "last_connection": "YYYY-MM-DD HH:MM",
      "total_session_time": "",
      "off_hours_connections": 0,
      "failed_auth_attempts": 0
    }
  ],
  "findings": [
    {
      "type": "",
      "evidence": "",
      "threshold_applied": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "timeline": [
    {"timestamp": "YYYY-MM-DD HH:MM:SS", "event": "", "user": "", "remote_ip": "", "note": ""}
  ],
  "indicators": {
    "ips": [],
    "tunnel_ips": [],
    "users": []
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
# Bao cao phan tich log VPN - [MUC TIEU]

## 1. Tom tat
- Pham vi: [user / IP / khoang thoi gian / nguon log]
- Ket luan: [1-3 dong]
- Phan cap: [P1-P4] - dieu kien da chung minh: [...]

## 2. Thong ke ket noi
| User | Tong su kien | IP duy nhat | Lan dau | Lan cuoi | Ngoai gio | Xac thuc that bai |
|---|---|---|---|---|---|---|

## 3. Tong hop bat thuong (xep theo muc)
| # | Loai | Bang chung | Nguong ap dung | Ket luan |
|---|---|---|---|---|

## 4. Timeline
| Thoi gian | Su kien | User | Remote IP | Nhan xet |
|---|---|---|---|---|

## 5. IOC
| Loai | Gia tri | Nguon | Ghi chu |
|---|---|---|---|

## 6. Hanh dong de xuat
| Hanh dong | Chu tri | Uu tien | Ghi chu |
|---|---|---|---|

## 7. Gioi han va du lieu con thieu
- [artefact chua co]
```

## Ghi chu

- Mốc thời gian pháp lý lấy từ `../../_shared/compliance-vietnam.md`.
- Phân cấp P1-P4 lấy từ `../../_shared/severity-and-escalation.md`.
- Trường "risk_level" trong bản v2.0 được bỏ: dùng "severity" theo thang chuẩn P1-P4.
