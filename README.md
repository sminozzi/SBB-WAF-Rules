# Supercharge Your Comodo WAF with StopBadBots Custom Rules

<p align="center">
  <img src="[LINK_TO_A_LOGO_OR_BANNER_IMAGE_IF_YOU_HAVE_ONE]" alt="StopBadBots WAF Rules Banner">
</p>

This repository contains a custom ModSecurity ruleset designed to enhance the default **Comodo WAF**. It adds a critical layer of proactive, behavior-based defense to stop modern threats like brute-force bots, aggressive scanners, and other malicious activity that the core ruleset might miss.

---

### ► Important Prerequisite

> **Note:** These rules are designed exclusively to work with and enhance the **Comodo WAF** rule set. They are **not** a standalone firewall and should not be used without Comodo's core rules already in place.

---

## Key Advantages

Integrating this ruleset gives you several key advantages over a default Comodo configuration:

-   🛡️ **Proactive Brute-Force Protection:** Automatically detects and temporarily blocks IPs that repeatedly fail to log in to WordPress, saving server resources and securing accounts.
-   🎯 **Intelligent Scanner Mitigation:** Identifies and blocks bots that generate an excessive number of "404 Not Found" errors, a classic sign of vulnerability scanning.
-   ⚡ **Reduced Server Load:** By blocking malicious bots early, these rules prevent them from executing resource-intensive scripts (like PHP and database queries), making your site faster for legitimate users.
-   🤝 **Community-Driven and Battle-Tested:** These rules are born from real-world experience and feedback. They target the real attacks we see every day.

---

## Installation Guide

> ⚠️ **Disclaimer:** Always back up your server configuration files before making any changes. While these rules are tested, you use them at your own risk. Incorrectly modifying server configurations can lead to your website going offline.

### 1. Download the Ruleset

Clone this repository or download the latest release as a ZIP file.

```bash
git clone https://github.com/your-username/your-repo-name.git```

The package includes two key files:
- `stopbadbots_waf.conf`: The main rules file.
- `web-shells-php.data`: A list of known malicious file names used by one of the rules.

### 2. Locate and Upload Files

You need to upload these files to your Comodo WAF rules directory. For most servers using CWP (Control Web Panel), this path is:

`/usr/local/apache/modsecurity-cwaf/rules/`

Upload both `stopbadbots_waf.conf` and `web-shells-php.data` to this directory.

### 3. Activate the Ruleset

To make ModSecurity load your new rules, you must `Include` the file in your Apache configuration. The best way to do this is to add the following line at the end of your main ModSecurity config file (e.g., `modsecurity.conf`) or a custom include file:

```apache
Include /usr/local/apache/modsecurity-cwaf/rules/stopbadbots_waf.conf
