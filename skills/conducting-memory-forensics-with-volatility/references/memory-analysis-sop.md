# SOP phan tich memory - conducting-memory-forensics-with-volatility

Command va quy trinh chi tiet. SKILL.md giu lai cac buoc 1-7; file nay giu command va quy tac.

## Step 1 - Thu thap memory image

**Windows (WinPmem):**
```
winpmem_mini_x64.exe output.raw
```

**Linux (AVML):**
```
./avml output.lime
```

## Step 2 - Xac dinh he dieu hanh va profile

```bash
vol -f memory.raw windows.info
```

## Step 3 - Phan tich tien trinh

```bash
# List all running processes
vol -f memory.raw windows.pslist

# Show process tree (parent-child relationships)
vol -f memory.raw windows.pstree

# Scan for hidden/unlinked processes (rootkit detection)
vol -f memory.raw windows.psscan
```

## Step 4 - Kiem tra ket noi mang

```bash
vol -f memory.raw windows.netscan
```

## Step 5 - Phat hien injection va malware

```bash
vol -f memory.raw windows.malfind
```

## Step 6 - Truy xuat credential

```bash
# Extract from LSASS
vol -f memory.raw windows.lsass_dump

# Parse cached credentials
vol -f memory.raw windows.hashdump
```

## Step 7 - Lap timeline

```bash
vol -f memory.raw windows.timeliner
```

## Quy tac phan tich

1. Chay `windows.info` truoc. Khong co profile phu hop -> dung va tra `insufficient_evidence`.
2. Luon chay ca `pslist` va `psscan` de so sanh; khong ket luan co tien trinh an chi tu `psscan`.
3. `malfind` chi la dau hieu. Phai doi chieu voi `windows.dlllist`, EDR log va hash cua file tren dia.
4. `netscan` cho biet ket noi tai thoi diem chup, khong phai toan bo lich su. Can doi chieu voi
   proxy/DNS log de xac nhan.
5. `lsass_dump` va `hashdump` tra ve du lieu credential that bi. Khong ghi hash/credential tho
   vao bao cao, khong gui ra ngoai. Chi ghi nhan su co, loai credential va bang chung lien quan.
6. `windows.timeliner` gop su kien theo moc thoi gian. Chuan hoa timezone theo
   `../../_shared/off-hours-and-baseline.md`.
7. Moi ket luan ghi ro nguon plugin va dong du lieu nao la bang chung.
