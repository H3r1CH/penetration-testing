---
description: >-
  https://udemy.com/course/uncle-rats-api-security-testing-guide;
  https://hackxpert.com/API-testing.php
---

# XSS Rat

## OWASP API Security Project

[https://owasp.org/www-project-api-security/](https://owasp.org/www-project-api-security/)

### OWASP Security Top 10

1. [API1:2019 Broken Object Level Authorization](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa1-broken-object-level-authorization.md)
2. [API2:2019 Broken User Authentication](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa2-broken-user-authentication.md)
3. [API3:2019 Excessive Data Exposure](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa3-excessive-data-exposure.md)
4. [API4:2019 Lack of Resources & Rate Limiting](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa4-lack-of-resources-and-rate-limiting.md)
5. [API5:2019 Broken Function Level Authorization](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa5-broken-function-level-authorization.md)
6. [API6:2019 Mass Assignment](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa6-mass-assignment.md)
7. [API7:2019 Security Misconfiguration](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa7-security-misconfiguration.md)
8. [API8:2019 Injection](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa8-injection.md)
9. [API9:2019 Improper Assets Management](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xa9-improper-assets-management.md)
10. [API10:2019 Insufficient Logging & Monitoring](https://github.com/OWASP/API-Security/blob/master/2019/en/src/0xaa-insufficient-logging-monitoring.md)

## API Top 10 Demo

#### API1 Broken Object Level Authorization

```bash
# Endpoints (GET):
/api/v1/resources/books/all
/api/v1/resources/books?id=xxxx
```

Sending these request in Postman to see what they return shows a lot of 403 Forbidden but the HTTP verb can be changed (to POST) to give us visibility to see them.

#### API2 Broken Authentication

Username and Password login page.

Viewed page source. Found Username and mentions that the password will have to be guessed.

#### API3 Excessive Data Exposure

```bash
# Endpoint (GET):
/api/v1/cards
/api/v1/cards?id=0
```

Specifying the ID value, the page shows more information that should be returned, specifically the CVV portion of the credit card information.

#### API4 Lack of Resources & Rate Limiting

All labs can be tested here as none of them have rate limiting. Example using the Username and Password login form.

Attackers can try to attempt credentials over and over again. No captcha either.

#### API5 Broken Function Level Authorization

```bash
# Endpoints:
/api/v2/resources/books/all
# Hint that there is an /admin endpoint somehwere
/api/v2/resources/admin
/api/v2/admin
/api/v3/admin
```

Check all found paths to identify interesting ones such as admin. Can also modify the API version path to see if it acts differently. Lastly, change the type of request type i.e. GET to POST.

#### API6 Mass Assignment

Example User Registration

```http
POST /api/register HTTP/1.1
[..]
{“email”:”user1@example.com”}

HTTP/1.1 200 OK
[..]
{”userid”:”112345”,“email”:”user1@example.com”,”email_verified”:false
```

Example Attack

```http
POST /api/register HTTP/1.1
[..]
{“email”:”user2@example.com”,”email_verified”:true}

HTTP/1.1 200 OK
[..]
{”userid”:”112346”,“email”:”user2@example.com”,”email_verified”:true}
```

By figuring out what JSON objects are available from the first request's response, an attacker can try to manipulate and force their values in the request itself.

#### API7 Security Misconfiguration

Some examples of security misconfigurations include insecure default configurations, incomplete or ad-hoc configurations, open cloud storage, misconfigured HTTP headers, unnecessary HTTP methods, overly permissive Cross-Origin resource sharing (CORS), and verbose error messages.

```bash
# Example request
GET /api/v2/network/connections/593065
# Example attack
GET /api/v2/network/connections/593065aaaaa
```

The example attack could potentially cause a too detailed exception error.

#### API8 Injection

Username and Password login page.

Passing in a username in the Username field and then the password as `' or 1=1`

A couple other examples:

```bash
# SQL Injection
GET /api/v1.1/posts?id=12358
# Attack
GET /api/v1.1/posts?id=12358; DROP TABLE users

# Command Injection
GET /api/v1.1/files?id=1123581321
# Attack
GET /api/v1.1/files?id=1123581321; rm -rf /var/www/html/users
```

#### API9 Improper Assets Management

We should always wonder for every API if all the current endpoint should even be available and if we maybe can't do with only allowing the whole API to communicate internally. To aid us we can ask ourselves if this API even needs to be in production at all and who should have access to the API. If the specific API does needs to be in production, we can also ask ourselves if we are not using an outdated version of the API, if there is sensitive data exposed or how the data flows throughout the application and APIs. A lack of documentation is a problem that plagues many companies and this only makes things worse because an undocumented API that does not send out traffic can remain undetected for a long time. To ease the removal of older APIs we might consider a retirement plan for APIs that are no longer needed. Another tool we have at our disposal are inventory management systems which index every API and it's version, this can be used to perform a regular inspection of the API plan. As long as these older APIs and their libraries remain unpatched, the system will be vulnerable to Improper Assets Management.

#### API10 Insufficient Logging and Monitoring

This vulnerability is a bit more complexity than it was at first sight, of course the API is vulnerable if it does not create any log entries or when the log level is set incorrectly but we should not neglect to also check whether or not the contents of the log messages are what is expected and that they contain enough detail. A second aspect that is a bit harder to control is the log integrity, for example a malicious user might insert a special character that when written to the log breaks it. This is known as log injection and it could nullify all the logging efforts made. When we finally have a clear plan of what to log, when and on what environment we need to ensure we are monitoring these logs, failure to do so would result in a massive decrease in the efficiency of our logging efforts. Monitor everything, not just the logs but make you monitor the APIs and the infrastructure as well.

## Building and Hacking APIs

## API Firewalls

```bash
# Installing a demo API (Something to protect)
pip3 install connexion[swagger-ui]
cd /tmp
git clone https://github.com/spec-first/connexion.git
python3 /tmp/connexion/examples/openapi3/app.py
```

```bash
# Installing the Firewall
docker pull wallarm/api-firewall
docker run -d -v /tmp/connexion/examples/openapi3/methodresolver/openapi/:/tmp \
-e APIFW_SERVER_URL=http://178.79.152.114:9090/v1.0/ -e APIFW_API_SPECS=/tmp/pets-api.yaml \
-e APIFW_REQUEST_VALIDATION=BLOCK -e APIFW_RESPONSE_VALIDATION=BLOCK -p 8282:8282 wallarm/api-firewall
docker container ls
docker logs <CONTAINER ID>
```

```bash
# Requests made to the application

```

## API Hacking with Postman







