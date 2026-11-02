# Bang nguong - analyzing-email-audit-logs

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi model/du lieu -> cham lai va ghi ngay thay doi o cuoi file.

| Nguon cua gia tri | Trang thai |
|---|---|
| Skill v2.0 (mặc định nội bộ, dùng trong các lần phân tích trước) | Chua hieu chinh tren bo du lieu ngan hang |
| `../_shared/off-hours-and-baseline.md` (khung gio lam viec, baseline) | Ban duy nhat, dung chung cho moi skill |

Khong co bang so hieu chinh (precision/recall) nao cho cac nguong duoi day. Do do cac nguong
nay chi la **gia tri mac dinh de khoi tao dau do**, khong phai nguong da duoc chung nghiep thuc.

## Nguong khoi luong gui ra ngoai va tep dinh kem

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Mot tep dinh kem duoc gui ra ngoai | > 10 MB | Ghi nhan, doi chieu voi baseline | Skill v2.0 (mặc định) |
| So tep dinh kem gui ra ngoai trong mot khung | > 5 tep trong 1 gio | Ghi nhan, kiem tra mau thoi gian gui | Skill v2.0 (mặc định) |
| Tong dung luong gui ra ngoai trong mot ngay | > 50 MB | Ghi nhan, so sanh baseline | Skill v2.0 (mặc định) |
| Tep nen co ten nhay cam | Tieu chi "ten tep nhay cam" khong quy dinh dinh nghia | Khong gan nhan tu dong, de analyst danh gia | Skill v2.0 (mặc định) |

## Nguong nguoi nhan ngoai

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So email gui ra ngoai trong mot ngay | > 50 email/ngay | Ghi nhan, kiem tra mau so sanh voi baseline | Skill v2.0 (mặc định) |
| So email gui ra ngoai trong mot ngay (muc binh thuong) | < 10 email/ngay | Gia tri baseline mau, dung khi so sanh | Skill v2.0 (mặc định) |
| BCC lap trong thoi gian ngan | "Nang" - khong co nguong so | Chi danh nhan, khong tu ket luan | Skill v2.0 (mặc định) |
| Tu chuyen tiep (forwarding rule) | Bat ky rule dang hoat dong | Ghi nhan, dua vao danh sach hanh dong de xuat | Skill v2.0 (mặc định) |

## Nguong baseline hanh vi (gia tri mau)

| Chi so | Gia tri mau | Y nghia |
|---|---|---|
| Email ra ngoai moi ngay | < 10 | Baseline mac dinh |
| Kich thuoc dinh kem moi ngay | < 20 MB | Baseline mac dinh |
| So nguoi nhan ngoai khac nhau trong tuan | < 20 | Baseline mac dinh |
| Hoat dong ngoai gio | "Hiem" | Baseline mac dinh |

> Thoi gian lam viec, khung gio ngoai gio va dieu kien dung rule mu + sigma: xem
> `../_shared/off-hours-and-baseline.md`. Baseline phai lay tu tung nguoi dung, khong dung chung cho ca nhom.

## Nguong kich hoat phan tich lien-mailbox

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So nguoi dung trong pham vi | >= 2 nguoi dung | Kich hoat quy trinh kiem tra dia chi ca nhan xuyen hop thu | Skill v2.0 (mặc định) |

## Nguong leo thang sang phong ro ri du lieu

| Tieu chi | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Loai du lieu | PII, tai chinh, thong tin dang nhap | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Khoi luong | > 100 ban ghi hoac > 50 MB | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Dau hieu mau | Chuyen du lieu lap lich / theo lo | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Y do | Bang chung co y do co tinh hoa lo du lieu | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |

> Muc P1-P4 va ma leo thang: xem `../_shared/severity-and-escalation.md`.
> Moc thoi gian phap ly (bao cao vi pham, ho so danh gia tac dong): xem `../_shared/compliance-vietnam.md`.

## Ghi chu calibration

- Cac nguong tren giu nguyen tu skill v2.0 de khong lam mat ky thuat da dung truoc day.
- Chua co bang hieu chinh (precision/recall) tren du lieu ngan hang. Neu trien khai chinh thuc,
  thay bang nguong da chot va ghi rõ mau so, so mau, ket qua.
- Moi nguong moi phai kem do hoc va duoc ghi vao file nay truoc khi dung trong bao cao.
