# Dieu kien va hop dong dieu tra - DFIR Cedarpelta Analysis

Chi tiet cho phan `## Prerequisites` cua `SKILL.md`. SKILL.md giu dieu kien bat buoc, quy
tac STOP va tro toi file nay.

## Dieu kien van hanh (bat buoc)

- Bang chung Cedarpelta duoc xac dinh qua inventory, classification, collection metadata, ten file
  hoac quyet dinh cua dieu tra vien.
- Bang chung goc chi doc.
- Co vung lam viec rieng cho ghi chu, truy van, lenh parser va tap ket qua thu nghiem.
- Co vung dau ra on dinh cho findings, summaries, su kien chuan hoa va artefact ban giao.
- Hanh dong parser duoc gioi han theo gia thuyet, nhom artefact, user, host, khung thoi gian,
  Event ID, IOC hoac pivot seed.
- Khuyen nghi parser dung gia tri complexity chuan hoa: `direct_ingest`, `light`, `specialized`,
  `skip`.
- Schema kiem tra dau ra san co tu repository dang chay, `schemas/` cua skill, hoac contract cua
  ben nhan.

## Dieu kien doi chieu (khong bat buoc)

```text
intake notes
initial scope
triage result
previous findings
timeline artifacts
preliminary hoac final report drafts
known benign assets hoac allowlists
organization capability hoac logging profile
```

## Truong hop bi chan

Khi thieu yeu cau dieu tra, evidence inventory, parser plan, raw evidence location hoac approved
scope, dung tai buoc do va tra `insufficient_evidence` kem danh sach artefact con thieu.
Khong du doan, khong thay the bang chung.
