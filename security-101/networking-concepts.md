# TryHackMe - Networking Concepts

**Room:** Networking Concepts  
**Difficulty:** Easy  
**Focus:** OSI Model, TCP/IP Model, IP addressing and subnets, TCP/UDP protocols, encapsulation, practical telnet usage

---

## Overview

This room introduces fundamental networking concepts that form the foundation for understanding how computers communicate. It covers two essential networking models (OSI and TCP/IP), explains IP addressing and routing, explores transport layer protocols, and demonstrates practical network communication using telnet.

**Learning Objectives:**
- Understand the ISO OSI network model layers
- Learn IP addresses, subnets, and routing concepts
- Differentiate between TCP and UDP protocols
- Understand port numbers and their purpose
- Practice connecting to TCP ports using command-line tools

---

## Task 2: OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that describes how network communications should occur. It consists of seven layers, numbered from bottom to top.

### The Seven Layers

**Mnemonic to remember:** "Please Do Not Throw Spinach Pizza Away"

**Layer 1: Physical Layer**
- Deals with physical connections (cables, antennas, radio frequencies)
- Defines how binary digits (0 and 1) are transmitted
- Examples: Ethernet cables, WiFi bands (2.4 GHz, 5 GHz, 6 GHz), optical fiber

**Layer 2: Data Link Layer**
- Enables data transfer between nodes on the same network segment
- Uses MAC (Media Access Control) addresses
- MAC addresses are 6 bytes, expressed in hexadecimal (e.g., `cc:5e:f8:02:21:a7`)
- Examples: Ethernet (802.3), WiFi (802.11)

**Layer 3: Network Layer**
- Handles logical addressing and routing between different networks
- Routes packets through multiple networks to reach destination
- Examples: IP (Internet Protocol), ICMP, VPN protocols (IPSec, SSL/TLS)

**Layer 4: Transport Layer**
- Enables end-to-end communication between applications on different hosts
- Provides flow control, segmentation, and error correction
- Examples: TCP (Transmission Control Protocol), UDP (User Datagram Protocol)

**Layer 5: Session Layer**
- Establishes, maintains, and synchronizes communication sessions
- Manages data synchronization and recovery from transmission failures
- Examples: NFS (Network File System), RPC (Remote Procedure Call)

**Layer 6: Presentation Layer**
- Ensures data is in a format the application can understand
- Handles encoding, compression, and encryption
- Examples: ASCII/Unicode encoding, JPEG/GIF/PNG, MIME

**Layer 7: Application Layer**
- Provides network services directly to end-user applications
- Examples: HTTP, FTP, DNS, POP3, SMTP, IMAP

### OSI Model Summary Table

| Layer | Number | Name | Function | Examples |
|-------|--------|------|----------|----------|
| Application | 7 | Application | Network services for applications | HTTP, FTP, DNS |
| Presentation | 6 | Presentation | Data encoding, compression, encryption | JPEG, MIME, ASCII |
| Session | 5 | Session | Session management and synchronization | NFS, RPC |
| Transport | 4 | Transport | End-to-end communication | TCP, UDP |
| Network | 3 | Network | Routing and logical addressing | IP, ICMP |
| Data Link | 2 | Data Link | Node-to-node data transfer | Ethernet, WiFi |
| Physical | 1 | Physical | Physical medium and signals | Cables, radio waves |

**Question:** Which layer is responsible for end-to-end communication between running applications?  
**Answer:** `Transport` or `4`

**Question:** Which layer is responsible for routing packets to the proper network?  
**Answer:** `Network` or `3`

**Question:** In the OSI model, which layer is responsible for encoding the application data?  
**Answer:** `Presentation` or `6`

**Question:** Which layer is responsible for transferring data between hosts on the same network segment?  
**Answer:** `Data Link` or `2`

---

## Task 3: TCP/IP Model

The TCP/IP model is a practical, implemented model developed by the Department of Defense in the 1970s. It was designed to maintain network functionality even when parts of the network are compromised.

### TCP/IP Model Layers (4 Layers)

**From top to bottom:**

1. **Application Layer**
   - Combines OSI layers 5, 6, and 7
   - Handles application-level protocols

2. **Transport Layer**
   - Same as OSI layer 4
   - Manages end-to-end communication

3. **Internet Layer**
   - Equivalent to OSI layer 3 (Network)
   - Handles routing and addressing

4. **Link Layer**
   - Same as OSI layer 2
   - Manages physical network access

### TCP/IP vs OSI Mapping

| TCP/IP Layer | OSI Layers |
|--------------|------------|
| Application | 7, 6, 5 (Application, Presentation, Session) |
| Transport | 4 (Transport) |
| Internet | 3 (Network) |
| Link | 2 (Data Link) |
| (Physical) | 1 (Physical) |

**Note:** Some modern textbooks include the physical layer, making it a 5-layer model.

**Question:** To which layer does HTTP belong in the TCP/IP model?  
**Answer:** `Application`

