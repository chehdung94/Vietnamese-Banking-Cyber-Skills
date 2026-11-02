# Quy trinh phan hoi va khoi phuc - playbook-abnormal-user-behavior-response

Moi hanh dong kham phuc trong file nay chi la **DE XUAT**. Khong chay khi chua co xac
nhan ro rang cua nguoi dung (Rules dong 6).

## 1. Ke hoach truyen thong noi bo

Cac moc T+ la SLA phong giao noi bo, khong phai moc thoi gian phap ly.
Moc phap ly: xem `../_shared/compliance-vietnam.md`.

| Moc | Doi tieu | Noi dung |
|---|---|---|
| T+15 phut | Security Lead | UEBA alert cho [user]. Dang dieu tra. Xac dinh xep hang rui ro ban dau. |
| T+1 gio | CISO (neu xep hang cao) + IT Ops + HR | Bat dau phep tac thuong. IT Ops: bat giam sat nang cao cho tai khoan. HR: kiem tra su kien HR gan day (offline, kiem tra tac). |
| T+4 gio | CISO + Legal + HR | Hoan thanh danh gia rui ro. Quyet dinh co tam khoa tai khoan hay khong. Chuan bi ke hoach truyen thong. |
| T+24 gio | CISO | Bao cao cuoi. Quyet dinh buoc xu ly ky luat. Cap nhat quy tac phat hien neu false positive. |

Muc phan hoi theo phan cap su co lay tu `../_shared/severity-and-escalation.md`.

## 2. Khuyen nghia tam thoi

### 2.1 Nghi insider doc hai

1. **Tang cuong giam sat** (khong chan):
   - Bat canh bao real-time cho tai khoan.
   - Theo doi email va file transfer.
   - Review badge access log.
2. **Hanh chi quyen truy cap** (neu bang chung manh):
   - Tam thoi go quyen truy cap du lieu nhay.
   - Disable external email forwarding rules.
   - Chan USB (neu DLP ho tro).
3. **Neu xac nhan doc hai:**
   - Phoi hop voi HR va Legal.
   - Bao ton bang chung (KHONG thong bao nguoi dung).
   - Ke hoach cham dot tai khoan (neu da quyet dinh).

### 2.2 Tai khoan bi chiem (khong phai insider)

1. **Ngan chặn ngay:**
   - Force password reset.
   - Revoke tat ca session dang active.
   - Bat MFA neu chua co.
2. **Uu tien dieu tra:**
   - Xac dinh vector xam nhap (phishing, credential theft).
   - Kiem tra cac tai khoan khac bi chiem.
   - Review dau hieu lateral movement.

## 3. Hanh dong ngan han va hardening

### 3.1 Ngan han

| Hanh dong | Mo ta | Uu tien |
|---|---|---|
| Go quyen truy cap nhay | Tam go cho den khi dieu tra xong | Cao |
| Force password reset | Neu ngh tai khoan bi chiem | Cao |
| Bat MFA | Neu chua bat | Cao |
| Lien lac voi nguoi dung | Chi sau khi HR / Legal phe duyet | Trung binh |

### 3.2 Dai han

| Control | Muc dich | Hinh thuc trien khai |
|---|---|---|
| **Data Access Governance** | Gioi han quyen theo nguyen can biet | Varonis DataPrivilege |
| **UEBA Alert Tuning** | Giam false positive | Review va tune rule |
| **DLP Policies** | Phat hien exfiltration tuong lai | Microsoft Purview / Varonis |
| **Security Awareness** | Giam rui ro phishing / ransomware | Chuong trinh dao tao |
| **Privileged Access Monitoring** | Phat hien lam dung quyen | PAM solution |

## 4. Xu ly false positive

- [ ] Ghi ro vi sao alert la false positive.
- [ ] Ghi nhan ly do kinh doanh chinh dang.
- [ ] Cap nhat baseline UEBA cho nguoi dung / peer group nay.
- [ ] Gui yeu cau tune rule cho doi an ninh.
- [ ] Theo doi tuong tu trong ky giam sat tiep theo (khung thoi gian lay tu
  `../_shared/off-hours-and-baseline.md` va lich cham soc).

## 5. Doc them

| Nguon | Noi dung |
|---|---|
| `../_shared/severity-and-escalation.md` | Thang phan cap P1-P4, muc phan hoi, nguoi duoc bao, ma leo thang |
| `../_shared/compliance-vietnam.md` | Han chot thong bao vi pham, ho so danh gia tac dong, thong bao nguoi bi pham vuot |
| `../_shared/off-hours-and-baseline.md` | Khung gio lam viec, timezone, quy tac baseline mu/sigma |
| `references/thresholds.md` | Bang nguong cua skill nay |
