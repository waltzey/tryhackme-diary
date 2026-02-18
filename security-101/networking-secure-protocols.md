# TryHackMe - Networking Secure Protocols

**Room:** Networking Secure Protocols  
**Difficulty:** Easy  
**Focus:** TLS/SSL, HTTPS, SMTPS, POP3S, IMAPS, SSH, SFTP, FTPS, VPN

---

## Overview

This room concludes the networking series by exploring how security is added to protocols that originally transmitted data in cleartext. It covers Transport Layer Security (TLS), Secure Shell (SSH), and Virtual Private Networks (VPN) - the three main approaches to securing network traffic in modern systems.

**Learning Objectives:**
- Understand SSL/TLS and its role in securing protocols
- Learn how HTTP, SMTP, POP3, and IMAP are secured with TLS
- Master SSH for secure remote access and file transfer
- Understand VPN concepts and use cases
- Practice analyzing encrypted vs decrypted network traffic

---

## Task 1: Introduction

### The Problem with Plaintext Protocols

Without security measures, all network traffic is vulnerable:

**Confidentiality:** Anyone capturing packets can read passwords, credit card numbers, and private documents

**Integrity:** Attackers can modify data in transit (e.g., changing payment amounts)

**Authenticity:** No guarantee you're communicating with the legitimate server (man-in-the-middle attacks)

### The Solutions

**TLS (Transport Layer Security)**
- Adds encryption to existing protocols
- HTTP → HTTPS, SMTP → SMTPS, POP3 → POP3S, IMAP → IMAPS
- The "S" suffix stands for "Secure"

**SSH (Secure Shell)**
- Replaces insecure TELNET for remote access
- Provides secure file transfer (SFTP)
- Enables secure tunneling for other protocols

**VPN (Virtual Private Network)**
- Creates secure networks over untrusted infrastructure
- Connects remote offices and users
- Encrypts all traffic between endpoints

---

## Task 2: TLS

### Historical Development

**1995:** Netscape released SSL 2.0 (Secure Sockets Layer)  
**1999:** IETF developed TLS 1.0 (upgrade to SSL 3.0)  
**2018:** TLS 1.3 released with major security improvements

Over two decades of development have resulted in the current robust TLS protocol.

### What TLS Provides

**Confidentiality**
- Encrypts data so eavesdroppers cannot read it
- Protects passwords, personal information, financial data

**Integrity**
- Prevents tampering with data in transit
- Detects if packets have been modified

**Authentication (via certificates)**
- Verifies server identity
- Prevents impersonation and man-in-the-middle attacks

### TLS Certificates

**Certificate Signing Request (CSR)**
1. Server administrator creates CSR
2. Submits to Certificate Authority (CA)
3. CA verifies and issues signed certificate
4. Certificate proves server identity to clients

**Certificate Authorities**
- Browsers/systems maintain list of trusted CAs
- Clients verify server certificates against trusted CA list
- Popular CAs: Let's Encrypt (free), DigiCert, GlobalSign

**Self-Signed Certificates**
- Created without CA verification
- Cannot prove server authenticity
- Used for testing/development only
- Browsers show warnings for self-signed certificates

### Protocols Secured by TLS

| Original Protocol | Secured Version | Purpose |
|-------------------|-----------------|---------|
| HTTP | HTTPS | Web browsing |
| SMTP | SMTPS | Email sending |
| POP3 | POP3S | Email retrieval |
| IMAP | IMAPS | Email synchronization |
| DNS | DoT | Domain resolution |
| MQTT | MQTTS | IoT messaging |
| SIP | SIPS | VoIP signaling |

**Question:** What is the protocol name that TLS upgraded and built upon?  
**Answer:** `SSL`

**Question:** Which type of certificates should not be used to confirm the authenticity of a server?  
**Answer:** `self-signed`

---

## Task 3: HTTPS

### HTTP Without TLS

**Standard HTTP process:**
1. DNS resolution (domain → IP)
2. TCP three-way handshake
3. HTTP communication (GET, POST, etc.)

**Security problem:** All traffic visible in cleartext

**Example cleartext HTTP traffic:**
```
GET /login HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0

username=admin&password=Pa$$w0rd123
```

Anyone capturing packets can read credentials, session tokens, and private data.

### HTTPS (HTTP over TLS)

**HTTPS process:**
1. DNS resolution
2. TCP three-way handshake
3. **TLS handshake and session establishment**
4. Encrypted HTTP communication

### Wireshark Analysis - HTTPS

