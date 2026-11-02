# Phan tich hoat dong tep tin - playbook-abnormal-user-behavior-response

Day la chi tiet Section phat hien bat thuong hoat dong tep tin (nhom du lieu chinh).
Nguong: `references/thresholds.md`.

## 1. Nhom 1 - giam sat hoat dong tep tin

Theo doi cac thao tac lien quan den tep tin voi nguong canh bao cu the (bang o
`references/thresholds.md`):

| Thao tac | Nguong canh bao | Muc do | Mo ta |
|---|---|---|---|
| Mo tep hang loat | xem bang nguong | Cao | Sao chep hoac truy xuat du lieu hang loat |
| Xoa tep | xem bang nguong | Trung binh | Nham giau dau vet |
| Cut (move) tep | xem bang nguong | Trung binh | Chuyen du lieu ra khoi vi tri thuong |
| Tai tep xuong | xem bang nguong | Cao | Khoi luong vuot muc binh thuong |
| Tao file nen | xem bang nguong | Cao | Chuan bi du lieu de exfiltrate |

## 2. Nhom 2 - phan tich so sanh hanh vi

So sanh hanh vi nguoi dung qua cac khoang thoi gian de xac dinh deviant behavior.

Cong thuc:

```
Hanh vi trung binh = mu (trung bien cac mau)
Nguong canh bao     = mu + 3 sigma
```

Quy tac baseline (nguon duy nhat):
`../_shared/off-hours-and-baseline.md`.

## 3. Chi so thu thap

| Chi so | Mo ta | Don vi |
|---|---|---|
| So tep truy cap | So luong file mo/doc | files/day |
| So lan dang nhap | Login count | logins/day |
| Khoi luong du lieu | Data transmitted | MB/day |
| Thoi gian hoat dong | Trong gio lam viec | hours |
| Thoi gian ngoai gio | Ngoai gio lam viec | hours |
| So thiet bi | Unique devices used | devices |

## 4. Phan tich log de phat hien bat thuong

### 4.1 Thu thap log tu cac nguon

- Windows Event Logs (Security 4656, 4663, 4670).
- File Server Audit Logs (Varonis, NTFS Auditing).
- EDR Process Events (CrowdStrike, Defender).
- Cloud Storage Sync Logs (OneDrive, SharePoint).

### 4.2 Xay dung baseline tu du lieu thuc te

```
Baseline = mu (trung binh hoat dong trong chu ky baseline theo bang nguong)
Do lech chuan = sigma
Nguong = mu + 3 sigma (tu dong tinh tu log)
```

Quy tac so mau: xem `../_shared/off-hours-and-baseline.md`. Khong co file baseline ->
ghi `insufficient_evidence`, khong uoc luong mu/sigma.

### 4.3 So sanh hoat dong hien tai voi baseline

- Hoat dong vuot nguong trong bang -> canh bao.
- Vuot muc cao hon trong bang -> canh bao ngay lap tuc.
- Quy tac chuyen doi timezone theo nguon log: `../_shared/off-hours-and-baseline.md`.

### 4.4 Chi so can phan tich tu log

| Chi so | Nguon log | Cach phat hien |
|---|---|---|
| Tan suat truy cap file | Windows Security, Varonis | Dem event ID 4656 / 4663 tren thu muc |
| Khoi luong du lieu | File Server, Cloud Storage | Tong hop size cua file accessed |
| Thoi gian truy cap | Security Event Logs | Kiem tra timestamp co ngoai gio lam viec khong |
| Loai file truy cap | File extension logging | Kiem tra truy cap .pst, .bak, .csv, .xlsx |
| Vi tri truy cap | IP, hostname | So sanh voi danh sach thiet bi thuong |
| Toc do truy cap | Timestamp analysis | Tinh khoang cach thoi gian giua cac event |

## 5. Vi du phan tich log

```
Nguon: Windows Security Log (Event ID 4663)
Mau log:
  "TimeCreated: [timestamp]
   User: DOMAIN\[user]
   Object: \[file server path]
   AccessMask: READ_DATA"

Phan tich:
  - So luong file accessed trong 1 phut: [so]
  - Baseline cua user nay: [so] file/phut
  - Ty le: [so] lan nguong binh thuong
  - Ket luan: BAT THUONG -> canh bao duoc kich hoat
```

## 6. Vi du phat hien thuc te

```json
{
  "user": "[EMAIL]",
  "timestamp": "[timestamp]",
  "detection": {
    "pattern": "MassFileAccess",
    "files_opened": 5420,
    "time_window_minutes": 3.5,
    "files_per_minute": 1548,
    "threshold_applied": "bang nguong trong references/thresholds.md",
    "result": "EXCEEDED",
    "jev_rank": "High",
    "severity": "P3"
  },
  "risk_indicators": {
    "unusual_volume": true,
    "rapid_succession": true,
    "after_hours": false,
    "new_device": false
  },
  "recommended_action": "Immediate investigation required"
}
```

## 7. Thu thap bang chung cho hoat dong tep

| Check | Nguon log | Muc dich |
|---|---|---|
| [ ] | EDR solution (CrowdStrike, Defender) | Process execution, file access |
| [ ] | Varonis DatAdvantage | File server activity, permission changes |
| [ ] | Windows Event Logs (Security 4656, 4663) | File open / modify operations |
| [ ] | DLP alerts | Trigger truy cap du lieu nhay |
| [ ] | Cloud storage logs | OneDrive / SharePoint sync activity |
