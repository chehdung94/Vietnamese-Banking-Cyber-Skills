# Bang nguong - DFIR Cedarpelta Analysis

Nguon su that duy nhat cho cac moc va nguong cua skill nay. `SKILL.md` khong nhoe lai so lieu.
Doi quy trinh dieu tra -> cham lai va ghi ngay thay doi o cuoi file.

> Moc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## Khung pivot mac dinh

| Loai seed | Khoang truoc | Khoang sau | Ghi chu |
|---|---|---|---|
| login_rdp | 24 gio | 24 gio | Session logon, RemoteConnectionManager |
| process_execution | 4 gio | 24 gio | Prefetch, Amcache, process tree |
| persistence | 7 ngay | 24 gio | Run key, service, scheduled task, WMI |
| data_staging_exfiltration | 24 gio | 24 gio | SRUM, browser, cloud client, archive |

Cua so pivot co the dieu chinh theo gia thuyet va bang chung, nhung phai ghi ro ly do trong
tai lieu pivot chain. Chi tiet quy tac pivot: `references/pivot_workflow.md`.

## Tieu chi do tin cay

| Muc | Dieu kien | Hanh dong |
|---|---|---|
| high | nhieu artefact doc lap cung ket luan, it mo ho | Xac nhan va ghi finding |
| medium | bang chung ho tro ket luan nhung con thieu hoac khong day du mot so nguon | Ghi limitation, tao follow-up |
| low | bang chung yeu, gian tiep, khong day du hoac chi mot nguon | Giu o dang gia thuyet, khong khu ket luan |

Bang chung thieu phai lam giam do tin cay, tru khi duoc ghi ro la khong ap dung.

## Cong thuc phan tich (parse complexity)

| Muc | Hanh dong |
|---|---|
| direct_ingest | doc truc tiep du lieu da co cau truc hoac ban text, chuan hoa neu can |
| light | trich xuat nhe bang text, CSV, HTML hoac bang, gioi han theo nhiem vu |
| specialized | dung parser forensic duoc khuyen nghi, scope theo gia thuyet hoac pivot |
| skip | khong parse tru khi Supervisor doi scope hoac doi phan loai |

Chi chay hoac khuyen nghi parser co trong `schemas/parser_recommendation_matrix.json` hoac duoc
Supervisor duyet. Chi tiet: `references/parser_policy.md`.

## Dieu kien khong cho dong ho so

- Con gia thuyet quan trong chua giai quyet.
- Chua thuc hien cross-check bat buoc.
- Thieu bang chung cot loi chan ket luan.
- Mau thuan giua ket qua Cedarpelta va ket qua agent khac.
- Con follow-up action dang co `blocks_closure` la true.

## Ghi chu calibration

- Bang nay dua tren SOP hien hanh cua skill; SOP va tai lieu lien quan la nguon giai thich chi tiet.
- Khi doi quy trinh, cap nhat ca bang nay va `references/pivot_workflow.md` de khong mau thuan.
