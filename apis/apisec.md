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

In the instance where there is no documentation and no specification file, you will have to reverse-engineer the API based on your interactions with it

#### Building a Collection in Postman

From Postman:

* Create a Workspace then select Create Collection.
* Select Capture Request on the bottom right.
* Select Enable proxy --> Enable proxy (Port 5555)
* Specify the URL to target. Ex: crapi.apisec.ai

From Browser:

* Navigate to crapi.apisec.ai
* Select Postman option from FoxyProxy
* Refresh page and you'll see requests populate in Postman
* Continue to perform actions in the application i.e. sign up, login, navigate around, updating profile pictures, adding video, changing email etc. Postman will continue to populate

From Postman:

* After done, capturing can be stopped in Postman
* Select Requests and check ones you want to add to the collection
* Select ellipsis to the right of Collection URL to Add folders and organize the API calls
* Now can browse and send requests to get a sense of what is going on

#### Automatic Documentation

mitmweb

* Start mitmweb: `mitmweb`
* Change FoxyProxy to use Burp/Port 8080
* Perform actions in the application i.e. sign up, login, navigate around, updating profile pictures, adding video, changing email etc. mitmproxy will continue to populate
* When done, select File --> Save and turn off proxy

mitmproxy2swagger

```bash

sudo mitmproxy2swagger -i ~/Downloads/flows -o spec.yml -p http://crapi.apisec.ai -f flow
sudo vim spec.yml
# Remove the "/ignore:" for all lines where there looks to be an API call
# Ex: ignore:/identity/api/auth/login to /identity/api/auth/login
# Rerun the initial command
sudo mitmproxy2swagger -i ~/Downloads/flows -o spec.yml -p http://crapi.apisec.ai -f flow --examples
```

