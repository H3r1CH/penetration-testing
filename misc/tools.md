# Tools

## Amass



### Examples

```bash
amass enum -d tesla.com
```

### Script

Combined with assetfinder script. Updated version with Httprobe.

```bash
#!/bin/bash

url=$1

if [ ! -d "$url" ]; then
    mkdir $url
fi
if [ ! -d "$url/recon" ];then
    mkdir $url/recon

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt
cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt

echo "[+] Harvesting subdomains with Amass..."
amass enum -d $url >> $url/recon/f.txt
sort -u $url/recond/f.txt >> $url/recon/final.txt
rm $url/recond/f.xt
```

## assetfinder

### Examples

```bash
assetfinder tesla.com
assetfinder --subs-only tesla.com
```

### Script

Updated version with Amass.

```bash
#!/bin/bash

url=$1

if [ ! -d "$url" ]; then
    mkdir $url
fi
if [ ! -d "$url/recon" ];then
    mkdir $url/recon

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt
cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt
```

## ffuf

### Wordlists

```bash
# Words
/usr/share/seclists/Discovery/Web-Content/big.txt
/usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt
# Files with Extensions
/usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt
# Extensions
/usr/share/seclists/Discovery/Web-Content/web-extensions.txt
# Directories
/usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
# Subdomain
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Basic Usages

```bash
# Help Page
ffuf -h
# Using FUZZ Keyword
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt
# Using custom Keyword
ffuf -u http://10.10.140.239/NORAJ -w /usr/share/seclists/Discovery/Web-Content/big.txt:NORAJ
```

### Finding Pages and Directories

```bash
# Checking the index page for technology
ffuf -u http://10.10.140.239/indexFUZZ -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt
# After identifying technology + basic extensions
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e php,txt
# Checking lowercase pages
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```

### Using Filters

```bash
# Filter out HTTP Status Code: -fc 403
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403
# Match HTTP Status Code: -mc 200
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 200
# Filter out file size: -fs 0
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fs 0
# Regex mathing all files that start with a .
ffuf -u http://10.10.140.239/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fr '/\..*'
```

### Fuzzing Parameters

```bash
# Parameter fuzz for an API pareamter
ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?FUZZ=1' -c -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fw 39
ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?FUZZ=1' -c -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fw 39
# Scripts to brute force the parameter value of a number
ruby -e '(0..255).each{|i| puts i}' | ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
ruby -e 'puts (0..255).to_a' | ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
for i in {0..255}; do echo $i; done | ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
seq 0 255 | ffuf -u 'http://10.10.140.186/sqli-labs/Less-1/?id=FUZZ' -c -w - -fw 33
# wordlist-based brute-force attacks
ffuf -u http://10.10.140.186/sqli-labs/Less-11/ -c -w /usr/share/seclists/Passwords/Leaked-Databases/hak5.txt -X POST -d 'uname=Dummy&passwd=FUZZ&submit=Submit' -fs 1435 -H 'Content-Type: application/x-www-form-urlencoded' 
```

### Finding vhosts and Subdomains

```bash
# Resolving Public subdomain
ffuf -u http://FUZZ.mydomain.com -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
# Resolving Private subdomain
ffuf -u http://mydomain.com -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.mydomain.com' -fs 0
```

### Proxifying ffuf Traffic

```bash
# Send traffic through a web proxy
ffuf -u http://10.10.140.186/ -c -w /usr/share/seclists/Discovery/Web-Content/common.txt -x http://127.0.0.1:8080
# Send only mathces to your proxy
ffuf -u http://10.10.140.186/ -c -w /usr/share/seclists/Discovery/Web-Content/common.txt -replay-proxy http://127.0.0.1:8080
```

## gobuster

## gowitness

### Examples

```
gowitness single https://tesla.com
```

## httprobe

### Examples

```bash
cat tesla.com/recon/final.txt | httprobe -s -p https:443 | sed 's/https\?:\/\///' | tr -d ':443'
```

### Script

Combined with assetfinder and Amass script.

```bash
#!/bin/bash

url=$1

if [ ! -d "$url" ]; then
    mkdir $url
fi
if [ ! -d "$url/recon" ];then
    mkdir $url/recon

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt
cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt

echo "[+] Harvesting subdomains with Amass..."
amass enum -d $url >> $url/recon/f.txt
sort -u $url/recond/f.txt >> $url/recon/final.txt
rm $url/recond/f.xt

