# Post-Compromise Enumeration

## Domain Enumeration with PowerView

#### Setup

```powershell
C:\Users\fcastle\Desktop>powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\fcastle\Desktop> . .\PowerView.ps1
```

#### Get-NetDomain

Get information about the Domain

```powershell
PS C:\Users\fcastle\Desktop> Get-NetDomain

Forest                  : MARVEL.local
DomainControllers       : {HYDRA-DC.MARVEL.local}
Children                : {}
DomainMode              : Unknown
DomainModeLevel         : 7
Parent                  :
PdcRoleOwner            : HYDRA-DC.MARVEL.local
RidRoleOwner            : HYDRA-DC.MARVEL.local
InfrastructureRoleOwner : HYDRA-DC.MARVEL.local
Name                    : MARVEL.local
```

#### Get-NetDomainController

Get specific Domain Controllers

```powershell
PS C:\Users\fcastle\Desktop> Get-NetDomainController

Forest                     : MARVEL.local
CurrentTime                : 1/20/2022 1:57:31 AM
HighestCommittedUsn        : 53277
OSVersion                  : Windows Server 2019 Standard Evaluation
Roles                      : {SchemaRole, NamingRole, PdcRole, RidRole...}
Domain                     : MARVEL.local
IPAddress                  : 192.168.72.137
SiteName                   : Default-First-Site-Name
SyncFromAllServersCallback :
InboundConnections         : {}
OutboundConnections        : {}
Name                       : HYDRA-DC.MARVEL.local
Partitions                 : {DC=MARVEL,DC=local, CN=Configuration,DC=MARVEL,DC=local, CN=Schema,CN=Configuration,DC=MARVEL,DC=local, DC=DomainDnsZones,DC=MARVEL,DC=local...}
```

#### Get-DomainPolicy

Get all policies in the Domain

```powershell
PS C:\Users\fcastle\Desktop> Get-DomainPolicy

Name                           Value
----                           -----
Kerberos Policy                {MaxTicketAge, MaxServiceAge, MaxClockSkew, MaxRenewAge...}
System Access                  {MinimumPasswordAge, MaximumPasswordAge, LockoutBadCount, PasswordComplexity...}
Version                        {Revision, signature}
Registry Values                {MACHINE\System\CurrentControlSet\Control\Lsa\NoLMHash}
Unicode                        {Unicode}
```

```powershell
PS C:\Users\fcastle\Desktop> (Get-DomainPolicy)."System Access"
Name Value
---- -----
MinimumPasswordAge {1} 
MaximumPasswordAge {42} 
LockoutBadCount {0} 
PasswordComplexity {1} 
RequireLogonToChangePassword {0} 
LSAAnonymousNameLookup {0} 
ForceLogoffWhenHourExpire {0} 
PasswordHistorySize {24} 
ClearTextPassword {0} 
MinimumPasswordLength {7}
```

#### Get-NetUser

Get a bunch of user details

```powershell
PS C:\Users\fcastle\Desktop> Get-NetUser
...
logoncount            : 0
badpasswordtime       : 12/31/1600 4:00:00 PM
description           : Admin
distinguishedname     : CN=Tony Stark,CN=Users,DC=MARVEL,DC=local
objectclass           : {top, person, organizationalPerson, user}
displayname           : Tony Stark
userprincipalname     : tstark@MARVEL.local
name                  : Tony Stark
objectsid             : S-1-5-21-676298576-3461144594-1832349395-1107
samaccountname        : tstark
admincount            : 1
codepage              : 0
samaccounttype        : 805306368
whenchanged           : 6/18/2020 12:53:23 AM
accountexpires        : 9223372036854775807
countrycode           : 0
adspath               : LDAP://CN=Tony Stark,CN=Users,DC=MARVEL,DC=local
instancetype          : 4
objectguid            : 3efcaafd-dc9c-485e-8abf-a21b8a44d5d0
lastlogon             : 12/31/1600 4:00:00 PM
lastlogoff            : 12/31/1600 4:00:00 PM
objectcategory        : CN=Person,CN=Schema,CN=Configuration,DC=MARVEL,DC=local
dscorepropagationdata : {6/18/2020 12:49:06 AM, 1/1/1601 12:00:00 AM}
givenname             : Tony
memberof              : {CN=Group Policy Creator Owners,OU=Groups,DC=MARVEL,DC=local, CN=Domain Admins,OU=Groups,DC=MARVEL,DC=local, CN=Enterprise Admins,OU=Groups,DC=MARVEL,DC=local, CN=Schema
                        Admins,OU=Groups,DC=MARVEL,DC=local...}
whencreated           : 6/18/2020 12:48:27 AM
sn                    : Stark
badpwdcount           : 0
cn                    : Tony Stark
useraccountcontrol    : 66048
usncreated            : 16443
primarygroupid        : 513
pwdlastset            : 6/17/2020 5:48:27 PM
usnchanged            : 16498
...
```

