# Bang vi tri artifact registry - analyzing-windows-registry-for-artifacts

Nguon su fact duy nhat cho cac duong dan registry ma skill nay doc. SKILL.md khong nhoe lai bang nay.
Doi phien ban Windows -> cham lai va ghi ngay thay doi o cuoi file.

## Hoat dong nguoi dung

| Hive | Path | Artifacts |
|------|------|-----------|
| NTUSER.DAT | HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer | RecentApps, RunMRU |
| NTUSER.DAT | HKCU\Software\Microsoft\Office\*\*\UserMRU | Recent documents |
| USRCLASS.DAT | Local Settings\Software\Microsoft\Windows\Shell | BagMRU |

## Co che persistence

| Hive | Path | Purpose |
|------|------|---------|
| NTUSER.DAT | HKCU\Software\Microsoft\Windows\CurrentVersion\Run | User autorun |
| SYSTEM | HKLM\SYSTEM\CurrentControlSet\Services | Service entries |
| SOFTWARE | HKLM\Software\Microsoft\Windows\CurrentVersion\Run | System autorun |

## Thong tin he thong

| Hive | Path | Artifacts |
|------|------|-----------|
| SYSTEM | HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation | Timezone |
| SOFTWARE | HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion | OS Version |
| SAM | HKLM\SAM\SAM\Domains | User accounts |

## Khu vuc phai kiem tra them khi su co la persistence

| Khu vuc | Path | Ly do can kiem tra |
|---------|------|-------------------|
| Winlogon | SYSTEM\CurrentControlSet\Control\Winlogon | Shell/Userinit bi thay the |
| AppInit_DLLs | SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows | DLL injection khoi dong |
| Image File Execution Options | SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options | Debugger/IFEO hijack |
| Explorer\RunOnce | NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RunOnce | Chay mot lan sau dang nhap |
| Boot Execute | SYSTEM\CurrentControlSet\Control\Session Manager | Chay truoc khoi dong he thong |
| UserAssist | NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist | So lan chay mo dieu huong bang ROT13 |
| ShimCache | SYSTEM\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags | Lich su chay file |
| BAM/DAM | SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\ServiceStartToggle | Lich su chay theo user |

## Ban do artifact registry -> MITRE ATT&CK

Chi dung ID co trong `../../_shared/mitre-nist-mapping.md` (MITRE ATT&CK v18).

| Registry Location | ATT&CK Technique | Dien giai |
|-------------------|------------------|-----------|
| HKCU\Run, HKLM\Run, RunOnce | T1547.001 | Registry Run Keys / Startup Folder |
| HKLM\Services (service de novo) | Chua co ID hop le trong mapping | Ghi nhan hanh vi "tao service moi", doi chieu thu cong |
| UserAssist, ShimCache, RunMRU | T1112 | Modify Registry |
| HKLM\Run voi image la shell/cmd de doi | T1059.001 | Chay lenh qua shell |
| Registry hive co chua LSASS material | T1003.001 | OS Credential Dumping - LSASS |

## Ghi chu calibration

- Cac duong dan o tren lay tu he thong Windows pho bien. Ten khoa phu thuoc phien ban Windows
  (vi du `UserAssist` doi layout theo Windows 7 vs Windows 10+). Khong co san ban goc nao kiem chung
  duoc trong repo -> ghi `NOT_AVAILABLE` khi tra loi cho ve cau truc nội dung khoa.
- Skill nay khong co nguong phat hien dinh luong (khong co bang so nguong de so sanh).
  Moi quyet dinh "bat thuong" phai dua tren bang chung so sanh voi baseline, khong tu dat nguong moi.
