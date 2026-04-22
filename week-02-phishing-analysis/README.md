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
