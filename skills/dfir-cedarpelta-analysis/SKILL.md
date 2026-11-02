---
name: dfir-cedarpelta-analysis
description: "Reason through Cedarpelta live-response evidence with a hypothesis-driven DFIR loop - evidence, hypothesis, cross-check, pivot, finding, limitation, follow-up, closure. Use when Cedarpelta Build or Live Response collection output is in scope. Not for SIEM-only, firewall-only, VPN-only, email-only, malware reverse engineering, or generic report-writing tasks."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: dfir
mitre_attack: [T1003.001, T1021.001, T1048, T1053.005, T1059.001, T1074, T1078, T1112, T1114, T1204, T1547.001]
nist_csf: [DE.CM-1, DE.CM-9, DE.AE-1, DE.AE-3, PR.AA-01, PR.AA-06, RS.MI-1, RS.RP-1]
tags:
  - dfir
  - cedarpelta
  - live-response
  - hypothesis-driven
  - tier-3
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# DFIR Cedarpelta Analysis

## Overview

Skill nay la bo nao dieu tra cho bang chung Cedarpelta Build / Live Response Collection. Noi dung
dinh nghia cach agent suy luan, dieu tra, pivot, doi chieu cheo va kiem soat dong ho so.

```text
Evidence -> Hypothesis -> Cross-check -> Pivot -> Finding -> Limitation -> Follow-up -> Closure
```

Khong hanh x nhu mot bo phan tich artefact don thuan. Khong ket luan vu an "sach" chi vi mot nguon
artefact sach. Bang chung thieu lam giam do tin cay, tru khi duoc ghi ro la khong ap dung. Trich dan
dung MITRE ATT&CK v18 va NIST SP 800-61r3; danh muc ID hop le nam tai
`../_shared/mitre-nist-mapping.md`.

## When to Use

- Bang chung Cedarpelta Build hoac Live Response Collection nam trong pham vi.
- Cuoc dieu tra can kiem tra phu mo artefact rieng cua Cedarpelta, hunt theo gia thuyet, pivot, doi
  chieu cheo bat buoc, xu ly gia han theo parser, danh gia do tin cay hoac kiem soat dong ho so.
- Ben nhan ben duoi can ket qua Cedarpelta, su kien chuan hoa, khuyen nghi parser hoac doi chieu voi
  ket qua khac va su kien timeline.

Khong dung lam phuong phap chinh cho cong viec chi co SIEM, firewall, VPN, email, malware reverse
engineering hoac viet bao cao chung.

## Prerequisites

Dieu kien bat buoc:

- analysis request hoac tasking instruction.
- Cedarpelta evidence inventory hoac evidence list.
- Cedarpelta raw evidence location.
- artifact classification hoac collection manifest.
- parser recommendation matrix hoac parser plan.
- approved investigation scope.

