# THM

## OWASP API Security Top 10

### Rule I - Broken Object Level Authorization (BOLA)

#### How does it happen?

Generally, API endpoints are utilized for a common practice of retrieving and manipulating data through object identifiers. BOLA refers to Insecure Object Reference (IDOR) - which creates a scenario where the user uses the input functionality and gets access to the resources they are not authorized to access. In an API, such controls are usually implemented through programming in Models (Model-View-Controller Architecture) at the code level.

#### Likely Impact

The absence of controls to prevent unauthorized object access can lead to data leakage and, in some cases, complete account takeover. User's or subscribers' data in the database plays a critical role in an organization's brand reputation; if such data is leaked over the internet, that may result in substantial financial loss.

#### Practical Example

* Bob developed an endpoint `/apirule1/users/{ID}`
  * Allows for requesting information by sending an employee ID.
* The problem is that the endpoint is not validating any incoming API call to confirm whether the request is valid. It is not checking for any authorization from the requester.
* The solution is that an authorization mechanism should be implemented to identify who can make API calls to access employee ID information
  * This purpose is achieved through access tokens or authorization tokens in the header.

#### Mitigation Measures

* An authorization mechanism that relies on user policies and hierarchies should be adequately implemented.
* Strict access control methods to check if the logged-in user is authorized to perform specific actions.
* Promote using completely random values (strong encryption and decryption mechanism) for nearly impossible-to-predict tokens.

### Rule II - Broken User Authentication (BUA)

#### How does it happen?

User authentication is the core aspect of developing any application containing sensitive data. Broken User Authentication (BUA) reflects a scenario where an API endpoint allows an attacker to access a database or acquire a higher privilege than the existing one. The primary reason behind BUA is either invalid implementation of authentication like using incorrect email/password queries etc., or the absence of security mechanisms like authorization headers, tokens etc.&#x20;

Consider a scenario in which an attacker acquires the capability to abuse an authentication API; it will eventually result in data leaks, deletion, modification, or even the complete account takeover by the attacker. Usually, hackers have created special scripts to profile, enumerate users on a system and identify authentication endpoints. A poorly implemented authentication system can lead any user to take on another user's identity.

#### Likely Impact

In broken user authentication, attackers can compromise the authenticated session or the authentication mechanism and easily access sensitive data. Malicious actors can pretend to be someone authorized and can conduct an undesired activity, including a complete account takeover.

#### Practical Example

* Bob developed an endpoint `apirule2/user/login_v` that will authenticate based on provided email and password
* The endpoint will return a token, which will be passed as an Authorization-Token header (GET request) to `apirule2/user/details` to show details of the specific employee.
  * Bob developed the login endpoint successfully however, he only used email to validate the user and ignored the password field in the SQL query. An attacker only requires a victim's email address to get a valid token or account takeover.
* As seen the vulnerable endpoint received a token which can be forwarded to `/apirule/user/details` to get details of a user.
* To fix this, the login query logic will be fixed to check both email and password for validation.

#### Mitigation Measures

* Ensure complex passwords with higher entropy for end users..
* Do not expose sensitive credentials in GET or POST requests.
* Enable strong JSON Web Tokens (JWT), authorization headers, etc.
* Ensure the implementation of multifactor authentication (where possible), account lockout, or a captcha system to mitigate brute force against particular users.
* Ensure that passwords are not saved in plain text in the database to avoid further account takeover by the attacker.

### Rule III - Excessive Data Exposure

#### How does it happen?

Excessive data exposure occurs when applications tend to disclose more than desired information to the user through an API response. The application developers tend to expose all object properties (considering the generic implementations) without considering their sensitivity level. They leave the filtration task to the front-end developer before it is displayed to the user. Consequently, an attacker can intercept the response through the API and quickly extract the desired confidential data. The runtime detection tools or the general security scanning tools can give an alert on this kind of vulnerability. However, it cannot differentiate between legitimate data that is supposed to be returned or sensitive data.

#### Likely Impact

A malicious actor can successfully sniff the traffic and easily access confidential data, including personal details, such as account numbers, phone numbers, access tokens and much more. Typically, APIs respond with sensitive tokens that can be later on used to make calls to other critical endpoints.

#### Practical Example

