# 🛡️ Supercharge Your Comodo WAF with StopBadBots Custom Rules

> **Important:** These rules are designed **exclusively** to work with and enhance the **Comodo WAF** rule set.  
> They are **not** a standalone firewall and should not be used without Comodo's core rules already in place.

The default **Comodo WAF** is a strong foundation for web security.  
However, many modern threats are **behavior-based**, involving bots that scan, probe, and attempt brute-force attacks.  
This custom ruleset from **StopBadBots** adds **proactive protection** to stop these threats **before** they can cause harm.

---

## 🚀 1. Why Add These Rules?

Adding our rules to your Comodo WAF provides:

✅ **Proactive Brute-Force Protection** — Detects and blocks IPs with multiple failed WordPress login attempts.  
✅ **Intelligent Scanner Detection** — Blocks bots causing too many 404 errors (vulnerability scans).  
✅ **Reduced Server Load** — Stops bad bots before they trigger heavy PHP/database processes.  
✅ **Real-World Tested** — Based on real traffic analysis from high-traffic websites.  

---

## ⚙️ 2. Installation Guide

> ⚠️ **Backup First:** Always make a backup before editing your WAF configuration.  
> Incorrect changes can make your site go offline.

### 1️⃣ Download the Ruleset
📥 Download the 3 files 

After downloading, unzip the package. You will find:
- `stopbadbots-waf.conf` → main rules file
- `web-shells-php.data` → malicious file name patterns
- `readme.txt`

---

### 2️⃣ Locate Your Comodo Rules Directory
On most **CWP (Control Web Panel)** servers, the rules folder is:
```bash
/usr/local/apache/modsecurity-cwaf/rules/
```

---

### 3️⃣ Upload the Files
Copy both:
```text
stopbadbots-waf.conf
web-shells-php.data
readme.txt
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
Maintained with 💙 by StopBadBots.
