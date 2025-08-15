## StopBadBots WAF - Additional Security Rules: Detailed Documentation

### Introduction

This document provides a detailed explanation for each rule included in the **StopBadBots WAF - Additional Security Rules** set (Version 1.1). The purpose of this ruleset is to provide a powerful, supplementary security layer for web servers using the ModSecurity Web Application Firewall. It is designed to be a "drop-in" enhancement to existing security profiles, such as the one provided by Comodo WAF, focusing on blocking common and emerging threats with high efficiency.

Each section below corresponds to a rule or a logical block of rules from the configuration file.

Of course. Here is a paragraph written in English that explains the flexibility of the ruleset and how to adjust parameters to avoid false positives, as you requested.


### Fine-Tuning and Flexibility

This ruleset is designed to be highly effective out-of-the-box, but for optimal performance, it can and should be fine-tuned to your specific environment to minimize false positives (instances where legitimate user traffic is incorrectly blocked). Several rules contain parameters that are common candidates for adjustment:

*   **Behavioral Thresholds:** The rules that block based on behavior have clear, adjustable limits. The **Excessive 404s Blocker** (Rules 1000004 & 1000028) can be made more or less strict by changing the hit count (currently `@gt 5`) or the time window (`expirevar:…=180`). Similarly, the **Excessive HEADs Blocker** (Rules 1000005-7) can be relaxed if your applications or monitoring tools legitimately use more than one `HEAD` request per minute.

*   **Policy Enforcement:** The **Allowed HTTP Methods** rule (1000003) is a strict policy that only permits `GET`, `HEAD`, and `POST`. If your web applications or APIs rely on other methods like `PUT` or `DELETE`, you must add them to the `@within` list to prevent breaking their functionality.

*   **Signature and Keyword Lists:** Many rules block based on lists of known bad signatures. These lists may need adjustment for your specific tools:
    *   The **Command-Line User-Agent** rule (1000010) blocks clients like `curl` and `python-requests`. If you use these tools for legitimate internal monitoring or API calls, you may need to remove them from the list or create a more specific rule to allow them from trusted IP addresses.
    *   The **Vulnerability Scanner User-Agent** rule (1000011) contains a long list of scanner names. If your organization uses a commercial tool on this list (like Qualys or Nessus) for internal security audits, you will need to remove its signature to allow your scans to complete.
    *   The **External Data Files** (`bad-bots.data`, `restricted-files.data`) offer the greatest flexibility. If a legitimate bot is blocked or a necessary file is restricted, you can simply edit these `.data` files to whitelist the specific entry.

    Of course. Here is a new paragraph, written in English, that explains the stance on AI bots and instructs users on how to allow them if they wish.


### Policy on AI Crawlers and Content Scraping

As part of our proactive defense strategy, this ruleset is configured by default to block web crawlers from major AI companies. We have taken this stance because these bots are primarily used to scrape vast amounts of content to train Large Language Models (LLMs). This process can consume significant server resources and may result in your unique content being used to answer user queries on their platforms, often without attribution, as if it were part of their own intelligence. However, we recognize that some website owners may wish to allow these bots access for potential inclusion in future AI-driven search results. If you hold this view, you can easily permit them by editing the **`bad-bots.data`** file and removing the specific AI bot User-Agents (such as `ChatGPT-User`, `ClaudeBot`, etc.) from the blocklist.


### Rejecting Direct Access by IP Address

Furthermore, this ruleset enforces a strict policy of rejecting direct access via the server's IP address. Legitimate visitors and search engines invariably use a hostname (e.g., `www.yourdomain.com`), which is essential for web servers hosting multiple sites to serve the correct content. In contrast, a significant portion of automated scanning and malicious bot activity operates by sweeping entire IP ranges and sending generic requests directly to IP addresses. This is a common reconnaissance technique used to find vulnerable servers without knowing the domains they host. By blocking these requests, we filter out a massive volume of low-quality, malicious traffic, reduce the attack surface, and save server resources. This policy is enforced by **Rule 1000014**, which specifically inspects the `Host` header to block any request that uses a literal IP address instead of a hostname.

Users are strongly encouraged to deploy this ruleset in a logging-only mode initially, monitor their ModSecurity logs for any false positives, and then adjust these specific parameters to achieve the optimal balance between robust security and a seamless experience for their users and internal tools.


### **Phase 1: Request Headers Analysis (Fast Blocking)**

Rules in this phase are the first line of defense. They analyze incoming request headers, which are the first part of a request the server processes. Blocking threats at this early stage is highly efficient and saves server resources.

