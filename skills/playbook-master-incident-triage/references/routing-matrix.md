# Bang dieu huong - playbook-master-incident-triage

Muc tieu: chon dung Tier 2 playbook de xu ly. Chi dua vao loai su co, dieu kien
va bang chung san co. Bang chi tiet ve phan cap: `references/thresholds.md`.

## Loai su co -> Tier 2 playbook

| Loai su co | Dieu kien | Route den |
|---|---|---|
| Phishing | Email doc hai, trang thu thong tin xac thuc, tep dinh kem doc hai | `../playbook-phishing-response/SKILL.md` |
| Hanh vi nguoi dung bat thuong / insider | User da xac thuc co hoat dong khac baseline, can giam sat them | `../playbook-abnormal-user-behavior-response/SKILL.md` |
| Truy cap noi bo ngoai gio (RDP / lateral) | RDP tu may nguoi dung A den may nguoi dung B ngoai gio lam viec | `../playbook-after-hours-internal-access-investigation/SKILL.md` |
| Review truoc khi nghi viep | HR thong bao ngay nghi viep, can danh gia rui ro rut du lieu truoc moc cutoff | `../playbook-pre-departure-insider-review/SKILL.md` |
| Giam sat watchlist hang ngay | Chay chuong trinh giam sat danh sach nhan vien theo dot log | `../playbook-daily-watchlist-monitoring/SKILL.md` |
| Malware / ransomware | Ma hoa dang chay, C2 xac nhan | `NOT_AVAILABLE` - khong co playbook rieng trong repo, leo thang CIRT va cach ly he thong |
| Rut du lieu / data leak | Da co bang chung du lieu bi rut ra ngoai | `NOT_AVAILABLE` - khong co playbook rieng trong repo, leo thang CIRT, thu muc an toan, danh gia thong bao theo `../_shared/compliance-vietnam.md` |
| Khong ro loai su co | Da phan cap P1/P2 nhung chua xac dinh loai | `NOT_AVAILABLE` - tach ly he thong, leo thang CIRT, tiep tuc phan loai Tier 3 |

> Luu y: `NOT_AVAILABLE` nghia la khong co skill dung trong repo hien tai, khong phai
> loai su co khong ton tai.

## Cau hoi phan loai

- He thong nao bi anh huong?
- Timeline su co la gi (thoi diem xay ra hanh vi va thoi diem phat hien)?
- Ai phat hien, nguon phat hien la gi?
- Du lieu nao co the bi anh huong, da co bang chung chua?
- Co ba hieu lateral movement hoac attacker van con trong he thong khong?
- Day co phai chua trien khai dung Tier 2 nao khong?

## Luong phan loai theo tier

Kiem tra cau truc 3 tier tai
`../playbook-daily-watchlist-monitoring/references/tier_flow.md`.

## Ghi chu calibration

- Neu them playbook moi vao repo, bo sung hang tuong ung va phan loai theo loai su co.
- Khong sua file nay de ghi nguong so lieu; nguong nam o `references/thresholds.md`.
