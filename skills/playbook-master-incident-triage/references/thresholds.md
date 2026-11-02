# Bang nguong - playbook-master-incident-triage

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.

## Phan cap P1-P4

Khong tao bang phan cap rieng trong skill nay. Thang phan cap va muc phan hoi lay
nguyen ban tu `../_shared/severity-and-escalation.md` (ban duy nhat).

| Muc | Nhan | Nguon |
|---|---|---|
| P1 | Critical | `../_shared/severity-and-escalation.md` |
| P2 | High | `../_shared/severity-and-escalation.md` |
| P3 | Medium | `../_shared/severity-and-escalation.md` |
| P4 | Low | `../_shared/severity-and-escalation.md` |

Quy tac dung khi xep phan cap (trich tu file nguon):

- Phan cap = muc cao nhat trong cac dieu kien. Khong ha cap.
- Dieu kien "chua xac nhan" khong duoc nang len P1/P2 neu chi co nghi ngo.
- Review nguoi sap nghi viep co quyen du lieu nhay = **P2** (theo
  `../playbook-daily-watchlist-monitoring/references/tier_flow.md`).
- Da co du lieu bi rut voi bang chung = **P1**.
- Moi muc phai ghi ro dieu kien nao da chong minh trong bao cao.

| Dieu kien chua xac nhan | Hanh dong | Nguon |
|---|---|---|
| Nghi ngo chua co bang chung | Giu nguyen muc hien tai, ghi "chua xac nhan", yeu cau them du lieu | `../_shared/severity-and-escalation.md` |

## Moc thoi gian phap ly

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).

Khong ghi so gio/ngay/ngay-hieu-luc cuc the trong body skill.

| Muc | Nguon |
|---|---|
| Han chot thong bao vi pham quy dinh BVTVCDN | `../_shared/compliance-vietnam.md` |
| Han chot ho so danh gia tac dong | `../_shared/compliance-vietnam.md` |
| Han chot thong bao cho nguoi bi pham vuot | `../_shared/compliance-vietnam.md` |

## Gio lam viec / ngoai gio

| Muc | Nguon |
|---|---|
| Khung gio lam viec, timezone, quy tac ngoai gio | `../_shared/off-hours-and-baseline.md` |

## Uu tien tai san ngan hang

Dung khi trong bang phan cap khong phan biet duoc, uu tien tai san quan trong hon.

| Nhom tai san | Uu tien | Vi du |
|---|---|---|
| Core banking | P1 | T24, Flexcube, BaNKS |
| Du lieu khach hang | P1 | PII, du lieu tai khoan |
| He thong thanh toan | P1 | SWIFT, Napas, Internet Banking |
| Mang noi bo | P2 | AD, DNS, Domain Controller |
| He thong van phong | P3 | Email, SharePoint, Office 365 |

## Ghi chu calibration

- Bang nay chi tro toi nguon. Neu `../_shared/severity-and-escalation.md` doi, dung ban moi.
- Khong tu dat nguong moi trong skill nay. Moi nguong phai co bang hoac so tuong doi.
