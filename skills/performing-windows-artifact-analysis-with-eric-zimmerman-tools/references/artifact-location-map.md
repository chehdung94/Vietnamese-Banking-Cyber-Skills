# Ban do artifact Windows - performing-windows-artifact-analysis-with-eric-zimmerman-tools

Nguon su fact duy nhat cho cac artifact va cong cu cua skill nay. SKILL.md khong nhoe lai bang nay.
Doi phien ban Windows hoac bo cong cu -> cham lai va ghi ngay thay doi o cuoi file.

## Cong cu EZ Tools

| Tool | Purpose | Key Output |
|------|---------|------------|
| KAPE | Automated artifact collection | Parsed CSV/JSON |
| MFTECmd | Parse MFT files | Timeline, file details |
| PECmd | Parse Prefetch files | Execution history |
| LECmd | Parse LNK files | Access timestamps |
| JLECmd | Parse Jump Lists | Recent documents |
| Timeline Explorer | Visual timeline analysis | Correlated events |

## Artifact co gia tri dieu tra ngan hang

| Artifact | Location | Investigation Value |
|----------|----------|-------------------|
| Recent Docs | NTUSER.DAT | File access history |
| UserAssist | Registry | Program execution |
| BAM/DAM | SYSTEM | Background activity |
| Prefetch | Prefetch folder | Application usage |
| ShimCache | SYSTEM | Program execution |

## Vung thu thap mac dinh cua KAPE

| Vung | Chu ghi chu |
|------|------------|
| Registry hives | SYSTEM, SOFTWARE, SAM, SECURITY, NTUSER.DAT, USRCLASS.DAT |
| File system | Prefetch, Recent (LNK), Jump Lists, ShimCache, BAM/DAM, Amcache |
| Event logs | Security, System, Sysmon (neu co) |
| Web history | Cho kiem tra hoat dong nguoi dung, pham vi theo case |

Danh sach day du phai xem `.\KAPE.exe --tlist` tren may dang phan tich - khong suy doan tu bang tren.

## Ban do artifact -> MITRE ATT&CK

Chi dung ID co trong `../../_shared/mitre-nist-mapping.md` (MITRE ATT&CK v18).

| Artifact | ATT&CK Technique | Dien giai |
|----------|------------------|-----------|
| Run key / RunOnce | T1547.001 | Registry Run Keys / Startup Folder |
| UserAssist, ShimCache, RunMRU | T1112 | Modify Registry |
| Task da len lich | T1053.005 | Scheduled Task |
| Registry hive / prefetch chua lenh shell | T1059.001 | Command and Scripting Interpreter |
| Registry hive chua material LSASS | T1003.001 | OS Credential Dumping - LSASS |

## Ghi chu calibration

- Cac bang o tren la ban do dinh huong, khong phai bang nguong. Moi nguong dinh luong (vi du so
  lan chay mot chuong trinh trong prefetch) phai lay tu baseline cua he thong; neu chua co
  baseline -> ghi `insufficient_evidence`, khong uoc luong.
- KAPE thu thap theo target list cua phien ban tai; luu lai output cua `--tlist` kem bao cao de
  chung minh pham vi thu thap.
