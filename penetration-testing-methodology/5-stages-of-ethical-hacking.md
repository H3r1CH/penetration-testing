# 5 Stages of Ethical Hacking

## 5 Stages of Ethical Hacking

### Reconnaissance

#### AKA Footprinting

* Finding out specific IP addresses, TCP & UDP services, Identifies vulnerabilities
* Network, Host, People involved

#### Active

Directly interacting with the target to gather information about the target.

#### Passive

Trying to collect the information about the target without directly accessing the target. To this purpose, hacker can use social media, public websites etc.

### Scanning (& Enumeration)

Nmap, Nessus, Nikto, etc.

1. **Pre-attack** - Hacker scans the network for specific information based on the information gathered during reconnaissance.
2. \*\*Port scanning \*\*- This phase involves scanning the target for the information like open ports, live systems, various services running on the host.
3. \*\*Vulnerability Scanning \*\*- Checking the target for weaknesses or vulnerabilities which can be exploited. Usually done with help of automated tools
4. \*\*Network Mapping/Information Extraction \*\*- Finding the topology of network, routers, firewalls servers if any, and host information and drawing a network diagram with the available information. This map may serve as a valuable piece of information throughout the haking process.

### Gaining Access (Exploitation)

This phase is where an attacker breaks into the system/network using various tools or methods. After entering into a system, he has to increase his privilege to administrator level so he can install an application he needs or modify data or hide data.

#### Examples

* Phishing, MitM, Brute Force, Spoofing, DoS, BOF, Session Hijacking, BEC

### Maintaining Access

Hacker may just hack the system to show it was vulnerable or he can be so mischievous that he wants to maintain or persist the connection in the background without the knowledge of the user. This can be done using Trojans, Rootkits or other malicious files. The aim is to maintain the access to the target until he finishes the tasks he planned to accomplish in that target.

### Clearing Tracks

No thief wants to get caught. An intelligent hacker always clears all evidence so that in the later point of time, no one will find any traces leading to him. This involves modifying/corrupting/deleting the values of Logs, modifying registry values and uninstalling all applications he used and deleting all folders he created.

#### Examples

* Clearing the cache and cookies
* Modifying registry values
* Modifying/corrupting/deleting the values of Logs
* Clearing out Sent emails
* Closing all the open ports
* Uninstalling all applications that he/she be used
