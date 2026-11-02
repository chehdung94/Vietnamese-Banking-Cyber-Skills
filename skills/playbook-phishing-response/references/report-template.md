# Mau bao cao - playbook-phishing-response

## Bao cao tom tat (markdown)

```markdown
## Bao cao phan hoi phishing

- Ma su co: [PHISH-YYYYMMDD]
- Thoi diem phat hien: [timestamp]
- Nguon phat hien: [mail gateway / bao cao nguoi dung / SIEM]
- Phan cap: [P1 Critical | P2 High | P3 Medium | P4 Low]
- Dieu kien da chung minh: [ghi ro dieu kien nao co bang chung]

### Pham vi
- Email phat tan: [so luoc] (nguon: mail trace)
- Da click: [so luoc] (nguon: proxy)
- Da submit thong tin: [so luoc] (nguon: proxy)
- Tai khoan bi chiem: [so luoc] (nguon: identity log)

### IOC
- Sender IP: [...]
- Domain: [...]
- URL: [...]
- Hash tep dinh kem: [...]

### Hanh dong de xuat (khong tu thuc thi)
1. ...

### Artefact con thieu
- ...
```

## JSON

```json
{
  "incident_id": "PHISH-YYYYMMDD",
  "status": "ok|insufficient_evidence",
  "scope": {
    "total_received": null,
    "total_clicked": null,
    "total_submitted": null,
    "compromised_accounts": null
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "sender_ips": [],
    "domains": [],
    "urls": [],
    "file_hashes": []
  },
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [],
  "remediation_status": {
    "gateway_blocked": false,
    "emails_purged": false,
    "passwords_reset": false
  }
}
```

## Quy tac

- Truong khong co du lieu ghi `[NOT_AVAILABLE]` va day vao `missing_artifacts`.
- Khong uoc luong so luong. Khong dien so lieu 0 neu chua co du lieu.
- Hanh dong kham phuc chi de xuat.
