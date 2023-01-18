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

### Rule VI