* The company MHT launched a comment-based web portal that takes users' comments and stores them in the database and other information like location, device info, etc., to improve the user experience.
* Bob was tasked to develop an endpoint for showing users' comments on the company's main website. He developed an endpoint `apirule3/comment_v/{id}` that fetches all information available for a comment from the database. Bob assumed that the front-end developer would filter out information while showing it on the company's main website.
* What is the issue here? The API is sending more data than desired. Instead of relying on a front-end engineer to filter out data, only relevant data must be sent from the database.

#### Mitigation Measures

* Never leave sensitive data filtration tasks to the front-end developer.
* Ensure time-to-time review of the response from the API to guarantee it returns only legitimate data and checks if it poses any security issue.
* Avoid using generic methods such as `to_string()` and `to_json()`
* Use API endpoint testing through various test cases and verify through automated and manual tests if the API leaks additional data.

### Rule IV - Lack of Resources & Rate Limiting

#### How does it happen?

Lack of resources and rate limiting means that APIs do not enforce any restriction on the frequency of clients' requested resources or the files' size, which badly affects the API server performance and leads to the DoS (Denial of Service) or non-availability of service. Consider a scenario where an API limit is not enforced, thus allowing a user (usually an intruder) to upload several GB files simultaneously or make any number of requests per second. Such API endpoints will result in excessive resource utilization in network, storage, compute etc.&#x20;

Nowadays, attackers are using such attacks to ensure the non-availability of service for an organization, thus tarnishing the brand reputation through increased downtime. A simple example is non-compliance with the Captcha system on the login form, allowing anyone to make numerous queries to the database through a small script written in Python.

#### Likely Impact

The attack primarily targets the Availability principles of security; however, it can tarnish the brand's reputation and cause financial loss.

#### Practical Example

* The company MHT purchased an email marketing plan (20K emails per month) for sending marketing, password recovery emails etc. Bob realized that he had successfully developed a login API, but there must be a "Forgot Password" option that can be used to recover an account.
* He started building an endpoint `/apirule4/sendOTP_v` that will send a 4-digit numeric code to the user's email address. An authenticated user will use that One Time Password (OTP) to recover the account.
* What is the issue here? Bob has not enabled any rate limiting in the endpoint. A malicious actor can write a small script and brute force the endpoint, sending many emails in a few seconds and using the company's recently purchased email marketing plan (financial loss).
* Finally, Bob came up with an intelligent solution (`/apirule4/sendOTP_s`) and enabled rate limiting such that the user has to wait 2 minutes to request an OTP token again.

#### Mitigation Measures

Ensure using a captcha to avoid requests from automated scripts and bots

Ensure implementation of a limit, i.e., how often a client can call an API within a specified time and notify instantly when the limit is exceeded.

Ensure to define the maximum data size on all parameters and payloads, i.e., max string length and max number of array elements.

### Rule V - Broken Function Level Authorization

#### How does it happen?

Broken Function Level Authorization reflects a scenario where a low privileged user (e.g., sales) bypasses system checks and gets access to confidential data by impersonating a high privileged user (Admin). Consider a scenario of complex access control policies with various hierarchies, roles, and groups and a vague separation between regular and administrative functions leading to severe authorization flaws. By taking advantage of these issues, the intruders can easily access the unauthorized resources of another user or, most dangerously – the administrative functions.&#x20;

Broken Function Level Authorization reflects IDOR permission, where a user, most probably an intruder, can perform administrative-level tasks. APIs with complex user roles and permissions that can span the hierarchy are more prone to this attack.

#### Likely Impact

The attack primarily targets the authorization and non-repudiation principles of security. Broken Functional Level Authorization can lead an intruder to impersonate an authorized user and let the malicious actor get administrative rights to perform sensitive tasks.

#### Practical Example

* Bob developed an endpoint `/apirule5/users_v` to fetch data of all employees from the database. To add protection, he added another layer to security by adding a special header `isAdmin` in each request. The API only fetches employee information from the database if `isAdmin=1` and `Authorization-Token` are correct.
  * The authorization token for HR user Alice is `YWxpY2U6dGVzdCFAISM6Nzg5Nzg=`
* We can see that Alice is a non-admin user (HR) but can see all employee's data by setting custom requests to the endpoint with `isAdmin value = 1`.
* The issue can be resolved programmatically by implementing correct authorization rules and checking the functional roles of each user in the database during the query.

#### Mitigation Measures

* Ensure proper design and testing of all authorization systems and deny all access by default.
* Ensure that the operations are only allowed to the users belonging to the authorized group.
* Make sure to review API endpoints against flaws regarding functional level authorization and keep in mind the apps and group hierarchy's business logic.

### Rule VI  - Mass Assignment