**Without decryption key:**
- Packets show "Application Data"
- Content appears as gibberish
- Cannot determine actual protocol or data

**With decryption key:**
- Full HTTP requests/responses visible
- Can see GET, POST, headers, cookies
- Useful for debugging, but requires private key access

### TLS Negotiation Packets

The TLS handshake involves multiple packets:
- Client Hello
- Server Hello
- Certificate exchange
- Key exchange
- Finished messages

### Port Numbers

| Protocol | Port |
|----------|------|
| HTTP | 80 |
| HTTPS | 443 |
| Alternative HTTPS | 8443 |

### Key Benefits

**No Protocol Changes Required**
- HTTP remains unchanged
- TCP/IP layers unmodified
- TLS sits between HTTP and TCP

**Transparent Encryption**
- Applications work the same way
- Security added without code changes
- Backward compatibility maintained

**Question:** How many packets did the TLS negotiation and establishment take in the Wireshark HTTPS screenshots above?  
**Answer:** `[Count packets marked with "2" in the TLS establishment phase]`

**Question:** What is the number of the packet that contains the GET /login when accessing the website over HTTPS?  
**Answer:** `[Find packet number with decrypted HTTP GET request]`

---

## Task 4: SMTPS, POP3S, and IMAPS

### Adding TLS to Email Protocols

The same TLS approach used for HTTPS applies to email protocols:
- SMTP → SMTPS (sending email)
- POP3 → POP3S (downloading email)
- IMAP → IMAPS (synchronizing email)

### Port Number Changes

**Insecure Protocols:**

| Protocol | Default Port |
|----------|--------------|
| HTTP | 80 |
| SMTP | 25 |
| POP3 | 110 |
| IMAP | 143 |

**Secure Protocols (over TLS):**

| Protocol | Default Port |
|----------|--------------|
| HTTPS | 443 |
| SMTPS | 465, 587 |
| POP3S | 995 |
| IMAPS | 993 |

### SMTPS Ports Explained

**Port 465:** SMTPS (SMTP over TLS from start)  
**Port 587:** SMTP with STARTTLS (upgrades to TLS after initial connection)

### Security Benefits

**Before TLS:**
- Email credentials visible in plaintext
- Message content readable by anyone
- Passwords easily intercepted

**With TLS:**
- Encrypted authentication
- Protected message content
- Secure attachment transfer

### Practical Impact

Network traffic capture of insecure protocols reveals:
- ✅ SMTP: Username, password, email content
- ✅ POP3: Login credentials, downloaded messages
- ✅ IMAP: Authentication, synchronized emails

Network traffic capture of TLS-secured protocols reveals:
- ❌ SMTPS: Encrypted - unreadable
- ❌ POP3S: Encrypted - unreadable
- ❌ IMAPS: Encrypted - unreadable

**Question:** If you capture network traffic, in which of the following protocols can you extract login credentials: SMTPS, POP3S, or IMAP?  
**Answer:** `IMAP` (without TLS - only the insecure version)

---

## Task 5: SSH

### The TELNET Problem

TELNET (port 23) provides remote terminal access but transmits everything in cleartext:
- Login credentials visible
- All commands readable
- Session data exposed

### SSH Development

**1995:** Tatu Ylönen released SSH-1 (same year as SSL 2.0)  
**1996:** SSH-2 defined with improved security  
**1999:** OpenBSD released OpenSSH (open-source implementation)

Today's SSH clients are typically based on OpenSSH.

### SSH Benefits

**Secure Authentication**
- Password-based authentication
- Public key authentication (passwordless)
- Two-factor authentication support

**Confidentiality**
- End-to-end encryption
- Protects against eavesdropping
- Notifies of new server keys (MITM protection)

**Integrity**
- Cryptography protects data integrity
- Detects tampering attempts

**Tunneling**
- Creates secure "tunnels" for other protocols
- Enables VPN-like connections
- Port forwarding capabilities

**X11 Forwarding**
- Run graphical applications over network
- Display remote GUI on local system
- Useful for remote desktop scenarios

### Basic SSH Usage

**Connect to SSH server:**
```bash
ssh username@hostname
```

**If username matches local user:**
```bash
ssh hostname
```

**With X11 forwarding (graphical apps):**
```bash
ssh hostname -X
```

**Example - Running Wireshark remotely:**
```bash
ssh 192.168.124.148 -X
wireshark &
```

### SSH vs TELNET

| Feature | TELNET | SSH |
|---------|--------|-----|
| Port | 23 | 22 |
| Encryption | None | Strong |
| Authentication | Password only | Multiple methods |
| Integrity | No protection | Cryptographic |
| Tunneling | Not supported | Full support |

