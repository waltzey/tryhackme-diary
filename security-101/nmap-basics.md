# TryHackMe - Nmap Basics

**Room:** Nmap Basics  
**Difficulty:** Easy  
**Focus:** Network scanning, host discovery, port scanning, service detection

---

## Overview

Nmap (Network Mapper) is the industry-standard open-source network scanner, first published in 1997. It's a powerful and flexible tool for discovering live hosts, identifying running services, and gathering network intelligence. This room covers essential Nmap usage for penetration testing and network reconnaissance.

**Learning Objectives:**
- Discover live hosts on networks
- Identify running services on target systems
- Understand different types of port scans
- Detect service versions and operating systems
- Control scan timing and output formats

---

## Task 1: Introduction

### The Network Discovery Problem

**Manual Discovery Challenges:**

**Problem 1: Who is online?**
- Manually checking 254 IPs in 192.168.0.1/24 network
- `ping` blocked by firewalls (ICMP filtering)
- `arp-scan` only works on local network
- Time-consuming and inefficient

**Problem 2: What services are running?**
- Using `telnet` to check ports one-by-one
- 65,535 ports per protocol (TCP/UDP)
- Manual scripting is slow and unreliable

### The Nmap Solution

Nmap solves both problems with:
- Sophisticated host discovery methods
- Efficient port scanning techniques
- Service and version detection
- OS fingerprinting
- Flexible targeting and output options

**First published:** 1997  
**Type:** Open-source network scanner  
**Use cases:** Network inventory, security auditing, penetration testing

---

## Task 2: Host Discovery - Who Is Online?

### Target Specification

Nmap offers flexible ways to specify scan targets:

**IP Range (using `-`):**
```bash
nmap 192.168.0.1-10
```
Scans IPs from 192.168.0.1 to 192.168.0.10

**Subnet (using `/`):**
```bash
nmap 192.168.0.1/24
```
Scans entire subnet (192.168.0.0-255)

**Hostname:**
```bash
nmap example.thm
```

### Ping Scan: `-sn`

**Purpose:** Discover live hosts without port scanning

**Command:**
```bash
nmap -sn 192.168.66.0/24
```

### Local Network Scanning

**"Local" network:** Directly connected via Ethernet or WiFi

**Example scan:**
```bash
root@tryhackme:~# nmap -sn 192.168.66.0/24

Nmap scan report for XiaoQiang (192.168.66.1)
Host is up (0.0069s latency).
MAC Address: 44:DF:65:D8:FE:6C (Unknown)

Nmap scan report for S190023240007 (192.168.66.88)
Host is up (0.090s latency).
MAC Address: 7C:DF:A1:D3:8C:5C (Espressif)

Nmap done: 256 IP addresses (7 hosts up) scanned in 2.64 seconds
```

**How it works:**
- Nmap sends **ARP requests** to discover hosts
- Devices respond to ARP, marked as "Host is up"
- **MAC addresses** are visible (useful for vendor identification)
- Fast and reliable on local networks

**Benefits:**
- Shows network card vendors
- Helps guess device types
- Minimal traffic generation

### Remote Network Scanning

**"Remote" network:** Separated by one or more routers

**Example scan:**
```bash
root@tryhackme:~# nmap -sn 192.168.11.0/24

Nmap scan report for 192.168.11.1
Host is up (0.018s latency).

Nmap scan report for 192.168.11.151
Host is up (0.0013s latency).

Nmap done: 256 IP addresses (5 hosts up) scanned in 10.67 seconds
```

**How it works (remote networks):**

Cannot use ARP (crosses routers), so Nmap uses:
1. **ICMP echo requests** (ping)
2. **ICMP timestamp requests**
3. **TCP SYN to port 443**
4. **TCP ACK to port 80**

**Example probe sequence:**
- 192.168.11.1 responded to ICMP echo (live)
- 192.168.11.2 didn't respond to any probes (down)
  - 2 ICMP echo requests
  - 2 ICMP timestamp requests
  - 2 TCP SYN to port 443
  - 2 TCP ACK to port 80
  - Router sent ICMP destination unreachable

**Note:** No MAC addresses shown (traffic routed, not switched)

### Advanced Host Discovery

**Additional options (beyond scope):**
- `-PS[portlist]` - TCP SYN discovery on specific ports
- `-PA[portlist]` - TCP ACK discovery on specific ports
- `-PU[portlist]` - UDP discovery on specific ports