#### How does it happen?

Mass assignment reflects a scenario where client-side data is automatically bound with server-side objects or class variables. However, hackers exploit the feature by first understanding the application's business logic and sending specially crafted data to the server, acquiring administrative access or inserting tampered data.

Consider a user's profiles dashboard where users can update their profile like associated email, name, address etc. The username of the user is a read-only attribute and cannot be changed; however, a malicious actor can edit the username and submit the form. If necessary filtration is not enabled on the server side (model), it will simply insert/update the data in the database.

#### Likely Impact

The attack may result in data tampering and privilege escalation from a regular user to an administrator.

#### Practical Example

* Bob has been assigned to develop a signup API endpoint `/apirule6/user` that will take a name, username and password as input parameters (POST). The user's table has a `credit column` with a default value of `50`. Users will upgrade their membership to have a larger credit value.
* Bob has successfully designed the form and used the mass assignment feature in Laravel to store all the incoming data from the client side to the database
* What is the problem here? Bob is not doing any filtering on the server side. Since using the mass assignment feature, he is also inserting credit values in the database (malicious actors can update that value).
* The solution to the problem is pretty simple. Bob must ensure necessary filtering on the server side (`apirule6/user_s`) and ensure that the default value of credit should be inserted as `50`, even if more than 50 is received from the client side (as shown below).&#x20;

#### Mitigation Measures

