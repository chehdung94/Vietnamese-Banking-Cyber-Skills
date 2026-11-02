# Bang nguong - playbook-pre-departure-insider-review

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.

## 1. Khung dieu tra (investigation window)

| Muc | Gia tri | Nguon |
|---|---|---|
| Bat dau | Ngay nop don nghi viep (ngay nop don) | `../playbook-daily-watchlist-monitoring/references/tier_flow.md` |
| Ket thuc | Ngay nghi thuc te (ngay nghi duoc duyet) | `../playbook-daily-watchlist-monitoring/references/tier_flow.md` |
| Buffer theo doi | 30 ngay sau ngay nghi thuc te | `../playbook-daily-watchlist-monitoring/references/tier_flow.md` |

## 2. Phan loai theo do dai cua khung dieu tra

| Loai | Khoang thoi gian giua nop don va nghi | Muc do rui ro | Hanh dong |
|---|---|---|---|
| Ngan han | duoi 7 ngay | Cao nhat | Giam sat ngay, leo thang nhanh |
| Trung han | tu 7 den 30 ngay | Cao | Giam sat hang ngay |
| Dai han | tren 30 ngay | Trung binh | Giam sat hang tuan |

Nguon: `../playbook-daily-watchlist-monitoring/references/tier_flow.md`.

## 3. Phan cap su co

| Tinh huong | Phan cap | Nguon |
|---|---|---|
| Review nhan vien sap nghi viep co quyen du lieu nhay | P2 | `../_shared/severity-and-escalation.md` |

Khong tao bang phan cap rieng. Thang P1 Critical / P2 High / P3 Medium / P4 Low, muc phan
hoi va danh sach nguoi duoc bao lay tu `../_shared/severity-and-escalation.md`.

## 4. Gio lam viec / ngoai gio

| Muc | Nguon |
|---|---|
| Khung gio lam viec, timezone, quy tac ngoai gio | `../_shared/off-hours-and-baseline.md` |

## 5. Nguong phan tich theo kenh

| Kenh | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Email audit | Luon phan tich | Bat buoc | Bang ghi trong skill nay |
| Web gateway / DLP | Luon phan tich | Bat buoc | Bang ghi trong skill nay |
| Device control (DLP) | Luon phan tich | Bat buoc | Bang ghi trong skill nay |
| Endpoint security | Luon phan tich | Bat buoc | Bang ghi trong skill nay |
| VPN logs | Tra loi 5 cau hoi Value Assessment; neu co tu 2 cau tra loi "Khong" -> SKIP va ghi ly do | Chay neu du dieu kien | `../analyzing-vpn-logs/SKILL.md` |
| Endpoint - muc do su co malware / virus | tu 7 tro len | Ghi nhan vao phat hien | Bang ghi trong skill nay |

## 6. Danh sách domain tin cay (whitelist) cho phan tich email

| Nhom | Vi du |
|---|---|
| Doi tac ngan hang | bidv.com.vn, sacombank.com, vietcombank.com.vn, napas.com.vn... |
| Nha cung cap known | microsoft.com, nha cung cap dich vu |
| Co quan quan ly | sbv.gov.vn, nhnn.gov.vn |

Danh sach nay phai duoc xac nhan boi chu so quan ly truoc khi dung, va phai ghi nguon
trong bao cao. Moi domain ngoai whitelist co xac nhan kinh doanh moi duoc coi la hop le.

## 7. Moc thoi gian phap ly

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).

| Muc | Nguon |
|---|---|
| Han chot thong bao vi pham quy dinh BVTVCDN | `../_shared/compliance-vietnam.md` |
| Han chot ho so danh gia tac dong | `../_shared/compliance-vietnam.md` |

## Ghi chu calibration

- Cac nguong trong muc 1, 2, 5, 6 lay tu tai lieuu repo. Neu co nguong moi, cap nhat
  bang nay va ghi ro nguon.
- Cac nguong muc 3 lay tu `../_shared/severity-and-escalation.md` va
  `../playbook-daily-watchlist-monitoring/references/tier_flow.md`.
