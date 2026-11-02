# Bang nguong - analyzing-dlp-alerts

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi model/du lieu -> cham lai va ghi ngay thay doi o cuoi file.

| Nguon cua gia tri | Trang thai |
|---|---|
| Skill v2.0 (mặc định nội bộ) | Chua hieu chinh tren bo du lieu ngan hang |
| `../_shared/severity-and-escalation.md` (muc P1-P4) | Ban duy nhat, dung chung cho moi skill |

Khong co bang so hieu chinh (precision/recall) cho cac nguong duoi day. Cac nguong la **gia tri
mac dinh de khoi tao dau do**, khong phai nguong da duoc chung nghiep thuc.

## Nguong diem rui ro do DLP/SIEM gan san

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Diem rui ro do nguon canh bao | >= 80 | Gan nhan muc cao, can review ngay | Skill v2.0 (mặc định, ví dụ Splunk) |
| Diem rui ro do nguon canh bao | >= 60 | Gan nhan muc vua, can review | Skill v2.0 (mặc định, ví dụ Splunk) |
| Diem rui ro do nguon canh bao | >= 40 | Gan nhan muc thap, theo doi | Skill v2.0 (mặc định, ví dụ Splunk) |
| Diem rui ro do nguon canh bao | < 40 | Gan nhan muc rui ro thap nhat theo nguon | Skill v2.0 (mặc định, ví dụ Splunk) |

> Doi diem rui ro co the dien ra giua cac ban ghi. Nguong nay KHONG quyet dinh muc P1-P4.
> Muc phan cap lay tu `../_shared/severity-and-escalation.md`.

## Nguong lien quan su kien giua cac nguon log

| Su kien trigger | Su kien dien tiep | Cua so thoi gian | Nhan dinh gi | Nguon |
|---|---|---|---|---|
| DLP chan USB | Phat hien virus tren endpoint | < 1 gio | USB co mang malware | Skill v2.0 (mặc định) |
| DLP chan USB | Truy cap bi chan (access protection violation) | < 1 gio | Nguoi dung co tinh bypass control | Skill v2.0 (mặc định) |
| DLP chan USB | Ket noi HTTP di ra ngoai | < 24 gio | Khai thac: staging du lieu | Skill v2.0 (mặc định) |
| Web filter chan | SMTP den cung domain | < 24 gio | Nguoi dung doi kenh | Skill v2.0 (mặc định) |

> "Dinh nghia" cac su kien tren phu thuoc bo phan loai san (SIEM). Khong co bo phan loai san ->
> ghi `NOT_AVAILABLE` va dung phan loai mo ta.

## Nguong khoi luong du lieu

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| So ban ghi trong mot canh bao | > 100 ban ghi | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Kich thuoc du lieu trong mot canh bao | > 100 MB | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| So nguoi dung bi lien quan | > 1 nguoi dung | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |

## Nguong leo thang sang phong ro ri du lieu

| Tieu chi | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Loai du lieu | PII, tai chinh, suc khoe, thong tin dang nhap | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| So ban ghi | > 100 ban ghi | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| So nguoi dung | Nhieu nguoi dung lien quan | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Dau hieu mau | Lap lai nhieu lan hoac phoi hop | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |
| Xac nhan ra ngoai | Du lieu xac nhan da gui ra ngoai to chuc | De xuat chuyen xu ly phong ro ri du lieu | Skill v2.0 (mặc định) |

> Muc P1-P4 va ma leo thang: xem `../_shared/severity-and-escalation.md`.
> Moc thoi gian phap ly: xem `../_shared/compliance-vietnam.md`.

## Ghi chu calibration

- Cac nguong giu nguyen tu skill v2.0 de khong lam mat ky thuat da dung truoc day.
- Chua co bang hieu chinh tren du lieu ngan hang. Neu trien khai chinh thuc, thay bang nguong da
  chot va ghi rõ mau so, so mau, ket qua.
- Moi nguong moi phai kem do hoc va duoc ghi vao file nay truoc khi dung trong bao cao.
