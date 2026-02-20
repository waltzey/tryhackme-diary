# TryHackMe - Tcpdump Basics

**Room:** Tcpdump Basics  
**Difficulty:** Easy  
**Focus:** Command-line packet capture, filtering expressions, protocol analysis

---

## Overview

Tcpdump is a powerful command-line packet analyzer for Unix-like systems. Written in C/C++ and built on the libpcap library, it offers optimal speed and stability for capturing and analyzing network traffic. This room covers essential tcpdump usage including packet capture, filtering, and display options.

**Learning Objectives:**
- Capture network packets and save to files
- Apply filtering expressions to isolate specific traffic
- Control packet display formats
- Analyze protocol behavior at the command line

---

## Task 1: Introduction

### Why Study Network Traffic?

Network protocols operate behind user-friendly interfaces, hiding technical complexity. Studying actual packet captures helps understand:
- How ARP queries work on local networks
- TCP three-way handshakes in action
- Protocol "conversations" in real-time

### Tcpdump History

- **Developed:** Late 1980s / early 1990s
- **Language:** C and C++
- **Platform:** Unix-like systems
- **Library:** libpcap (foundation for many network tools)
- **Windows port:** winpcap

**Question:** What is the name of the library that is associated with tcpdump?  
**Answer:** `libpcap`

---

## Task 2: Basic Packet Capture

### Core Command Structure

```bash
tcpdump [options] [filter expression]
```

### Essential Options

**Specify Network Interface: `-i INTERFACE`**

List available interfaces:
```bash
ip address show
# or shortened
ip a s
```

Example output:
```
1: lo: <LOOPBACK,UP,LOWER_UP>
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP>
```

Capture options:
- `-i eth0` - Listen on specific interface
- `-i wlo1` - Listen on WiFi interface
- `-i any` - Listen on all interfaces

**Save Captured Packets: `-w FILE`**

```bash
tcpdump -i eth0 -w capture.pcap
```

- Saves packets to file for later analysis
- Common extension: `.pcap`
- Can be opened in Wireshark
- Screen won't show packets when using `-w`

**Read from File: `-r FILE`**

```bash
tcpdump -r capture.pcap
```

- Analyzes previously captured packets
- Doesn't require root privileges
- Useful for studying protocol behavior
- Can apply filters to saved captures

**Limit Packet Count: `-c COUNT`**

```bash
tcpdump -i eth0 -c 10
```

- Captures specified number of packets then stops
- Without `-c`, capture continues until Ctrl+C
- Useful for quick samples

**Disable DNS/Port Resolution: `-n` and `-nn`**

```bash
tcpdump -i ens5 -c 5 -n
```

- `-n` - Don't resolve IP addresses to hostnames
- `-nn` - Don't resolve IPs OR port numbers
- Faster output, avoids DNS delays
- Shows raw numerical values

Example output with `-n`:
```
08:55:18.989213 IP 10.10.117.2.22 > 10.11.81.126.53378: Flags [P.], seq 2888580014:2888580210
```

**Verbose Output: `-v`, `-vv`, `-vvv`**

- `-v` - Adds TTL, identification, total length, IP options
- `-vv` - More verbose output
- `-vvv` - Maximum verbosity
- Shows additional protocol details

### Command Summary

| Option | Purpose | Example |
|--------|---------|---------|
| `-i INTERFACE` | Specify interface | `-i eth0` |
| `-w FILE` | Write to file | `-w data.pcap` |
| `-r FILE` | Read from file | `-r data.pcap` |
| `-c COUNT` | Limit packet count | `-c 100` |
| `-n` | No DNS resolution | `-n` |
| `-nn` | No DNS/port resolution | `-nn` |
| `-v` | Verbose output | `-v`, `-vv`, `-vvv` |

### Practical Examples

**Example 1: Capture 50 packets on wired Ethernet**
```bash
tcpdump -i eth0 -c 50 -v
```

