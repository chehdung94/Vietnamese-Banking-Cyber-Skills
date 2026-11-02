# Chi bao rui ro va huong dan cham diem - playbook-abnormal-user-behavior-response

Day la chi tiet cua ma tran 7 chi bao. SKILL.md chi nho quy tac dung chung.

> **Luu y:** Diem Jev chi **xep hang** rui ro de uu tien dieu tra, **khong phai** phan cap
> su co. Phan cap P1-P4 lay tu `../_shared/severity-and-escalation.md`.
> Diem chi co gia tri khi kem rationale tu log/bang chung cu the.

## 1. Ma tran 7 chi bao

| Chi bao | Trong so | Nguoi dung co dieu kien nay? | Diem |
|---|---|---|---|
| **Recent HR Event** | Cao | Yes / No / Unknown | 0 - 10 |
| **Access to Sensitive Data** | Cao | Yes / No / Unknown | 0 - 10 |
| **Unusual Data Volume** | Trung binh | Yes / No / Unknown | 0 - 5 |
| **Off-Hours Activity** | Trung binh | Yes / No / Unknown | 0 - 5 |
| **Personal Device Usage** | Trung binh | Yes / No / Unknown | 0 - 5 |
| **External Communication** | Thap | Yes / No / Unknown | 0 - 3 |
| **Job Performance Issues** | Thap | Yes / No / Unknown | 0 - 3 |

Dai diem toi da va ngan xep hang: `references/thresholds.md`.

## 2. Huong dan calibration diem

Moi diem phai co rationale tu log/bang chung cu the. Khong gan diem dua tren "cam nhan".

| Chi bao | Bang chung y eu (diem thap) | Bang chung trung binh | Bang chung manh |
|---|---|---|---|
| **Recent HR Event** | Khong co su kien HR | Da nop don nghi viep, offboarding thong thuong | Nghi viep + ky luat + truy cap du lieu nhay |
| **Access to Sensitive Data** | Khong truy cap du lieu nhay | Truy cap gián tiep (chi bao cao) | Truy cap truc tiep CRUD du lieu PII / tai chinh khach hang |
| **Unusual Data Volume** | Khong co lech | lech nho duoi 2 lan baseline | lech lon hon 5 lan baseline; nhieu kenh song song |
| **Off-Hours Activity** | Khong co | thinh thoang (duoi 5 lan / thang) | mau hien thuong xuyen; su kien trung tam quanh ngay nghi viep |
| **Personal Device Usage** | Khong co | co thu bi chan (Evidence = 0) | sao chep du lieu thanh cong; bo qua DLP; nhieu loai thiet bi |
| **External Communication** | Chi doi tac nghiep | khong luong / thoi diem khong thuong voi doi tac | Gmail/Yahoo ca nhan kem file dinh kem; gui standalone khong co nguoi nhan noi bo |
| **Job Performance Issues** | Khong co thong tin; dat yeu | canh bao mieng | PIP hoac ky luat da lap thanh van ban |

Dieu kien do voi so 0 - 3 la bang chung yeu, 4 - 6 trung binh, 7 - 10 manh.

## 3. Quy tac cham diem (bat buoc)

- [ ] Moi diem co rationale cu the (dong log, timestamp, event ID).
- [ ] Diem nam trong pham vi cho phep (khong vuot max).
- [ ] Tong diem = tong truc tiep 7 chi bao. Khong ap "cap". Phan loai theo ngan.
- [ ] Da phan biet "DLP BLOCKED (khong mat du lieu)" va "SUCCESSFUL exfiltration".
- [ ] Da thuc hien cross-user correlation neu co tu 2 nguoi dung tro len.
- [ ] Context dinh tinh tach rieng (Section 5), khong cong vao diem chinh thuc.

## 4. Checklist co hieu mau (behavioral red flags)

- [ ] User tai luong lon du lieu khach hang.
- [ ] Truy cap du lieu ngoai pham vi cong viec (khong co business justification).
- [ ] Sua doi quyen truy cap khong duoc authorize.
- [ ] Dung USB ca nhan hoac cloud storage ca nhan.
- [ ] In / copy tai lieu nhay.
- [ ] Gui email kem file dinh kem lon den dia chi ngoai.
- [ ] Dang nhap tu dia diem la / quoc gia khac.
- [ ] Thay doi hanh vi dot ngot (vi du phan lon thoi gian truoc day binh thuong).
- [ ] Copy du lieu vao thu muc it nguoi dung hoac thu muc ca nhan.
- [ ] Dung service account hoac shared account.

## 5. Context dinh tinh mo rong (khong cong vao diem)

| # | Phat hien | Muc do | Nguon | Ghi chu |
|---|---|---|---|---|
| E1 | Virus / malware tren endpoint | Critical | Endpoint security | Dac biet neu tu USB ca nhan |
| E2 | User vi pham rule Access Protection | Critical | Endpoint security | Hanh vi truy cap trai phep |
| E3 | HTTP traffic bypass web proxy | High | Endpoint + Proxy | Pattern tu dong hoac script |
| E4 | Webmail upload bi chan | Medium | Web gateway | Hien y dung webmail ca nhan |
| E5 | Cross-mailbox xuat hien Gmail ca nhan (collusion) | Critical | Email | Can dieu tra mo rong |

## 6. Ban cham diem mau (vi du)

```json
{
  "jev_rank": "High",
  "jev_rank_only": true,
  "indicators": {
    "recent_hr_event": {"score": 6, "rationale": "Don nghi viep duoc HR xac nhan truoc khoang phat hien"},
    "access_to_sensitive_data": {"score": 8, "rationale": "Event 4663 doc tep luong du lieu khach hang"},
    "unusual_data_volume": {"score": 4, "rationale": "Vuot nguong baseline theo bang nguong"},
    "off_hours_activity": {"score": 3, "rationale": "Hoat dong ngoai khung gio lam viec"},
    "personal_device_usage": {"score": 0, "rationale": "Khong co bang chang"},
    "external_communication": {"score": 2, "rationale": "Mot email ra ngoai kem file dinh kem"},
    "job_performance_issues": {"score": 0, "rationale": "Khong co thong tin"}
  },
  "qualitative_context": [
    {"id": "E3", "note": "HTTP bypass proxy, khong cong vao diem"}
  ],
  "severity": "P2",
  "severity_source": "../_shared/severity-and-escalation.md"
}
```