### List Scan: `-sL`

**Preview targets without scanning:**
```bash
nmap -sL 192.168.0.1/24
```

**Purpose:**
- Lists all 256 targets that would be scanned
- Doesn't actually send any packets
- Useful for confirming target range before real scan

### Subnet Calculation

**Understanding CIDR notation:**

`/27` = 255.255.255.224 netmask
- Network bits: 27
- Host bits: 5
- Hosts per subnet: 2^5 - 2 = 30 usable hosts

**192.168.0.1/27 range:**
- First IP: 192.168.0.1 (network + 1)
- Last usable IP: 192.168.0.30
- Broadcast: 192.168.0.31

**Question:** What is the last IP address that will be scanned when your scan target is 192.168.0.1/27?  
**Answer:** `192.168.0.31` (Nmap scans broadcast address too)

---

## Task 3: Port Scanning - Who Is Listening?

### Understanding Ports

**TCP and UDP:**
- Each protocol has 65,535 ports
- Ports 1-1023: Well-known ports
- Common services:
  - TCP 80: HTTP
  - TCP 443: HTTPS
  - TCP 22: SSH
  - UDP 53: DNS

### TCP Connect Scan: `-sT`

**How it works:**
- Completes full TCP three-way handshake
- SYN → SYN-ACK → ACK
- Then tears down connection (RST-ACK)

**Command:**
```bash
nmap -sT 192.168.124.148
```

**Behavior:**

**Open port (e.g., port 22):**
1. Nmap sends SYN
2. Target responds SYN-ACK
3. Nmap sends ACK (connection established)
4. Nmap sends RST-ACK (tears down connection)

**Closed port (e.g., port 23):**
1. Nmap sends SYN
2. Target responds RST-ACK immediately

**Characteristics:**
- Works without root privileges
- More likely to be logged
- Completes full TCP handshake
- Default when run as non-root user

### SYN Scan (Stealth): `-sS`

**How it works:**
- Only sends TCP SYN packet
- Never completes three-way handshake
- "Half-open" scanning

**Command:**
```bash
sudo nmap -sS 192.168.124.148
```

**Behavior:**

**Open port:**
1. Nmap sends SYN
2. Target responds SYN-ACK
3. Nmap sends RST (aborts connection)

**Closed port:**
1. Nmap sends SYN
2. Target responds RST-ACK

**Advantages:**
- Fewer logs (connection never established)
- Faster than connect scan
- More stealthy
- **Requires root/sudo privileges**

**Why "stealth"?**
- Many older IDS systems only logged completed connections
- Modern systems detect it easily
- Still more subtle than connect scan

### UDP Scan: `-sU`

**Why scan UDP?**

Many critical services use UDP:
- DNS (port 53)
- DHCP (ports 67/68)
- NTP (port 123)
- SNMP (port 161)
- VoIP protocols

**Command:**
```bash
sudo nmap -sU 192.168.124.148
```

**How it works:**

**Closed UDP port:**
- Nmap sends UDP packet
- Target responds: ICMP destination unreachable (port unreachable)

**Open UDP port:**
- Nmap sends UDP packet
- No response (or service-specific response)

**Characteristics:**
- Slower than TCP scans
- Relies on ICMP responses for closed ports
- Open ports often don't respond (UDP is connectionless)
- Can be unreliable due to firewalls

### Port Range Options

**Default behavior:**
- Scans 1,000 most common ports

**Fast mode: `-F`**
```bash
nmap -F 192.168.124.148
```
Scans 100 most common ports

**Specific port range: `-p[range]`**

Examples:
```bash
nmap -p 80 192.168.124.148          # Single port
nmap -p 22,80,443 192.168.124.148   # Specific ports
nmap -p 1-100 192.168.124.148       # Range
nmap -p- 192.168.124.148            # All 65,535 ports
nmap -p1-1023 192.168.124.148       # Well-known ports
```

**Special notation:**
- `-p-` = `-p1-65535` (all ports)
- `-p-25` = `-p1-25` (ports 1 through 25)

### Scan Type Summary

| Option | Scan Type | Requires Root | Stealth Level |
|--------|-----------|---------------|---------------|
| `-sT` | TCP Connect | No | Low |
| `-sS` | TCP SYN | Yes | Medium |
| `-sU` | UDP | Yes | Medium |

### Practical Examples

**Quick web server check:**
```bash
nmap -p 80,443 example.com
```

