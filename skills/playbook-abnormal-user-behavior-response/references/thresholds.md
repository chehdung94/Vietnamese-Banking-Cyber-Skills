# Bang nguong - playbook-abnormal-user-behavior-response

Nguon su fact duy nhat cho nguong cua skill nay. SKILL.md khong nhoe lai so lieu.

> **Luu y quan trong:** Cac diem va ngan cua "Jev" o day chi la **xep hang rui ro**
> (risk rank) de sap xep uu tien dieu tra. Chung **khong phai** muc phan cap su co.
> Muc phan cap su co (P1/P2/P3/P4) lay tu `../_shared/severity-and-escalation.md`.
> Jev khong bao gio nang muc phan cap.

## 1. Trong so 7 chi bao (Jev) va dai diem toi da

| Chi bao | Trong so toi da | Diem toi da |
|---|---|---|
| Recent HR Event | High | 10 |
| Access to Sensitive Data | High | 10 |
| Unusual Data Volume | Medium | 5 |
| Off-Hours Activity | Medium | 5 |
| Personal Device Usage | Medium | 5 |
| External Communication | Low | 3 |
| Job Performance Issues | Low | 3 |

| Hanh dong | Nguon |
|---|---|
| Diem = 0 khi khong co bang chung, chi ghi "khong ro" | Rules dong 1-3 |
| Diem phai kem rationale tu log cu the | `references/indicators.md` |
| Tong diem = tong truc tiep 7 chi bao, khong ap cap | `references/indicators.md` |

## 2. Ngan xep hang Jev (khong phai phan cap su co)

| Diem | Xep hang | Hanh dong |
|---|---|---|
| 21 - 28 | Critical | Dieu tra ngay, can nghi suy hoac tam khoa tai khoan |
| 14 - 20 | High | Dieu tra uu tien, tang cuong giam sat |
| 7 - 13 | Medium | Dieu tra tieu chuan |
| 0 - 6 | Low | Giam sat, co the false positive |

> Xep hang nay chi sap xep thu tu xu tri. Muc phan cap P1-P4 lay tu
> `../_shared/severity-and-escalation.md`.

## 3. Baseline va nguong bat thuong

| Quy tac | Gia tri | Nguon |
|---|---|---|
| Chu ky baseline | 7 - 30 ngay lam viec gan nhat cua tung user | `../_shared/off-hours-and-baseline.md` |
| So mau toi thieu cho phep dung luong baseline | it nhat 7 ngay baseline | `../_shared/off-hours-and-baseline.md` |
| So mau moi toi thieu moi ngay | it nhat 20 mau | `../_shared/off-hours-and-baseline.md` |
| Cong thuc nguong | baseline + 3 sigma | `../_shared/off-hours-and-baseline.md` |
| Khong co file baseline | ghi `insufficient_evidence`, khong uoc luong mu/sigma | `../_shared/off-hours-and-baseline.md` |

Cong thuc (chi ap dung khi du dieu kien so mau o tren):

```
Hanh vi trung binh = mu (trung binh cac mau trong chu ky baseline)
Nguong canh bao     = mu + 3 sigma
Canh bao ngay lap tuc khi vuot muc cao hon trong bang nay
```

| Muc | Nguon |
|---|---|
| Canh bao | baseline + 3 sigma | `../_shared/off-hours-and-baseline.md` |
| Canh bao ngay lap tuc | baseline + 5 sigma | Bang ghi chu trong skill nay, ghi ro nguon khi tach calibration moi |

## 4. Hoat dong tep tin

| Thao tac | Nguong canh bao | Muc do | Mo ta |
|---|---|---|---|
| Mo tep hang loat | 500 tep tro len trong 4 phut tro xuong | Cao | Phan anh sao chep hoac truy xuat du lieu hang loat |
| Xoa tep | Vuot nguong baseline + 3 sigma | Trung binh | Co the la nham giau dau vet |
| Cut (move) tep | Vuot nguong baseline + 3 sigma | Trung binh | Chuyen du lieu ra khoi vi tri thuong |
| Tai tep xuong | Vuot nguong baseline + 3 sigma | Cao | Khoi luong vuot muc binh thuong |
| Tao file nen | tren 500MB, dinh dang .zip / .rar / .7z | Cao | Chuan bi du lieu de exfiltrate |

## 5. Kenh exfiltration

| Kenh | Canh bao khi | Muc do |
|---|---|---|
| Email | tren 5 email ra ngoai kem file dinh kem | Cao |
| OneDrive / SharePoint | bat ky hoat dong cloud storage ca nhan nao | Cao |
| USB | bat ky hoat dong USB co du lieu nhay | Cao |
| VPN | tren 10GB du lieu truyen qua | Cao |
| In | bat ky tai lieu nhay nao du in | Trung binh |

## 6. Phan loai su co DLP

| Tinh huong | Hieu luc DLP | Rui ro hanh vi |
|---|---|---|
| BLOCKED + Evidence = 0 | DLP dang hoat dong | Cao |
| BLOCKED + Evidence > 0 | Co lot mot phan | Critical |
| ALLOWED + khong co policy | Lo hong DLP | Critical |
| NOT DETECTED | Diem blind spot | Critical |

## 7. Endpoint security

| Chi bao | Nguong | Nguon |
|---|---|---|
| Muc do su co malware / virus tren endpoint | tu 7 tro len | Bang ghi trong skill nay, tach calibration tai references/thresholds.md |
| Rule violation truy cap | bat ky | Bang ghi trong skill nay |

## 8. Chi bao collusion

| Chi bao | Xep hang | Hanh dong |
|---|---|---|
| User A gui email standalone den Gmail ca nhan cua User B | Critical | Leo thang ngay |
| User A xuat hien trong mailbox forwarding rules cua User B | Critical | Leo thang ngay |
| Shared external communication pattern giua cac user | High | Mo rong dieu tra |
| Hoat dong cung thoi diem bat thuong giong nhau | Medium | Ghi nhan de review |

## 9. Phan cap su co

Khong tao bang phan cap rieng. P1 Critical / P2 High / P3 Medium / P4 Low va muc phan hoi
lay tu `../_shared/severity-and-escalation.md`.

## Ghi chu calibration

- Cac nguong muc 4, 5, 7, 8 lay tu kinh nghiem xu ly su co trong repo. Neu co du lieu
  huan luyen du de chot lai, ghi thay doi o cuoi file va ghi ro nguon.
- Moi nguong phai co bang hoac so tuong doi. Thieu bang goc -> ghi `NOT_AVAILABLE` va ly do.
