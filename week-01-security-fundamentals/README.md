# Week 2: Phishing Analysis (Kali Linux SOC Focus)

## 🎯 Weekly Goal
Mastering manual email dissection, header forensics, and infrastructure investigation.

---

## 📅 Day 1: Email Fundamentals & Lab Basics
In this lab, I practiced navigating the Kali filesystem and using command-line tools to extract IOCs (Indicators of Compromise) from raw email files.

### Key Skills Demonstrated:
* **Directory Management:** Created a forensic workspace using `mkdir`.
* **Artifact Extraction:** Used `grep` to pull hidden URLs from a `.eml` file.
* **Network Recon:** Utilized `nslookup` and `whois` to verify domain legitimacy.

### Lab Evidence:
![Extracting URLs with Grep](images/screenshot_grep_results.png)
*Figure 1: Using grep to identify suspicious links.*

![Domain Investigation](images/screenshot_whois.png)
*Figure 2: Verifying the age and ownership of a suspicious domain.*

---

## 📅 Day 2: Email Headers & Spoofing Detection
Today I focused on the "invisible" envelope of an email to detect spoofing.

### Key Skills Demonstrated:
* **Header Analysis:** Compared `From` vs `Return-Path`.
* **Authentication Checks:** Verified `SPF` status to detect unauthorized senders.

### Lab Evidence:
![SPF Failure Detection](images/screenshot_spf_check.png)
*Figure 3: Identifying a spoofed sender via SPF fail results.*