**Thorough scan of all TCP ports:**
```bash
sudo nmap -sS -p- 192.168.1.100
```

**Scan common UDP services:**
```bash
sudo nmap -sU -p 53,67,123,161 192.168.1.1
```

**Combined TCP and UDP:**
```bash
sudo nmap -sS -sU -p 53 8.8.8.8
```

**Question:** How many TCP ports are open on the target system at MACHINE_IP?  
**Answer:** `[Run: nmap -sS MACHINE_IP or nmap MACHINE_IP]`

**Question:** Find the listening web server on MACHINE_IP and access it with your browser. What is the flag that appears on its main page?  
**Answer:** `[Run: nmap -p 80,8080,443,8443 MACHINE_IP, then browse to http://MACHINE_IP:PORT]`

---

## Task 4: Version Detection - Extract More Information

### OS Detection: `-O`

**Detect target operating system:**
```bash
sudo nmap -sS -O 192.168.124.211
```

**Example output:**
```
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 52:54:00:54:FA:4E (QEMU virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
```

**How it works:**
- Analyzes TCP/IP stack behavior
- Examines packet responses
- Compares against OS fingerprint database
- Makes educated guess

**Limitations:**
- Not 100% accurate
- Broad ranges (e.g., Linux 4.15 - 5.8)
- Firewalls can interfere
- Virtual machines may be misidentified

**Requires:** Root/sudo privileges

### Service Version Detection: `-sV`

**Detect service versions:**
```bash
sudo nmap -sS -sV 192.168.124.211
```

**Example output:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
```

**How it works:**
- Connects to open ports
- Sends probes to trigger service banners
- Analyzes responses
- Matches against service signature database

**Information revealed:**
- Service name (SSH, HTTP, FTP, etc.)
- Version number (OpenSSH 8.9p1)
- Operating system hints (Ubuntu Linux)
- Protocol details

**Benefits:**
- Identifies vulnerable software versions
- Reveals misconfigurations
- Helps plan exploitation strategies

### Aggressive Scan: `-A`

**All-in-one option:**
```bash
sudo nmap -A 192.168.124.211
```

**Enables:**
- `-O` - OS detection
- `-sV` - Version detection
- `-sC` - Default NSE scripts
- `--traceroute` - Network path

**Example output:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10
|_ssh-hostkey: RSA key fingerprint...
Running: Linux 4.X|5.X
OS details: Linux 4.15 - 5.8
```

**Use cases:**
- Maximum information gathering
- Comprehensive reconnaissance
- Vulnerability assessment

**Warning:**
- Very noisy (easily detected)
- Takes longer to complete
- Generates significant traffic

### Skip Host Discovery: `-Pn`

**Problem:** Target doesn't respond to ping

**Solution:** Treat all hosts as online
```bash
nmap -Pn 192.168.124.211
```

**When to use:**
- Firewalls block ICMP
- Host appears down but is actually up
- Want to scan specific host regardless

**Behavior:**
- Skips host discovery phase
- Immediately starts port scanning
- Assumes target is alive

**Example scenario:**
```bash
# Host appears down
nmap 192.168.1.100
# No results

# Force scan anyway
nmap -Pn 192.168.1.100
# Discovers open ports!
```

### Detection Options Summary

| Option | Purpose | Requires Root |
|--------|---------|---------------|
| `-O` | OS detection | Yes |
| `-sV` | Service version | No |
| `-A` | Aggressive (all features) | Yes |
| `-Pn` | Skip ping, assume online | No |

<img width="1222" height="1365" alt="image" src="https://github.com/user-attachments/assets/e2c340e4-5238-4273-9699-663efd66e2bf" />

### Practical Examples

**Quick version check:**
```bash
nmap -sV -p 22,80,443 example.com
```

**Full reconnaissance:**
```bash
sudo nmap -A -p- 192.168.1.100
```

**Scan firewall-protected host:**
```bash
sudo nmap -Pn -sS -sV 10.0.0.50
```

**Question:** What is the name and detected version of the web server running on MACHINE_IP?  
**Answer:** `[Run: nmap -sV -p 80 MACHINE_IP or check the VERSION column]`

---

## Task 5: Timing - How Fast is Fast?

### Timing Templates

Nmap provides six timing templates to control scan speed:

