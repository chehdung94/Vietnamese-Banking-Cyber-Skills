# Bang nguong - playbook-phishing-response

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.

## Phan cap su co va muc phan hoi

Khong tao bang phan cap rieng. Thang P1-P4, muc phan hoi va danh sach nguoi duoc bao
lay tu `../_shared/severity-and-escalation.md` (ban duy nhat).

| Tinh huong | Phan cap theo nguon | Nguon |
|---|---|---|
| Phishing nguoi dung bi chiem (nhap mat khau, tai file, thong tin bi rut) | `../_shared/severity-and-escalation.md` | `../_shared/severity-and-escalation.md` |
| Mot endpoint co hoat dong nghi sau email | `../_shared/severity-and-escalation.md` | `../_shared/severity-and-escalation.md` |
| Brute force that bai / hien tuong don le | `../_shared/severity-and-escalation.md` | `../_shared/severity-and-escalation.md` |

Quy tac: phan cap = muc cao nhat trong cac dieu kien; "chua xac nhan" khong duoc nang
len muc cao neu chi co nghi ngo.

## Nguong phat hien so luong

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So nhan vien nhan email phat tan | [NOT_AVAILABLE] - khong co bang chung san trong repo | Do lai tu mail trace log | - |
| So nhan vien click link | [NOT_AVAILABLE] - khong co bang chung san trong repo | Do lai tu proxy/web log | - |
| So nhan vien submit thong tin (POST) | [NOT_AVAILABLE] - khong co bang chung san trong repo | Do lai tu proxy/web log | - |
| So tai khoan bi chiem | [NOT_AVAILABLE] - khong co bang chung san trong repo | Do lai tu identity log | - |

> Khong tu dat nguong moi. Neu to chuc muon dung nguong so luong, cap nhat bang nay
> va ghi nguon cu the.

## Moc thoi gian phap ly

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).

| Muc | Nguon |
|---|---|
| Han chot thong bao vi pham quy dinh BVTVCDN | `../_shared/compliance-vietnam.md` |
| Han chot ho so danh gia tac dong xu ly du lieu ca nhan | `../_shared/compliance-vietnam.md` |
| Thong bao cho nguoi bi pham vuot | `../_shared/compliance-vietnam.md` |

## Ghi chu calibration

- Cac moc T+ trong ke hoach truyen thong noi bo la SLA phong giao, khong phai moc phap ly.
  Moc phap ly chi lay tu `../_shared/compliance-vietnam.md`.
