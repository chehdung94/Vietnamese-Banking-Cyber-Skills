# Mau bao cao - playbook-abnormal-user-behavior-response

## 1. Bao cao phan tich hanh vi (JSON)

```json
{
  "skill": "playbook-abnormal-user-behavior-response",
  "status": "ok|insufficient_evidence",
  "incident_id": "UEBA-YYYYMMDD-XXX",
  "scope": {
    "user": "[user]",
    "department": "[department]",
    "role": "[role]",
    "investigation_window": "[start] -> [end]"
  },
  "summary": {},
  "alert_info": {
    "alert_id": "[alert id]",
    "rule_name": "[rule name]",
    "triggered_at": "[timestamp]"
  },
  "findings": [],
  "indicators": {
    "risk_indicators": {},
    "data_accessed": {
      "total_files": null,
      "sensitive_files": null,
      "data_types": []
    },
    "exfiltration_indicators": {
      "personal_cloud": null,
      "external_email": null,
      "usb_activity": null
    }
  },
  "jev_rank": "Critical|High|Medium|Low",
  "jev_rank_note": "Jev chi xep hang, khong phai phan cap su co",
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

## 2. Executive summary (markdown)

```markdown
## Bao cao UEBA - hanh vi bat thuong nguoi dung

**Thong tin nguoi dung:**
- Username: [ten]
- Phong ban: [phong ban]
- Chuc vu: [chuc vu]
- Thoi gian lam viec: [thoi gian]

**Mo ta bat thuong:**
[Mo ta anomaly duoc phat hien]

**Xep hang rui ro (Jev):** [Critical/High/Medium/Low] - chi la xep hang uu tien dieu tra
**Phan cap su co:** [P1 Critical | P2 High | P3 Medium | P4 Low] (theo ../_shared/severity-and-escalation.md)
- Dieu kien da chung minh: [ghi ro dieu kien nao co bang chung]

**Bang chung chinh:**
1. [Evidence 1]
2. [Evidence 2]
3. [Evidence 3]

**Khuyen nghia (de xuat, khong tu thuc thi):**
- [ ] Buoc 1
- [ ] Buoc 2

**Quyet dinh cua Ban Giam doc:**
[Decision box]
```

## 3. Bao cao phat hien hoat dong tep

Mau JSON: `references/file-activity-analysis.md`, Section 6.

## Quy tac

- `jev_rank` khong bao gio thay the `severity`.
- `severity` lay tu `../_shared/severity-and-escalation.md`.
- Truong khong co du lieu ghi `[NOT_AVAILABLE]` va day vao `missing_artifacts`.
- Khong dien so lieu 0 khi chua co du lieu.