**Example 2: Capture WiFi traffic to file**
```bash
tcpdump -i wlo1 -w data.pcap
```
(Stop with Ctrl+C when done)

**Example 3: Capture all interfaces without resolution**
```bash
tcpdump -i any -nn
```

**Question:** What option can you add to your command to display addresses only in numeric format?  
**Answer:** `-n`

---

## Task 3: Filtering Expressions

### Why Filter?

Without filters, tcpdump captures ALL network traffic - thousands of packets per second. Filtering lets you focus on specific conversations or protocols.

### Filter by Host

**Syntax:**
```bash
host IP_ADDRESS
host HOSTNAME
```

**Example: Capture traffic with specific host**
```bash
sudo tcpdump host example.com -w http.pcap
```

**Directional filtering:**
- `src host IP` - Packets FROM this source
- `dst host IP` - Packets TO this destination

Examples:
```bash
tcpdump src host 192.168.1.100
tcpdump dst host 10.0.0.5
```

### Filter by Port

**Syntax:**
```bash
port PORT_NUMBER
```

**Example: Capture all DNS traffic (port 53)**
```bash
sudo tcpdump -i ens5 port 53 -n
```

Output shows DNS queries:
```
17:26:33.591670 IP 192.168.139.132.47902 > 192.168.139.2.53: 47108+ A? example.org. (29)
17:26:33.591717 IP 192.168.139.132.47902 > 192.168.139.2.53: 5+ AAAA? example.org. (29)
```

**Directional port filtering:**
- `src port PORT` - From this source port
- `dst port PORT` - To this destination port

Examples:
```bash
tcpdump src port 80
tcpdump dst port 443
```

### Filter by Protocol

**Syntax:**
```bash
PROTOCOL
```

**Available protocols:**
- `ip` - IPv4
- `ip6` - IPv6
- `tcp` - TCP
- `udp` - UDP
- `icmp` - ICMP
- `arp` - ARP

**Example: Capture only ICMP traffic**
```bash
sudo tcpdump -i ens5 icmp -n
```

Output shows ping and traceroute:
```
18:11:00.624681 IP 192.168.139.132 > 93.184.215.14: ICMP echo request
18:11:00.781482 IP 93.184.215.14 > 192.168.139.132: ICMP echo reply
18:11:04.168792 IP 192.168.139.2 > 192.168.139.132: ICMP time exceeded
```

### Logical Operators

**Combine filters with logical operators:**

**AND operator:**
```bash
tcpdump host 1.1.1.1 and tcp
```
Both conditions must be true

**OR operator:**
```bash
tcpdump udp or icmp
```
Either condition can be true

**NOT operator:**
```bash
tcpdump not tcp
```
Captures everything except TCP

### Filter Summary

| Filter Type | Syntax | Example |
|-------------|--------|---------|
| Host | `host IP/NAME` | `host 192.168.1.1` |
| Source host | `src host IP` | `src host 10.0.0.1` |
| Dest host | `dst host IP` | `dst host 8.8.8.8` |
| Port | `port NUMBER` | `port 80` |
| Source port | `src port N` | `src port 443` |
| Dest port | `dst port N` | `dst port 22` |
| Protocol | `PROTO` | `tcp`, `udp`, `icmp` |
| AND | `EXPR and EXPR` | `tcp and port 80` |
| OR | `EXPR or EXPR` | `port 80 or port 443` |
| NOT | `not EXPR` | `not arp` |

### Complex Filter Examples

**Example 1: Capture SSH traffic**
```bash
tcpdump -i any tcp port 22
```

**Example 2: Capture NTP traffic on WiFi**
```bash
tcpdump -i wlo1 udp port 123
```

**Example 3: Capture HTTPS traffic with specific host**
```bash
tcpdump -i eth0 host example.com and tcp port 443 -w https.pcap
```

### Counting Packets

**Pipe output to wc (word count)**
```bash
tcpdump -r traffic.pcap src host 192.168.124.1 -n | wc
```