| Template | Name | Speed | Use Case |
|----------|------|-------|----------|
| `-T0` | Paranoid | Extremely slow | IDS evasion |
| `-T1` | Sneaky | Very slow | IDS evasion |
| `-T2` | Polite | Slow | Reduce bandwidth |
| `-T3` | Normal | Default | Normal scanning |
| `-T4` | Aggressive | Fast | Fast networks |
| `-T5` | Insane | Very fast | Very fast networks |

### Timing Comparison

**Test scenario:** SYN scan of 100 most common ports
```bash
nmap -sS MACHINE_IP -F -T[0-4]
```

**Results:**

| Timing | Duration |
|--------|----------|
| T0 (paranoid) | 9.8 hours |
| T1 (sneaky) | 27.53 minutes |
| T2 (polite) | 40.56 seconds |
| T3 (normal) | 0.15 seconds |
| T4 (aggressive) | 0.13 seconds |

### How Timing Works

**T0 (Paranoid):**
- Waits **5 minutes** between each port
- Single probe at a time
- Minimal network footprint
- Avoids triggering IDSs

**T1 (Sneaky):**
- Waits **15 seconds** between ports
- Still very slow
- Better IDS evasion

**T2 (Polite):**
- Waits **0.4 seconds** between ports
- Reduces bandwidth usage
- Less aggressive than default

**T3 (Normal):**
- Default behavior
- No intentional delays
- Adaptive timing based on network

**T4 (Aggressive):**
- Assumes fast, reliable network
- Maximum parallelization
- May miss results on slow networks

### Syntax Options

**By name:**
```bash
nmap -T paranoid 192.168.1.1
nmap -T sneaky 192.168.1.1
nmap -T aggressive 192.168.1.1
```

**By number:**
```bash
nmap -T0 192.168.1.1
nmap -T 4 192.168.1.1
```

### Advanced Timing Control

**Parallel Probes:**
```bash
nmap --min-parallelism 10 --max-parallelism 100 192.168.1.1
```
- Controls simultaneous port probes
- Default: Nmap auto-adjusts (1 to several hundred)
- Poor network: Drops to 1 probe
- Good network: Increases parallelism

**Packet Rate:**
```bash
nmap --min-rate 100 --max-rate 1000 192.168.1.1
```
- Packets per second
- Applies to entire scan (not per host)
- `--min-rate`: Minimum guaranteed rate
- `--max-rate`: Maximum allowed rate

**Host Timeout:**
```bash
nmap --host-timeout 5m 192.168.1.0/24
```
- Maximum time to wait per host
- Useful for slow hosts or connections
- Skip hosts that don't respond in time
- Format: seconds (s), minutes (m), hours (h)

### Timing Options Summary

| Option | Purpose | Example |
|--------|---------|---------|
| `-T<0-5>` | Timing template | `-T4` |
| `--min-parallelism` | Minimum parallel probes | `--min-parallelism 10` |
| `--max-parallelism` | Maximum parallel probes | `--max-parallelism 100` |
| `--min-rate` | Minimum packets/second | `--min-rate 50` |
| `--max-rate` | Maximum packets/second | `--max-rate 500` |
| `--host-timeout` | Max time per host | `--host-timeout 10m` |

### Practical Usage

**Stealth scan (slow):**
```bash
sudo nmap -sS -T1 -p- 192.168.1.100
```

**Fast local network scan:**
```bash
nmap -T4 192.168.1.0/24
```

**Guaranteed speed:**
```bash
nmap --min-rate 300 -p- 192.168.1.100
```

**Skip slow hosts:**
```bash
nmap --host-timeout 30s 10.0.0.0/24
```

**Question:** What is the non-numeric equivalent of -T4?  
**Answer:** `aggressive`

---

## Task 6: Output - Controlling What You See

### Verbosity and Debugging

**Default output:**
```bash
nmap -sS 192.168.139.1/24
```

Minimal output showing only final results.

**Verbose output: `-v`**
```bash
nmap -sS 192.168.139.1/24 -v
```

**Shows:**
- ARP ping scan progress
- DNS resolution status
- Port scan stages
- Real-time discoveries
- Packet statistics

**Example verbose output:**
```
Initiating ARP Ping Scan at 19:01
Scanning 255 hosts [1 port/host]
Completed ARP Ping Scan at 19:01, 7.94s elapsed (255 total hosts)
Initiating Parallel DNS resolution
Initiating SYN Stealth Scan
Discovered open port 902/tcp on 192.168.139.1
```

