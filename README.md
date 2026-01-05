# 🛡️ Supercharge Your Comodo WAF with StopBadBots Custom Rules updated 2026

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

**The installation is very simple.**

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


### 6️⃣ Whitelist your own server
Since new and powerful rules have been added, don't forget to whitelist your own server's IP address.


✅ **Done!** Your new WAF rules are now active.


---

## ❤️ 3. Support & Contribution

This ruleset is maintained in our free time. You can help us:

- ⭐ **Star this repository** on GitHub  
- 📣 **Share** with other webmasters and developers  
- 🐛 **Report issues & false positives** in the issues section  
- 🔌 **Install our free [StopBadBots WordPress Plugin](https://wordpress.org/plugins/stopbadbots/)**  

---

### **FAQ - Frequently Asked Questions**

**1. Are the original Comodo WAF rules still updated?**

No. The Comodo WAF is considered a discontinued product and has not received rule updates since early 2024. Sticking with the old rules can leave your server vulnerable to new threats. The SBB-WAF rules were created to solve this exact problem by offering modern, continuous protection.

**2. Why does my control panel (Plesk, cPanel, DirectAdmin) show an error when trying to update the Comodo rules?**

Your panel tries to download updates from `waf.comodo.com`, a server that is often inaccessible or unresponsive because the service has been discontinued.

**3. Are the SBB-WAF rules free? What's the difference with the commercial version?**

Yes, the SBB-WAF rule set available on GitHub is **completely free**. It provides a robust and updated layer of protection for 2025. We don not offer a commercial version.

**4. Do I need to uninstall the old Comodo rules before installing the SBB-WAF rules?**

No. Our process is designed to enhance, not replace. We keep the original Comodo rules as a stable foundation and automatically add our SBB-WAF rules as a powerful new layer of protection. This ensures you get a modern defense against today's threats without losing the core protection you already have. 

**5. Is the installation compatible with my control panel (cPanel, Plesk, DirectAdmin)?**

This document provides the manual installation instructions.

**6. Can these rules cause "false positives" and block my website's functionality (e.g., WordPress, WooCommerce)?**

The SBB-WAF rules are developed based on over 10 years of data from our WordPress plugin, which allows us to minimize the chance of false positives on popular platforms. While any WAF can occasionally block a legitimate action, our rules are fine-tuned to be effective without compromising functionality. If an issue occurs, specific rules can be disabled.

**7. How often are the SBB-WAF rules updated?**

The free rule set available on GitHub is a stable and updated version for 2025. New versions will be released as needed.

**8. What makes the SBB-WAF rules better than the old Comodo rules?**

The SBB-WAF rules supplement Comodo's solid foundation with protections against modern threats. They are focused on blocking aggressive bots, vulnerability scanners, access attempts to sensitive files (like `xmlrpc.php`), and recent attack vectors that the outdated Comodo rules do not cover. In our tests, the combination of rules doubled the effectiveness of blocking malicious traffic.

**9. Does the installation require advanced technical knowledge?**

If you have SSH access to your server and can run basic commands, the installation is quite straightforward. Our step-by-step guide is designed to be clear and easy to follow. Simply copy, paste, and execute the provided commands.

**10. Is it safe to use rules from a third party instead of the official Comodo ones?**

Given that the "official" Comodo rules are no longer maintained, using an updated alternative is safer than using rules from 2024. Our company, StopBadBots, has over a decade of experience in website security, with a plugin active on thousands of WordPress installations. Our reputation is built on providing effective and trustworthy protection.

**Together, we make the web safer!**  
Maintained with 💙 by [StopBadBots](https://StopBadBots.com) 
