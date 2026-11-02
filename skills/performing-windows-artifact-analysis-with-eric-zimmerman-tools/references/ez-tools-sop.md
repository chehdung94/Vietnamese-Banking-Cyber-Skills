# SOP thu thap va phan tich artifact - performing-windows-artifact-analysis-with-eric-zimmerman-tools

Command va quy trinh chi tiet. SKILL.md giu lai cac buoc 1-6; file nay giu command va quy tac.

## Step 1 - KAPE: thu thap tu dong

```bash
# List available targets
.\KAPE.exe --tlist

# Collect from target
.\KAPE.exe --tsource C: --tdest C:\KAPE\Output --tflush
```

Luon luu output cua `--tlist` kem bao cao de chung minh pham vi thu thap thuc te.

## Step 2 - PECmd: parse Prefetch

```bash
PECmd.exe -f C:\Windows\Prefetch\APP.EXE-*.pf --json C:\Output\
```

## Step 3 - MFTECmd: parse MFT

```bash
MFTECmd.exe -f C:\$MFT --csv C:\Output\
```

## Step 4 - LECmd: parse LNK

```bash
LECmd.exe -d C:\Users\*\AppData\Roaming\Microsoft\Windows\Recent --json C:\Output\
```

## Step 5 - JLECmd: parse Jump Lists

```bash
JLECmd.exe -d C:\Users\*\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations --json C:\Output\
```

## Step 6 - Gom timeline

Dung Timeline Explorer de gop output cac tool ve mot timeline chuan hoa. Chu hoa cac truong thoi gian
khac nhau (Prefetch run time, LNK access time, registry last write, MFT modify time) theo cung
moc moc thoi gian, chuan hoa theo `../../_shared/off-hours-and-baseline.md`.

## Quy tac phan tich

1. Prefetch chi ghi lai so lan chay va moc thoi gian gan nhat. Khong duoc suy dien "chay lan dau"
   tu prefetch.
2. LNK va Jump List chi cho biet tep da mo, KHONG chung minh tep da doc hay da exfiltrate.
3. ShimCache, BAM/DAM cho biet chuong trinh da chay voi user nao. Doi chieu voi timeline tai khoan.
4. Registry artifact (UserAssist, RunMRU) la bang chung nang cao hon ten tep: xem
   `../../analyzing-windows-registry-for-artifacts/SKILL.md` cho quy trinh phan tich chi tiet.
5. Khi cung cap lenh thu thap, luu y thu thap tren dia bi thay doi - ghi ro trong bao cao.
6. Moi su kien timeline ghi ro nguon tool va truong thoi gian cu the.
