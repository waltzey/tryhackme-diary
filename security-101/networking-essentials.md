# TryHackMe - Networking Essentials

**Room:** Networking Essentials  
**Difficulty:** Easy  
**Focus:** DHCP, ARP, NAT, ICMP, ping, traceroute, routing protocols

---

## Overview

This room builds on fundamental networking concepts by exploring the protocols and technologies that enable modern networks to function automatically and efficiently. It covers how devices obtain network configurations, resolve addresses, route packets, and share limited IP address space.

**Learning Objectives:**
- Understand Dynamic Host Configuration Protocol (DHCP)
- Learn Address Resolution Protocol (ARP) for MAC address resolution
- Explore Network Address Translation (NAT) for IP address conservation
- Master Internet Control Message Protocol (ICMP) for network diagnostics
- Practice using ping and traceroute for troubleshooting
- Learn basic routing protocol concepts

---

## Task 2: DHCP - Give Me My Network Settings

DHCP automates network configuration, eliminating the need for manual IP address assignment and preventing address conflicts.

### Why DHCP?

**Manual configuration challenges:**
- Time-consuming for mobile devices
- Risk of IP address conflicts
- Error-prone process
- Not practical for large networks

**DHCP solves these by automatically providing:**
- IP address and subnet mask
- Default gateway (router)
- DNS server addresses

### DHCP Configuration

**Server:** Listens on UDP port 67  
**Client:** Sends from UDP port 68

### DHCP Process: DORA (4 Steps)

**1. DHCP Discover**
- Client broadcasts request for DHCP server
- Source IP: `0.0.0.0` (client has no IP yet)
- Destination IP: `255.255.255.255` (broadcast)
- Destination MAC: `ff:ff:ff:ff:ff:ff` (broadcast)

**2. DHCP Offer**
- Server responds with available IP address
- Includes network configuration parameters
- Uses client's MAC address for delivery