**Increase verbosity:**
- `-v` - Verbose
- `-vv` - Very verbose
- `-vvv` - Even more verbose
- `-v2`, `-v4` - Specify level numerically

**Interactive verbosity:**
- Press `v` during scan to increase verbosity
- Press `d` during scan to increase debugging

### Debugging: `-d`

**Debug output:**
```bash
nmap -d 192.168.139.1
```

**Debugging levels:**
- `-d` - Basic debugging
- `-dd` - More debugging
- `-d9` - Maximum debugging (thousands of lines)

**When to use:**
- Troubleshooting scan issues
- Understanding Nmap behavior
- Learning protocol details
- Development and testing

**Warning:** High debug levels generate massive output

### Output Formats

**Three main formats:**

**1. Normal Output: `-oN <filename>`**
```bash
nmap -sS 192.168.139.1 -oN scan.txt
```
- Human-readable format
- Similar to terminal output
- Easy to read and share

**2. XML Output: `-oX <filename>`**
```bash
nmap -sS 192.168.139.1 -oX scan.xml
```
- Machine-readable format
- Parsable by other tools
- Structured data
- Import into databases

**3. Grepable Output: `-oG <filename>`**
```bash
nmap -sS 192.168.139.1 -oG scan.gnmap
```
- Line-based format
- Easy to search with `grep`
- Simple parsing with `awk`
- One line per host

**4. All Formats: `-oA <basename>`**
```bash
nmap -sS 192.168.139.1 -oA gateway
```

**Creates three files:**
- `gateway.nmap` - Normal output
- `gateway.xml` - XML output
- `gateway.gnmap` - Grepable output

### Output Format Examples

**Normal output (gateway.nmap):**
```
Nmap scan report for g5000 (192.168.139.1)
Host is up (0.0000070s latency).
Not shown: 999 closed tcp ports (reset)
PORT    STATE SERVICE
902/tcp open  iss-realsecure
```

**Grepable output (gateway.gnmap):**
```
Host: 192.168.139.1 ()  Status: Up
Host: 192.168.139.1 ()  Ports: 902/open/tcp//iss-realsecure///
```

**XML output (gateway.xml):**
```xml
<host>
  <address addr="192.168.139.1" addrtype="ipv4"/>
  <ports>
    <port protocol="tcp" portid="902">
      <state state="open"/>
    </port>
  </ports>
</host>
```

### Using Output Files

**Search with grep:**
```bash
grep "open" scan.gnmap
grep "22/tcp" scan.txt
```

**Parse with awk:**
```bash
awk '/open/ {print $2}' scan.gnmap
```

**Import XML:**
- Import into vulnerability scanners
- Feed into automation scripts
- Parse with Python/Ruby

### Output Options Summary

| Option | Format | Use Case |
|--------|--------|----------|
| `-oN` | Normal | Human reading |
| `-oX` | XML | Tool integration |
| `-oG` | Grepable | Command-line parsing |
| `-oA` | All three | Complete documentation |
| `-v` | Verbose | Real-time progress |
| `-d` | Debug | Troubleshooting |

### Best Practices

**Standard scan with output:**
```bash
sudo nmap -sS -sV -oA company-scan 192.168.1.0/24 -v
```

**Quick search results:**
```bash
grep "80/open" company-scan.gnmap | awk '{print $2}'
```

**Archive scans:**
```bash
nmap -A target.com -oA scans/target-$(date +%Y%m%d)
```

**Question:** What option must you add to your nmap command to enable debugging?  
**Answer:** `-d`

---

## Task 7: Conclusion and Summary

### Key Concepts Learned

**1. Host Discovery**
- Ping scans with `-sn`
- Local vs remote network behavior
- ARP vs ICMP-based discovery

**2. Port Scanning**
- TCP connect scan (`-sT`)
- TCP SYN scan (`-sS`)
- UDP scan (`-sU`)
- Port range specifications

**3. Service Detection**
- OS detection (`-O`)
- Version detection (`-sV`)
- Aggressive scanning (`-A`)
- Forcing scans (`-Pn`)

**4. Timing Control**
- Templates (`-T0` through `-T5`)
- Parallelism control
- Packet rate control
- Host timeouts

**5. Output Management**
- Verbosity levels
- Debug modes
- Multiple output formats
- File management

### Privilege Requirements

**Running as root/sudo:**
- **Enables:** SYN scan (`-sS`)
- **Enables:** OS detection (`-O`)
- **Enables:** Raw packet crafting
- **Default scan:** `-sS` (SYN scan)

