# Network Scanning

## nmap

### Examples

```bash
nmap -T4 -p- <IP> -oA nmap/tcp_all_ports  # Scan all TCP ports
nmap -T4 -p- <IP> -oA --open -vv nmap/tcp_all_ports_verbose  # Same but be verbose and show open ports
nmap -sCV -p<Found Ports> <IP> -oA nmap/tcp_def_scripts  # Default scripts with found ports
nmap --script *vuln* -p<Found Ports> <IP> -oA nmap/tcp_vlun_scripts  # Vuln scripts with found ports
```

### Basic Scans

```bash
nmap <IP>  # Basic scan of the top 1000 ports
nmap -p- <IP>  # Basic scan of all 65535 ports
nmap -iL <FILENAME>  # Input from list of hosts/networks
nmap <IP> -v  # Increase verbosity level (use -vv or more for greater effect)
```

### Host Discovery

```bash
nmap -sn <IP>  # Ping Scan - disable port scan
nmap -Pn <IP>  # Treat all hosts as online -- skip host discovery
```

### Scan Techniques

```bash
nmap -sS <IP>  # SYN scan
nmap -sU <IP>  # UDP scan
```

### Port Specification

```bash
nmap -p22 <IP>  # Scan a specific port
nmap -p22,80,443 <IP>  # Scan multiple specific ports
nmap -p1-65535 <IP>  # Scan a range of ports
```

### Service/Version & OS Detection

```bash
nmap -sV <IP>  # Probe open ports to determine service/version info
nmap -O <IP>  # Enable OS detection
nmap -A <IP>  # Enable OS detection, version detection, script scanning, and traceroute
```

### Script Scan

```bash
nmap -sC <IP>  # equivalent to --script=default
nmap --script=<script name>  # Specify a script or scripts using regex
nmap --script <filename>|<category>|<directory>/|<expression>[,...]
nmap --script-updatedb  # Update the script database
```

### Scan Speeds

```bash
nmap -T1 <IP>  # Slowest
nmap -T2 <IP>
nmap -T3 <IP>  # Default speed when not specified
nmap -T4 <IP>
nmap -T5 <IP>  # Fastest
```

### Output

```bash
nmap <IP> -oN <filename>  # Output scan in normal format
nmap <IP> -oX <filename>  # Output scan in XML format
nmap <IP> -oG <filename>  # Output scan in Grepable format
nmap <IP> -oA <filename>  # Output scan in All 3 formats
```

## netdiscover

### About

Netdiscover is an active/passive address reconnaissance tool, mainly developed for those wireless networks without dhcp server, when you are wardriving. It can be also used on hub/switched networks.

Built on top of libnet and libpcap, it can passively detect online hosts, or search for them, by actively sending ARP requests.

Netdiscover can also be used to inspect your network ARP traffic, or find network addresses using auto scan mode, which will scan for common local networks.

Netdiscover uses the OUI table to show the vendor of the each MAC address discovered and is very useful for security checks or in pentests.

### Example

```bash
ip a s  # Get the first 3 octets of your network i.e. 192.168.57.
netdiscover -r 192.168.57.0/24  # Scan a given range instead of auto scan
```

## arp-scan

### About

arp-scan is a command-line tool that uses the ARP protocol to discover and fingerprint IP hosts on the local network. It is available for Linux and BSD under the GPL licence.

Scan the local network, using the information from the primary network interface.

### Example

```bash
arp-scan -l
```