#### **Rule 1000001: Block Direct Access to `xmlrpc.php`**
*   **Purpose:** To completely block access to the `xmlrpc.php` file in WordPress.
*   **How it Works:** The rule inspects the `REQUEST_URI` and blocks any request that contains `/xmlrpc.php`.
*   **Threat(s) Mitigated:**
    *   **Brute-Force Attacks:** Attackers use the `xmlrpc.php` API to attempt thousands of username/password combinations with a single HTTP request, bypassing traditional login rate limiters.
    *   **DDoS Amplification Attacks:** The `pingback` feature within `xmlrpc.php` can be abused to force thousands of WordPress sites to attack a single target, amplifying a DDoS attack.

#### **Rules 1000002 & 1000003: Enforce Allowed HTTP Methods**
*   **Purpose:** To reduce the server's attack surface by only allowing the most common and necessary HTTP methods: `GET`, `HEAD`, and `POST`.
*   **How it Works:** This is a two-part rule chain:
    1.  **Rule 1000002** acts as an exception. It checks if the request method is `OPTIONS` and if the URI begins with `/wp-json/` (the WordPress REST API). If both are true, it disables the main blocking rule (`1000003`) for this specific request. This is necessary for modern WordPress features and plugins to function correctly.
    2.  **Rule 1000003** is the main enforcer. If the exception is not met, this rule blocks any request that does not use `GET`, `HEAD`, or `POST`.
*   **Threat(s) Mitigated:** Prevents the use of potentially dangerous or unnecessary methods like `PUT`, `DELETE`, `TRACE`, `CONNECT`, etc., which could be leveraged for file manipulation, remote administration, or information disclosure if the server is misconfigured.

#### **Rule 1000004: Excessive 404s Blocker (The Enforcer)**
*   **Purpose:** To proactively block IP addresses that are behaving like automated vulnerability scanners by rapidly requesting many non-existent pages.
*   **How it Works:** This rule is the "blocker" part of a two-rule system. It runs in `phase:1` and checks if a counter (`IP:bf_counter`) associated with the visitor's IP address has exceeded a value of 5. If it has, the IP is blocked immediately. This rule does not increment the counter; it only reads it. The counting is handled by rule `1000028`.
*   **Threat(s) Mitigated:** Vulnerability Scanning, Malicious Content Discovery, Directory Brute-Forcing.

#### **Rules 1000005 - 1000007: Excessive HEAD Requests Blocker**
*   **Purpose:** To rate-limit `HEAD` requests, which are often used by scanners to quickly check for the existence of files or directories without the overhead of downloading the full content.
*   **How it Works:** This is a three-part behavioral rule block:
    1.  **Rule 1000005** (`initcol`): When the *first* `HEAD` request arrives from an IP, it creates a counter that will expire in 60 seconds.
    2.  **Rule 1000006** (`setvar`): For *every* subsequent `HEAD` request from that same IP, it increments the counter.
    3.  **Rule 1000007** (The Blocker): If the counter for an IP is greater than 1 within the 60-second window, the request is blocked.
*   **Threat(s) Mitigated:** Reconnaissance, Vulnerability Scanning.

#### **Rules 1000008 - 1000012: User-Agent Analysis**
*   **Purpose:** This group of rules blocks requests based on their `User-Agent` header, a string that identifies the client software. These rules target known malicious tools, low-quality bots, and suspicious identifiers.
*   **How they Work:**
    *   **1000008:** Blocks an exact User-Agent string known to be associated with old, vulnerable browsers used in automated attacks.
    *   **1000009:** Blocks User-Agents containing suspicious substrings like "Mozlila" (a common typo for Mozilla used by bad bots).
    *   **1000010:** Blocks common User-Agents for command-line tools (`curl`, `wget`, etc.) that are often used for automated attacks.
    *   **1000011:** Blocks a long list of User-Agents belonging to well-known vulnerability scanners and hacking tools (`nmap`, `sqlmap`, `nikto`, etc.).
    *   **1000012:** Blocks User-Agents from a regularly updated external file (`bad-bots.data`), making the blocklist easy to maintain.
*   **Threat(s) Mitigated:** Vulnerability Scanning, Bad Bots, Spam Bots, Scripted Attacks.

#### **Rule 1000013: XSS in Forwarding Headers**
*   **Purpose:** To prevent Cross-Site Scripting (XSS) attacks injected into HTTP headers that track the original IP of a visitor (e.g., behind a load balancer or CDN).
*   **How it Works:** It scans the `X-Forwarded-For` and `Client-IP` headers for the presence of `<script` tags.
*   **Threat(s) Mitigated:** Cross-Site Scripting (XSS). This protects backend applications or log analysis tools that might render the content of these headers insecurely.

---

### **Phase 2: Request Body Analysis (Deep Inspection)**

These rules run after the entire request, including its body (e.g., form data), has been received by the server. This allows for deeper inspection of the data being sent.