**Running as local user:**
- **Limited to:** Connect scan (`-sT`)
- **Limited to:** ICMP echo
- **Cannot:** Craft custom packets
- **Default scan:** `-sT` (Connect scan)

**Best practice:** Always use `sudo nmap` for full capabilities

### Complete Command Reference

| Category | Option | Description |
|----------|--------|-------------|
| **Listing** | `-sL` | List targets without scanning |
| **Discovery** | `-sn` | Ping scan - host discovery only |
| **TCP Scan** | `-sT` | TCP connect scan |
| | `-sS` | TCP SYN scan (stealth) |
| **UDP Scan** | `-sU` | UDP scan |
| **Port Range** | `-F` | Fast - 100 common ports |
| | `-p[range]` | Specify port range |
| | `-Pn` | Skip ping - treat all as online |
| **Detection** | `-O` | OS detection |
| | `-sV` | Service version detection |
| | `-A` | Aggressive (OS + version + more) |
| **Timing** | `-T<0-5>` | Timing template |
| | `--min-parallelism` | Min parallel probes |
| | `--max-parallelism` | Max parallel probes |
| | `--min-rate` | Min packets/second |
| | `--max-rate` | Max packets/second |
| | `--host-timeout` | Max time per host |
| **Output** | `-v` | Verbose output |
| | `-d` | Debug output |
| | `-oN` | Normal output to file |
| | `-oX` | XML output to file |
| | `-oG` | Grepable output to file |
| | `-oA` | All formats |

### Common Scan Patterns

**Basic network sweep:**
```bash
sudo nmap -sn 192.168.1.0/24
```

**Quick port scan:**
```bash
sudo nmap -sS -F 192.168.1.100
```

**Thorough reconnaissance:**
```bash
sudo nmap -sS -sV -O -p- 192.168.1.100 -oA full-scan
```

**Stealth scan:**
```bash
sudo nmap -sS -T1 -p 22,80,443 target.com
```

**Fast aggressive scan:**
```bash
sudo nmap -A -T4 192.168.1.0/24
```

**Web server enumeration:**
```bash
nmap -sV -p 80,443,8080,8443 target.com
```

**UDP service discovery:**
```bash
sudo nmap -sU -p 53,67,123,161 192.168.1.1
```

### Next Steps

With Nmap basics mastered:
- Practice on **authorized targets only**
- Explore **NSE scripts** (Nmap Scripting Engine)
- Study **advanced evasion techniques**
- Learn **Zenmap** (GUI interface)
- Master **network security concepts**
- Combine with other tools (Metasploit, Burp Suite)

**Advanced Nmap rooms in Network Security module:**
- Nmap Live Host Discovery
- Nmap Basic Port Scans
- Nmap Advanced Port Scans
- Nmap Post Port Scans

---

## Practical Application

### Real-World Scenarios

**Scenario 1: New Network Assessment**
```bash
# Discover live hosts
sudo nmap -sn 10.0.0.0/24 -oA discovery

# Scan discovered hosts
sudo nmap -sS -sV -p- -iL discovery.gnmap -oA full-scan
```

**Scenario 2: Web Application Testing**
```bash
# Find web servers
nmap -p 80,443,8000-9000 target.com

# Detailed version info
nmap -sV -p 80,443 target.com -oN web-scan.txt
```

**Scenario 3: Stealth Reconnaissance**
```bash
# Slow scan to avoid detection
sudo nmap -sS -T1 -p- target.com -oA stealth-scan
```

**Scenario 4: Quick Vulnerability Check**
```bash
# Aggressive scan for quick results
sudo nmap -A -T4 target.com -oX vuln-check.xml
```

### Security Considerations

**Legal and Ethical:**
- **Only scan authorized targets**
- Get written permission
- Respect scope boundaries
- Follow responsible disclosure

**Operational:**
- Scans can be detected
- May trigger IDS/IPS alerts
- Can impact target performance
- Logs will show your IP

**Best Practices:**
- Use VPNs when appropriate
- Rate-limit aggressive scans
- Save all scan outputs
- Document methodology

---

**Question:** What kind of scan will Nmap use if you run nmap MACHINE_IP with local user privileges?  
**Answer:** `TCP connect scan` (or `-sT`)

---

**Room Completed:** ✓  
**Date:** 02/20/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
