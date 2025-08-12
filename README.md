# Supercharge Your Comodo WAF with StopBadBots Custom Rules

> **Important:** These rules are designed exclusively to work with and enhance the **Comodo WAF** rule set. They are not a standalone firewall and should not be used without Comodo's core rules already in place.

The default Comodo WAF provides a solid foundation for server security. However, many modern threats are behavioral, involving bots that scan, probe, and attempt to brute-force your site. This custom ruleset from StopBadBots adds a critical layer of proactive, behavior-based defense to stop these threats before they can cause harm.

## 1. Advantages of Adding These Rules

Integrating our ruleset gives you several key advantages over the default Comodo configuration:

- **Proactive Brute-Force Protection:** Automatically detects and temporarily blocks IPs that repeatedly fail to log in to WordPress, saving your server's resources and securing your accounts.
- **Intelligent Scanner Mitigation:** Identifies and blocks bots that generate an excessive number of "404 Not Found" errors, a classic sign of vulnerability scanning.
- **Reduced Server Load:** By blocking malicious bots early, these rules prevent them from executing resource-intensive scripts (like PHP and database queries), making your site faster for legitimate users.
- **Community-Driven and Battle-Tested:** These rules are born from real-world experience managing high-traffic websites and from feedback within the webmaster community. They target the real attacks we see every day.

## 2. Installation Guide

> **Warning:** Always back up your server configuration files before making any changes. **While these rules are tested, use them at your own risk**. **_Incorrectly modifying server configurations can lead to your website going offline._**

1. ### Download the Ruleset  
   First, download the complete package which includes the main rules file and any necessary data files.  
   [Download StopBadBots WAF Rules]([LINK_TO_DOWNLOAD_YOUR_RULES_ZIP_FILE])  

   After downloading, unzip the package. You will find two key files:  
   - `stopbadbots_waf.conf` (the rules)  
   - `web-shells-php.data` (a list of malicious file names)  

2. ### Locate Your Comodo Rules Directory  
   You need to upload these files to the same directory where your Comodo WAF rules are stored. For most servers using CWP (Control Web Panel), this directory is:  
   ```bash
   /usr/local/apache/modsecurity-cwaf/rules/
   ```
   Use an FTP client (like FileZilla) or the command line to access this directory.

3. ### Upload the Files  
   Upload both `stopbadbots_waf.conf` and `web-shells-php.data` to the directory you located in the previous step.

4. ### Activate the Ruleset  
   To make ModSecurity load your new rules, you must "include" the file in your Apache configuration. The best way to do this is often in a dedicated custom configuration file. Find the main ModSecurity configuration file (e.g., `modsecurity.conf`) or a custom include file and add the following line at the end:  
   ```apache
   Include /usr/local/apache/modsecurity-cwaf/rules/stopbadbots_waf.conf
   ```

5. ### Test and Restart Apache  
   Before restarting, always test your Apache configuration for syntax errors:  
   ```bash
   apachectl configtest
   ```
   If you see `Syntax OK`, you are safe to restart Apache to apply the changes:  
   ```bash
   service httpd restart
   ```
   Congratulations! Your new security rules are now active.

## 3. Help Us Continue This Work

This ruleset is a passion project, maintained and updated in our free time. If you find these rules valuable and want to encourage their continued development, here are a few ways you can support our work:

- **Share the Word:** Tell other webmasters, developers, or your favorite online communities about this project. The more people we can help, the more motivated we are to improve it.
- **Provide Feedback:** If you have an idea for a new rule or encounter a false positive, let us know! Real-world feedback is invaluable for making the ruleset even better.
- **Install Our Free WordPress Plugin StopBadBots.**

Thank you for your interest and support. Together, we can make the web a safer place.
