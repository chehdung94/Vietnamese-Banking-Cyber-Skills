# Bối cảnh ngân hàng Việt Nam - analyzing-vpn-logs

## Phân loại dữ liệu liên quan

- Dữ liệu cá nhân khách hàng (PII).
- Hồ sơ tài chính, hồ sơ tài khoản, dữ liệu giao dịch.
- Dữ liệu hệ thống core banking và thanh toán.

## Quy định pháp lý

Mọi văn bản pháp lý, mốc thời gian và hồ sơ phải nộp: xem `../../_shared/compliance-vietnam.md`
(nguồn sự thật duy nhất). Skill này không trích dẫn số hiệu văn bản, thời hạn báo cáo hay
mẫu đơn tại chỗ.

## Kịch bản truy cập từ xa thường gặp trong ngân hàng

- Truy cập từ xa vào hệ thống core banking.
- Truy cập của nhà cung cấp bên thứ ba.
- Kết nối từ xa của nhân viên làm việc tại nhà / công tác trường.
- Truy cập của nhân viên sắp nghỉ việc trong giai đoạn thông báo nghỉ việc.

## Dải IP nội bộ (RFC 1918)

| Dải | Mô tả |
|---|---|
| [IP]/8 | Lớp A private |
| [IP]/12 | Lớp B private |
| [IP]/16 | Lớp C private |

## Dải IP tunnel VPN thường gặp

```text
[IP]/12   # VPN pool chung
[IP]/16   # SSL VPN pool
[IP]/16   # Remote access
```

Ghi trong báo cáo giá trị thực tế quan sát được từ log, không sao chép mẫu ở trên.

## Tài liệu tham khảo bên ngoài

- Tài liệu VPN của nhà cung cấp: Cisco ASA, Fortinet FortiGate, Palo Alto, Pulse Secure, Azure VPN Gateway.
- MITRE ATT&CK (v18): T1133 External Remote Services, T1021.001 Remote Desktop Protocol, T1078 Valid Accounts, T1110 Brute Force. Danh sách ID hợp lệ: `../../_shared/mitre-nist-mapping.md`.
- NIST Cybersecurity Framework 2.0. Danh sách mã hợp lệ: `../../_shared/mitre-nist-mapping.md`.
- RFC 1918 - Address Allocation for Private Internets.