Output format: `lines words characters`
```
910   17415  140616
```
910 packets matched the filter

**Question:** How many packets in traffic.pcap use the ICMP protocol?  
**Answer:** `[Run: tcpdump -r traffic.pcap icmp -n | wc -l]`

**Question:** What is the IP address of the host that asked for the MAC address of 192.168.124.137?  
**Answer:** `[Run: tcpdump -r traffic.pcap arp -n and look for ARP request]`

**Question:** What hostname (subdomain) appears in the first DNS query?  
**Answer:** `[Run: tcpdump -r traffic.pcap port 53 -c 1]`

---

## Task 4: Advanced Filtering

### Packet Size Filtering

**Filter by length:**
- `greater LENGTH` - Packets >= specified length
- `less LENGTH` - Packets <= specified length

Examples:
```bash
tcpdump greater 1500
tcpdump less 64
```

### Binary Operations

Understanding binary operations is key to advanced filtering:

**AND (`&`) operator:**
| Input A | Input B | Output |
|---------|---------|--------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**OR (`|`) operator:**
| Input A | Input B | Output |
|---------|---------|--------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

**NOT (`!`) operator:**
| Input | Output |
|-------|--------|
| 0 | 1 |
| 1 | 0 |

### Header Byte Access

**Syntax:**
```
proto[expr:size]
```

- `proto` - Protocol (arp, ether, icmp, ip, tcp, udp)
- `expr` - Byte offset (0 = first byte)
- `size` - Bytes to read (1, 2, or 4; default 1)

**Examples from pcap-filter:**

**Multicast Ethernet addresses:**
```bash
ether[0] & 1 != 0
```

**IP packets with options:**
```bash
ip[0] & 0xf != 5
```

### TCP Flags Filtering

**TCP flag constants:**
- `tcp-syn` - SYN flag
- `tcp-ack` - ACK flag
- `tcp-fin` - FIN flag
- `tcp-rst` - RST flag
- `tcp-push` - PUSH flag

**Filter examples:**

**Only SYN packets (all other flags unset):**
```bash
tcpdump "tcp[tcpflags] == tcp-syn"
```

**At least SYN flag set:**
```bash
tcpdump "tcp[tcpflags] & tcp-syn != 0"
```

**At least SYN or ACK:**
```bash
tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"
```

**Only RST flag set:**
```bash
tcpdump "tcp[tcpflags] == tcp-rst"
```

### Practical Use Cases

**Detect SYN scans:**
```bash
tcpdump "tcp[tcpflags] == tcp-syn"
```

**Find connection resets:**
```bash
tcpdump "tcp[tcpflags] & tcp-rst != 0"
```

**Identify large packets (potential exfiltration):**
```bash
tcpdump greater 15000
```

**Question:** How many packets have only the TCP Reset (RST) flag set?  
**Answer:** `[Run: tcpdump -r traffic.pcap "tcp[tcpflags] == tcp-rst" | wc -l]`

**Question:** What is the IP address of the host that sent packets larger than 15000 bytes?  
**Answer:** `[Run: tcpdump -r traffic.pcap greater 15000 -n]`

---

## Task 5: Displaying Packets

### Display Format Options

**Five essential display options:**
- `-q` - Quick/brief output
- `-e` - Include link-level (MAC) headers
- `-A` - Show packet data as ASCII
- `-xx` - Show packet data in hexadecimal
- `-X` - Show packets in both hex and ASCII

### Default Output

**Standard tcpdump output:**
```bash
tcpdump -r TwoPackets.pcap
```

Output:
```
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: Flags [P.], seq 2695955324:2695955349, ack 2856007037, win 16, length 25
18:59:59.980574 IP g5000.45248 > 104.18.12.149.https: Flags [P.], seq 1:30, ack 25, win 2175, length 29
```

### Quick Output: `-q`

**Shorter, cleaner output:**
```bash
tcpdump -r TwoPackets.pcap -q
```