**Question:** How many layers of the OSI model does the application layer in the TCP/IP model cover?  
**Answer:** `3`

---

## Task 4: IP Addresses and Subnets

### IPv4 Addressing

An IPv4 address consists of **4 octets** (32 bits total), with each octet representing a number from 0 to 255.

**Example:** `192.168.1.100`
- First octet: 192
- Second octet: 168
- Third octet: 1
- Fourth octet: 100

**Special addresses in a subnet:**
- **Network address:** First address (e.g., `192.168.1.0`)
- **Broadcast address:** Last address (e.g., `192.168.1.255`)

**Total IPv4 addresses:** Approximately 4 billion (2³² addresses)

### Subnet Masks

A subnet mask defines which portion of an IP address represents the network.

**Common notation:**
- `255.255.255.0` = `/24` (CIDR notation)
- `/24` means the leftmost 24 bits are the network portion

**Example subnet:** `192.168.66.89/24`
- Network: `192.168.66.0`
- Usable range: `192.168.66.1` to `192.168.66.254`
- Broadcast: `192.168.66.255`

### Checking Your Network Configuration

**Windows:**
```cmd
ipconfig
```

**Linux/Unix:**
```bash
ifconfig
# or
ip address show
# or shortened
ip a s
```

**Example output:**
```
inet 192.168.66.89/24 brd 192.168.66.255
```

This shows:
- IP address: `192.168.66.89`
- Subnet mask: `/24` (255.255.255.0)
- Broadcast address: `192.168.66.255`

### Private IP Address Ranges

**RFC 1918 defines three private IP ranges:**

| Range | CIDR Notation | Description |
|-------|---------------|-------------|
| 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 | Class A private |
| 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 | Class B private |
| 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | Class C private |

**Key concept:** Private IP addresses cannot directly access or be reached from the public internet. They require NAT (Network Address Translation) through a router with a public IP address.

### Routing

A router functions at **Layer 3** (Network layer), forwarding packets between different networks. It inspects the destination IP address and determines the best path to forward the packet toward its destination.

**Analogy:** A router is like a post office - it examines the destination address and determines where to send the packet next.

**Question:** Which of the following IP addresses is not a private IP address?
- 192.168.250.125
- 10.20.141.132
- 49.69.147.197
- 172.23.182.251

**Answer:** `49.69.147.197`

**Question:** Which of the following IP addresses is not a valid IP address?
- 192.168.250.15
- 192.168.254.17
- 192.168.305.19
- 192.168.199.13

**Answer:** `192.168.305.19` (305 exceeds the maximum octet value of 255)

---

## Task 5: UDP and TCP

Transport layer protocols enable communication between specific processes on networked hosts using **port numbers**.

### Port Numbers

- Range: **1 to 65535** (port 0 is reserved)
- Uses 2 octets (16 bits): 2¹⁶ - 1 = 65535
- Identifies the specific process/service on a host

### UDP (User Datagram Protocol)

**Characteristics:**
- **Connectionless** - No connection establishment required
- **No delivery guarantee** - Does not confirm packet reception
- **Faster** - Lower overhead than TCP
- **Simple** - Minimal protocol mechanisms

**Analogy:** Standard mail service with no delivery confirmation

**Use cases:**
- DNS queries
- Video streaming
- Online gaming
- VoIP calls (where speed matters more than perfect reliability)

### TCP (Transmission Control Protocol)

**Characteristics:**
- **Connection-oriented** - Requires connection establishment
- **Reliable** - Guarantees delivery and order
- **Acknowledges packets** - Receiver confirms reception
- **Flow control** - Manages data transmission rate
- **Error correction** - Detects and handles lost/duplicated packets

### TCP Three-Way Handshake

**Connection establishment process:**

1. **SYN** - Client sends SYN packet with initial sequence number
2. **SYN-ACK** - Server responds with SYN-ACK packet
3. **ACK** - Client sends ACK to complete the handshake

```
Client                          Server
  |                               |
  |-------- SYN ----------------->|
  |                               |
  |<------ SYN-ACK ---------------|
  |                               |
  |-------- ACK ----------------->|
  |                               |
  |     Connection Established    |
```

**Analogy:** TCP is like registered mail with delivery confirmation - slower but reliable.

**Question:** Which protocol requires a three-way handshake?  
**Answer:** `TCP`

**Question:** What is the approximate number of port numbers (in thousands)?  
**Answer:** `65` (approximately 65,000 ports)

---

## Task 6: Encapsulation

Encapsulation is the process where each layer adds its own header (and sometimes trailer) to the data received from the layer above.

### Encapsulation Process

**Top to bottom (sending data):**

1. **Application Data**
   - User data from the application (e.g., email text, web form)

2. **Transport Layer: Segment/Datagram**
   - TCP adds header → **TCP Segment**
   - UDP adds header → **UDP Datagram**

3. **Internet Layer: Packet**
   - IP adds header → **IP Packet**

4. **Link Layer: Frame**
   - Ethernet/WiFi adds header and trailer → **Frame**

