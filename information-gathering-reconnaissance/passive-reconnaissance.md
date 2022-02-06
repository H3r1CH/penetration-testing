# Passive Reconnaissance

## Passive Reconnaissance Overview <a href="#passive-reconnaissance-overview" id="passive-reconnaissance-overview"></a>

### Physical/Social <a href="#physical-social" id="physical-social"></a>

#### Location Information <a href="#location-information" id="location-information"></a>

* Satellite images, Drone recon, Building layout (badge readers, break areas, security, fencing)

#### Job Information <a href="#job-information" id="job-information"></a>

* Employees (name, job title, phone number, manager, etc.)
* Pictures (badge photos, desk photos, computer photos, etc.)

### Web/Host <a href="#web-host" id="web-host"></a>

#### Target Validation <a href="#target-validation" id="target-validation"></a>

* WHOIS, nslookup, dnsrecon

#### Finding Subdomains <a href="#finding-subdomains" id="finding-subdomains"></a>

* Google Fu, dig, Nmap, Sublist3r, Bluto, crt.sh, etc.

#### Fingerprinting <a href="#fingerprinting" id="fingerprinting"></a>

* Nmap, Wappalyzer, WhatWeb, BuiltWith, Netcat

#### Data Breaches <a href="#data-breaches" id="data-breaches"></a>

* HaveIBeenPwned, Breach-Parse, WeLeakInfo

## Identify Our Target <a href="#identify-our-target" id="identify-our-target"></a>

Bugcrowd. Rules of Engagement; Make sure your in scope, noting the out of scope items.

## Discovering Email Addresses <a href="#discovering-email-addresses" id="discovering-email-addresses"></a>

### Tools <a href="#tools" id="tools"></a>

* hunter.io
* phonebook.cz
* voilanorbert.com
* clearbit connect Chrome extension
* emailhippo - tools.verifyemailaddress.io
* email-chekcer.net/validate

## Breached Credentials

### Gathering Breached Credentials

#### Breach-Parse

Tool on GitHub by TCM that uses breached username and password credentials and searching on a specific domain names to limit the accounts for that domain. Credential Stuffing.

### Hunting Breached Credentials

#### DeHashed

Payed service at dehashed.com.

## Hunting Subdomains

### Tools

* Sublist3r
  * `sublist3r -d tesla.com`
* crt.sh (Website)
* OWASP Amass
* httprobe

## Identifying Website Technologies

### Tools

* builtwith.com
* Wappalyzer
* whatweb
  * `whatweb https://tesla.com`

## Information Gathering with Burp Suite

Intercept website traffic. View/modify requests. View Target details.

## Google Fu

### Examples

* `site:tesla.com -www`
* `site:tesla.com filetype:pdf`

### Resources

{% embed url="https://ahrefs.com/blog/google-advanced-search-operators" %}

{% embed url="https://moz.com/learn/seo/search-operators" %}

## Utilizing Social Media

### Websites

* LinkedIn
* Twitter
* Facebook

### Look For

* Pictures
* Badge photos
* Desk pictures
* People and their information

## OSINT Fundamentals
