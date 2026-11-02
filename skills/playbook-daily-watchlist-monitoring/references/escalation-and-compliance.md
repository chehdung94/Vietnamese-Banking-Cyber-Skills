# Leo thang va checklist phap ly - Daily Watchlist Monitoring

Tai lieu tach tu `SKILL.md` (ban v2.0). Noi dung ky thuat giu nguyen; moi moc thoi gian
phap ly va khung gio lam viec duoc tro ve nguon su fact duy nhat.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4 va cua so phan hoi: xem `../_shared/severity-and-escalation.md`.
> Khung gio lam viec, ngoai gio, baseline: xem `../_shared/off-hours-and-baseline.md`.

## 1. Khoi leo thang (Tier 2 -> Tier 3)

Dieu kien leo thang danh sach tai `references/thresholds.md`.

## 2. Nguon du lieu bo sung khi leo thang

| Nguon | Muc dich | Thoi gian luu giu |
|---|---|---|
| SIEM Logs | Hoat dong mang, chuyen tiep can ngang | 30 ngay |
| USB/Device Logs | Sao chep du lieu qua USB | 30 ngay |
| Endpoint Logs | Thuc thi tien trinh, truy cap file | 30 ngay |
| Email Gateway | Xac nhan email da gui | 90 ngay |
| VPN Logs | Dia diem ket noi | 30 ngay |

## 3. Ke hoach lien lac noi bo

Moc phan cap va cua so phan hoi theo `../_shared/severity-and-escalation.md`;
trinh tu thao tac noi bo nam trong `references/thresholds.md`.

## 4. Checklist cong bo viec (ngan hang)

Noi dung checklist phap ly lay nguyen tu `../_shared/compliance-vietnam.md`:

- [ ] Da xac dinh co phai vi pham du lieu ca nhan hay khong (khong phai moi su co an ninh mang deu la vi pham du lieu)
- [ ] Da ghi nhan thoi diem phat hien va thoi diem xay ra hanh vi (khac nhau - moc han chot tinh tu thoi diem xay ra hanh vi)
- [ ] Ho so danh gia tac dong da lap (Mẫu 10) va da nop A05 (Mẫu 02a)
- [ ] Neu co chuyen du lieu ra nuoc ngoai: ho so Mẫu 09 + Mẫu 01a da nop, phe duyet da co
- [ ] Thong bao vi pham da gui dung Mẫu 08
- [ ] Da thong bao cho người bị ảnh hưởng
- [ ] Da ghi nhan ho so, danh tinh DPO phu trach, luu giữ bang chứng

> Moi so lieu gio/ngay han chot chi lay tu `../_shared/compliance-vietnam.md`.
> Khong ghi lai thoi han cu the trong bao cao ngoai file do.

## 5. Phan loai du lieu ngan hang

```text
BANKING DATA SENSITIVITY:
├── PII: Ho ten, so CCCD, ngay sinh, dia chi
├── Financial: So tai khoan, so du, giao dich
├── Credit: Ho so tin dung, diem tin dung
└── Contact: So dien thoai, email

PROTECTION REQUIREMENTS:
├── Ghi nhan moi truy cap du lieu nhay
├── Canh bao truy cap du lieu nhay ngoai gio
├── Canh bao truy cap du lieu khach hang so luong lon
└── Bao cao su co theo muc tai phap ly (nguon: ../_shared/compliance-vietnam.md)
```

## 6. Script tu dong hoa

| Script | Muc dich | Input | Output |
|---|---|---|---|
| `analyze_watchlist_playbook.py` | Xu ly dong thoi AD + email + fileserver | Raw CSV export | `watchlist_{batch}_playbook_report.json` |

Thu tu chay hang ngay:

```text
1. python "D:\AI\Varonis Daily Logs\Monitoring\scripts\analyze_watchlist_playbook.py" --batch <batch>
2. python "D:\AI\Varonis Daily Logs\Monitoring\scripts\score_watchlist_jev.py" --batch <batch>
3. Merge outputs va sinh bao cao tuong quan
4. Review canh bao va phan loai muc rui ro
5. Sinh bao cao ngay
6. Leo thang neu can
```

> Script chi chay khi co batch export hop le. Ket qua script khong thay the
> phan tich cua nhan vien SOC va khong tu quyet dinh muc phan cap.
