# Wireless Hacking 101

## Intro to WPA

#### Key Terms

* SSID: The network "name" that you see when you try and connect
* ESSID: An SSID that _may_ apply to multiple access points, eg a company office, normally forming a bigger network. For Aircrack they normally refer to the network you're attacking.
* BSSID: An access point MAC (hardware) address
* WPA2-PSK: Wifi networks that you connect to by providing a password that's the same for everyone
* WPA2-EAP: Wifi networks that you authenticate to by providing a username and password, which is sent to a RADIUS server.
* RADIUS: A server for authenticating clients, not just for wifi.

The core of WPA(2) authentication is the 4 way handshake.

Most home WiFi networks, and many others, use WPA(2) personal. If you have to log in with a password and it's not WEP, then it's WPA(2) personal. WPA2-EAP uses RADIUS servers to authenticate, so if you have to enter a username and password in order to connect then it's probably that.

Previously, the WEP (Wired Equivalent Privacy) standard was used. This was shown to be insecure and can be broken by capturing enough packets to guess the key via statistical methods.

The 4 way handshake allows the client and the AP to both prove that they know they key, without telling each other. WPA and WPA2 use practically the same authentication method, so the attacks on both are the same.

The keys for WPA are derived from both the ESSID and the password for the network. The ESSID acts as a salt, making dictionary attacks more difficult. It means that for a given password, the key will still vary for each access point. This means that unless you precompute the dictionary for just that access point/MAC address, you will need to try passwords until you find the correct one.

## Capturing Packets to Attack

Using the Aircrack-ng suite, we can start attacking a wifi network. This will walk you through attacking a network yourself, assuming you have a monitor mode enabled NIC.

#### Aircrack-ng Suite

* aircrack-ng
* airdecap-ng
* airmon-ng
* aireplay-ng
* airodump-ng
* airtun-ng
* packetforge-ng
* airbase-ng
* airdecloak-ng
* airolib-ng
* airserv-ng
* buddy-ng
* ivstools
* easside-ng
* tkiptun-ng
* wesside-ng

We'll want to use aircrack-ng, airodump-ng and airmon-ng to attack WPA networks.

The aircrack tools come by default with Kali, or can be installed with a package manager or from [https://www.aircrack-ng.org/](https://www.aircrack-ng.org)

I suggest creating a hotspot on a phone/tablet, picking a weak password (From rockyou.txt) and following along with every stage. To generate 5 random passwords from rockyou, you can use this command on Kali: `head /usr/share/wordlists/rockyou.txt -n 10000 | shuf -n 5 -`

You will need a monitor mode NIC in order to capture the 4 way handshake. Many wireless cards support this, but it's important to note that not all of them do.

Injection mode helps, as you can use it to deauth a client in order to force a reconnect which forces the handshake to occur again. Otherwise, you have to wait for a client to connect normally.

#### Example Crack

* Useful Information
  * BSSID: 02:1A:11:FF:D9:BD
  * ESSID: 'James Honor 8'

`kali@kali:~/Desktop$ aircrack-ng -b 02:1A:11:FF:D9:BD -w /usr/share/wordlists/rockyou.txt NinjaJc01-01.cap`
