# Bang nguong va chi bao - conducting-memory-forensics-with-volatility

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi mo hinh OS, phi ban Volatility hoac cong cu -> cham lai va ghi ngay thay doi o cuoi file.

## Chi bao phat hien theo plugin Volatility

Bang nay dinh nghia dieu kien "khac thuong" cho tung plugin. Moi dieu kien phai duoc xac nhan bang
chung thu tu plugin, khong duoc suy doan tu ten tien trinh.

| Chi bao | Dieu kien phat hien | Hanh dong | Nguon |
|---|---|---|---|
| Tien trinh an (rootkit) | `windows.psscan` tra ve PID khong xuat hien trong `windows.pslist` | Tiep tuc phan tich, ghi nhan ky thuat phai xac nhan | Plugin psscan so sanh voi pslist |
| Tien trinh cha khong binh thuong | `windows.pstree` cho parent la khong phai `explorer.exe`, `winlogon.exe`, `services.exe`, `svchost.exe`, `lsass.exe`, `wininit.exe`, `csrss.exe` hoac `spoolsv.exe` | Doi chieu voi bang chung EDR | Khong co bang so duy nhat trong repo - xac nhan bang EDR/signed binary |
| Tien trinh khong dung, khong co cha | PID khong co entry trong `windows.pstree` | Tiep tuc phan tich | Plugin pstree |
| Injection vao tien trinh | `windows.malfind` chi mau `PEB`/`VAD` khong dang ke, `PAGE_NOACCESS`, hoac executable path rong | Doi chieu voi signature va tieng trinh | Plugin malfind, phai doi chieu them |
| Truy cap LSASS | `windows.lsass_dump` tra ve dump, hoac `windows.handles` co handle sang lsass.exe tu tien trinh lao | Bao P1-P2 theo bang phan cap | Plugin lsass_dump/handles |
| Ket noi C2 dang ngo | `windows.netscan` tra ve ket noi ngoai khong doi chieu voi danh sach dich vu hop le | Doi chieu voi proxy/DNS log | Can danh sach dich vu hop le - `NOT_AVAILABLE` neu chua co |
| Credential da bi dum | `windows.hashdump` tra ve hash | Khong ghi hash ra ngoai bao cao; chi ghi su co | Plugin hashdump |

## Quy tac phan cap cho chi bao memory

Phan cap lay tu `../../_shared/severity-and-escalation.md` (nguon duy nhat), khong dat lai trong file nay.

| Chi bao | Dieu kien phan cap |
|---|---|
| Truy cap LSASS / dump credential | P2 (credential theft da xac nhan), P1 neu da rut du lieu |
| Tien trinh an bi xac nhan | P2 tru, len P1 neu kem dau hieu C2 da xac nhan |
| File/registry goc khong doi | Khong tu nang len P1 |

## Ghi chu calibration

- Skill nay khong co bang so nguong dinh luong (nhu nguong so ket noi, nguong so injection) duoc
  chung minh trong repo. Moi nguong dinh luong phai lay tu bang nguong cua he thong dich vu
  hien huu hoac tu bang nguong cua skill phan tich khac; khong tu dat nguong moi.
- `windows.psscan` co the bao PID da ket thuc (khong con trong RAM) - phai doi chieu voi `pslist`
  truoc khi ket luan co tien trinh an.
