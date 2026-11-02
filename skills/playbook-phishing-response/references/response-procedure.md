# Quy trinh phan hoi - playbook-phishing-response

Moi hanh dong kham phuc trong file nay chi la **DE XUAT**. Khong chay khi chua co xac
nhan ro rang cua nguoi dung (Rules dong 6).

## 1. Ke hoach truyen thong noi bo

Cac moc T+ la SLA phong giao noi bo, khong phai moc thoi gian phap ly.
Moc phap ly: xem `../_shared/compliance-vietnam.md`.

| Moc | Doi tieu | Noi dung |
|---|---|---|
| T+15 phut | Helpdesk | Dang co chien dich phishing, tam thoi khong reset password hang loat cho den khi co danh sach user bi anh huong. |
| T+1 gio | IT Ops | Cung cap danh sach IP / domain / sender email de dua vao blacklist. |
| T+4 gio | CISO / Ban Giam doc | Neu co nhan vien da nhap mat khau va co nguy co lo lọt du lieu noi bo / khach hang. |

Muc phan hoi theo phan cap su co lay tu `../_shared/severity-and-escalation.md`.

## 2. Khuyen nghia tam thoi

1. **Co lap user:** tam thoi vo hieu hoa (disable) tai khoan AD / O365 cua user da bao
   cao hoac user nghi ngo click link.
2. **Canh bao toan cong ty (tuy chon):** gui email canh bao khung cap khong mo email co
   tieu de kem theo.

## 3. Phan tich email (identification)

- Trich xuat sender IP, kiem tra SPF / DKIM / DMARC.
- Trich xuat tat ca URL trong body.
- Trich xuat hash cua tep dinh kem (neu co).

## 4. Xac dinh pham vi (scope)

Dua vao mail trace log va proxy log:

- Tong so email phat tan.
- So user da click.
- So user da submit thong tin (POST request).
- So tai khoan bi chiem xac nhan.

Khong uoc luong so lieu. Neu log khong ghi, ghi `[NOT_AVAILABLE]`.

## 5. Ngăn chặn (containment) - DE XUAT

| Lop | Hanh dong | Uu tien |
|---|---|---|
| **Email gateway** | Block sender domain / IP, block theo tieu de | Cao |
| **Mang (proxy / FW)** | Block URL / domain doc hai | Cao |
| **Identity (AD / Entra)** | Force password reset, revoke session cho tai khoan bi chiem | Cao |

## 6. Loai bo (eradication) - DE XUAT

Mau lenh tham khao (Exchange / O365 search and purge). Can xac nhan cua nguoi dung
truoc khi chay:

```powershell
# VI DU - chi de xuat, khong chay tu dong
Search-Mailbox -SearchQuery "Subject:'[Tieu de phishing]'" -DeleteContent
```

## 7. Doc them

| Nguon | Noi dung |
|---|---|
| `../_shared/severity-and-escalation.md` | Thang phan cap P1-P4, muc phan hoi, nguoi duoc bao, ma leo thang |
| `../_shared/compliance-vietnam.md` | Han chot thong bao vi pham, ho so danh gia tac dong, thong bao nguoi bi pham vuot |
| `../_shared/off-hours-and-baseline.md` | Khung gio lam viec va quy tac chuyen doi timezone theo nguon log |
| `references/thresholds.md` | Bang nguong cua skill nay |
