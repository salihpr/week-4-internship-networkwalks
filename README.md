<div align="center">

# 🛡️ Web Application Penetration Testing — Mediroza General Hospital
### Project 4 · Network Walks Academy Internship — Week 4 · Batch B083A

![Kali Linux](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)
![Penetration Testing](https://img.shields.io/badge/Type-Penetration%20Testing-critical?style=for-the-badge&logo=hackaday&logoColor=white)
![SQL Injection](https://img.shields.io/badge/Vulnerability-SQL%20Injection-red?style=for-the-badge&logo=mysql&logoColor=white)
![Nikto](https://img.shields.io/badge/Tool-Nikto-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Batch](https://img.shields.io/badge/Batch-B083A-blue?style=for-the-badge)
![Week](https://img.shields.io/badge/Week-4-9cf?style=for-the-badge)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Muhammed%20Salih-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/muhammed-salih-cv-9a292433a)
[![GitHub](https://img.shields.io/badge/GitHub-salihpr-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/salihpr)

</div>

---

## 📌 About This Project

This repository documents **Project 4** of my Cybersecurity Internship at **Network Walks Academy (Batch B083A)** — a **Week 4 practical task** involving a black-box web application penetration test against the publicly reachable website of **Mediroza General Hospital** (`https://medirozahospital.com`).

The engagement covers reconnaissance with **Nikto**, discovery of an exposed legacy directory and database backup, **SQL injection–based authentication bypass**, and **password cracking** of "protected" patient PDF reports using Network Walks' own online security tools.

> ⚠️ **Disclaimer:** This assessment was carried out strictly for educational purposes as part of an authorised internship training exercise. All findings are documented responsibly for learning and awareness. Sensitive data captured during testing (database backup, patient reports) is **not reproduced in this repository** — only sanitised screenshots and technical findings are shared.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|---|---|
| **Kali Linux** (VirtualBox) | Testing operating system |
| **Nikto v2.6.0** | Web server scanning & directory enumeration |
| **Mozilla Firefox ESR** | Manual browsing & SQL injection testing |
| **Network Walks – Hash Calculator** ([networkwalks.com/hash-calculator](https://networkwalks.com/hash-calculator/)) | Extracting crackable `$pdf$` hashes from encrypted PDFs |
| **Network Walks – Password Cracker** ([networkwalks.com/password-cracker](https://networkwalks.com/password-cracker/)) | Dictionary attack to recover PDF passwords |

---

## 🗂️ Table of Contents

1. [Reconnaissance — Nikto Scan](#1-reconnaissance--nikto-scan)
2. [SQL Injection — Patient Portal Login Bypass](#2-sql-injection--patient-portal-login-bypass)
3. [Access Gained — My Reports Page](#3-access-gained--my-reports-page)
4. [Hash Extraction — Network Walks Hash Calculator](#4-hash-extraction--network-walks-hash-calculator)
5. [Password Cracking — Network Walks Password Cracker](#5-password-cracking--network-walks-password-cracker)
6. [Opened Patient Reports](#6-opened-patient-reports)
7. [Exposed /old/ Directory](#7-exposed-old-directory)
8. [Exposed Database Backup File](#8-exposed-database-backup-file)
9. [Findings Summary](#9-findings-summary)
10. [Author](#-author)

---

## 1. Reconnaissance — Nikto Scan

The engagement began with a Nikto scan against the target to enumerate the web server and look for common misconfigurations.

```bash
nikto -h https://medirozahospital.com/ -o nikto.txt
```

The scan flagged **directory indexing enabled** on three sensitive paths: `/staff/`, `/patient/` and `/old/`.

![Nikto scan output](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_10_46_10.png)

---

## 2. SQL Injection — Patient Portal Login Bypass

The Patient Portal login form (`/patient/login.php`) was tested for SQL injection using classic authentication-bypass payloads entered into the **Username** field:

```text
admin'--
' OR '1'='1'--
```

Both payloads successfully bypassed authentication with no valid password required, confirming the login query is vulnerable to SQL injection (unsanitised input concatenated directly into the SQL statement).

![SQL injection payload in login form](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_10_37.png)

---

## 3. Access Gained — My Reports Page

The SQL injection bypass granted access to the authenticated **"My Reports"** area of the Patient Portal, listing downloadable, password-protected pathology PDF reports belonging to real patients.

![My Reports page after bypass](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_10_57_41.png)

---

## 4. Hash Extraction — Network Walks Hash Calculator

The downloaded PDF reports were encrypted. The **Network Walks Hash Calculator** ([networkwalks.com/hash-calculator](https://networkwalks.com/hash-calculator/)) was used to extract a crackable `$pdf$` hash (pdf2john / hashcat-compatible format) from each report, ready to be handed to the **Password Cracker** tool ([networkwalks.com/password-cracker](https://networkwalks.com/password-cracker/)).

![Hash Calculator extracting PDF hash](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_10_59_23.png)

---

## 5. Password Cracking — Network Walks Password Cracker

The extracted hashes were run through a dictionary/wordlist attack using the Network Walks Password Cracker. All three PDF passwords were recovered within seconds:

| Report | Password Recovered |
|---|---|
| patient_report_1.pdf | `123456` |
| patient_report_2.pdf | `password` |
| patient_report_3.pdf | `!@#$%^&` |

![Password cracked — 123456](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_01_19.png)

![Password cracked — password](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_02_18.png)

![Password cracked — special characters](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_08_49.png)

---

## 6. Opened Patient Reports

Using the recovered passwords, the encrypted pathology reports were unlocked, exposing patient names, dates of birth, referring doctors, and laboratory results.

![Opened patient report 1](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_09_06.png)

![Opened patient report 2](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_09_34.png)

![Opened patient report 3](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_09_43.png)

---

## 7. Exposed /old/ Directory

Following up on the Nikto finding, the `/old/` directory was browsed directly and confirmed to have **directory indexing enabled**, publicly exposing a legacy database backup file.

![Index of /old/ directory](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_11_06.png)

---

## 8. Exposed Database Backup File

The exposed file, `mediroza_db_backup_2019.sql`, was publicly downloadable with **no authentication**, revealing full staff records (names, emails, phone numbers, national ID numbers, salaries) and a complete shareholder register (names, shareholding percentages, share class).

![Database backup file contents](https://raw.githubusercontent.com/salihpr/week-4-internship-networkwalks/main/screenshorts/VirtualBox_kali%20linux_30_09_2026_11_11_17.png)

---

## 9. Findings Summary

| # | Finding | Risk Rating |
|---|---|---|
| 1 | Directory Listing / Indexing Enabled on Sensitive Paths (`/staff/`, `/patient/`, `/old/`) | 🟠 Medium |
| 2 | Sensitive Data Exposure — Public 2019 Database Backup (Staff PII, Salaries & Shareholder Data) | 🔴 Critical |
| 3 | SQL Injection — Authentication Bypass on Patient Portal Login | 🔴 Critical |
| 4 | Weak Password Protection on Patient Pathology PDF Reports | 🟠 High |

**Recommended fixes:** disable directory indexing, remove the exposed `/old/` directory and backup file from the public web server, rewrite the login query using parameterised statements, enforce strong per-report passwords, and add 2FA to the Patient Portal.

> 📄 A full formal VAPT report with CVSS scoring, detailed remediation steps, and a risk-based roadmap is included separately as part of this internship deliverable.

> 🔄 **More screenshots and findings will be added to this repository as testing continues.**

---

<div align="center">

## 👤 Author

**Muhammed Salih**
Cybersecurity Intern — Network Walks Academy (Batch B083A)

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/muhammed-salih-cv-9a292433a)
[![GitHub](https://img.shields.io/badge/GitHub-salihpr-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/salihpr)

</div>