**3. DHCP Request**
- Client accepts the offered IP address
- Still broadcasts from `0.0.0.0` (hasn't applied IP yet)
- Confirms acceptance to server

**4. DHCP Acknowledge**
- Server confirms IP assignment
- Client can now use the assigned IP address

### Example DHCP Exchange

```bash
tshark -r DHCP-G5000.pcap -n
```

Output:
```
1   0.000000      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Discover
2   0.013904 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP Offer
3   4.115318      0.0.0.0 → 255.255.255.255 DHCP 342 DHCP Request
4   4.228117 192.168.66.1 → 192.168.66.133 DHCP 376 DHCP ACK
```

**Result:** Client receives IP `192.168.66.133` along with gateway and DNS configuration.

**Question:** How many steps does DHCP use to provide network configuration?  
**Answer:** `4`

**Question:** What is the destination IP address that a client uses when it sends a DHCP Discover packet?  
**Answer:** `255.255.255.255`

**Question:** What is the source IP address a client uses when trying to get IP network configuration over DHCP?  
**Answer:** `0.0.0.0`

---

## Task 3: ARP - Bridging Layer 3 Addressing to Layer 2 Addressing

ARP translates IP addresses (Layer 3) to MAC addresses (Layer 2), enabling communication on Ethernet and WiFi networks.

### The Problem

**Scenario:**
- Host A knows Host B's IP address (e.g., `192.168.66.1`)
- Host A needs Host B's MAC address to create Ethernet frame
- How does Host A discover Host B's MAC address?

### The Solution: ARP

**Address Resolution Protocol (ARP)** enables MAC address discovery on local networks.

### ARP Process

**1. ARP Request**
- Sender broadcasts request: "Who has IP X.X.X.X?"
- Destination MAC: `ff:ff:ff:ff:ff:ff` (broadcast)
- Contains sender's IP and MAC

**2. ARP Reply**
- Target host responds: "IP X.X.X.X is at MAC YY:YY:YY:YY:YY:YY"
- Sent directly to requester's MAC address
- Contains target's IP and MAC

### Example ARP Exchange

Using tshark:
```bash
tshark -r arp.pcapng -Nn
```

Output:
```
1 0.000000000 cc:5e:f8:02:21:a7 → ff:ff:ff:ff:ff:ff ARP 42 Who has 192.168.66.1? Tell 192.168.66.89
2 0.003566632 44:df:65:d8:fe:6c → cc:5e:f8:02:21:a7 ARP 42 192.168.66.1 is at 44:df:65:d8:fe:6c
```

Using tcpdump:
```bash
tcpdump -r arp.pcapng -n -v
```

Output:
```
17:23:44.506615 ARP Request who-has 192.168.66.1 tell 192.168.66.89
17:23:44.510182 ARP Reply 192.168.66.1 is-at 44:df:65:d8:fe:6c
```

### Key ARP Concepts

**Encapsulation:**
- ARP is NOT encapsulated in IP packets
- Directly encapsulated in Ethernet frames
- Considered Layer 2 (deals with MAC) or Layer 3 (supports IP)

**ARP Cache:**
- Systems maintain ARP cache (ARP table)
- Stores recent IP-to-MAC mappings
- Reduces need for repeated ARP requests

**Question:** What is the destination MAC address used in an ARP Request?  
**Answer:** `ff:ff:ff:ff:ff:ff`

**Question:** In the example above, what is the MAC address of 192.168.66.1?  
**Answer:** `44:df:65:d8:fe:6c`

---

## Task 4: ICMP - Troubleshooting Networks

ICMP provides network diagnostics and error reporting, powering essential troubleshooting tools like ping and traceroute.

### Ping - Testing Connectivity

Ping uses ICMP Echo Request (Type 8) and Echo Reply (Type 0) to test network connectivity and measure round-trip time.

**Basic ping command:**
```bash
ping 192.168.11.1 -c 4
```

**Output:**
```
PING 192.168.11.1 (192.168.11.1) 56(84) bytes of data.
64 bytes from 192.168.11.1: icmp_seq=1 ttl=63 time=11.2 ms
64 bytes from 192.168.11.1: icmp_seq=2 ttl=63 time=3.81 ms
64 bytes from 192.168.11.1: icmp_seq=3 ttl=63 time=3.99 ms
64 bytes from 192.168.11.1: icmp_seq=4 ttl=63 time=23.4 ms

--- 192.168.11.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 3.805/10.596/23.366/7.956 ms
```

**Ping statistics:**
- **Packets transmitted/received:** Shows connectivity
- **Packet loss:** Indicates network reliability
- **RTT (Round Trip Time):** Network latency metrics
  - **min:** Fastest response
  - **avg:** Average response time
  - **max:** Slowest response
  - **mdev:** Standard deviation (consistency)

**Why ping might fail:**
- Target system offline
- Firewall blocking ICMP
- Network path broken
- Incorrect IP address

### Traceroute - Discovering Network Path

Traceroute reveals every router between your system and the destination by exploiting the IP TTL (Time-to-Live) field.

**How it works:**
1. Send packet with TTL=1
2. First router decrements TTL to 0, drops packet, sends ICMP Time Exceeded
3. Send packet with TTL=2
4. Second router decrements TTL to 0, responds
5. Repeat incrementing TTL until destination reached

**Basic traceroute command:**
```bash
traceroute example.com
```

**Output:**
```
traceroute to example.com (93.184.215.14), 30 hops max, 60 byte packets
 1  _gateway (192.168.66.1)  4.414 ms  4.342 ms  4.320 ms
 2  192.168.11.1 (192.168.11.1)  5.849 ms  5.830 ms  5.811 ms
 3  100.104.0.1 (100.104.0.1)  11.130 ms  11.111 ms  11.093 ms
 4  10.149.1.45 (10.149.1.45)  6.156 ms  6.138 ms  6.120 ms
 5  * * *
 6  * * *
 7  * * *
 8  172.16.48.1 (172.16.48.1)  5.667 ms  8.165 ms  6.861 ms
 9  ae81.edge4.Marseille1.Level3.net (212.73.201.45)  50.811 ms
10  NTT-level3-Marseille1.Level3.net (4.68.68.150)  93.351 ms
[...]
16  93.184.215.14 (93.184.215.14)  140.574 ms  140.543 ms  140.514 ms
```

**Reading traceroute output:**
- Each line represents one router (hop)
- Three time measurements per hop (three probes sent)
- `* * *` means router didn't respond (ICMP blocked or packet dropped)
- Private IPs (10.x, 172.16-31.x, 192.168.x) are internal routers
- Public IPs reveal geographic location and ISP

**Question:** Using the example images above, how many bytes were sent in the echo (ping) request?  
**Answer:** `56`

**Question:** Which IP header field does the traceroute command require to become zero?  
**Answer:** `TTL`

---

## Task 5: Routing

Routing determines the path packets take through interconnected networks to reach their destination.

### Routing Protocols

**OSPF (Open Shortest Path First)**
- Open standard routing protocol
- Routers share link state information
- Each router builds complete network map
- Calculates shortest path using Dijkstra's algorithm
- Fast convergence when network topology changes

**EIGRP (Enhanced Interior Gateway Routing Protocol)**
- **Cisco proprietary** protocol
- Hybrid routing algorithm
- Considers bandwidth, delay, load, and reliability
- Efficient updates (only sends changes)
- Fast convergence

**BGP (Border Gateway Protocol)**
- Primary routing protocol for the Internet
- Routes between autonomous systems (AS)
- Policy-based routing decisions
- Handles routing between ISPs
- Extremely scalable

**RIP (Routing Information Protocol)**
- Simple distance-vector protocol
- Uses hop count as metric (max 15 hops)
- Suitable for small networks
- Slower convergence than modern protocols
- Easy to configure

### Routing Concepts

**Static Routing:**
- Manually configured routes
- Predictable and secure
- No routing protocol overhead
- Doesn't adapt to network changes

**Dynamic Routing:**
- Automatically discovers routes
- Adapts to topology changes
- Requires routing protocol
- More complex but flexible

**Question:** Which routing protocol discussed in this task is a Cisco proprietary protocol?  
**Answer:** `EIGRP`

---

## Task 6: NAT - Network Address Translation

NAT allows multiple devices with private IP addresses to share a single public IP address for Internet access.

### The IPv4 Address Shortage Problem

**IPv4 limitations:**
- Maximum ~4 billion addresses (2³²)
- Billions of devices need Internet access
- Without NAT, we'd run out of IPv4 addresses

### How NAT Works

NAT-enabled routers maintain a translation table mapping:
- Internal IP address + port number
- External IP address + port number

**Example translation:**

| Internal Address | Internal Port | External Address | External Port |
|------------------|---------------|------------------|---------------|
| 192.168.0.129 | 15401 | 212.3.4.5 | 19273 |
| 192.168.0.131 | 22845 | 212.3.4.5 | 19274 |
| 192.168.0.135 | 33012 | 212.3.4.5 | 19275 |

**From the laptop perspective:**
- Connection from `192.168.0.129:15401`

**From the web server perspective:**
- Connection from `212.3.4.5:19273`

### NAT Benefits

**Address conservation:**
- Many private IPs share one public IP
- Extends IPv4 address space significantly
- Cost savings (fewer public IPs needed)

**Security:**
- Internal network hidden from Internet
- Provides basic firewall functionality
- External hosts can't directly access internal devices

### NAT Limitations

**Connection tracking:**
- Router must maintain translation table
- Requires processing power and memory
- Limited by available port numbers

**Port exhaustion:**
- Each connection needs unique port mapping
- Maximum ~65,000 simultaneous connections per public IP
- Can be limiting for large networks

**Question:** In the network diagram above, what is the public IP that the phone will appear to use when accessing the Internet?  
**Answer:** `212.3.4.5`

**Question:** Assuming that the router has infinite processing power, approximately speaking, how many thousand simultaneous TCP connections can it maintain?  
**Answer:** `65` (approximately 65,000 connections using ports 1-65535)

---

## Task 7: Closing Notes

This room covered essential networking protocols that operate behind the scenes to make networks functional and efficient.

**Question:** Click on the View Site button to access the related site. Follow the instructions to obtain the flag.  
**Answer:** `[Access the site and follow instructions]`

---

## Key Takeaways

### DHCP Automation

- Eliminates manual IP configuration
- Prevents address conflicts
- Essential for mobile devices and large networks
- DORA process: Discover, Offer, Request, Acknowledge

### ARP Translation

- Bridges Layer 3 (IP) to Layer 2 (MAC) addressing
- Required for Ethernet/WiFi communication
- Uses broadcast for discovery
- Maintains cache to reduce network traffic

### ICMP Diagnostics

**Ping:**
- Tests connectivity and measures latency
- ICMP Echo Request/Reply mechanism
- Essential troubleshooting tool

**Traceroute:**
- Maps network path to destination
- Reveals intermediate routers
- Uses TTL manipulation cleverly
- Helps identify where connectivity fails

### NAT Conservation

- Enables IPv4 address space extension
- Multiple private IPs share single public IP
- Provides basic security through obscurity
- Essential for home and enterprise networks

### Routing Protocols

- Enable dynamic path discovery
- Adapt to network topology changes
- Different protocols for different network sizes
- BGP powers Internet-scale routing

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Troubleshooting No Internet Access**
```bash
# Check if you have an IP address
ip a s

# Test local gateway
ping 192.168.1.1

# Test DNS
ping 8.8.8.8

# Test external connectivity
ping google.com

# Find where connection fails
traceroute google.com
```

**Scenario 2: Network Performance Issues**
```bash
# Measure latency to server
ping -c 100 server.com

# Identify slow hop in path
traceroute server.com

# Check packet loss
ping -c 1000 server.com | grep loss
```

**Scenario 3: ARP Troubleshooting**
```bash
# View ARP cache
arp -a

# Clear ARP cache (Windows admin)
arp -d

# Clear ARP cache (Linux)
sudo ip -s -s neigh flush all
```

---

## Command Reference

| Command | Platform | Purpose |
|---------|----------|---------|
| `ping <host>` | All | Test connectivity |
| `ping -c <count> <host>` | Linux/Unix | Limit ping count |
| `ping -n <count> <host>` | Windows | Limit ping count |
| `traceroute <host>` | Linux/Unix | Trace network path |
| `tracert <host>` | Windows | Trace network path |
| `arp -a` | All | Display ARP cache |
| `ip neigh` | Linux | Display ARP cache (modern) |
| `tcpdump -i <interface> arp` | Linux | Capture ARP traffic |
| `tshark -r <file>` | All | Read packet capture |

---

## Next Steps

With networking essentials mastered:
- Study **Networking Core Protocols** for deeper protocol understanding
- Practice with **Wireshark** for packet analysis
- Learn **subnetting** calculations and VLSM
- Explore **IPv6** addressing and transition mechanisms
- Study **firewall** rules and ICMP filtering
- Practice network troubleshooting methodologies

These protocols form the invisible infrastructure that makes modern networking possible. Understanding them is essential for network administration, troubleshooting, and cybersecurity work.

---
<img width="827" height="493" alt="image" src="https://github.com/user-attachments/assets/98642bd9-be01-407c-9d14-7f17311294ca" />

**Room Completed:** ✓  
**Date:** 02/18/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
