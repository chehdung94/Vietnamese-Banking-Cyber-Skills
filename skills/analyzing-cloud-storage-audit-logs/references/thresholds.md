# Bang nguong - analyzing-cloud-storage-audit-logs

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi model/du lieu -> cham lai va ghi ngay thay doi o cuoi file.

| Nguon cua gia tri | Trang thai |
|---|---|
| Skill v2.0 (mặc định nội bộ) | Chua hieu chinh tren bo du lieu ngan hang |
| `../_shared/off-hours-and-baseline.md` (khung gio lam viec, baseline) | Ban duy nhat, dung chung cho moi skill |

Khong co bang so hieu chinh (precision/recall) cho cac nguong duoi day. Cac nguong la **gia tri
mac dinh de khoi tao dau do**, khong phai nguong da duoc chung nghiep thuc.

## Nguong tai ve hang loat (mass download)

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So tep tai ve trong mot khoang | > 100 tep trong 1 gio | Ghi nhan, kiem tra mau thoi gian tai ve | Skill v2.0 (mặc định) |
| Dung luong tai ve trong 24 gio | > 1 GB | Ghi nhan, so sanh baseline tai ve | Skill v2.0 (mặc định) |
| Tep co phu lieu nhay cam | .pst, .bak, .csv (du phoi duong dan) | Ghi nhan de review | Skill v2.0 (mặc định) |

## Nguong chia se ngoai

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So su kien chia se ngoai trong mot khoang | > 10 su kien | Ghi nhan, kiem tra loai link va quyen | Skill v2.0 (mặc định) |
| So su kien chia se ngoai trong mot ngay | > 5 su kien/ngay | Ghi nhan, so sanh baseline chia se | Skill v2.0 (mặc định) |
| So su kien chia se ngoai (muc binh thuong) | "Hiem" | Gia tri baseline mau | Skill v2.0 (mặc định) |

## Nguong leo thang sang phong ro ri du lieu

| Tieu chi | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Loai du lieu | PII, tai chinh, thong tin dang nhap | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Khoi luong | > 100 ban ghi hoac > 500 MB | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Dich | Cloud ca nhan xac nhan | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Y do | Bang chung co y do co tinh hoa lo du lieu | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |

> Muc P1-P4 va ma leo thang: xem `../_shared/severity-and-escalation.md`.
> Moc thoi gian phap ly (bao cao vi pham, ho so danh gia tac dong): xem `../_shared/compliance-vietnam.md`.

## Ghi chu calibration

- Cac nguong giu nguyen tu skill v2.0 de khong lam mat ky thuat da dung truoc day.
- Chua co bang hieu chinh tren du lieu ngan hang. Neu trien khai chinh thuc, thay bang nguong da
  chot va ghi rõ mau so, so mau, ket qua.
- Moi nguong moi phai kem do hoc va duoc ghi vao file nay truoc khi dung trong bao cao.
- Quy tac baseline, khung gio ngoai gio va dieu kien dung mu + sigma: `../_shared/off-hours-and-baseline.md`.
