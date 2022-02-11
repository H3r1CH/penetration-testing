# Networking

## IP Addresses

Communicates over Layer 3 (Network)

### IPv4

32 bit address. 2^32 = 4,294,967,296 (Possible amount of IP addresses).

#### Example

```bash
# Example 1
kali@kali:~$ ifconfig
etho0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
       inet 192.168.1.1 netmask 255.255.255.0 broadcast 192.168.1.255
# Example 2
kali@kali:~$ ip a s 
```

### Classes

#### Private IP Addresses

| Network Class        | Network Numbers                | Network Mask  | No. of Networks | No. of Hosts per Network |
| -------------------- | ------------------------------ | ------------- | --------------- | ------------------------ |
| Class A              | 10.0.0.0                       | 255.0.0.0     | 126             | 16,646,144               |
| Class B              | 172.16.0.0 to 172.31.0.0       | 255.255.0.0   | 16,383          | 65,024                   |
| Class C              | 192.168.0.0 to 192.168.255.255 | 255.255.255.0 | 2,097,151       | 254                      |
| Loopback (localhost) | 127.0.0.0 to 127.0.0.7         | 255.255.255.0 | -               | -                        |

### NAT (Network Address Translation)

A technique for letting multiple computers share an IP address. NAT assigns unique addresses to each computer on the local network and adjusts incoming/outgoing network traffic to send data to the right place.

### IPv6

128 bit address. 2^128 = 3.4028236692093846346337460743177e+38 (Possible amount of IP addresses)

#### Example

```bash
# Example 1
kali@kali:~$ ifconfig
etho0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
       inet6 fe80::20c:29ff:fe0a:4205 prefixlen 64 scopeid 0x20<link>
# Example 2
kali@kali:~$ ip a s
```

## MAC Addresses

Communicates over Layer 2 (Data Link)

48 bit address. Two halves: the first 24 bits form the Organizationally Unique Identifier (OUI) and the last 24 bits form a serial number (formally called an extension identifier).

A media access control address (MAC address) is a unique identifier assigned to a network interface controller (NIC) for use as a network address in communications within a network segment.

#### Example

```bash
# Example 1
kali@kali:~$ ifconfig
etho0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
       ether 00:0c:29:0a:42:05 txqueuelen 1000 (Ethernet)
# Example 2
kali@kali:~$ ip a s
```

## TCP, UDP, and the Three-Way Handshake

### TCP

Transmission Control Protocol. Connection oriented.

#### Three-Way Handshake

1. \*\*SYN \*\*- Client sends a SYNchronize packet to the server.
2. \*\*SYN-ACK \*\*- Server sends back a SYNchronize-ACKnowledgement to the client.
3. \*\*ACK \*\*- Client sends an ACKnowledge to the server.

### UDP

User Datagram Protocol. Connectionless. No handshake takes place.

## Common Ports and Protocols

{% tabs %}
{% tab title="TCP" %}
* 21 (FTP)
* 22 (SSH)
* 23 (Telnet)
* 25 (SMTP)
* 53 (DNS)
* 80/443 (HTTP/S)
* 110 (POP3)
* 139+445 (SMB)
* 143 (IMAP)
{% endtab %}

{% tab title="UDP" %}
* 53 (DNS)
* 67, 68 (DHCP)
* 69 (TFTP)
* 161 (SNMP)
{% endtab %}
{% endtabs %}

## The OSI Model

| Layer | Layer Name         | Examples                | Description                                                                         |
| ----- | ------------------ | ----------------------- | ----------------------------------------------------------------------------------- |
| 7     | Application Layer  | HTTP, SMTP, DNS,        | Human computer interaction layers where application can access the network services |
| 6     | Presentation Layer | WMV, JPEG, MOV,         | Ensures that data is in a usable format and is where data encryption occurs         |
| 5     | Session Layer      | Session management,     | Maintains connections and is responsible for controlling ports and sessions         |
| 4     | Transport Layer    | TCP, UDP,               | Transmits data using transmission protocols including TCP and UDP                   |
| 3     | Network Layer      | IP addresses, routing,  | Decides which physical path the data will take                                      |
| 2     | Data Link Layer    | Switching, MAC address, | Defines the format of data on the network                                           |
| 1     | Physical Layer     | Data cables, cat6,      | Transmits raw bit stream over the physical medium                                   |

#### Mnemonic

* PDNTSPA
* Please Do Not Throw Sausage Pizza Away

#### Troubleshooting

* Typically start with layer 1 and work your way to layer 7

## Subnetting

TBD