Dieu kien van hanh va dieu kien doi chieu khong bat buoc: xem
[prerequisites contract](references/prerequisites-contract.md).

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep. Tra
`insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2. Khong du doan khi thieu yeu
cau dieu tra, inventory, parser plan, raw evidence location hoac approved scope.

## Execution Steps

Luon tai va lam theo [SOP Cedarpelta](references/cedarpelta_analysis_sop.md) cho trinh tu dieu tra va
thu tu buoc.

1. **Xac dinh pham vi bang chung** - ghi evidence root, package thu thap, pham vi duoc giao, phan lo tru.
2. **Baseline host truoc phan tich** - ghi nen tang may chu; chi la buoc dinh huong, khong ket luan.
3. **Kiem tra do phu artefact** - doi chieu duong dan output voi
   [artifact map](references/cedarpelta_collection_artifact_map.md), gan trang thai tung nhom.
4. **Kiem tra readiness cong cu** - xac nhan co tool/parser cho tung ho so artefact; khong parse.
5. **Xay dung gia thuyet** - trang thai theo [hypothesis lifecycle](references/hypothesis_lifecycle.md); chi
   sang `resolved` khi da kiem tra nhom artefact, da doi chieu cheo, da ghi limitation, da co follow-up.
6. **Chon kich ban hunt** - danh gia [hunting scenarios](references/hunting_scenarios.md), ghi evidence da
   kiem tra, evidence con thieu va do tin cay.
7. **Quyet dinh parser** - chi parse theo scope/gia thuyet, theo
   [parser policy](references/parser_policy.md) va
   [parser matrix](schemas/parser_recommendation_matrix.json).
8. **Doi chieu cheo bat buoc** - khong dong scenario tren mot nguon artefact duy nhat; xem
   [cross-check rules](references/cross_check_rules.md).
9. **Pivot** - moi seed event hoac finding tao pivot chain theo
   [pivot workflow](references/pivot_workflow.md), kem pre/during/post-event.
10. **Ghi finding** - giu do tin cay, evidence refs, affected entities, timeline refs, limitation va hanh
    dong de xuat.
11. **Sinh follow-up action** - moi muc chua giai quyet co action; gan `blocks_closure` khi viec do co the
    lam doi ket luan vu an.
12. **Quyet dinh dong ho so** - kiem tra dieu kien chan trong [bang nguong](references/thresholds.md).

Ghi ket qua sau moi buoc vao case file theo
[mau bao cao theo buoc](references/cedarpelta_stepwise_report_template.md); kiem tra dau ra bang
`schemas/`.

## Expected Output

1. **Bao cao** (markdown): tom tat, finding, limitation, follow-up action va ly do dong ho so. Muc phan cap
   P1-P4 lay theo `../_shared/severity-and-escalation.md`.
2. **JSON** (fenced, mau minh hoa):

```json
{
  "skill": "dfir-cedarpelta-analysis",
  "status": "ok",
  "scope": {},
  "summary": {},
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

- Case output phai bo sung `case_id`, `artifact_coverage`, `hunting_scenarios`, `hypotheses`,
  `pivot_chains`, `missing_or_unparsed_evidence`, `follow_up_action_items`, `supervisor_verification`,
  `confidence` va kiem tra duoc theo
  [schema findings](schemas/cedarpelta_findings.schema.json).
- `status` la `ok` hoac `insufficient_evidence`; khi `insufficient_evidence`, dien `missing_artifacts` va
  khong ket luan ve hanh vi.
- `confidence` chi nhan `high`, `medium`, `low` theo [bang nguong](references/thresholds.md).
- `recommended_actions` chi de xuat; khong chay lenh khoa, xoa, vo hieu key hay tat tinh nang khi chua co
  xac nhan ro rang cua nguoi dung (Rules dong 6).

## References

- [SOP Cedarpelta Analysis](references/cedarpelta_analysis_sop.md) - trinh tu dieu tra va quy tac dong ho so.
- [Prerequisites contract](references/prerequisites-contract.md) - dieu kien van hanh va dieu kien doi chieu.
- [Collection artifact map](references/cedarpelta_collection_artifact_map.md) - do phu artefact.
- [Stepwise report template](references/cedarpelta_stepwise_report_template.md) - mau ghi ket qua theo buoc.
- [Investigator tool readiness](references/investigator_tool_readiness.md) va
  [artifact groups](references/artifact_groups.md) - tool, fallback, nhom artefact.
- [Hunting scenarios](references/hunting_scenarios.md) - kich ban hunt.
- [Hypothesis lifecycle](references/hypothesis_lifecycle.md) - trang thai gia thuyet.
- [Cross-check rules](references/cross_check_rules.md) - doi chieu cheo bat buoc.
- [Pivot workflow](references/pivot_workflow.md) - seed va khung pivot.
- [Parser policy](references/parser_policy.md) - chon parser theo pham vi.
- [Bang nguong](references/thresholds.md) - khung pivot, do tin cay, dieu kien dong ho so.
- `schemas/` - schema findings, normalized event, timeline event, follow-up action, parser matrix.
- `../_shared/severity-and-escalation.md` - phan cap P1-P4; `../_shared/compliance-vietnam.md` - moc thoi
  gian phap ly; `../_shared/mitre-nist-mapping.md` - danh muc MITRE ATT&CK v18 va NIST CSF 2.0 hop le.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
