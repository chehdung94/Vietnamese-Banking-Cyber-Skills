# Mau bao cao - playbook-master-incident-triage

## Ban ghi triage (JSON)

```json
{
  "incident_id": "INC-YYYYMMDD-XXX",
  "detection_source": "[EDR/SIEM/bao cao nguoi dung]",
  "time_event_occurred": "[timestamp hanh vi]",
  "time_detected": "[timestamp phat hien]",
  "systems_affected": [],
  "initial_severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "route_to": "[ten skill Tier 2]",
  "escalation_required": true,
  "immediate_actions_proposed": [],
  "affected_assets": [],
  "initial_indicators": [],
  "missing_artifacts": [],
  "status": "ok|insufficient_evidence"
}
```

## Bản tóm tắt điều hướng (markdown)

```markdown
## Phan loai su co

- Ma su co: [ID]
- Loai su co: [Category/Subcategory]
- Phan cap: [P1 Critical | P2 High | P3 Medium | P4 Low]
- Dieu kien da chung minh: [ghi ro dieu kien nao da co bang chung]
- Phan hoi mong doi: [lay tu ../_shared/severity-and-escalation.md]
- Nguoi duoc bao: [lay tu ../_shared/severity-and-escalation.md]
- Route den: [skill Tier 2]

### Hanh dong de xuat (khong tu thuc thi)
1. ...

### Artefact con thieu
- ...
```

## Quy tac

- `initial_severity` lay tu `../_shared/severity-and-escalation.md`.
- Moi truong khong co du lieu ghi `[NOT_AVAILABLE]` va ghi vao `missing_artifacts`.
- Hanh dong kham phuc chi de xuat, khong chay tu dong.
