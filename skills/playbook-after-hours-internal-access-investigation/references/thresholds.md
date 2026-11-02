# Bang nguong - playbook-after-hours-internal-access-investigation

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.

## 1. Khung gio ngoai gio (dieu kien kich hoat)

| Muc | Nguon |
|---|---|
| Gio lam viec, timezone, ngay lam viec | `../_shared/off-hours-and-baseline.md` |
| Quy tac su kien ngoai gio duoc coi la ngoai gio hay khong | `../_shared/off-hours-and-baseline.md` |

Khong ghi khoang gio cu the trong body skill. Neu co ca nhan dang ky lam viec ngoai gio
(danh sach nghi phe duyet) hoac dich vu dong bo duoc xac nhan, su kien do khong duoc coi
la ngoai gio.

## 2. Phan cap su co va muc phan hoi

Khong tao bang phan cap rieng. Thang P1 Critical / P2 High / P3 Medium / P4 Low, muc phan
hoi va danh sach nguoi duoc bao lay tu `../_shared/severity-and-escalation.md`.

| Tinh huong | Phan cap theo nguon |
|---|---|
| Da co bang chung du lieu bi rut | `../_shared/severity-and-escalation.md` |
| Truy cap khong duoc phep (khong co ly do kinh doanh, ngoai gio) | `../_shared/severity-and-escalation.md` |
| Truy cap da duoc phep nhung vi pham chinh sach | `../_shared/severity-and-escalation.md` |
| Hoat dong IT support hop le | `../_shared/severity-and-escalation.md` |

Quy tac: phan cap = muc cao nhat trong cac dieu kien; khong ha cap; "chua xac nhan" khong
duoc nang len muc cao khi chi co nghi ngo.

## 3. Dieu kien kich hoat dieu tra

| Dieu kien | Nguong / dinh nghia | Hanh dong | Nguon |
|---|---|---|---|
| RDP tu may nguoi dung A den may nguoi dung B | Bat ky phien RDP nao ngoai khung gio lam viec | Mo dieu tra | Bang ghi trong skill nay |
| Hoat dong tai khoan sau ngay vo hieu hoa | Bat ky hoat dong thanh cong sau ngay disable | Mo dieu tra, bao Identity | Bang ghi trong skill nay |
| Moi nguoi dung B khong biet | Xac nhan tu phong van | Xac dinh khai thac quyen | Bang ghi trong skill nay |
| Khong co ticket hoac approval | Khong tim thay ticket | Xac dinh vi pham chinh sach | Bang ghi trong skill nay |

## 4. Nguong khoi luong du lieu (chua co bang chung)

| Chi so | Nguong | Nguon |
|---|---|---|
| Khoi luong du lieu chuyen qua RDP de ra ngoai | `NOT_AVAILABLE` - khong co bang chung san trong repo | Phai bo sung bang chong minh moi dat |
| So phien RDP cho cung mot nguoi dung | `NOT_AVAILABLE` - khong co bang chung san trong repo | Phai bo sung bang chong minh moi dat |
| Ty le ngoai gio | `NOT_AVAILABLE` - khong co bang chung san trong repo | Phai bo sung bang chong minh moi dat |

> Khong tu dat nguong moi. Neu to chuc muon dung nguong so lieu, cap nhat bang nay va
> ghi ro nguon.

## 5. Moc thoi gian phap ly

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).

| Muc | Nguon |
|---|---|
| Han chot thong bao vi pham quy dinh BVTVCDN | `../_shared/compliance-vietnam.md` |
| Han chot ho so danh gia tac dong | `../_shared/compliance-vietnam.md` |
| Thong bao cho nguoi bi pham vuot | `../_shared/compliance-vietnam.md` |

## Ghi chu calibration

- Cac moc T+ trong ke hoach truyen thong la SLA phong giao noi bo, khong phai moc phap ly.
- Cac so lieu trong vi du mau la du lieu minh hoa, khong phai nguong.
