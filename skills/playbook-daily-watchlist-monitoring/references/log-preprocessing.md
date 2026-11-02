# Tien xu ly log - Daily Watchlist Monitoring

SOP tien xu ly tach tu `SKILL.md` (ban v2.0). Noi dung ky thuat giu nguyen, chi chinh
ten bien va bo phan trung lap voi cac bang nguong.

## 1. Nguon log

| Nguon | Module Varonis | Cot trong chinh | Khoi luong tham khao |
|---|---|---|---|
| **AD Logs** | Active Directory | Account name, Event time, Event type, Event description | 30.000 - 50.000/ngay |
| **Email Logs** | Exchange Online | Account name, Mail recipient, Event type, Attachment name | 3.000 - 5.000/ngay |
| **Fileserver Logs** | Windows File Server | Account name, Path, Object name, Event operation | 10.000 - 20.000/ngay |

## 2. Quy trinh lam sach

### Buoc 1: Loc theo watchlist

```text
INPUT:  Raw log file + Watchlist
PROCESS: Khop Account name voi danh sach watchlist
OUTPUT: Log da loc, chi giu nhan vien watchlist
```

### Buoc 2: Chuan hoa thoi gian

```text
INPUT:  Event time theo timezone goc cua nguon log
PROCESS: Quy doi theo bang trong ../_shared/off-hours-and-baseline.md
OUTPUT: Event time local, date, hour, day_of_week
```

Bang quy doi timezone theo nguon log (AD/Windows dung gio VN truc tiep, Entra ID/Exchange
Online la UTC can cong 7 gio, generation time la moc khong doi) nam trong
`../_shared/off-hours-and-baseline.md`. Khong tu quy doi.

### Buoc 3: Phan loai ngoai gio

```text
WORKING HOURS / OFF-HOURS:
  - theo khung gio lam viec tai ../_shared/off-hours-and-baseline.md
  - ngoai gio = truoc moc bat dau, sau moc ket thuc, hoac ngay khong lam viec
  - ngoai gio chi ghi nhan khi khong co ca nhan dang ky lam viec ngoai gio
    (danh sach nghi phe duyet) va khong phai dich vu dong bo (sync) da xac nhan
```

### Buoc 4: Loai noise

```text
AD LOGS - REMOVE:
  - System accounts (SERVICE$, COMPUTER$, $)
  - TGT authentication events (giai phap chu ky Kerberos binh thuong)
  - Service accounts (account ket thuc bang _svc, _service)
  - Duplicate events (cung user, cung action, cung giay)
  - Events tu he thong da ngung hoat dong

EMAIL LOGS - REMOVE:
  - Email nhan (chi phan tich email gui)
  - Email noi bo (ca nguoi gui va nguoi nhan deu cung domain)
  - Email tu dong tao (noreply@, system@, automated@)
  - Loi moi lich va phan hoi cuoc hop
  - Duplicate events (cung message ID)

FILESERVER LOGS - REMOVE:
  - File mo/doc voi file cong viec thong thuong (.lnk, .url, .tmp)
  - Events tu thu muc he thong (Windows, Program Files, AppData)
  - Events tu Recycle Bin ($RECYCLE.BIN)
  - Truy cap file shortcut (.lnk)
  - Duplicate events (cung user, cung file, cung giay)
  - Events tu tien trinh backup/sync (OneDrive sync, SharePoint sync)
```

## 3. Thu tuc khop ten

Quy tac so sanh ten lay tu `../_shared/off-hours-and-baseline.md` (khong doi thu tu uu tien):

1. Khop bang ten day du sau khi bo dau va chuan hoa "Last, First" -> "First Last".
2. Uu tien ma nhan vien, sau den ten day du, cuoi cung moi den ten goi.
3. KHONG khop bang dieu kien "ten goi OR ten day du". Ten goi Viet Nam ("An", "Thi", "Van")
   gay false positive o quy mo nhan vien.
4. Ten trung ten -> ghi "ambiguous", hoi nguoi dung, KHONG tu chon.

Vi du dang ky (khong phai quy tac bo sung):

```text
WATCHLIST FORMAT: "Nguyen Van A" (thu tu Viet Nam)
AD LOG FORMAT:    "A, Nguyen Van" (thu tu phuong Tay)
```

## 4. Chi so tinh theo nguon

### AD

- Tong so su kien xac thuc theo user theo ngay.
- So su kien xac thuc ngoai gio.
- So ngay hoat dong duy nhat.
- Mau hinh xac thuc (NTLM, Kerberos, TGT, TGS).

### Email

- Tong so su kien email (gui / nhan / xoa) theo user.
- So email ra ngoai (nguoi nhan khong thuoc domain to chuc).
- Tong kich thuoc file dinh kem theo user.
- So email gui ngoai gio.

### Fileserver

- Tong so su kien file (doc / tao / cap nhat / xoa) theo user.
- So su kien file ngoai gio.
- So lan truy cap file nhay.
- So lan tao / xoa thu muc.

## 5. Tu khoa du lieu nhay

Danh sach tu khoa du lieu nhay nam trong `references/thresholds.md`.

## 6. Tham chieu chung

- Nguong phat hien: `references/thresholds.md`.
- Khung gio lam viec, ngoai gio, baseline: `../_shared/off-hours-and-baseline.md`.
- Phan cap P1-P4: `../_shared/severity-and-escalation.md`.
- Moc thoi gian phap ly: `../_shared/compliance-vietnam.md`.