Navigate to the Swagger Editor: [https://editor.swagger.io/](https://editor.swagger.io/)

Select File --> Import file --> and select the spec.yml file.

From Postman, Import a new Collection, selecting the same spec.yml file.

### Using APIs and Excessive Data

| Convention | Example                                                                                                                           | Meaning                                                                                                                                                                                                                  |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| : or {}    | <p>/user/:id</p><p>/user/{id}</p><p>/user/2727</p><p>/account/:username</p><p>/account/{username}</p><p>/account/scuttleph1sh</p> | The colon or curly brackets are used by some APIs to indicate a path variable. In other words, “:id” represents the variable for an ID number and “{username}” represents the account username you are trying to access. |
| \[ ]       | /api/v1/user?find=\[name]                                                                                                         | Square brackets indicate that the input is optional.                                                                                                                                                                     |
| \|\|       | “blue” \|\| “green” \|\| “red”                                                                                                    | Double bars represent different possible values that can be used.                                                                                                                                                        |

Can review the APIs using the Swagger editor as well as Postman

From Postman, we can edit the Workspace to provide Authorization using the Bearer Token which will be applied to all of the requests within it.

Using the Variables tab, you can create variables that will be used through the requests i.e. baseurl

Can also create a New Environment to create variables across multiple Collections.

Can change the request type for a request to see how it reacts i.e. GET to POST

Now to to review and send requests to look at the responses for Excessive Data Exposure

## Scanning APIs

### Finding Security Misconfigurations

Once you have discovered an API and used it as it was intended, you can proceed to perform a baseline vulnerability scan.

#### OWASP ZAP

API Specification File:

Plug in the target URL, but to improve the results and to make sure we hit everything, we can import the target's API specification file i.e. specs.yml. Select Import --> Import an OpenAPI definition from the local file system. The Sites window should fill with the target's endpoints and API requests. You can right-click on the root folder and start an active scan.

Manual Explore:

Select the Manual Explore option and set the URL of the target and select Launch Browser. Start using the application as intended by navigating around, signing up and signing in, and any other available actions. Once authenticated and performed a baseline set of actions an active scan can be started. Can also turn on the Attack Mode. Investigate the results.

## API Authentication Attacks

### Classic Authentication Attacks

#### Password Brute-Force Attacks

Burp Suite:

Utilized Intruder to brute force Username/Email and Password.

wfuzz:

```bash
# All usernames against all passwords
wfuzz -d '{"email":"admin@email.com","password":"FUZZ"}' -H 'Content-Type: application/json' -z file,/usr/share/wordlists/rockyou.txt -u http://crapi.apisec.ai/identity/api/auth/login --hc 500
ffuf -u http://127.0.0.1/vapi/api2/user/login -d "email=HFUZZ&password=WFUZZ" -H "Content-type: application/x-www-form-urlencoded" -mode clusterbomb -w emails.txt:HFUZZ -w passwords.txt:WFUZZ -fc 401
# Line by line e.g. first email against first password, second email against second password
ffuf -u http://127.0.0.1/vapi/api2/user/login -d "email=HFUZZ&password=WFUZZ" -H "Content-type: application/x-www-form-urlencoded" -mode pitchfork -w emails.txt:HFUZZ -w passwords.txt:WFUZZ -fc 401

```

#### Password Spraying

Long list of users with a short list of targeted passwords. Knowing the password policy such as login attempts and password complexity will help build out the password list.

Example finding user's emails via excessive data exposure vulnerability. Can copy the response from Postman and use grep to filter out just the emails:

```bash
grep -oe "[a-zA-Z0-9._]+@[a-zA-Z]+.[a-zA-Z]+" response.json > users.tmp
grep -oe "[a-zA-Z0-9._]+@[a-zA-Z]+.[a-zA-Z]+" response.json | sort -u > users.txt
```

Burp Suite:

Can use the Cluster bomb within intruder to select the email and password values.

wfuzz:

```bash
wfuzz -d '{"email":"FUZZ","password":"FUZ2Z"}' -H 'Content-Type: application/json' -z file,emails.txt -z file,passwords.txt -u http://crapi.apisec.ai/identity/api/auth/login --hc 500

```

Creating targeted password lists: [https://github.com/sc0tfree/mentalist](https://github.com/sc0tfree/mentalist), [https://github.com/Mebus/cupp](https://github.com/Mebus/cupp)

### API Token Attacks

#### Token Analysis

Example using crAPI API authentication request and proxy it over to Burp Suite.

1. Intercept login request and send to Sequencer
2. Select Configure next to Custom location.
   1. Highlight the whole token in between the quotes and Save.
3. Select Start live capture
   1. Will capture 20,000 tokens
4. When completed, select Analyze now if it doesn't start automatically
5. Review results looking at the complexity and randomness of them.
   1. Shows complex tokens can be predictable.
   2. Also check if the tokens are being generated sequentially

#### JWT Attacks

JWTs consist of three parts, all of which are base64 encoded and separated by periods: the header, payload, and signature.

[https://jwt.io/introduction](https://jwt.io/introduction)

If you’ve captured another user’s JWT or maybe discovered a leaked token during reconnaissance, you can try sending it to the provider and pass it off as your own.

#### Automating JWT Attacks with JWT\_TOOL

[https://github.com/ticarpi/jwt\_tool/wiki/Attack-Methodology](https://github.com/ticarpi/jwt\_tool/wiki/Attack-Methodology)

<pre class="language-bash"><code class="lang-bash"><strong>jwt_tool &#x3C;JWT TOKEN>
</strong><strong>jwt_tool -t http://127.0.0.1:8888/identity/api/v2/user/dashboard -rh "Authorization: Bearer &#x3C;JWT TOKEN>" -M pb
</strong><strong>jwt_tool &#x3C;JWT TOKEN> -X a
</strong></code></pre>

#### The None Attack

If you ever come across a JWT using "none" as its algorithm, you’ve found an easy win. After decoding the token, you should be able to clearly see the header, payload, and signature. From here, you can alter the information contained in the payload to be whatever you’d like.

#### The Algorithm Switch Attack

There is a chance the API provider isn’t checking the JWTs properly. If this is the case, we may be able to trick a provider into accepting a JWT with an altered algorithm. One of the first things you should attempt is sending a JWT without including the signature. This can be done by erasing the signature altogether and leaving the last period in place, like this:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJoYWNrYXBpcy5pbyIsImV4cCI6IDE1ODM2Mzc0ODgsInVzZ XJuYW1lIjoiU2N1dHRsZXBoMXNoIiwic3VwZXJhZG1pbiI6dHJ1ZX0.`

If this isn’t successful, attempt to alter the algorithm header field to "none". Decode the JWT, update the "alg" value to "none", base64-encode the header, and send it to the provider. To simplify this process, you can also use the jwt\_tool to quickly create a token with the algorithm switched to none.

`jwt_tool eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyYWFhQGVtYWlsLmNvbSIsImlhdCI6MTY1ODg1NTc0MCwiZXhwIjoxNjU4OTQyMTQwfQ._EcnSozcUnL5y9SFOgOVBMabx_UAr6Kg0Zym-LH_zyjReHrxU_ASrrR6OysLa6k7wpoBxN9vauhkYNHepOcrlA -X a`

A more likely scenario than the provider accepting no algorithm is that they accept multiple algorithms. For example, if the provider uses RS256 but doesn’t limit the acceptable algorithm values, we could alter the algorithm to HS256. This is useful, as RS256 is an asymmetric encryption scheme, meaning we need both the provider’s private key and a public key in order to accurately hash the JWT signature. Meanwhile, HS256 is symmetric encryption, so only one key is used for both the signature and verification of the token. If you can discover and obtain the provider’s RS256 public key then switch the algorithm from RS256 to HS256, there is a chance you may be able to leverage the RS256 public key as the HS256 key. It uses the format jwt\_tool TOKEN -X k -pk public-key.pem, as shown below. You will need to save the captured public key as a file on your attacking machine (You can simulate this attack by taking any public key and saving it as public-key-pem).

`jwt_tool eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyYWFhQGVtYWlsLmNvbSIsImlhdCI6MTY1ODg1NTc0MCwiZXhwIjoxNjU4OTQyMTQwfQ._EcnSozcUnL5y9SFOgOVBMabx_UAr6Kg0Zym-LH_zyjReHrxU_ASrrR6OysLa6k7wpoBxN9vauhkYNHepOcrlA -X k -pk public-key-pem`

#### JWT Crack Attack

The JWT Crack attack attempts to crack the secret used for the JWT signature hash, giving us full control over the process of creating our own valid JWTs.

```bash
crunch 5 5 -o crAPIpw.txt
jwt_tool <JWT TOKEN> -C -d crAPIpw.txt
```

In jwt.io you can add your user token to the JWT debugger, add the newly discovered secret, and update the "sub" claim to any email that has registered.

## Exploiting API Authorization

### Broken Object Level Authorization

When hunting for BOLA there are three ingredients needed for successful exploitation:

1. Resource ID: a resource identifier will be the value used to specify a unique resource. This could be as simple as a number, but will often be more complicated.
2. Requests that access resources. In order to test if you can access another user's resource, you will need to know the requests that are necessary to obtain resources that your account should not be authorized to access.
3. Missing or flawed access controls. In order to exploit this weakness, the API provider must not have access controls in place. This may seem obvious, but just because resource IDs are predictable, does not mean there is an authorization vulnerability present.

The third item on the list is something that must be tested, while the first two are things that we can seek out in API documentation and within a collection of requests. Once you have the combination of these three ingredients then you should be able to exploit BOLA and gain unauthorized access to resources.

#### Finding Resource IDs and Requests

If you can successfully access the information you shouldn’t be authorized to access, you have discovered an authorization vulnerability.

Here are a few ideas for requests that could be good targets for an authorization test.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

#### Searching for BOLA

First, let's think about the purpose of our target app and review the documentation. Thinking through the purpose of the app will give you a conceptual overview and help aim your sights. Ask questions like: What can you do with this app? Do you get your own profile? Can you upload files? Do you have an account balance? Is there any part of the app that has data specific to your account? Questions like these will help you search through the available requests and find a starting point for discovering requests that access resources.

#### Authorization Testing Strategy

When searching for authorization vulnerabilities the most effective way to find authorization weaknesses is to create two accounts and perform A-B testing. The A-B testing process consists of:

1. Create a UserA account
2. Use the API and discover requests that involve resource IDs as UserA
3. Document requests that include resource IDs and should require authorization
4. Create a UserB account
5. Obtaining a valid UserB token and attempt to access UserA's resources

### Broken Function Level Authorization

Where BOLA is all about accessing resources that do not belong to you, BFLA is all about performing unauthorized actions. BFLA vulnerabilities are common for requests that perform actions of other users. These requests could be lateral actions or escalated actions

For BFLA we will be hunting for very similar requests to BOLA:

1. Resource ID: a resource identifier will be the value used to specify a unique resource
2. Requests that perform authorized action. In order to test if you can access another update, delete, or otherwise alter the resources of other users
3. Missing or flawed access control. In order to exploit this weakness, the API provider must not have access controls in place

## Testing for Improper Assets Management

## Mass Assignment

## Server-Side Request Forgery

## Injection Attacks

## Evasion and Combining Techniques
