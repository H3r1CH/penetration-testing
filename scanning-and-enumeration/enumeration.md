# Enumeration

TCM's modified script - [https://pastebin.com/MhE6zXVt](https://pastebin.com/MhE6zXVt)

## Some Steps

1. nmap scan
2. nmap script scan (http, smb, smtp, tftp, etc.)
3. Metasploit enumeration
4. searchsploit / exploitDB (others)
5. Google

### Other Tools

* Reconnoitre
* Vanquish
* Sn1per
* SPARTA

## Ping

> When pinging a machine we can confirm the OS type by the TTL.&#x20;
>
> Windows default TTL is 128 which decreases by 1 when a packet traverses a router, so look for 127. If its between 64 and 128, chances are it's Windows.
>
> Linux default TTL is 64. If its below 64, chances are it's Linux.
>
> Anything above 128, chances are it is a network infrastructure. Cisco router is 255.
