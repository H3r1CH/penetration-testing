# API Security Fundamentals

## Why API security?



## OWASP API Security Top 10 (2023)

1. Broke Object Level Authorization
   1. Example: User A can access User B's data
2. Broken Authentication
   1. Example: Unauthorized transactions
3. Broke Object Property Level Authorization
   1. Example: User is able to set "account-type=premium"
4. Unrestricted Resource Consumption
   1. Example: Missing/inadequate rate controls; mass data harvesting; execution timeouts
5. Broken Function Level Authorization
   1. Example: May be used to escalate privilege; Modify parameters
6. Unrestricted Access to Sensitive Business Flows
   1. Example: Loss of critical business activity
7. Server Side Request Forgery
   1. Example: Creates channel for malicious requests,data access or other fraudulent activity
8. Security Misconfiguration
   1. Example: Lack of security hardening, missing security patches
9. Improper Inventory Management
   1. Example: Zombie, shadow, and rogue APIs; old versions of APIs
10. Unsafe Consumption of APIs
    1. Example: API risk from 3rd party APIs, so data theft, breach, account takeover

## API attack analysis

Threat Modeling

* Identify: APIs, business. flows, data, access paths
* Access: vulnerabilities, logic flaws, access controls, 3rd party risk
* Probability: examine the likelihood of an attack
* Impact: understand the damage, loss, consequences of an attack
* Mitigation: develop a plan to address the risk

What do you have that attacks want?

* Personal information, financial information, corporate data, fraud, critical infrastructure

How are APIs used in your business?

* Website functionality, mobile application, customer/partner API access, internal microservices, 3rd party data and services

## 3 pillars of API security

Governance - Developing secure APIs

Monitoring - Detecting threats in production

Testing - Ensuring APIs are free of flaws

## Best practices...

