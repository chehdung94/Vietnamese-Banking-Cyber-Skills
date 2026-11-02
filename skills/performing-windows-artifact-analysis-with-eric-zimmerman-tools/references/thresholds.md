# Bang nguong va tieu chi danh gia - performing-windows-artifact-analysis-with-eric-zimmerman-tools

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.
Doi phien ban Windows hoac bo cong cu -> cham lai va ghi ngay thay doi o cuoi file.

## Dieu kien phat hien theo loai artifact

| Dieu kien | Nguong | Hanh dong | Nguon |
|---|---|---|---|
| Prefetch: so lan chay mot chuong trinh | So sanh voi baseline cua cung chuong trinh | Ghi nhan khi lech, doi chieu | **NOT_AVAILABLE** - can baseline theo may, khong co bang so trong repo |
| LNK / Jump List tro tep ngoai vung du lieu cua user | 1 muc dang ngo | Ghi nhan, doi chieu | Ban do artifact trong `artifact-location-map.md` |
| ShimCache / BAM-DAM ghi nhan chuong trinh chay voi user khac voi user dang xuat hien | 1 muc dang ngo | Ghi nhan, doi chieu log tai khoan | Ban do artifact trong `artifact-location-map.md` |
| Registry Run/RunOnce co muc dang ngo | 1 muc dang ngo | Chuyen sang quy trinh chuyen sau | `../../analyzing-windows-registry-for-artifacts/SKILL.md` |
| MFT: file bi xoa ngoai khoi duong dan mong doi | 1 muc dang ngo | Ghi nhan, doi chieu | Quy trinh MFTECmd |
| Timeline: su kien xay ra ngoai gio lam viec | So su kien so voi khung gio lam viec | Ghi nhan, phan loai ngoai gio | `../../_shared/off-hours-and-baseline.md` |

## Phan cap cho tung loai phat hien

Phan cap lay tu `../../_shared/severity-and-escalation.md` (nguon duy nhat), khong dat lai trong file nay.

| Phat hien | Dieu kien phan cap |
|---|---|
| Persistence da xac nhan | P3 |
| Persistence kem dau hieu C2 | P1 |
| Truy cap credential qua registry | P2, P1 neu da rut du lieu |

## Ghi chu calibration

- Prefetch chi cho so lan chay va moc thoi gian gan nhat. KHONG duoc suy dien "lan chay dau tien"
  hay "chay nhieu hon moi la bat thuong" ma khong co baseline.
- LNK va Jump List chi chung minh tep da mo, KHONG chung minh tep da doc hay da exfiltrate.
- Skill nay khong co bang so nguong dinh luong duoc chung minh trong repo. Moi nguong dinh luong
  phai lay tu baseline cua he thong dang phan tich; neu chua co -> ghi `insufficient_evidence`.
- Pham vi thu thap thuc te phai duoc chung minh bang output `--tlist` cua KAPE kem bao cao.
