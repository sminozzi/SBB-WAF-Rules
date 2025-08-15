# 🛡️ Supercharge Your Comodo WAF with StopBadBots Custom Rules updated 2025

> **Important:** These rules are designed **exclusively** to work with and enhance the **Comodo WAF** rule set.  
> They are **not** a standalone firewall and should not be used without Comodo's core rules already in place.

The default **Comodo WAF** is a strong foundation for web security.  
However, many modern threats are **behavior-based**, involving bots that scan, probe, and attempt brute-force attacks.  
This custom ruleset from **StopBadBots** adds **proactive protection** to stop these threats **before** they can cause harm.

---

## 🚀 1. Why Add These Rules?
**To begin with, these rules can double the number of blocked threats. See below for why.**

Our ruleset is engineered to provide a multi-layered security upgrade, combining intelligent behavioral analysis with an extensive, up-to-date knowledge base of known threats.

✅ **Powered by Comprehensive & Up-to-Date Blocklists**  
The core strength of our WAF comes from three external data files containing hundreds of current threat signatures. This includes extensive, curated lists for:
*   **Unwanted Bots & AI Crawlers (`bad-bots.data`)**
*   **Known Web Shell Filenames (`web-shells-php.data`)**
*   **Sensitive & Restricted Files (`restricted-files.data`)**  
This approach makes it incredibly simple to keep your defenses current and to customize the ruleset by adding your own block targets or whitelisting entries.

✅ **Proactive Behavioral Blocking**  
Stops attackers during their initial reconnaissance phase before they can find a vulnerability. Our rules automatically identify and block malicious patterns like vulnerability scans (too many 404s), rapid `HEAD` requests, and direct probing by IP address.

✅ **Advanced Threat Protection**  
Provides a strong, signature-based defense against a wide range of critical web application attacks, including Remote Code Execution (RCE), PHP Object Injection, Cross-Site Scripting (XSS), and Directory Traversal.

✅ **Optimized for Performance**  
Saves server resources by blocking threats at the earliest possible stage. By rejecting bad bots and obvious attacks before they reach your application, it significantly reduces CPU, RAM, and database load.

✅ **Battle-Tested and Reliable**  
This is not a theoretical ruleset. It was built and refined based on the analysis of real-world traffic and attack vectors from high-traffic websites, ensuring the rules are effective against the actual techniques attackers use today.

---

## ⚙️ 2. Installation Guide

> ⚠️ **Backup First:** Always make a backup before editing your WAF configuration.  
> Incorrect changes can make your site go offline.

### 1️⃣ Download the Ruleset
📥 Download all files 

After downloading, unzip the package. You will find:
- `guide.md`  → Documentation 
- `stopbadbots-rules-waf.conf` → main rules file
- `web-shells-php.data` →  List of known web shell filenames
- `bad-bots.data` → List of unwanted bot User-Agents
- `restricted-files.data` → List of sensitive/restricted filenames
- `readme.md`  → General information file with a brief description, installation steps, and usage guide.


### 2️⃣ Locate Your Comodo Rules Directory
On most **CWP (Control Web Panel)** servers, the rules folder is:
```bash
/usr/local/apache/modsecurity-cwaf/rules/
```

---

### 3️⃣ Upload the Files
Copy all:
```text
stopbadbots-rules-waf.conf
web-shells-php.data
bad-bots.data
restricted-files.data
```
to the above directory (via FTP, SCP, or File Manager).

---

### 4️⃣ Activate the Ruleset
Edit your ModSecurity configuration (`modsecurity.conf` or a custom include file)  
and add at the end:
```apache
Include /usr/local/apache/modsecurity-cwaf/rules/stopbadbots-waf.conf
```

---

### 5️⃣ Test & Restart Apache
Check syntax:
```bash
apachectl configtest
```
If you see:
```
Syntax OK
```
Restart Apache:
```bash
service httpd restart
```

✅ **Done!** Your new WAF rules are now active.

---

## ❤️ 3. Support & Contribution

This ruleset is maintained in our free time. You can help us:

- ⭐ **Star this repository** on GitHub  
- 📣 **Share** with other webmasters and developers  
- 🐛 **Report issues & false positives** in the issues section  
- 🔌 **Install our free [StopBadBots WordPress Plugin](https://wordpress.org/plugins/stopbadbots/)**  

---

**Together, we make the web safer!**  
Maintained with 💙 by [StopBadBots](https://StopBadBots.com) 
