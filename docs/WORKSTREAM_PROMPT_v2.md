# Prompt cho Workstream: nang cap bo skill len v2.1 chuan Droid

Dung nguyen van ban duoi day trong Factory App -> New Workstream.

---

Muc tieu: nang cap bo skill trong `D:\AI\Vietnamese-Banking-Cyber-Skills` len v2.1 chuan Droid,
chay song song theo nhom skill, moi nhom mot PR. Khong viet lai tu dau, chi tach/refine.

## Doc truoc khi lam (bat buoc, dung lam nguon su that)

- `skills/_shared/compliance-vietnam.md` - nguon phap ly duy nhat (moc thoi gian, mau bieu)
- `skills/_shared/severity-and-escalation.md` - thang P1-P4 duy nhat + ma leo thang
- `skills/_shared/off-hours-and-baseline.md` - khung gio lam viec, timezone, cach tinh baseline
- `skills/_shared/mitre-nist-mapping.md` - mapping MITRE/NIST da sua (dung ID trong file nay)
- `skills/_shared/rules-block.md` - khoi `## Rules` bat buoc, chep nguyen van

## Quy tac khong duoc pham

1. Moi agent mot nhom skill, moi nhom mot PR. Khong sua file ngoai nhom duoc giao.
2. Khong tu dat nguong moi. Moi nguong phai co bang hoac so trong `references/thresholds.md`.
3. Khong suy doan moc phap ly. Chi tro toi `../_shared/compliance-vietnam.md`.
4. Khong sua file trong `skills/_shared/`.
5. `name` phai khop ten thu muc. Giu `compatibility: droid`, `allowed-tools`, `license`.
6. `description` tieng Anh (cong thuc: hanh dong + khi nao dung + ranh gioi). Body tieng Viet.
7. Khong tao file ngoai `references/`, `schemas/`, `scripts/`.
8. Khong push len GitHub. Khong xoa skill. Khong doi cau truc 3 tier.
9. Khong sua `drafts/playbook-data-leak-response` thanh skill chinh.

## Phan cong agent

- Agent A - Identity & Network: `analyzing-azure-ad-signin-logs`, `analyzing-vpn-logs`, `analyzing-ueba-alerts-varonis`
- Agent B - Mail, DLP, Cloud: `analyzing-email-audit-logs`, `analyzing-dlp-alerts`, `analyzing-cloud-storage-audit-logs`
- Agent C - DFIR host: `analyzing-windows-registry-for-artifacts`, `collecting-volatile-evidence-from-compromised-host`, `conducting-memory-forensics-with-volatility`, `performing-network-forensics-with-wireshark`, `performing-windows-artifact-analysis-with-eric-zimmerman-tools`
- Agent D - Tier 1/2 playbooks: `playbook-master-incident-triage`, `playbook-phishing-response`, `playbook-abnormal-user-behavior-response`, `playbook-after-hours-internal-access-investigation`, `playbook-pre-departure-insider-review`
- Agent E - Watchlist + Cedarpelta + draft: `playbook-daily-watchlist-monitoring`, `dfir-cedarpelta-analysis`, `drafts/playbook-data-leak-response`

## Viec moi agent phai lam cho tung skill

1. Doc `SKILL.md` va `skills/_shared/rules-block.md`.
2. Chuyen bang nguong dai, SOP dai, vi du mau ra `references/`.
   `SKILL.md` giu: muc dich, trigger, guardrail, buoc thuc thi, tro toi output schema.
3. Dam bao co khoi `## Rules` (dung noi dung `rules-block.md`, khong sua tu).
4. Gom moi nguong vao `references/thresholds.md` cua skill do (neu skill co nguong).
5. Moi tham chieu phap ly sai -> thay bang cau tro `../_shared/compliance-vietnam.md`.
   Xoa moi so moc thoi gian khong co nguon. KHONG ghi "36 gio", "01/07/2025", "32/2024/QH15".
6. Bo sung `## Prerequisites` co cau truc (danh sach) + quy tac "STOP neu thieu".
7. Bo sung `## Expected Output` chuan, kem fenced JSON mau.
8. Chu hoa danh so heading trong skill.
9. Bo sung `jev_contract.json` cho skill co dinh tinh:
   `analyzing-ueba-alerts-varonis`, `analyzing-cloud-storage-audit-logs`, `analyzing-dlp-alerts`,
   `playbook-abnormal-user-behavior-response`.
   Lay mau mau tu `D:\AI\Varonis Daily Logs\Monitoring\jev_contract.json`.
   Dat `calibrated: false` va ghi ro: chi xep hang, KHONG nang muc khi chua calibration.
   Cau hoi phai la dinh danh (noul / score / choice), KHONG de trong.
10. Moi skill co `## Rules` cuoi cung phai chan "khong duoc xuat gia tri khong co trong bang chung"
    va "lenh khac phuc chi duoc de xuat".

## Tieu chi hoan thanh (moi agent tu kiem tra truoc khi mo PR)

- Khong con ID MITRE/NIST sai trong frontmatter va trong body (doi chieu `mitre-nist-mapping.md`).
- Moi nguong nam trong `references/thresholds.md`, khong con nguong trong body.
- `SKILL.md` moi skill <= 8 KB.
- Link tuong doi `../<skill>/SKILL.md` va `../_shared/...` deu resolve.
- Frontmatter parse duoc boi YAML, `name` khop thu muc, `compatibility: droid` co mat.
- Ba cao: danh sach file doi, KB tiet kiem duoc, muc con lai.

## Cach verify (chay truoc khi mo PR)

```powershell
python -c "import yaml,sys;d=yaml.safe_load(open(r'<path>',encoding='utf-8').read().split('---')[1]);print(d['name'])"
```

Neu chua co `pyyaml`: `python -m pip install pyyaml`.