### Data Units at Each Layer

| Layer | Data Unit Name |
|-------|----------------|
| Application | Data |
| Transport (TCP) | Segment |
| Transport (UDP) | Datagram |
| Internet/Network | Packet |
| Data Link | Frame |

### The Life of a Packet - Example

**Scenario:** Searching for a room on TryHackMe

1. **Application:** Browser creates HTTP request with search query
2. **Transport:** TCP establishes three-way handshake, creates segments
3. **Internet:** IP layer adds source and destination IP addresses
4. **Link:** WiFi/Ethernet adds link layer header and trailer
5. **Router:** Removes link layer info, inspects IP, forwards to next hop
6. **Repeat:** Each router repeats until packet reaches destination
7. **Reverse:** Destination server reverses the process to extract data

**Question:** On a WiFi, within what will an IP packet be encapsulated?  
**Answer:** `frame`

**Question:** What do you call the UDP data unit that encapsulates the application data?  
**Answer:** `datagram`

**Question:** What do you call the data unit that encapsulates the application data sent over TCP?  
**Answer:** `segment`

---

## Task 7: Telnet

Telnet is a network protocol for remote terminal connection. While originally used for remote administration, we can use telnet to connect to any service listening on a TCP port.

### Telnet Syntax

```bash
telnet <IP_ADDRESS> <PORT>
```

**To close connection:** Press `CTRL + ]`, then type `quit`

### Practical Examples

**Echo Server (Port 7):**
```bash
telnet MACHINE_IP 7
```
- Echoes back everything you type
- Useful for testing connectivity

**Daytime Server (Port 13):**
```bash
telnet MACHINE_IP 13
```
- Returns current date and time
- Connection closes automatically after response

**Web Server (Port 80):**
```bash
telnet MACHINE_IP 80
GET / HTTP/1.1
Host: telnet.thm
[Press Enter twice]
```
- Manually craft HTTP request
- Server responds with HTTP headers and HTML content

### Security Note

Echo and daytime servers are considered security risks and should not be run in production. They're enabled here for educational demonstration only.

**Question:** Use telnet to connect to the web server on MACHINE_IP. What is the name and version of the HTTP server?  
**Answer:** `[Connect via telnet to port 80 and check HTTP response headers]`

**Question:** What flag did you get when you viewed the page?  
**Answer:** `[Extract flag from HTTP response]`

---

## Key Takeaways

### OSI Model Understanding

- **7 layers** provide a conceptual framework for network communications
- Each layer has specific responsibilities and protocols
- Layers 1-4 are most critical for practical networking work
- Understanding layers helps troubleshoot network issues effectively

### IP Addressing Essentials

- IPv4 uses 32-bit addresses (4 octets)
- Private IP ranges must be memorized for practical work
- Subnet masks define network boundaries
- Routers connect different networks by forwarding packets

### Transport Layer Protocols

**TCP vs UDP:**
- **TCP:** Reliable, connection-oriented, slower (web browsing, file transfer, email)
- **UDP:** Fast, connectionless, unreliable (streaming, gaming, DNS)

### Encapsulation Hierarchy

Application Data → Segment/Datagram → Packet → Frame

Each layer adds headers, creating a "nested" structure that gets unwrapped at the destination.

### Practical Skills

- Use `telnet` to manually interact with TCP services
- Understand how to craft basic protocol requests
- Diagnose network connectivity at different layers

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Troubleshooting Network Connectivity**
```bash
# Check your IP configuration
ip a s

# Test basic connectivity
ping 8.8.8.8

# Check if specific port is open
telnet target_ip 80
```

**Scenario 2: Understanding Network Traffic**
- Packet capture tools show encapsulation in action
- Each layer's headers visible in network analysis
- Understanding layers helps identify where problems occur

**Scenario 3: Service Testing**
```bash
# Test if web server is responding
telnet webserver.com 80
GET / HTTP/1.1
Host: webserver.com

# Test SSH availability
telnet server.com 22

# Test SMTP mail server
telnet mailserver.com 25
```

---

## Command Reference

| Command | Platform | Purpose |
|---------|----------|---------|
| `ipconfig` | Windows | View network configuration |
| `ifconfig` | Linux/Unix | View network configuration |
| `ip a s` | Linux | View network configuration (modern) |
| `telnet <ip> <port>` | All | Connect to TCP port |
| `CTRL + ]` | Telnet | Escape to telnet prompt |
| `quit` | Telnet | Close telnet connection |

---

## Next Steps

With foundational networking concepts mastered:
- Progress to **Networking Essentials** for deeper protocol understanding
- Study **packet analysis** with Wireshark
- Learn **subnetting calculations** for network design
- Explore **routing protocols** and network topologies
- Practice with **network simulation tools** (Packet Tracer, GNS3)

Understanding these fundamentals is crucial for cybersecurity work - network traffic analysis, penetration testing, and incident response all require solid networking knowledge.

---

**Room Completed:** ✓  
**Date:** 02/12/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
