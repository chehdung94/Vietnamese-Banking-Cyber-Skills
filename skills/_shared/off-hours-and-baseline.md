# Gio ngoai gio va baseline - ban duy nhat

## Khung gio lam viec

| Muc | Gia tri | Nguon |
|---|---|---|
| Gio lam viec | 07:45 - 17:30 | watchlist_config.json -> working_hours (production) |
| Timezone | Asia/Ho_Chi_Minh (UTC+7) | watchlist_config.json |
| Ngay lam viec | Thu 2 - Thu 6 | Bo sung theo calendar ngan hang |

Moi skill phai dung dung khung nay. Cac moc goc (06:00-22:00, 07:00-18:00, 08:00-18:00, 22:00-06:00) da bi bo.

Quy tac: su kien ngoai khoang 07:45-17:30 la "ngoai gio" UNLESS co ca nhan dang ky lam viec ngoai gio (bang danh sach nghi phe duyet) hoac la dich vu dong bo (sync) duoc xac nhan.

## Timezone theo nguon log (quan trong, hay sai)

| Nguon | Timezone goc | Cach xu ly |
|---|---|---|
| Active Directory / Windows Server | Gio VN | Dung truc tiep |
| Entra ID / Exchange Online | UTC | Cong +7 |
| Generation time (UTC) | UTC | Khong doi, luon la moc |

## Baseline mu/sigma

1. Baseline lay tu 7-30 ngay lam viec gan nhat cua tung user.
2. Khong co file baseline -> ghi `insufficient_evidence`, KHONG uoc luong mu/sigma. Rule baseline co the bo qua, nhung phai ghi ro ly do.
3. Rule mu+3sigma chi ap dung khi co it nhat 7 ngay baseline va it nhat 20 mau moi ngay.
4. Luu baseline theo user, khong dung chung cho ca nhom.

## Quy tac so sanh ten

1. Khop bang ten day du (bo dau, chuan hoa "Last, First" -> "First Last").
2. Ten giong nhau -> ghi "ambiguous", hoi nguoi dung, KHONG tu chon.
3. KHONG khop bang "ten goi OR ten day du". Ten goi Viet Nam ("An", "Thi", "Van") tao false positive o quy mo 300+ nhan vien.
4. Uu tien ma nhan vien > ten day du > ten goi.
