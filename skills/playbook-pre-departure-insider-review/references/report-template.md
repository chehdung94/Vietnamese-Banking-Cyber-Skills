# Mau bao cao - playbook-pre-departure-insider-review

## 1. Executive summary

```markdown
## Bao cao pre-departure review

- User: [ten] | Phong ban: [phong ban] | Chuc vu: [chuc vu]
- Ngay nop don: [date] | Ngay nghi du kien: [date]
- Xep hang rui ro (Jev): [Critical/High/Medium/Low] - chi la xep hang
- Phan cap su co: [P1 Critical | P2 High | P3 Medium | P4 Low]
  (theo ../_shared/severity-and-escalation.md)
- Key findings: [top 3]
- DLP gaps identified: [danh sach]
- Recommendation: [hanh dong de xuat]
- Artefact con thieu: [danh sach]
```

## 2. Cau truc bao cao dieu tra

1. Executive summary.
2. User profile (ho so, quyen truy cap, phong ban, chuc vu).
3. Log scope va phuong phap (khung dieu tra, nguon log, truong du lieu, whitelist).
4. Findings theo tung nguon (email, web gateway, device control, endpoint, VPN neu chay).
5. Timeline synthesis.
6. Cross-user correlation (neu ap dung).
7. Risk scoring (ma tran 7 chi bao + calibration + context dinh tinh tach rieng).
8. Danh gia hieu luc DLP.
9. Ket luan va khuyen nghia.

## 3. JSON

```json
{
  "skill": "playbook-pre-departure-insider-review",
  "status": "ok|insufficient_evidence",
  "scope": {
    "user": "[user]",
    "department": "[department]",
    "resignation_date": "[date]",
    "last_working_date": "[date]",
    "investigation_window": "[start] -> [end]"
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "risk_indicators": {},
    "exfiltration_indicators": {},
    "dlp_gaps": []
  },
  "jev_rank": "Critical|High|Medium|Low",
  "jev_rank_note": "Jev chi xep hang, khong phai phan cap su co",
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [],
  "skipped_analyses": []
}
```

## 4. Quy tac

- `severity` lay tu `../_shared/severity-and-escalation.md`.
- `jev_rank` khong thay the `severity`.
- Truong khong co du lieu ghi `[NOT_AVAILABLE]` va day vao `missing_artifacts`.
- Hanh dong kham phuc chi de xuat.