Output:
```
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: tcp 25
18:59:59.980574 IP g5000.45248 > 104.18.12.149.https: tcp 29
```

**Shows:**
- Timestamp
- Source/destination IPs and ports
- Protocol and length
- Minimal details

### Link-Level Headers: `-e`

**Include MAC addresses:**
```bash
tcpdump -r TwoPackets.pcap -e
```

Output:
```
18:59:59.979771 44:df:65:d8:fe:6c > 02:83:1e:40:5d:17, ethertype IPv4 (0x0800), length 91: 104.18.12.149.https > g5000.45248: Flags [P.], length 25
```

**Useful for:**
- Learning ARP and DHCP
- Tracking packet sources on LAN
- Identifying rogue devices
- Network troubleshooting

### ASCII Display: `-A`

**Show packet data as text:**
```bash
tcpdump -r TwoPackets.pcap -A
```

Output:
```
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: Flags [P.], length 25
E..M..@.5..)h.....BY.......|.;5}..........
..1...k......j.3.2.....&9a.....-L
```

**Best for:**
- Plaintext protocols (HTTP, FTP, SMTP)
- Reading unencrypted data
- Quick content inspection

**Not useful for:**
- Encrypted data (HTTPS, SSH)
- Compressed data
- Binary protocols
- Non-English text

### Hexadecimal Display: `-xx`

**Show all packet bytes in hex:**
```bash
tcpdump -r TwoPackets.pcap -xx
```

Output:
```
0x0000:  0283 1e40 5d17 44df 65d8 fe6c 0800 4500
0x0010:  004d fbd8 4000 3506 d229 6812 0c95 c0a8
0x0020:  4259 01bb b0c0 a0b1 037c aa3b 357d 8018
0x0030:  0010 f905 0000 0101 080a 189a 310d ebfa
0x0040:  6b2e 1703 0300 146a 8f33 1832 e6a2 fb99
0x0050:  eb26 3961 dad4 1611 152d 4c
```

**Useful for:**
- Encrypted data analysis
- Binary protocol inspection
- Header analysis
- Protocol implementation verification

### Combined Hex and ASCII: `-X`

**Best of both worlds:**
```bash
tcpdump -r TwoPackets.pcap -X
```

Output:
```
0x0000:  4500 004d fbd8 4000 3506 d229 6812 0c95  E..M..@.5..)h...
0x0010:  c0a8 4259 01bb b0c0 a0b1 037c aa3b 357d  ..BY.......|.;5}
0x0020:  8018 0010 f905 0000 0101 080a 189a 310d  ..............1.
0x0030:  ebfa 6b2e 1703 0300 146a 8f33 1832 e6a2  ..k......j.3.2..
0x0040:  fb99 eb26 3961 dad4 1611 152d 4c         ...&9a.....-L
```

**Features:**
- Left side: Hexadecimal bytes
- Right side: ASCII interpretation
- Complete packet visibility
- Best for detailed analysis

### Display Options Summary

| Option | Purpose | Best For |
|--------|---------|----------|
| `-q` | Brief output | Quick overview |
| `-e` | Show MAC addresses | Layer 2 analysis |
| `-A` | ASCII text | Plaintext protocols |
| `-xx` | Hex only | Binary/encrypted data |
| `-X` | Hex + ASCII | Complete analysis |

**Question:** What is the MAC address of the host that sent an ARP request?  
**Answer:** `[Run: tcpdump -r traffic.pcap arp -e and find ARP request]`

---

## Task 6: Conclusion

Tcpdump provides powerful command-line packet analysis capabilities essential for understanding network protocols.

**Skills acquired:**
- Capturing packets with various options
- Filtering by host, port, and protocol
- Advanced filtering with TCP flags and byte offsets
- Displaying packets in multiple formats
- Reading and analyzing saved captures

---

## Key Takeaways

### Essential Workflow

