# APISEC

## Docker Commands

```bash
# Pull service images
sudo docker-compose pull
# Create and start containers (file, detached mode)
sudo docker-compose up -d
sudo docker-compose -f docker-compose.yml --compatibility up -d
# Stop services
sudo docker-compose stop
# List containers
sudo docker-compose ps
```

## API Reconnaissance

### API Types

1. Open/Public APIs - meant to be easily found and used by end-users. Public APIs may be entirely public without authentication or be meant for use by authenticated users.
2. Internal/Private APIs - intended for use, privately, within an organization. These APIs are often documented less than partner APIS, if at all, and if any documentation exists it is even harder to find.
3. Partner APIs - intended to be used exclusively by partners of the provider. These might be harder to find if you are not a partner. Partner APIs may be documented, but documentation is often limited to the partner.

### Web API Indicators

#### URL Schemes

```http
https://target-name.com/api/v1
https://api.target-name.com/v1
https://target-name.com/docs
https://dev.target-name.com/rest
```

Look for API indicators within directory names like:

`/api, /api/v1, /v1, /v2, /v3, /rest, /swagger, /swagger.json, /doc, /docs, /graphql, /graphiql, /altair, /playground`

#### Subdomains

```http
api.target-name.com
uat.target-name.com
dev.target-name.com
developer.target-name.com
test.target-name.com
```

#### Headers / Data

_HTTP Request and Response Headers containing:_

_`"Content-Type: application/json, application/xml"`_

&#x20;_Also, watch for HTTP Responses that include statements like:_

_`{"message": "Missing Authorization token"}`_

### Passive

Passive API Reconnaissance **** is the act of obtaining information about a target without directly interacting with the target’s systems. When you take this approach, your goal is to find and document public information about your target’s attack surface.&#x20;

#### Google Dorking

First, just Google search for companies API. Ex: `reddit api`

```python
intitle:"api" site:"coinbase.com"
intitle:json site:ebay.com
intitle:"index.of" intext:"api.txt"  # Finds publicly available API key files.
inurl:"/api/v1" site:microsoft.com
inurl:"/wp-json/wp/v2/users"  # Finds all publicly available WordPress API user directories.
inurl:"/api/v1" intext:"index of /"  # Finds potentially interesting API directories.
ext:php inurl:"api.php?action="  # Finds all sites with a XenAPI SQL injection vulnerability. (This query was posted in 2016; four years later, there are currently 141,000 results.)
basintitle:"index of" api_key OR "api key" OR apiKey -pool  # This is one of my favorite queries. It lists potentially exposed API keys.
```

#### Git Dorking

Begin by searching GitHub for your target organization’s name paired with potentially sensitive types of information, such as “api key,” "api keys", "apikey", "authorization: Bearer", "access\_token", "secret", or “token.” Then browse code, issues, and review proposed changes in pull requests.

Similar to Google Dorking, with GitHub, you can specify parameters like:

`filename:swagger.json`, `extension:.json`, `"authorization: Bearer"`,&#x20;

#### TruffleHog

[https://github.com/trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog)

TruffleHog is a great tool for automatically discovering exposed secrets. You can simply use the following Docker run to initiate a TruffleHog scan of your target's GitHub.

`sudo docker run -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --org=target-name`

#### API Directories

Programmableweb.com is a go-to source for API-related information ([https://www.programmableweb.com/apis/directory](https://www.programmableweb.com/apis/directory)). To learn about APIs, you can use their API University. To gather information about your target, use the API Directory, a searchable database of over 23,000 APIs. Expect to find API endpoints, version information, business logic information, the status of the API, source code, SDKs, articles, API documentation, and a changelog.

#### Shodan

```bash
# Try searching on company/target name, port, etc.
hostname:"targetname.com"  # Using hostname will perform a basic Shodan search for your target’s domain name. This should be combined with the following queries to get results specific to your target.
"content-type: application/json"  # APIs should have their content-type set to JSON or XML. This query will filter results that respond with JSON.
"content-type: application/xml"  # This query will filter results that respond with XML.
"200 OK"  # You can add "200 OK" to your search queries to get results that have had successful requests. However, if an API does not accept the format of Shodan’s request, it will likely issue a 300 or 400 response.
"wp-json"  # This will search for web applications using the WordPress API.
```

#### The Wayback Machine

[https://archive.org/web/](https://archive.org/web/)

Check for differences between the API documentation. Later, when you are actively testing the API, make sure to test using old endpoints,

### Active

Active reconnaissance is the process of interacting directly with the target primarily through the use of scanning. We will use our recon to search for our target's APIs and any useful information.

#### nmap

```bash
nmap -p- 127.0.0.1
nmap -sC -sV -p<PORTS> 127.0.0.1
```

#### amass

```bash
amass enum -list  # See which data sources are available (paid and free)
amass enum -active -d crapi.apisec.ai
amass enum -active -d microsoft.com | grep api
```

#### gobuster

```bash
gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --wildcard -b 200
gobuster dir -u http://127.0.0.1:8888/community -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

#### kiterunner

```bash
kr scan HTTP://127.0.0.1 -w ~/api/wordlists/data/kiterunner/routes-large.kite
# Use a text wordlist rather than a .kite file, use the brute option with the text file of your choice
kr brute <target> -w ~/api/wordlists/data/automated/nameofwordlist.txt
```

#### DevTools

F12 --> Network --> ctrl+r

Add URL column (Right click on column header and check URL)

Can copy URL as curl request and import into Postman

## Endpoint Analysis

### Reverse Engineering an API

### Using APIs and Excessive Data

## Scanning APIs

## API Authentication Attacks

## Exploiting API Authorization

## Testing for Improper Assets Management

## Mass Assignment

## Server-Side Request Forgery

## Injection Attacks

## Evasion and Combining Techniques
