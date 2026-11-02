# Collusion va hieu luc DLP - playbook-abnormal-user-behavior-response

## 1. Cross-user correlation (phat hien keo theo)

> **Kich hoat:** khi co tu 2 nguoi dung tro len trong cung pham vi dieu tra. Chi co
> 1 nguoi dung -> bo qua phan nay.

### 1.1 Quy trinh phat hien keo theo

1. Lap intersection matrix: xac dinh quan he giua cac nguoi dung (cung phong ban? cung
   du an? cung quan ly?).
2. Communication pattern: tim email giua cac nguoi dung (noi bo va ra ngoai).
3. Cross-mailbox check: tim dia chi Gmail ca nhan cua User A trong recipients cua User B
   (va nguoc lai).
4. Timeline correlation: doi chieu timeline hoat dong de tim hoat dong phoi hop.
5. Shared resources: kiem tra file / folder / application duoc ca hai nguoi dung truy cap.

### 1.2 Chi bao keo theo

| Chi bao | Xep hang | Hanh dong |
|---|---|---|
| User A gui email standalone den Gmail ca nhan cua User B | Critical | Leo thang ngay |
| User A xuat hien trong mailbox forwarding rules cua User B | Critical | Leo thang ngay |
| Shared external communication pattern giua cac nguoi dung | High | Mo rong dieu tra |
| Hoat dong cung thoi diem bat thuong giong nhau | Medium | Ghi nhan de review |

### 1.3 Vi du output

```json
{
  "cross_user_analysis": {
    "users_in_scope": ["[user_a]", "[user_b]"],
    "relationship": "[mo ta quan he]",
    "collusion_detected": true,
    "collusion_events": [
      {
        "date": "[date]",
        "from": "[user_b]",
        "to": "[dia chi ngoai]",
        "type": "standalone_outbound",
        "jev_rank": "Critical",
        "note": "[ghi chu co bang chung]"
      }
    ]
  }
}
```

## 2. Danh gia hieu luc DLP

> **Ap dung:** khi co su kien DLP / Device Control trong pham vi dieu tra.

### 2.1 Phan loai su kien DLP

| Tinh huong | Trang thai DLP | Rui ro hanh vi |
|---|---|---|
| **BLOCKED + Evidence = 0** | DLP dang hoat dong | Cao |
| **BLOCKED + Evidence > 0** | Co lot mot phan | Critical |
| **ALLOWED + khong co policy** | Lo hong DLP | Critical |
| **NOT DETECTED** | Diem blind spot | Critical |

### 2.2 Ma tran hieu luc DLP (output)

| Lop bao ve | Tong su kien | Bi chan | Cho qua (lo hong) | Hieu luc |
|---|---|---|---|---|
| Device Control (USB/HDD) | [X] | [X] | [X] | [Effective / Gap] |
| Device Control (MTP) | [X] | [X] | [X] | [Effective / Gap] |
| Web Filter (webmail ca nhan) | [X] | [X] | [X] | [Effective / Gap] |
| Web Filter (cloud storage ca nhan) | [X] | [X] | [X] | [Effective / Gap] |
| Email DLP (ra ngoai) | [X] | [X] | [X] | [Effective / Gap] |
| Endpoint (outgoing HTTP) | [X] | [X] | [X] | [Partial] |

Dien giai:

- Bi chan thanh cong -> DLP dang hoat dong.
- Cho qua khi khong co policy -> lo hong can bo sung rule.
- Chi phat hien ma khong chan -> bao ve mot phan.

## 3. Diem dinh danh cho bao cao

- Cach phan biet DLP BLOCKED (khong mat du lieu) va SUCCESSFUL exfiltration.
- Cac nhanh DLP trong bao cao khong duoc cong vao diem Jev chinh thuc; chuyen sang
  context dinh tinh (`references/indicators.md`, Section 5).