```powershell
PS C:\Users\fcastle\Desktop> Get-NetUser | select cn
cn
--
Administrator 
Guest 
krbtgt 
Frank 
Castle 
Tony Stark 
Wade Wilson 
SQL Service
```

#### Get-UserProperty

```powershell
PS C:\Users\fcastle\Desktop> Get-UserProperty -Properties pwdlastset

name          pwdlastset
----          ----------
Administrator 6/17/2020 4:57:17 PM
Guest         12/31/1600 4:00:00 PM
krbtgt        6/17/2020 5:11:21 PM
Frank Castle  6/17/2020 5:46:31 PM
Tony Stark    6/17/2020 5:48:27 PM
Wade Wilson   6/17/2020 5:49:38 PM
SQL Service   6/17/2020 5:52:09 PM
```

#### Get-NetComputer

Get the computers in the domain.

```powershell
PS C:\Users\fcastle\Desktop> Get-NetComputer
HYDRA-DC.MARVEL.local
PUNISHER.MARVEL.local
DEADPOOL.MARVEL.local
```

```powershell
PS C:\Users\fcastle\Desktop> Get-NetComputer -FullData
...
```

#### Get-NetGroup

Get the groups

```powershell
PS C:\Users\fcastle\Desktop> Get-NetGroup
...
```

#### Get-NetGroupMember

Get members of a group

```powershell
PS C:\Users\fcastle\Desktop> Get-NetGroupMember -GroupName "Domain Admins"
```

#### Invoke-ShareFinder

Get a list of active shares

```powershell
PS C:\Users\fcastle\Desktop> Invoke-ShareFinder
\\HYDRA-DC.MARVEL.local\ADMIN$  - Remote Admin
\\HYDRA-DC.MARVEL.local\C$      - Default share
\\HYDRA-DC.MARVEL.local\hackme  -
\\HYDRA-DC.MARVEL.local\IPC$    - Remote IPC
\\HYDRA-DC.MARVEL.local\NETLOGON        - Logon server share
\\HYDRA-DC.MARVEL.local\SYSVOL  - Logon server share
\\PUNISHER.MARVEL.local\ADMIN$  - Remote Admin
\\PUNISHER.MARVEL.local\C$      - Default share
\\PUNISHER.MARVEL.local\IPC$    - Remote IPC
\\PUNISHER.MARVEL.local\Share   -
```

#### Get-NetGPO

Get the group policies of the domain

```powershell
PS C:\Users\fcastle\Desktop> Get-NetGPO
...
```

```powershell
PS C:\Users\fcastle\Desktop> Get-NetGPO |select displayname, whenchanged

displayname                       whenchanged
-----------                       -----------
Default Domain Policy             6/18/2020 12:20:10 AM
Default Domain Controllers Policy 6/18/2020 12:10:44 AM
Disable Windows Defender          6/18/2020 1:00:19 AM
```

#### Cheat Sheet

[https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

## Grabbing Data with Invoke-Bloodhound

> Copied over SharpHound.ps1 to the Windows target machine

#### Setup

```powershell
C:\Users\fcastle\Desktop>powershell -ep bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\fcastle\Desktop> . .\SharpHound.ps1
```

#### Execution

```powershell
PS C:\Users\fcastle\Desktop> Invoke-BloodHound -CollectionMethod All -Domain MARVEL.local -ZipFileName file.zip
```

Copy the zip file locally to Kali and then upload into Bloodhound.

## Enumerating Domain Data with Bloodhound

#### Queries

* Find all Domain Admins
* Find Shortest Paths to Domain Admins
* Shortest Paths to High Value Targets

#### On Graph Relationship

Access Boxes that have Domain Admin logged in (Green)

* HasSession
  * Token impersonation; Mimikatz
* MemberOf
  * Group that the account belongs to