echo "[+] Probing for alive domains..."
cat $url/recon/final.txt | sort -u | httprobe -s -p https:443 | sed 's/https\?:\/\///' | tr -d ':443' >> alive.txt
```

## hydra

### FTP

```bash
hydra -l user -P passlist.txt ftp://192.168.0.1
```

### SSH

```bash
# Using a specific username and specific password
hydra -l <username> -p <password> <ip> -t 4 ssh
# Using a username list and password list
hydra -L <full path to users list file> -P <full path to pass list file> <ip> -t 4 ssh
# Using a specific username and a password list
hydra -l jan -P /usr/share/wordlists/rockyou.txt 10.10.81.254 -t 4 ssh
```

### MySQL

```bash
hydra -L usernames.txt -P xxxxx.txt <ip> mysql
hydra -L <USERS_LIST> -P <PASSWORDS_LIST> <IP> mysql -vV -I -u
```

### HTTP

```bash
hydra -l <username> -P <password list> <ip> http-post-form "/<login url>/:username=^USER^&password=^PASS^:F=incorrect" -V
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.177.14 http-post-form "/login/:username=^USER^&password=^PASS^&Login=Login:Your username or password is incorrect."
```

```html
# Check input types and names
<input type="text" name="log" id="user_login" class="input" value="" size="20" /></label>
<input type="password" name="pwd" id="user_pass" class="input" value="" size="20" /></label>
<input type="submit" name="wp-submit" id="wp-submit" class="button button-primary button-large" value="Log In" />
<input type="hidden" name="testcookie" value="1" />
```

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 10.10.248.119 http-post-form "/wp-login.php/:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Locationtestcookie=1:S=Location"
```

```bash
# Use hydra for logging into web browser form
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.14.194 http-post-form "/Account/login.aspx?ReturnURL=/admin:__VIEWSTATE=VvRo%2F6BZHb2Awbdr5eTk9Nlfu1WCSJuXgkpjYDfz63IMQ385Fd2L%2B82aF5Xb82NRNIvOve45O2b7nBn%2FL1Ga0QPlfFBJbbXt3yQsgJ78NksKmm4sjAfbr3Ef38YZ%2B5bEsNkLwbBXS1WJxMHBkz0L%2FWNql9b8eY%2FBSLtcycQkzdVeMVlh&__EVENTVALIDATION=en7XtOPeeBOeVZk5EjflY%2BAF7C4gP00Y2M04gWkErBnss1kSFkrj0m3AdFupCkAxs3Kg4rqcp1zVWcmnSAaDkVRmNu348TWPiL62AvF5pW9olyAnkpmZq%2F8Pqt6PYY%2FX16MX6m01JrswnT3%2FAb8y2GyeZif3VzuaULbMRJ%2FEJ%2FP5xrkz&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login Failed"
```

## masscan

### Examples

```bash
masscan -e tun0 -p1-65535 <IP>
```

## netcat

### Chat Line

#### From Machine 1

```bash
nc -lp 4545
```

#### From Machine 2

```bash
nc <M1 IP> 4545
```

### Copy File

Example copying a file from Machine 1 to Machine 2.

#### From Machine 2

```bash
nc -lp 4545 > incoming.txt
```

#### From Machine 1

```bash
nc <M2 IP> 4545 < myfile.txt
```

### Client for Service

#### Web Server

```bash
nc -v google.com 80
GET index.html HTTP/1.1  # Then press enter twice
```

#### FTP Server

```bash
nc -v <IP> 21
user anonymous
pass <password>
help
```

## tcpdump

### Promiscuous Mode

Make sure promiscuous mode is set.

VM Settings --> Network --> Advanced --> Promiscuous mode.

### Examples

```bash
tcpdump -D  # List the interfaces
tcpdump -i eth0  # Specify the interface to listen on e.g. eth0
tcpdump -i any  # Listen on all interfaces
tcpdump -i eth0 icmp  # Listen for ICMP packets on a specific interface
tcpdump -c 10 -i any  # Provide count of packets to capture
tcpdump -c 10 -i any -n  # Surpress hostname resolution
tcpdump -c 10 -i any -nn  # Surpress hostname and port name resolution
tcpdump -i eth0 -c 10 -s 0 -X  # Display the whole packet
tcpdump -c 10 host <IP>  # Look at traffic between us and another server
tcpdump -c 10 udp  # Look at only the UDP packets on the network
tcpdump net <IP>/24  # Look at traffic to and from a subnet
tcpdump -c 10 port 22  # Look at traffic just to one port
tcpdump -c 10 dst portrange 1-1023  # Look at traffic to a range of desintation ports
tcpdump -c 10 ip6 -w ip6.cap  # Write out as a pcap file
```