**Question:** What is the name of the open-source implementation of the SSH protocol?  
**Answer:** `OpenSSH`

---

## Task 6: SFTP and FTPS

### SFTP (SSH File Transfer Protocol)

**Part of SSH protocol suite**
- Uses same port as SSH (22)
- Integrated with SSH authentication
- Included with OpenSSH by default

**Basic SFTP usage:**
```bash
sftp username@hostname
```

**Common SFTP commands:**
```bash
sftp> get filename          # Download file
sftp> put filename          # Upload file
sftp> ls                    # List remote files
sftp> cd directory          # Change remote directory
sftp> lcd directory         # Change local directory
sftp> pwd                   # Show remote directory
sftp> lpwd                  # Show local directory
sftp> exit                  # Quit SFTP
```

**Commands are Unix-like** (different from FTP commands)

### FTPS (FTP Secure)

**FTP secured with TLS**
- Similar to HTTPS approach
- Requires TLS certificate setup
- More complex firewall configuration

**Port numbers:**
- **FTP:** 21 (control)
- **FTPS:** 990 (control)
- Separate connections for control and data

### SFTP vs FTPS Comparison

| Feature | SFTP | FTPS |
|---------|------|------|
| Based on | SSH | FTP + TLS |
| Port | 22 | 990 |
| Setup complexity | Easy | Complex |
| Firewall friendly | Yes | Challenging |
| Certificate required | No | Yes |
| Authentication | SSH methods | FTP + certificates |

### When to Use Each

**Use SFTP when:**
- SSH server already running
- Simple setup preferred
- Firewall restrictions present
- No certificate management desired

**Use FTPS when:**
- Existing FTP infrastructure
- TLS certificate available
- FTP-specific features needed
- Legacy system compatibility required

**Question:** Click on the View Site button to access the related site. Follow the instructions to obtain the flag.  
**Answer:** `[Access site and complete challenge]`

---

## Task 7: VPN

### VPN Purpose

Virtual Private Network enables:
- Connecting geographically distributed offices
- Secure remote user access
- Protected communication over untrusted networks (Internet)

### VPN Components

**V - Virtual**
- Uses existing Internet infrastructure
- No dedicated physical lines needed
- Cost-effective solution

**P - Private**
- Encryption protects confidentiality
- Authentication verifies endpoints
- Integrity checks prevent tampering

**N - Network**
- Creates logical network over physical network
- Devices appear on same network
- Access to shared resources

### Site-to-Site VPN

**Scenario:** Company with multiple branch offices

**Setup:**
- VPN server at main branch
- VPN clients at remote branches
- Encrypted tunnel between sites

**Benefits:**
- Remote office accesses main office resources
- Secure inter-office communication
- Centralized resource management

**Network flow:**
- **Blue lines:** Encrypted VPN traffic over Internet
- **Green lines:** Decrypted traffic within offices

### Remote Access VPN

**Scenario:** Individual users connecting from home/travel

**Setup:**
- VPN server at company headquarters
- VPN client on user devices
- Encrypted tunnel per user

**Benefits:**
- Secure access to company resources
- Work from anywhere
- Protected public WiFi usage

### VPN and Internet Traffic

**Full tunnel VPN:**
- All traffic routed through VPN
- Internet sees VPN server's IP, not user's
- ISP only sees encrypted traffic

**Split tunnel VPN:**
- Only company traffic through VPN
- Direct Internet access for other traffic
- Better performance for general browsing

### Geographic Location Masking

**Example:** User connects to VPN server in Japan
- Websites see Japanese IP address
- Content customized for Japan
- Bypasses geographical restrictions

**Important considerations:**
- Some VPN servers leak actual IP
- DNS leaks can reveal location
- Always test VPN connection (DNS leak test)

### Legal Considerations

**VPN legality varies by country:**
- Some countries ban VPN usage
- Penalties may be severe
- Always check local laws before using VPN
- Especially important when traveling

### VPN Use Cases

**Corporate:**
- Connect remote offices
- Enable work-from-home
- Secure partner connections

**Personal:**
- Protect public WiFi usage
- Access geo-restricted content
- Bypass censorship
- Enhance privacy

**Question:** What would you use to connect the various company sites so that users at a remote office can access resources located within the main branch?  
**Answer:** `VPN`

---

## Task 8: Closing Notes

### Three Approaches to Network Security

**1. TLS Approach**
- Secures existing protocols with minimal changes
- Most common for application-layer protocols
- Examples: HTTPS, SMTPS, POP3S, IMAPS
- Requires certificate management