* Before using any framework, one must study how the backend insertions and updates are carried out. In the Laravel framework, [fillable and guarded](https://laravel.com/docs/9.x/eloquent#inserts) arrays mitigate the above-mentioned scenarios.&#x20;
* Avoid using functions that bind an input from a client to code variables automatically.
* Allowlist those properties only that need to get updated from the client side.&#x20;

### Rule VII - Security Misconfigurations

#### How does it happen?

Security misconfiguration depicts an implementation of incorrect and poorly configured security controls that put the security of the whole API at stake. Several factors can result in security misconfiguration, including improper/incomplete default configuration, publicly accessible cloud storage, [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS), and error messages displayed with sensitive data. Intruders can take advantage of these misconfigurations to perform detailed reconnaissance and get unauthorized access to the system.

Security misconfigurations are usually detected by vulnerability scanners or auditing tools and thus can be curtailed at the initial level. API documentation, a list of endpoints, error logs etc., must not be publicly accessible to ensure safety against security misconfigurations. Typically, companies deploy security controls like web application firewalls, which are not configured to block undesired requests and attacks.

#### Likely Impact

Security misconfiguration can give intruders complete knowledge of API components. Firstly, it allows intruders to bypass security mechanisms. Stack trace or other detailed errors can provide the malicious actor access to confidential data and essential system details, further aiding the intruder in profiling the system and gaining entry.

#### Practical Example

* The company MHT is facing serious server availability issues. Therefore, they assigned Bob to develop an API endpoint `/apirule7/ping_v` (GET) that will share details regarding server health and status.
* Bob successfully designed the endpoint; however, he forgot to implement any error handling to avoid any information leakage.
* What is the issue here? In case of an unsuccessful call, the server sends a complete stack trace in response, containing function names, controller and route information, file path etc. An attacker can use the information for profiling and preparing specific attacks on the environment.&#x20;
* The solution to the issue is pretty simple. Bob will create an API endpoint `/apirule7/ping_s` that will carry out error handling and only share desired information with the user

#### Mitigation Measures

* Limit access to the administrative interfaces for authorized users and disable them for other users.&#x20;
* Disable default usernames and passwords for public-facing devices (routers, Web Application Firewall etc.).
* Disable directory listing and set proper permissions for every file and folder.&#x20;
* Remove unnecessary pieces of code snippets, error logs etc. and turn off debugging while the code is in production.

### Rule VIII - Injection

#### How does it happen?

Injection attacks are probably among the oldest API/web-based attacks and are still being carried out by hackers on real-world applications. Injection flaws occur when user input is not filtered and is directly processed by an API; thus enabling the attacker to perform unintended API actions without authorization. An injection may come from[ Structure Query Language (SQL)](https://tryhackme.com/room/sqlinjectionlm), operating system (OS) commands, Extensible Markup Language (XML) etc. Nowadays, frameworks offer functionality to protect against this attack through automatic sanitization of data; however, applications built in custom frameworks like core PHP are still susceptible to such attacks.

#### Likely Impact

Injection flaws may lead to information disclosure, data loss, DoS, and complete account takeover. The successful injection attacks may also cause the intruders to access the sensitive data or even create new functionality and perform remote code execution.

#### Practical Example

* A few users of company MHT reported that their account password had changed, and they could not further log in to their original account. Consequently, the dev team found that Bob had developed a vulnerable login API endpoint `/apirule8/user/login_v` that is not filtering user input.
* A malicious attacker requires the username of the target, and for the password, they can use the payload `' OR 1=1--'` and get an authorization key for any account
* Bob immediately realized his mistake; he updated the API endpoint to `/apirule8/user/login_s` and used parameterized queries and built-in filters of Laravel to sanitize user input.
* As a result, all malicious payloads on username and password parameters were effectively mitigated

#### Mitigation Measures

* Ensure to use a well-known library for client-side input validation.
* If a framework is not used, all client-provided data must be validated first and then filtered and sanitized.&#x20;
* Add necessary security rules to the Web Application Firewall (WAF). Most of the time, injection flaws can be mitigated at the network level.
* Make use of built-in filters in frameworks like Laravel, Code Ignitor etc., to validate and filter data.&#x20;

### Rule IX - Improper Assets Management

#### How does it happen?

Inappropriate Asset Management refers to a scenario where we have two versions of an API available in our system; let's name them APIv1 and APIv2. Everything is wholly switched to APIv2, but the previous version, APIv1, has not been deleted yet. Considering this, one might easily guess that the older version of the API, i.e., APIv1, doesn't have the updated or the latest security features. Plenty of other obsolete features of APIv1 make it possible to find vulnerable scenarios, which may lead to data leakage and server takeover via a shared database amongst API versions.

It is essentially about not properly tracking API endpoints. The potential reasons could be incomplete API documentation or absence of compliance with the [Software Development Life Cycle](https://tryhackme.com/room/securesdlc). A properly maintained, up-to-date API inventory and proper documentation are more critical than hardware-based security control for an organization.

#### Likely Impact

The older or the unpatched API versions can allow the intruders to get unauthorized access to confidential data or even complete control of the system.

#### Practical Example

* During API development, the company MHT has developed different API versions like v1 and v2. The company ensured to use the latest versions and API calls but forgot to remove the old version from the server.
* Consequently, it was found that old API calls like `apirule9/v1/user/login` return more information like balance, address etc., against the user
* Bob being the developer of the endpoint, realized that he must immediately deactivate old and unused assets so that users can only access limited and desired information from the new endpoint `/apirul9/v2/user/login`

#### Mitigation Measures

* Access to previously developed sensitive and deprecated API calls must be blocked at the network level.
* APIs developed for R\&D, QA, production etc., must be segregated and hosted on separate servers.
* Ensure documentation of all API aspects, including authentication, redirects, errors, CORS policy, and rate limiting.&#x20;
* Adopt open standards to generate documentation automatically.

### Rule X - Insufficient Logging & Monitoring

#### How does it happen?

Insufficient logging & monitoring reflects a scenario when an attacker conducts malicious activity on your server; however, when you try to track the hacker, there is not enough evidence available due to the absence of logging and monitoring mechanisms. Several organizations only focus on infrastructure logging like network events or server logging but lack API logging and monitoring. Information like the visitor's IP address, endpoints accessed, input data etc., along with a timestamp, enables the identification of threat attack patterns. If logging mechanisms are not in place, it would be challenging to identify the attacker and their details. Nowadays, the latest web frameworks can automatically log requests at different levels like error, debug, info etc. These errors can be logged in a database or file or even passed to a [SIEM solution ](https://tryhackme.com/room/defensivesecurity)for detailed analysis.

#### Likely Impact

Inability to identify attacker or hacker behind the attack.

#### Practical Example

* In the past, the company MHT has been susceptible to multiple attacks, and the exact culprit behind the attacks could not be identified. Therefore, Bob was assigned to make an API endpoint `/apirule10/logging` (GET) that will log users' metadata (IP address, browser version etc.) and save it in the database as well
* Later, it was also decided that the same would be forwarded to a SIEM solution for correlation and analysis.

#### Mitigation Measures

* Ensure use of the Security Information and Event Management (SIEM) system for log management.&#x20;
* Keep track of all denied accesses, failed authentication attempts, and input validation errors, using a format imported by SIEM and enough detail to identify the intruder.
* Handle logs as sensitive data and ensure their integrity at rest and transit. Moreover, implement custom alerts to detect suspicious activities as well.&#x20;
