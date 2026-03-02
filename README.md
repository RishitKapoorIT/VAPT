# Pentest Lab Toolkit (DVWA)

This repository contains a single automation script, `pentest_lab_setup.sh`, that turns the original manual steps into an executable workflow for a DVWA-based web app pentest lab.

It helps you:
- set up required services/packages on Kali-like systems,
- deploy DVWA,
- run common recon/scanning steps,
- launch OWASP ZAP,
- generate a starter vulnerability report + CVSS helper.

---

## What was implemented

The script provides these commands:

- `setup`  
  Updates system packages, installs dependencies, starts/enables Apache + MariaDB, clones DVWA, sets lab permissions, and creates the `dvwa` database.

- `nmap`  
  Runs an Nmap scan against `TARGET_IP` and saves output to `nmap_<ip>.txt`.

- `nikto`  
  Runs Nikto against `http://<TARGET_IP>/DVWA` and saves output to `nikto_<ip>.txt`.

- `zap`  
  Launches OWASP ZAP (GUI) in the background.

- `report`  
  Generates:
  - `vulnerability_report.md` (report template with vulnerability table),
  - `cvss_helper.py` (small Python utility to log CVSS data into CSV).

- `all`  
  Runs the whole workflow (`setup`, `nmap`, `nikto`, `zap`, `report`).

---

## File overview

- `pentest_lab_setup.sh` → main toolkit script.
- `README.md` → this guide.
- Generated (when running `report`):
  - `vulnerability_report.md`
  - `cvss_helper.py`
  - `cvss_scores.csv` (created when CVSS entries are added)

---

## Requirements

- Kali Linux / Debian-like environment with `apt` and `systemd`
- Network access for package installation and DVWA clone
- Sudo/root privileges for setup operations

---

## Quick start

### 1) Make script executable

```bash
chmod +x pentest_lab_setup.sh
```

### 2) See help

```bash
./pentest_lab_setup.sh help
```

### 3) Run full workflow

```bash
TARGET_IP=<target_ip> ./pentest_lab_setup.sh all
```

Example:

```bash
TARGET_IP=192.168.56.101 ./pentest_lab_setup.sh all
```

---

## Common command examples

### Setup only

```bash
./pentest_lab_setup.sh setup
```

### Nmap only

```bash
TARGET_IP=192.168.56.101 ./pentest_lab_setup.sh nmap
```

### Nikto only

```bash
TARGET_IP=192.168.56.101 ./pentest_lab_setup.sh nikto
```

### Launch ZAP

```bash
TARGET_IP=192.168.56.101 ./pentest_lab_setup.sh zap
```

### Generate report files

```bash
./pentest_lab_setup.sh report
```

---

## CVSS helper usage

After running `report`, use:

```bash
python3 cvss_helper.py \
  --name "SQL Injection" \
  --vector "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H" \
  --score 9.8
```

Optional output file:

```bash
python3 cvss_helper.py --name "XSS" --vector "CVSS:3.1/..." --score 6.1 --out findings_cvss.csv
```

---

## Notes

- This toolkit is intended for **authorized lab/testing environments only**.
- The script uses permissive DVWA permissions (`chmod -R 777`) for lab convenience.
- OWASP ZAP scanning is started via GUI; configure and run automated scans from within ZAP.

