# Quy trinh phan tich da kenh - playbook-pre-departure-insider-review

## 1. Chuan bi phan tich

1. Xac dinh khung dieu tra (ngay nop don -> ngay nghi, kem buffer): `references/thresholds.md`.
2. Chot domain whitelist cho email: `references/thresholds.md`.
3. Dinh nghia ngoai gio theo `../_shared/off-hours-and-baseline.md` (co ngoai gio hay khong).
4. Kiem tra log co du baseline de so sanh khong. Khong co baseline -> ghi
   `insufficient_evidence`, khong uoc luong mu/sigma.

## 2. Email audit (bat buoc)

Tier 3: `../analyzing-email-audit-logs/SKILL.md`.

Quy trinh bat buoc:

1. Thiet lap domain whitelist (doi tac ngan hang, nha cung cap).
2. Phan loai email theo huong (INBOUND / OUTBOUND).
3. Phat hien email standalone den dia chi ca nhan (Gmail, Yahoo) khong co nguoi nhan noi bo.
4. Cross-user correlation neu co tu 2 nguoi dung.
5. Phan tich attachment: kich thuoc, loai, tan suat, co tep nhay khong.

## 3. Web gateway / DLP (bat buoc)

Tier 3: `../analyzing-dlp-alerts/SKILL.md`.

Trong tam quan sat:

- Cloud storage ca nhan (one drive personal, google drive personal, dropbox).
- Webmail ca nhan (mail google, mail yahoo).
- Upload len social media.
- Trang chia se tep tin.

## 4. Device control / DLP (bat buoc)

Tier 3: `../analyzing-dlp-alerts/SKILL.md`.

Trong tam quan sat:

- USB storage device (disk drive).
- External HDD / SSD.
- MTP device (dien thoai).
- Network adapter (duong gia truyen du lieu).

Phan biet "DLP BLOCKED (khong mat du lieu)" va "ALLOWED (du lieu da ra ngoai)".

## 5. Endpoint security (bat buoc)

Trong tam quan sat:

- Su kien malware / virus (muc do tu 7 tro len: xem `references/thresholds.md`).
- Vi pham rule Access Protection.
- Vi pham policy OUTGOING_HTTP.
- Vi pham web policy.

## 6. VPN (co dieu kien)

Tier 3: `../analyzing-vpn-logs/SKILL.md`.

Tra loi 5 cau hoi Value Assessment truoc khi phan tich. Neu so cau tra loi "Khong"
vuot nguong quy dinh trong `../analyzing-vpn-logs/references/thresholds.md` -> can nhac
SKIP, ghi ro ly do trong bao cao va ghi path nguon log de dung sau nay.

## 7. Phat hien keo theo (cross-user collusion)

> Kich hoat khi co tu 2 nhan vien sap nghi viep, dac biet neu cung phong ban.

Quy trinh:

1. Kiem tra email communication giua cac user.
2. Cross-mailbox: tim Gmail ca nhan cua User A trong recipients cua User B.
3. Timeline correlation: hoat dong phoi hop ngoai gio.
4. Shared resource access patterns.

Chi bao keo theo va muc do: `../playbook-abnormal-user-behavior-response/references/collusion-and-dlp.md`.

## 8. Danh gia rui ro

- Ap dung ma tran 7 chi bao tai `../playbook-abnormal-user-behavior-response/references/indicators.md`.
- Calibration rieng cho boi canh nghi viep: xem muc 9 file do.
- Xep hang Jev chi la xep hang; phan cap su co lay tu `../_shared/severity-and-escalation.md`.

## 9. Calibration rieng cho boi canh nghi viep

| Chi bao | Xac nhan them cho boi canh nghi viep |
|---|---|
| Recent HR Event | Diem dua tren boi canh don nghi viep: tu nguyen chon hay lien quan hieu nang |
| Access to Sensitive Data | Truong phong co quyen truy cap du lieu nhay tang mac dinh |
| Personal Device Usage | Phan biet DLP BLOCKED voi ALLOWED - hai truong huong khac nhau |
| External Communication | Email standalone la chi bao manh nhat |

## 10. Danh gia hieu luc DLP

Danh gia tung lop bao ve:

- Bi chan thanh cong -> DLP dang hoat dong.
- Cho qua khi khong co policy -> lo hong DLP.
- Chi phat hien ma khong chan -> bao ve mot phan.

Bieu mau hieu luc: `../playbook-abnormal-user-behavior-response/references/collusion-and-dlp.md`.
