# Mapping MITRE ATT&CK va NIST CSF 2.0 - ban duy nhat

## MITRE: dung ID nay, khong dung ID khac

| Hanh vi quan sat | ID dung | Ghi chu |
|---|---|---|
| Rut du lieu qua thu dien ra hop thu ca nhan | T1048 | KHONG dung T1041 (T1041 la exfil qua kenh C2) |
| Upload len OneDrive ca nhan / chia se ngoai | T1567.002 | Exfiltration to Cloud Storage |
| Gom du lieu tam tren may | T1074 | Data Staged |
| Thu thap thu | T1114 | Email Collection |
| Doc du lieu cloud storage | T1530 | Data from Cloud Storage |
| Brute force / password spraying | T1110 | |
| Account hop le bi chiem doat | T1078 | |
| Chuyen tiep can qua RDP | T1021.001 | |
| Command and control | T1071 | Application Layer Protocol |
| Dieu chinh khoan tai khoan | T1098 | Account Manipulation |
| Phat hien dich vu cloud | T1526 | Cloud Service Discovery |
| Dung dich vu tu ben ngoai | T1133 | External Remote Services |
| Task da len lich | T1053.005 | |
| Chay lenh PowerShell | T1059.001 | KHONG dung T1086 (da deprecated) |
| Ghi khoa registry | T1112 | Modify Registry - dung cho UserAssist |
| Persistence bang Run key | T1547.001 | Registry Run Keys |
| Dump LSASS | T1003.001 | KHONG dung T1003.004 cho registry hive |
| Lay cookie phiendang nhap | T1539 | KHONG dung cho "failed login" (dung T1110) |
| Doi quyen file / thu muc | T1222 | KHONG dung T1484 (T1484 la doi chinh sach domain) |
| Clipboard | T1115 | |
| Ransomware | T1486 | |
| Nhiem malware qua nguoi dung | T1204 | KHONG dung T1566 (T1566 la vector giao) |

## NIST CSF 2.0: dung ma 2.0

| Ma dung | Noi dung |
|---|---|
| DE.CM-1 | Networks and network services are monitored |
| DE.CM-3 | Monitoring finds anomalies and indicators of malicious behavior |
| DE.CM-9 | Computing hardware and software, runtime environments are monitored |
| DE.AE-1 | Potentially adverse events are identified |
| DE.AE-3 | Information is correlated from multiple sources |
| PR.AA-01 | Identities and credentials are managed |
| PR.AA-05 | Access permissions and authorizations are managed |
| PR.AA-06 | Users, devices and services are authenticated and authorized per risk |
| RS.MI-1 | Incidents are contained |
| RS.RP-1 | The incident response plan is executed |

KHONG dung ma CSF 1.1 (PR.AC-3, PR.AC-4, PR.AC-7) trong tai lieuu khai bao CSF 2.0.

## Moc tai lieuu

- MITRE ATT&CK v18. KHONG trich dan "v14".
- NIST SP 800-61r3 (Incident Handling Recommendations, 04/2025). KHONG trich dan "800-61" khong phai ban r3.
