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
aws_secret_access_key =</code></pre>

Check for Google dorks using ExploitDB. Ex: searching on `aws`

#### IAM Security Issues

* Create User:
  * IAM --> Users --> add Users
* Potential Issues
  * Weak password security (password strength)
  * Lack of MFA
  * Rotating Keys / Key revocation/deactivation
  * Too many permissions / privileges (least privileges)

#### S3

#### EC2

#### Lambda

#### ARNs

### Tools

#### AWS CLI

#### Pacu

#### AWS Bucket Dump

#### GrayhatWarfare

### Flaws

### CloudGoat