**2. SSH Approach**
- Comprehensive security suite
- Remote access, file transfer, tunneling
- Easier setup than TLS-based solutions
- Examples: SSH (replaces TELNET), SFTP (replaces FTP)

**3. VPN Approach**
- Network-level security
- Connects entire networks or devices
- Routes all traffic through encrypted tunnel
- Best for site-to-site and remote access

### Challenge - TLS Decryption

**Scenario:** Analyze HTTPS traffic with TLS keys

**Setup:**
1. Browser configured to log TLS session keys
2. Command: `chromium --ssl-key-log-file=~/ssl-key.log`
3. Packet capture file: `randy-chromium.pcapng`
4. Key log file: `ssl-key.log`

**Wireshark Configuration:**

**Step 1-3:** Right-click TLS packet → Protocol Preferences → Transport Layer Security → Open preferences

**Step 4:** Browse to `ssl-key.log` file location (Documents folder)

**Step 5:** Click OK to apply

**Result:** Wireshark decrypts TLS traffic, revealing plaintext HTTP

**Question:** One of the packets contains login credentials. What password did the user submit?  
**Answer:** `[Analyze decrypted packets to find password]`

---

## Key Takeaways

### TLS Fundamentals

- Provides confidentiality, integrity, and authentication
- Requires valid certificates from trusted CAs
- Self-signed certificates don't prove authenticity
- Adds minimal overhead to existing protocols

### Protocol Security Comparison

**Insecure Protocols:**
- Everything visible in network captures
- Credentials easily intercepted
- No integrity protection
- Should never be used in production

**TLS-Secured Protocols:**
- Encrypted traffic unreadable without keys
- Certificate-based authentication
- Integrity protection via cryptography
- Industry standard for security

### SSH Advantages

- Multiple authentication methods
- Built-in encryption and integrity
- Extensible protocol (tunneling, X11 forwarding)
- Simpler than TLS certificate management
- Standard for remote administration

### VPN Benefits

- Creates private networks over public infrastructure
- Protects all traffic between endpoints
- Enables secure remote access
- Cost-effective for distributed organizations

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Securing Web Application**
```bash
# Before: HTTP on port 80
http://example.com

# After: HTTPS on port 443  
https://example.com

# Requires: Valid TLS certificate from CA
```

**Scenario 2: Secure Remote Administration**
```bash
# Insecure (TELNET):
telnet server.com 23

# Secure (SSH):
ssh admin@server.com
```

**Scenario 3: Secure File Transfer**
```bash
# Insecure (FTP):
ftp server.com

# Secure option 1 (SFTP):
sftp admin@server.com

# Secure option 2 (FTPS):
# Requires TLS certificate setup
```

**Scenario 4: Analyzing Encrypted Traffic**
```bash
# Capture traffic
tcpdump -i eth0 -w capture.pcap

# In Wireshark with TLS keys:
# Edit → Preferences → Protocols → TLS
# (Pre)-Master-Secret log filename: /path/to/ssl-key.log
```

---

## Command Reference

| Purpose | Command | Notes |
|---------|---------|-------|
| SSH connection | `ssh user@host` | Port 22 |
| SSH with X11 | `ssh user@host -X` | Run GUI apps |
| SFTP connection | `sftp user@host` | File transfer |
| Download file (SFTP) | `get filename` | In SFTP session |
| Upload file (SFTP) | `put filename` | In SFTP session |

### Port Reference

| Protocol | Insecure Port | Secure Port |
|----------|---------------|-------------|
| HTTP/HTTPS | 80 | 443 |
| SMTP/SMTPS | 25 | 465, 587 |
| POP3/POP3S | 110 | 995 |
| IMAP/IMAPS | 143 | 993 |
| TELNET/SSH | 23 | 22 |
| FTP/FTPS | 21 | 990 |

---

## Next Steps

With networking secure protocols mastered:
- Study **cryptography fundamentals** for deeper understanding
- Learn **certificate management** and PKI infrastructure
- Practice **network forensics** with encrypted traffic
- Explore **VPN technologies** (OpenVPN, WireGuard, IPSec)
- Study **TLS vulnerabilities** and attack vectors
- Learn **SSH hardening** and key management

Understanding these security protocols is essential for:
- Securing web applications and APIs
- Implementing secure email infrastructure
- Managing remote access solutions
- Protecting sensitive data in transit
- Incident response and forensics
- Penetration testing and security auditing

---

**Room Completed:** ✓  
**Date:** 02/14/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