**1. Basic Capture**
```bash
sudo tcpdump -i eth0 -w capture.pcap
```

**2. Apply Filters**
```bash
sudo tcpdump -i eth0 host 192.168.1.100 and port 80
```

**3. Analyze Saved Capture**
```bash
tcpdump -r capture.pcap -nn -X
```

### Common Use Cases

**Troubleshooting DNS:**
```bash
sudo tcpdump -i any port 53 -nn
```

**Detecting Port Scans:**
```bash
sudo tcpdump "tcp[tcpflags] == tcp-syn" -n
```

**Monitoring HTTP Traffic:**
```bash
sudo tcpdump -i eth0 tcp port 80 -A
```

**Finding ARP Activity:**
```bash
sudo tcpdump -i eth0 arp -e
```

**Analyzing Encrypted Traffic:**
```bash
tcpdump -r capture.pcap port 443 -X
```

### Tcpdump vs Wireshark

**Tcpdump Advantages:**
- Lightweight and fast
- Works on remote servers (no GUI needed)
- Lower resource usage
- Scriptable and automatable
- Available on most Unix systems

**Wireshark Advantages:**
- Graphical interface
- Better for deep analysis
- Visual protocol dissection
- Easier for beginners
- Advanced filtering GUI

### Best Practices

**Capturing:**
1. Use `-nn` to avoid DNS delays
2. Apply filters early to reduce data
3. Save to files for later analysis
4. Limit captures with `-c` for testing

**Filtering:**
1. Start broad, then narrow down
2. Combine multiple conditions with `and`/`or`
3. Use hex filtering for advanced needs
4. Test filters on small captures first

**Analysis:**
1. Use `-X` for complete packet view
2. Use `-e` for layer 2 investigation
3. Count packets with `| wc -l`
4. Combine with grep for text search

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Diagnose Slow Network**
```bash
# Capture all traffic for 60 seconds
sudo tcpdump -i eth0 -w slow-network.pcap &
sleep 60
killall tcpdump

# Analyze for retransmissions
tcpdump -r slow-network.pcap "tcp[tcpflags] & tcp-syn != 0" | wc -l
```

**Scenario 2: Detect Malware C2**
```bash
# Capture suspicious outbound connections
sudo tcpdump -i eth0 "tcp[tcpflags] == tcp-syn and not dst net 192.168.0.0/16" -nn
```

**Scenario 3: Monitor DHCP Requests**
```bash
# Capture all DHCP traffic with MAC addresses
sudo tcpdump -i eth0 port 67 or port 68 -e -v
```

**Scenario 4: Investigate Data Exfiltration**
```bash
# Find large outbound packets
tcpdump -r suspicious.pcap "greater 10000 and src net 192.168.0.0/16" -nn
```

---

## Command Reference

### Essential Commands

```bash
# Basic capture
sudo tcpdump -i eth0

# Capture to file
sudo tcpdump -i eth0 -w capture.pcap

# Read from file
tcpdump -r capture.pcap

# Limit packets
tcpdump -i eth0 -c 100

# No resolution
tcpdump -i eth0 -nn

# Verbose
tcpdump -i eth0 -v

# Filter by host
tcpdump host 192.168.1.1

# Filter by port
tcpdump port 80

# Filter by protocol
tcpdump tcp

# Combine filters
tcpdump host 192.168.1.1 and port 80

# Show hex and ASCII
tcpdump -X

# Show MAC addresses
tcpdump -e
```

---

## Next Steps

With tcpdump basics mastered:
- Practice on **real network traffic**
- Learn **Wireshark** for GUI analysis
- Study **protocol specifications** (RFCs)
- Explore **advanced pcap-filter expressions**
- Automate analysis with **shell scripts**
- Learn **tshark** for advanced CLI filtering

Tcpdump is an essential tool for network administrators, security analysts, and anyone who needs to understand network behavior at a deep level.

---

**Room Completed:** ✓  
**Date:** 02/14/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
