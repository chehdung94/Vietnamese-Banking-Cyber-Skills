# SOP phan tich registry - analyzing-windows-registry-for-artifacts

Chi danh thuc thu thap va phan tich. SKILL.md giu lai cac buoc 1-6; file nay giu phan command chi tiet.

## Step 1 - Thu thap hive

```bash
# Using FTK Imager
ftkimager.exe --acquire C: C:\Forensics\registry_images

# Using regedit export
reg export HKLM\SOFTWARE C:\Forensics\SOFTWARE.hiv /y
reg export HKCU\NTUSER.DAT C:\Forensics\ntuser.hiv /y
```

## Step 2 - Parse hive

```bash
# Parse and extract specific keys
python regparse.py --hive SOFTWARE.hiv --keys "Microsoft\Windows\CurrentVersion\Run"
```

## Step 3 - Quet tat ca vi tri autorun

```bash
# Check all autorun locations
python autoruns_parser.py --hives "NTUSER.DAT,SYSTEM,SOFTWARE"
```

## Step 4 - Phan tich hoat dong nguoi dung

```bash
# Extract recent documents and applications
python user_activity.py --hive NTUSER.DAT.hiv
```

## Cac buoc phan tich chi tiet

### A. Persistence
1. Doc day du cac Run key (HKLM, HKCU, HKU, WOW6432Node, RunOnce).
2. Doc `SYSTEM\CurrentControlSet\Services`: ghi `ImagePath`, `Start`, `ObjectName`, `Type`.
3. Doi chieu `ImagePath` voi duong dan tai: file khong ton tai, duong dan trong thu muc tam, hoac
   file co timestamp khac thuong la deu la diem can xac minh - KHONG ket luon la malware.
4. Kiem tra Winlogon Shell/Userinit, AppInit_DLLs, IFEO theo bang vi tri trong
   `references/registry-artifact-locations.md`.

### B. Hoat dong nguoi dung
1. `UserAssist`:gia tri ROT13, dem so lan chay va timestamp -> danh sach chuong trinh da chay.
2. `RunMRU` va `RecentApps`: chuong trinh/moi xem gan nhat.
3. `ShimCache`, `BAM/DAM`: chay theo user, dung de khoang chay nguoi dung khac.
4. `Office\*\UserMRU`, `BagMRU`: tep nguoi dung vua mo.

### C. Thong tin he thong va dinh danh
1. `TimeZoneInformation`: timezone he thong. Moc thoi gian trong su co phai chuan hoa theo
   `../../_shared/off-hours-and-baseline.md` (khong tu quy doi).
2. `Microsoft\Windows NT\CurrentVersion`: OS build, install time.
3. `SAM\Domains`: danh sach user, flag. KHONG dich hash SAM thanh mat khau.
4. `ControlSet\Control\Terminal Server`: cau hinh RDP. Chuyen tiep can qua RDP gan voi T1021.001.

### D. Lien ket bang chung
1. Ghiep mo dong thoi gian (registry) voi log tai khoan, log EDR, log mang.
2. Moi ket luan ghi ro bang chung nao hoa hop. Gia thuyet phai gan nhan "gia thuyet".
3. Phan cap theo `../../_shared/severity-and-escalation.md`.

## Bao cao mau

Xem `references/registry-report-example.md` cho mau bao cao markdown toi thieu.
