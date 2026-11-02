# Bang nguong va tieu chi danh gia - collecting-volatile-evidence-from-compromised-host

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi model, thiet bi hoac quy trinh -> cham lai va ghi ngay thay doi o cuoi file.

## Thu tu bien dong (RFC 3227)

Bang nay la THU TU uu tien, khong phai nguong dinh luong. Thu theo thu tu, dung chay song song.

| Priority | Evidence | Collection Method |
|----------|----------|-------------------|
| 1 | CPU registers, cache | Hardware-level collection |
| 2 | RAM (Memory) | WinPmem, DumpIt, AVML |
| 3 | Network connections | netstat, ss, lsof |
| 4 | Running processes | ps, top, tasklist |
| 5 | Kernel tables | OS-specific commands |
| 6 | Disk files (modified) | File system parsing |
| 7 | Physical disk | Full image acquisition |

## Tieu chi danh gia manh tinh thu thap

Skill nay la buoc thu thap bang chung, khong phai buoc phan tinh. Moi muc danh gia duoi day chi
la dieu kien chat duoc cho viec ban giao sang buoc phan tich chuyen sau.

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Bang chung co hash SHA-256 cho moi file | 100% file da thu thap | Khong chuyen sang phan tich neu con file chua hash | Quy trinh chain of custody noi bo |
| Hash khop giua may thu thap va may phan tich | 100% file | Khong phan tich file lech hash | Quy trinh chain of custody noi bo |
| Memory image | 1 file .raw/.lime/.dmp, khong phai 0 byte | Khong chay plugin memory ma khong co image | Yeu cau ky thuat cua cong cu |
| Nguoi thu thap va thoi diem ghi nhan | Bắt buộc có | Không lập hồ sơ sự cố nếu thiếu | Quy trình chain of custody noi bo |

**NOT_AVAILABLE:** skill nay khong co bang so nguong phat hien (vi du nguong so ket noi la, nguong
so tien trinh la) de so sanh. Moi nguong dinh luong phai lay tu skill phan tich tuong ung
(`../../conducting-memory-forensics-with-volatility/SKILL.md`) hoac bang nguong cua no, khong tu dat.

## Ghi chu calibration

- Cac buoc thu thap nem vao may dang chay luon lam thay doi bang chung RAM. Ghi nhan ro rang
  trong bao cao la bang chung da bi thay doi boi viec thu thap.
- Cac muc "khong co" trong bang chieu keu hanh dong phai duoc ghi ro, khong de trong.
