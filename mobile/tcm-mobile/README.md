---
description: https://academy.tcm-sec.com/courses/enrolled/1557555
---

# TCM - Mobile

## Introduction and Course Resources

### Course Resources

* [OWASP Mobile Application Pentesting GitBook](https://mobile-security.gitbook.io/mobile-security-testing-guide/overview/0x03-overview)
* [OWASP Mobile Top 10](https://owasp.org/www-project-mobile-top-10/)
* [SecJuice Intro to Frida & Objections](https://www.secjuice.com/objection-frida-guide/)
* iOS Research and Exploration: Volume I by James Duffy
* [HackTricks Checklist (Android)](https://book.hacktricks.xyz/mobile-pentesting/android-checklist)
* [HackTricks Checklist (iOS)](https://book.hacktricks.xyz/mobile-apps-pentesting/ios-pentesting-checklist)

### Mobile Pentesting Certification Landscape

#### Certifications / Courses

* [eLearnSecurity Mobile Application Penetration Tester](https://elearnsecurity.com/product/emapt-certification/)
* [SANS Institute GMOB (GIAC Certified Device Security Analyst)](https://www.giac.org/certifications/mobile-device-security-analyst-gmob/)
* [EC-Council](https://www.eccouncil.org/hands-on-android-security/)
* [Infosec Institute](https://www.infosecinstitute.com/skills/learning-paths/certified-mobile-and-web-app-penetration-tester-cmwapt/)

## Penetration Testing Process

### The Penetration Testing Process

1. Reconnaissance
   1. Active - Physical recon, interacting with targets via social engineering, anything with a "hands-on" the target approach
   2. Passive - using tools like LinkedIn, Google, publicly available info to enumerate the target
2. Scanning/Enumeration
   1. Using tools that touch the target's physical or digital infrastructure to enumerate vulnerabilities or open ports, etc.
      1. Example: nmap, dirb, nikto
3. Exploitation
   1. Taking advantage of the vulnerabilities that you discovered through enumeration and recon
4. Privilege Escalation
   1. Once inside of the system or environment, moving laterally or vertically to obtain more access
      1. Lateral - from device or app to another device or app as the same user
      2. Vertical - from low level user account to a higher level account
5. Cover Your Tracks
   1. Eliminating evidence that may incriminate you, or leave signs of exploitation
   2. Changing time stamps, erasing video footage, etc.
6. Reporting
   1. The result of all your hard work, outlining the findings and exploits to the company to allow them to fix them
   2. This is what separates us from the bad guys

### The Mobile Application Penetration Testing Process

1. Reconnaissance
   1. Find information about the company
      1. Earnings Reports and Press Releases often contain info about Mobile Apps
   2. Find the Target App on the Play Store or Apple Store
      1. Read reviews
      2. Enumerate who created the app
      3. Enumerate the different app versions and patch notes
      4. Enumerate the company's other apps
2. Static Analysis
   1. Reading the Application Code via manual or automated tools to access the security
      1. Looking for hardcoded strings, security misconfigurations, or additional targets extracted from the app
   2. Static Analysis will sometimes results in the Pentesting Process being triggered, especially additional enumeration or fingerprinting
      1. Find a URL - recon, enumerate, exploit, etc.
         1. Many companies use other API gateways/path for mobile apps vs the traditional website
      2. Find an email/username - recon using phonebook.cz, etc.
      3. Find a storage bucket - recon, enumerate with cloud\_enum
3. Dynamic Analysis
   1. Running the application and manipulating it
      1. Intercepting traffic with proxies like Burp Suite/Proxyman
      2. Dumping memory from the application to check for insecurely stored secrets
      3. Checking local storage for files created at runtime
      4. Breaking SSL Pinning at Runtime
   2. Dynamic Analysis can often result in attacks related to the OWASP Top Ten
      1. SQL Injection, Cross-Site Scripting, IDOR, XXE, etc.
         1. Note on XSS: you often will not get XSS in the mobile app itself, but sometimes this can affect the full version of the website.
4. Reporting
   1. Often contains executive summary as well as specific vulnerabilities discovered
   2. Write report with both OWASP Top Ten (Web) and OWASPT Top Ten (Mobile) in mind
   3. Provide business with the criticality as well as steps to reproduce
   4. Remember to mention the positive security implementations
