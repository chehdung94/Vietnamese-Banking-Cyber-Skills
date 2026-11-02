# Checklist bang chung - playbook-phishing-response

Agent: trinh bay checklist nay cho nguoi dung / SOC de thu thap bang chung truoc khi
phan tich sau. Khong phan tich khi chua co cac muc bat buoc.

## 1. Bang chung email (RFI)

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **Email goc (EML/MSG)** | Trich xuat header, URL, payload HTML | Exchange/O365, hop thu nguoi dung | Pending |
| [ ] | **Tep dinh kem (zip co mat khau)** | Phan tich tep dinh kem doc hai (neu co) | Trich tu EML | Pending |
| [ ] | **Mail gateway / trace log** | Xac dinh bao nhieu user nhan email | Mail gateway / O365 Trace | Pending |
| [ ] | **Web proxy / firewall log** | User nao da click hoac truy cap link | Proxy / NGFW | Pending |
| [ ] | **User context / phong van** | User co nhap mat khau hoac tai file khong | Helpdesk / phong van | Pending |

## 2. Bang chung bo sung

| Check | Loai bang chung | Muc dich | Nguon | Trang thai |
|---|---|---|---|:---:|
| [ ] | **Identity log tai khoan nguoi dung** | Xac nhan tai khoan bi chiem hay chua | AD / Entra ID | Pending |
| [ ] | **Endpoint telemetry tai may nguoi dung** | Tien trinh / file da chay sau khi mo tep | EDR | Pending |
| [ ] | **DLP alert trong khoang su kien** | Phat hien du lieu nhay bi phat tan | DLP | Pending |
| [ ] | **Email dinh huong** | Email goc co dung dinh huong khong | Mail gateway | Pending |

## 3. Truong du lieu can biet

| Nguon | Truong can co | Ghi chu |
|---|---|---|
| Mail trace | timestamp, recipient, message id, action | Chu ky thoi gian: xem `../_shared/off-hours-and-baseline.md` |
| Proxy | timestamp, url, user, bytes, action | - |
| Identity | timestamp, user, result, ip, device | Quy tac doi timezone: xem `../_shared/off-hours-and-baseline.md` |
| EDR | timestamp, process, file hash, user | - |

Neu thieu truong bat buoc, tra `insufficient_evidence` va liet ke artefact con thieu.
