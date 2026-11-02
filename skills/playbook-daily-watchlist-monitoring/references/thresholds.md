# Bang nguong - Daily Watchlist Monitoring

Nguon su that duy nhat cho nguong cua skill nay. `SKILL.md` khong nhoe lai so lieu.
Doi model/du lieu -> cham lai va ghi ngay thay doi o cuoi file.

> Muc phan cap P1-P4 KHONG duoc tu dinh nghia o day. Lay theo bang trong
> `../_shared/severity-and-escalation.md`. Muc `MEDIUM/HIGH/CRITICAL` duoi day la
> muc *alert* cua playbook, duoc quy doi sang P1-P4 theo file do.
>
> Khung gio lam viec va quy tac ngoai gio: `../_shared/off-hours-and-baseline.md`.

## AD - xac thuc

| Dieu kien | Nguong | Hanh dong | Muc alert |
|---|---|---|---|
| HIGH_LOGIN_COUNT | tren 50 lan dang nhap trong mot ngay | Ghi nhan vao ho so user | MEDIUM |
| HIGH_LOGIN_COUNT | tren 100 lan dang nhap trong mot ngay | Canh bao canh chong | HIGH |
| OFF_HOURS_AUTH | tren 5 su kien ngoai gio trong mot tuan | Canh bao canh chong | MEDIUM |
| ACCESS_REQUEST | Bat ky request TGS toi tai nguyen nhay | Ghi nhan, can xac minh | CRITICAL |

## Email - hoat dong gui/nhan

| Dieu kien | Nguong | Hanh dong | Muc alert |
|---|---|---|---|
| EXTERNAL_EMAIL_WITH_ATTACHMENT | Bat ky email gui ra dia chi email ca nhan (ngoai danh sach) | Canh bao canh chong | HIGH |
| EXTERNAL_EMAIL_WITH_ATTACHMENT | Tong kich thuoc file dinh kem vuot 10 MB | Canh bao canh chong | HIGH |
| MASS_EMAIL_DELETE | tren 20 email bi xoa trong mot ngay | Canh bao canh chong | HIGH |
| OFF_HOURS_EMAIL | tren 5 email gui ngoai gio | Canh bao canh chong | MEDIUM |
| HIGH_EXTERNAL_EMAIL | tren 10 email gui ra ngoai trong mot ngay | Canh bao canh chong | HIGH |

## Fileserver - hoat dong file

| Dieu kien | Nguong | Hanh dong | Muc alert |
|---|---|---|---|
| MASS_FILE_ACCESS | tren 100 file trong mot gio | Canh bao canh chong | HIGH |
| MASS_FILE_DELETE | tren 20 file bi xoa trong mot ngay | Canh bao canh chong | CRITICAL |
| SENSITIVE_FILE_ACCESS | tren 5 file nhay duoc truy cap | Canh bao canh chong | HIGH |
| OFF_HOURS_FILE_ACCESS | tren 10 su kien file ngoai gio | Canh bao canh chong | MEDIUM |
| FOLDER_DELETE | Bat ky lan xoa thu muc | Canh bao canh chong | HIGH |

## Tu khoa du lieu nhay

```text
SENSITIVE_KEYWORDS = [
  'RRGL',               # Quan ly rui ro
  'KHACH HANG',         # Khach hang
  'PII',                # Du lieu dinh danh ca nhan
  'SALARY', 'LUONG',    # Luong, thu nhap
  'THONG TIN',          # Thong tin
  'CREDIT', 'TIN DUNG', # Du lieu tin dung
  'ACCOUNT', 'TAI KHOAN' # Du lieu tai khoan
]
```

## Quy tac tuong quan (cross-source)

| Quy tac | Dieu kien | Muc alert | Hanh dong |
|---|---|---|---|
| Multi-Source Off-Hours | Hoat dong ngoai gio o hai nguon tro len | HIGH | Leo thang |
| Delete Pattern | Xoa email VA xoa file trong cung ngay | CRITICAL | Leo thang ngay |
| Mass Access + External | Truy cap file hang loat keo theo email ngoai trong cung ngay | CRITICAL | Leo thang ngay |
| Sensitive + Off-Hours | Truy cap file nhay trong gio ngoai | HIGH | Dieu tra |
| Folder Delete + Email Delete | Xoa thu muc keo theo xoa email trong cung ngay | CRITICAL | Leo thang ngay |

## Dieu kien leo thang Tier 2 (sang dieu tra chuyen sau)

- [ ] Alert CRITICAL tu quy tac tuong quan da xuat hien.
- [ ] Ba alert HIGH tro len cho cung mot user trong bay ngay.
- [ ] User co delete pattern (xoa email keo theo xoa file).
- [ ] User truy cap du lieu nhay ngoai gio.
- [ ] User gui email ngoai voi file dinh kem lon.

## Moc bao cao noi bo

Moc phan cap va cua so phan hoi lay theo `../_shared/severity-and-escalation.md`.
Bang duoi chi la trinh tu thao tac noi bo, khong thay the moc phan hoi chinh thuc.

| Moc | Hanh dong | Tiep nhan |
|---|---|---|
| T+0 | Phat hien canh bao | SOC Analyst |
| T+15 phut | Danh gia so bo | SOC Lead |
| T+1 gio | Thong bao canh bao muc cao | CISO |
| T+4 gio | Quyet dinh leo thang | CISO + HR + Legal |
| T+24 gio | Bao cao dieu tra | CISO |

## Investigation window (nhan vien nghi viep)

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Buffer sau ngay nghi thuc te | 30 ngay | Giam sat them trong thoi gian buffer | Quy tac chay lo watchlist (Tier 2) |
| Window ngan han | it hon 7 ngay giua nop don va nghi | Giam sat ngay, escalate nhanh | Bang phan loai window |
| Window trung han | tu 7 den 30 ngay | Giam sat hang ngay | Bang phan loai window |
| Window dai han | tren 30 ngay | Giam sat hang tuan | Bang phan loai window |

Muc do rui ro theo window KHONG phai muc phan cap P1-P4. Muc phan cap lay theo
`../_shared/severity-and-escalation.md`. Chi tiet cau truc Tier 3: `references/tier_flow.md`.

## Ghi chu calibration

- Bang nguong nay duoc dua tren phan tich thuc te 334 nhan su nghi viep cùng
  36.964 su kien AD, 3.675 su kien email va 12.077 su kien fileserver.
- Doi nguon du lieu (phien ban Varonis moi, them nguon log) -> cham lai va cap nhat bang nay.
- Nguong baseline mu/sigma theo `../_shared/off-hours-and-baseline.md`; khong uoc luong
  mu/sigma khi thieu file baseline.