#### **Rule 1000014: Host Header is IP Address**
*   **Purpose:** To block requests that access the server directly via its IP address instead of a domain name.
*   **How it Works:** It checks if the `Host` header consists only of digits, dots, and colons.
*   **Threat(s) Mitigated:** Automated Scanning. Legitimate users access websites via hostnames (e.g., `www.example.com`). Bots and scanners often traverse entire IP ranges, probing servers directly without knowing the hosted domain names.

#### **Rule 1000015: Missing Accept Header**
*   **Purpose:** To block requests that are missing the `Accept` header.
*   **How it Works:** It checks for the existence of the `Accept` header. If the header is not present (`@eq 0`), the request is blocked.
*   **Threat(s) Mitigated:** Low-quality Bots and Simple Scripts. Nearly all legitimate web browsers include an `Accept` header to tell the server what content types they can handle. Its absence is a strong indicator of a poorly written script or bot.

#### **Rules 1000016 & 1000017: Sensitive File Access**
*   **Purpose:** To prevent attackers from accessing files that contain sensitive configuration data or could leak information about the server environment.
*   **How they Work:**
    *   **1000016:** Specifically blocks any attempt to access a file beginning with `wp-config`, such as `wp-config.php.bak` or `wp-config.old`.
    *   **1000017:** Blocks access to common information disclosure files like `readme.txt` (which can reveal the WordPress version) and `debug.log`.
*   **Threat(s) Mitigated:** Information Disclosure, Configuration Data Theft.

#### **Rules 1000018 & 1000019: Directory Traversal**
*   **Purpose:** To block Directory Traversal (or "Path Traversal") attacks, where an attacker uses sequences like `../` to access files and directories outside of the web root.
*   **How they Work:** These two rules provide comprehensive protection by looking for traversal patterns in two different places:
    *   **1000018:** Scans all incoming HTTP headers.
    *   **1000019:** Scans all request arguments (URL parameters and form data).
*   **Threat(s) Mitigated:** Directory Traversal, leading to Information Disclosure or unauthorized file access.

#### **Rules 1000020 - 1000025: Code and Data Injection Attempts**
*   **Purpose:** This critical block of rules looks for signatures of various code and data injection attacks within the request's arguments, body, and cookies.
*   **How they Work:**
    *   **1000020 (Shell Commands):** Looks for common Unix shell commands (`wget`, `curl`, `chmod`, etc.) to block Remote Code Execution (RCE) attempts.
    *   **1000021 & 1000022 (PHP Object Injection):** Look for the signature of a serialized PHP object (`O:[0-9]+:"`). This prevents Insecure Deserialization vulnerabilities, which can lead to RCE. One rule checks the body/args, the other checks cookies.
    *   **1000023 (PHAR Deserialization):** Blocks the `phar://` stream wrapper, another vector for PHP deserialization attacks.
    *   **1000024 (XXE Attack):** Looks for `<!ENTITY SYSTEM`, which is used in XML External Entity attacks to read local files or make unintended server-side requests.
    *   **1000025 (Prototype Pollution):** Blocks attempts to inject parameters named `__proto__`, `constructor`, or `prototype`, which can exploit a class of vulnerabilities in JavaScript-based applications.
*   **Threat(s) Mitigated:** Remote Code Execution (RCE), Insecure Deserialization, XXE, Prototype Pollution.

#### **Rules 1000026 & 1000027: Blocking Based on External File Lists**
*   **Purpose:** These rules use external data files to maintain long, up-to-date lists of malicious filenames, making the ruleset more powerful and easier to manage.
*   **How they Work:**
    *   **1000026 (`restricted-files.data`):** Blocks access to a list of sensitive configuration and data files like `.env`, `.htaccess`, `.git/config`, etc.
    *   **1000027 (`web-shells-php.data`):** Blocks access to a comprehensive list of known web shell filenames (e.g., `c99.php`, `r57.php`). Web shells are backdoors that give attackers administrative control over a server.
*   **Threat(s) Mitigated:** Information Disclosure, Server Backdoors.

---

### **Phase 4: Server Response Analysis**

Rules in this phase run *after* the web server has processed the request and generated a response. This is useful for taking action based on the server's response code.

#### **Rule 1000028: Excessive 404s Counter (The Detector)**
*   **Purpose:** To detect when a visitor requests a page that does not exist (a `404 Not Found` error).
*   **How it Works:** This is the "detector" part of the two-rule system for blocking scanners. When the server's `RESPONSE_STATUS` is `404`, this rule increments the `IP:bf_counter` variable for the visitor's IP address. This counter is then read by rule `1000004` in the next request from that same IP.
*   **Threat(s) Mitigated:** This rule does not mitigate a threat directly; it provides the intelligence needed for Rule `1000004` to perform the block.