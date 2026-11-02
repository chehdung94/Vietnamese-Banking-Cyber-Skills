# Bang nguong va tieu chi danh gia - analyzing-windows-registry-for-artifacts

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi phien ban Windows -> cham lai va ghi ngay thay doi o cuoi file.

## Dieu kien phat hien persistence

Moi muc dang ngo phai duoc doi chieu bang chung phu (duong dan tai co that, hash file, log EDR)
truoc khi ket luan. Neu khong doi chieu duoc -> ghi `NOT_AVAILABLE` va ghi ro muc do tin cay.

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Run/RunOnce tro toi file ngoai vung chay duoc | 1 muc dang ngo | Ghi nhan va doi chieu | Ban do vi tri trong `registry-artifact-locations.md` |
| Service tro toi binary khong ton tai tren dia | 1 muc dang ngo | Ghi nhan, doi chieu bang chung he thong | Ban do vi tri trong `registry-artifact-locations.md` |
| Service co `Start` khac mau chuan cua he thong | Can danh sach service chuan | Ghi nhan, doi chieu voi may mau | **NOT_AVAILABLE** - chua co danh sach chuan trong repo |
| Winlogon `Shell`/`Userinit` khac gia tri mac dinh | 1 muc dang ngo | Ghi nhan, doi chieu | Ban do vi tri trong `registry-artifact-locations.md` |
| AppInit_DLLs tro toi DLL ngoai thu vien chuan | 1 muc dang ngo | Ghi nhan, doi chieu | Ban do vi tri trong `registry-artifact-locations.md` |
| IFEO co khoa debugger | 1 muc dang ngo | Ghi nhan, doi chieu | Ban do vi tri trong `registry-artifact-locations.md` |

## Tieu chi danh gia hoat dong nguoi dung

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| UserAssist/RunMRU mo tep o duong dan khong thuoc khu vuc tai chuan cua user | 1 muc dang ngo | Ghi nhan, doi chieu | **NOT_AVAILABLE** - phu thuoc cau truc thu muc tai cua may |
| Chay chuong trinh ngoai gio lam viec | So su kien so voi khung gio lam viec | Ghi nhan, phan loai ngoai gio | `../../_shared/off-hours-and-baseline.md` |
| Username khong ton tai trong SAM | 1 muc dang ngo | Ghi nhan, doi chieu log tai khoan | Ban do vi tri trong `registry-artifact-locations.md` |

## Phan cap cho tung loai phat hien

Phan cap lay tu `../../_shared/severity-and-escalation.md` (nguon duy nhat), khong dat lai trong file nay.

| Phat hien | Dieu kien phan cap |
|---|---|
| Persistence da xac nhan tren may chua bi chiem doat | P3 |
| Persistence da xac nhan kem dau hieu C2 | P1 |
| Truy cap credential/LSASS qua registry | P2, P1 neu da rut du lieu |

## Ghi chu calibration

- Skill nay khong co bang so nguong dinh luong duoc chung minh trong repo (nhu nguong so key,
  nguong so lan chay). Moi nguong dinh luong phai lay tu baseline cua he thong dang phan tich;
  neu chua co baseline -> ghi `insufficient_evidence`, khong uoc luong.
- Cac muc danh ngo phai duoc tach hai: "bat thuong" (chua xac nhan) va "da xac nhan" (co bang
  chung phu). Khong tu nang phan cap chi vi muc dang ngo.
