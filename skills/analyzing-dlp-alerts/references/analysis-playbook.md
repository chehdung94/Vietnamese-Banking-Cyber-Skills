# Quy trinh phan tich chi tiet - analyzing-dlp-alerts

Tai lieu nay giu phan SOP dai, bang mau va vi du. SKILL.md chi giu phan dac biet va tro toi file nay.

## 1. Nen tang DLP ho tro

| Nen tang | Cach phan tich |
|---|---|
| Microsoft Purview | Purview Compliance Portal, PowerShell |
| Varonis DLP | Varonis WebGUI, Export Reports |
| Symantec DLP | DLP Console, Event Logs |
| Generic syslog | Truy van SIEM (Splunk, ELK, QRadar) |

## 2. Truong du lieu can thu thap

| Check | Truong | Vi du |
|---|---|---|
| [ ] | Alert ID | PURVIEW-2024-001234 |
| [ ] | Loai canh bao | EmailAttachment, FileUpload, Print |
| [ ] | Policy vi pham | "Block Sensitive Data External Email" |
| [ ] | Nguoi dung / tai khoan | [EMAIL] |
| [ ] | Thoi diem | 2024-01-15 14:30:00 UTC |
| [ ] | Phan loai du lieu | PII, Financial, Confidential |
| [ ] | Hanh dong da thuc hien | Blocked, Warning, Allowed |

## 3. Phan loai alert

| Loai alert | Mo ta | Huong xu ly |
|---|---|---|
| True positive | Vi pham chinh sach that, du lieu thuoc ve thuong co bi at risk | Tiep tuc dieu tra, phan cap theo bang shared |
| False positive | Policy kich hoat nhung khong co rui ro that (test, da duyet) | Ghi nhan, de xuat dieu chinh policy |
| Intentional | Co bieu chung kinh doanh duoc duyet | Ghi nhan keo lai, doi chieu bang chung duyet |
| Policy error | Rule sai, can tinh chinh | Ghi nhan, de xuat sua policy |

> Khong gan nhan false positive chi vi "giong mau". Can bang chung (nguoi dung, thoi diem, file,
> nguoi duyet) cho moi phan loai.

## 4. Loai du lieu at risk

| Loai du lieu | Vi du | Muc do rui ro tham chieu |
|---|---|---|
| PII | Ho ten, so dinh danh, ngay sinh, dia chi | Cao |
| Tai chinh | So tai khoan, the tin dung, giao dich | Rat cao |
| Suc khoe | Ho so benh an, bao hiem | Rat cao |
| Thong tin dang nhap | Mat khau, API key, token | Rat cao |
| IP / bibo mat | Ma nguon, ke hoach kinh doanh | Cao |
| Noi bo | Memo, du lieu nhan su, tai lieu noi bo | Trung binh |

> Muc P1-P4 cua su co khong lay truc tiep tu bang nay. Phan cap theo `../_shared/severity-and-escalation.md`.

## 5. Duong ro ri (route)

| Duong | Mo ta | Muc do rui ro tham chieu |
|---|---|---|
| Email ngoai | Gui ra dia chi ca nhan / ngoai to chuc | Rat cao |
| Cloud ca nhan | Upload len Google Drive, Dropbox | Rat cao |
| USB | Sao chep sang thiet bi luu tru | Cao |
| In | In tai lieu nhay cam | Cao |
| Cloud app | Upload len SaaS khong duyet | Cao |
| Mang xa hoi | Dang bai len nen tang mxh | Cao |

## 6. Luong du lieu va tinh la duy lap

| Chi so | Y nghia |
|---|---|
| So ban ghi | So muc du lieu bi lo |
| Kich thuoc tep | Tong dung luong lien quan |
| So nguoi nhan | Bao nhieu ben ngoai nhan duoc |
| Tinh la duy lap | Lan dau hay lap lai |

## 7. To hop timeline da nguon

1. Tao timeline thong nhat tu tat ca nguon log trong pham vi.
2. Sap xep theo thoi diem tang dan.
3. Danh dau cac su kien nam trong cua so lien quan (nguong tai `references/thresholds.md`).
4. Tao chuoi quan he khi chuoi su kien co logic (vi du: DLP chan -> phat hien virus -> truy cap bi chan).

Quy tac lien quan theo tung cap su kien: xem `references/thresholds.md`.

### 8. Danh gia hieu qua lop bao ve

| Lop bao ve | So su kien | So bi chan | So duoc phep (khoang trong) | Nhan gia |
|---|---|---|---|---|
| Device control | | | | |
| Email DLP | | | | |
| Web filter | | | | |
| Endpoint protection | | | | |

## 9. Trich xuat IOC

```json
{
  "alert_id": "PURVIEW-2024-001234",
  "user": "[EMAIL]",
  "timestamp": "NOT_AVAILABLE",
  "data_type": [],
  "record_count": 0,
  "exfiltration_route": "NOT_AVAILABLE",
  "recipients": [],
  "files_involved": [],
  "pattern": "FirstOccurrence",
  "risk_score": "NOT_AVAILABLE"
}
```

> Chi ghi gia tri co trong bang chung. Truong khong co du lieu ghi `[NOT_AVAILABLE]` (Rules dong 1-3).

## 10. Kiem tra hanh vi theo chuoi cua nguoi dung

| Kiem tra | Cach truy van |
|---|---|
| Canh bao DLP truoc do cua nguoi dung | Lich su trong DLP Console |
| Dang nhap tu dia diem la | Log sign-in Entra ID |
| Tai du lieu lon | Hoat dong file trong Varonis |
| Hoat dong ngoai gio | Phan tich thoi gian trong SIEM |

## 11. Hanh dong de xuat (chi DE XUAT, khong chay tu dong)

| Hanh dong | Cach thuc | Ghi chu |
|---|---|---|
| Chan domain nguoi nhan | Purview: sua policy DLP | Can xac nhan nguoi dung truoc khi chay |
| Thu hoi email | Exchange: `Search-Mailbox -DeleteContent` | Chi khi co xac nhan va co phep xoa |
| Thu hoi session cloud | Entra ID: revoke sessions | Can xac nhan |
| Khoa tai khoan | AD: disable account | Can doi chieu HR va muc do su co |
| Thong bao DPO | Email/Lich | Khi co du lieu ca nhan hoac tai chinh |

## 12. Ma leo thang

Dung ma leo thang trong `../_shared/severity-and-escalation.md`. Moc thoi gian phap ly lay tu
`../_shared/compliance-vietnam.md` (khong ghi so gio/ngay cuc the o day).
