# AWS

## Basic Concepts

#### AWS Pentesting Policy

[https://aws.amazon.com/security/penetration-testing/](https://aws.amazon.com/security/penetration-testing/)

#### AWS Keys

Comprised of access ID and the secret key itself.

* Create Key:
  * IAM --> Users --> Create access key
* View Keys:
  * IAM --> Users --> Security Credentials --> Access key ID

Find Keys:

<pre class="language-bash"><code class="lang-bash"><strong># Linux Location
</strong><strong>~/.aws/credentials
</strong># Windows Location
%USERPROFILE%/.aws/credentials
# Typical Entry
[default]
aws_access_key_id =
aws_secret_access_key =
</code></pre>

Check for Google dorks using ExploitDB. Ex: searching on `aws`

### IAM (Identity and Access Management) Security Issues

* Create User:
  * IAM --> Users --> add Users
* Potential Issues
  * Weak password security (password strength)
  * Lack of MFA
  * Rotating Keys / Key revocation/deactivation
  * Too many permissions / privileges (least privileges)

### S3 (Simple Storage Service)

* Create a bucket
  * S3 --> Buckets --> Create bucket
* Potential Issues
  * Public buckets
  * Permissions
  * Bucket versioning

Find Buckets:

```bash
nslookup flaws.cloud
Address: 52.218.245.11

# Reverse lookup
nslookup 52.218.245.11
11.245.218.52.in-addr.arpa    name = s3-website-us-west-2.amazonaws.com
```

Can also check website page source for S3 bucket URLs.

### EC2 (Elastic Compute Cloud)

* After OS initial installation, update the OS/install patches (patch management)
* Update 3rd party software and services (email server, web server, etc.)
* AMI (Amazon Machine Image) - supported and maintained image provided by AWS that provides the information required to launch an instance
* Obtain the credentials for their IAM roles from the metadata service at 169.254.169.254

### Lambda

AWS Lambda is a serverless, event-driven compute service that lets you run code for virtually any type of application or backend service without provisioning or managing servers.

* Make sure the code that is passed to it is properly sanitized
* Security misconfigurations such as permissions settings for other accounts
* Third partly libraries that are used
* Potential DoS if code takes input from users
* Obtains credentials from environmental variables.

### ARNs (Amazon Resource Names)

A way to uniquely identify AWS resources. We require an ARN when you need to specify a resource unambiguously across all of AWS

* Can potentially leak information if such details can be accessed
* Be familiar with the Format and Paths

## Tools

### GrayhatWarfare

Search Public Buckets: [https://buckets.grayhatwarfare.com/](https://buckets.grayhatwarfare.com/)

### AWS CLI

```bash
aws configure  #  Set Access Key ID, Secret Acces Key, Region name, output format
aws configure --profile <name>  # Set same as above for specific profile
aws --profile <name>
tail .aws/credentials  # Check the credentials files
aws help
aws iam <tab tab>
aws s3 <tab tab>
```

### Pacu

The Open Source AWS Exploitation Framework

[https://rhinosecuritylabs.com/aws/pacu-open-source-aws-exploitation-framework/](https://rhinosecuritylabs.com/aws/pacu-open-source-aws-exploitation-framework/)

```bash
pacu  # Start up pacu
# Create/Select a session
Pacu () > help
Pacu () > import_keys <profile name from .aws/credentials file>  # Import existing keys

```

### AWS Bucket Dump

Security Tool to Look For Interesting Files in S3 Buckets

[https://github.com/jordanpotti/AWSBucketDump](https://github.com/jordanpotti/AWSBucketDump)

```bash
# Example
python3 AWSBucketDump.py -l BucketNmes.txt -g interesting_Keywords.txt
```

####

###

###

{% hint style="info" %}
References:

* ITProTV - Intro to AWS Penetration Testing
  * https://www.linkedin.com/learning/introduction-to-aws-penetration-testing/
*
{% endhint %}
