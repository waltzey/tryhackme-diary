# TryHackMe - Networking Core Protocols

**Room:** Networking Core Protocols  
**Difficulty:** Easy  
**Focus:** DNS, WHOIS, HTTP/HTTPS, FTP, SMTP, POP3, IMAP

---

## Overview

This room explores essential application-layer protocols that power everyday internet services. It covers how domain names are resolved (DNS), website ownership information (WHOIS), web browsing (HTTP/HTTPS), file transfers (FTP), and email communication (SMTP, POP3, IMAP). The room emphasizes understanding protocol mechanics through hands-on telnet sessions.

**Learning Objectives:**
- Understand DNS record types and domain name resolution
- Query WHOIS databases for domain registration information
- Learn HTTP methods and web server communication
- Master FTP for file transfers
- Understand email protocols: SMTP for sending, POP3 and IMAP for receiving

---

## Task 2: DNS - Remembering Addresses

DNS (Domain Name System) translates human-readable domain names into IP addresses that computers use for communication.

### DNS Fundamentals

**Layer:** Application Layer (Layer 7)  
**Ports:** UDP 53 (primary), TCP 53 (fallback)

**Purpose:** Map domain names to IP addresses so users don't need to memorize numerical addresses.

### DNS Record Types

**A Record (Address)**
- Maps hostname to IPv4 address
- Example: `example.com` → `172.17.2.172`
- Most common record type

**AAAA Record (Quad-A)**
- Maps hostname to IPv6 address
- IPv6 is the next-generation internet protocol
- Example: `example.com` → `2606:2800:21f:cb07:6820:80da:af6b:8b2c`

**CNAME Record (Canonical Name)**
- Maps domain name to another domain name
- Creates aliases for domains
- Example: `www.example.com` → `example.com`

**MX Record (Mail Exchange)**
- Specifies mail server for domain
- Used when sending email to `user@example.com`
- Can have multiple MX records with priority values

### DNS Query Process

When you type a domain in your browser:
1. Browser checks local DNS cache
2. If not cached, queries DNS server
3. DNS server returns A record (IPv4) or AAAA record (IPv6)
4. Browser connects to the returned IP address

### Using nslookup

**Basic DNS lookup:**
```bash
nslookup www.example.com
```

**Output:**
```
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   www.example.com
Address: 93.184.215.14
Name:   www.example.com
Address: 2606:2800:21f:cb07:6820:80da:af6b:8b2c
```

### DNS Traffic Analysis

**Packet capture of DNS query:**
```bash
tshark -r dns-query.pcapng -Nn
```

**Output:**
```
1 0.000000000 192.168.66.89 → 192.168.66.1 DNS Standard query A www.example.com
2 0.059049584 192.168.66.1 → 192.168.66.89 DNS Standard query response A www.example.com A 93.184.215.14
3 0.059721705 192.168.66.89 → 192.168.66.1 DNS Standard query AAAA www.example.com
4 0.101568276 192.168.66.1 → 192.168.66.89 DNS Standard query response AAAA www.example.com AAAA 2606:2800:21f:cb07:6820:80da:af6b:8b2c
```

**Observation:**
- Query 1: Request for A record (IPv4)
- Query 2: Response with IPv4 address
- Query 3: Request for AAAA record (IPv6)
- Query 4: Response with IPv6 address

**Question:** Which DNS record type refers to IPv6?  
**Answer:** `AAAA`

**Question:** Which DNS record type refers to the email server?  
**Answer:** `MX`

---

## Task 3: WHOIS

WHOIS provides publicly available information about domain name registrations, including registrant details and domain registration dates.

### WHOIS Information

When someone registers a domain, they must provide:
- Registrant name
- Contact information (email, phone, address)
- Domain creation and expiration dates
- Name servers

### Privacy Protection

Many registrars offer privacy protection services that hide personal information from public WHOIS records by displaying proxy information instead.

### Using WHOIS Command

**Basic WHOIS lookup:**
```bash
whois example.com
```

**Example output (with privacy protection):**
```
Domain Name: EXAMPLE.COM
Registry Domain ID: XXXXXXXXX
Registrar WHOIS Server: whois.godaddy.com
Registrar URL: https://www.godaddy.com
Updated Date: 2017-07-05T16:02:43Z
Creation Date: 1993-04-02T00:00:00Z
Registrar Registration Expiration Date: 2026-10-20T14:56:17Z
Registrar: GoDaddy.com, LLC

Registrant Name: Registration Private
Registrant Organization: Domains By Proxy, LLC
Registrant Street: DomainsByProxy.com
```

### Key WHOIS Data Points

- **Creation Date:** When domain was first registered
- **Updated Date:** Last modification to WHOIS record
- **Expiration Date:** When domain registration expires
- **Registrar:** Company handling the registration
- **Name Servers:** DNS servers authoritative for the domain

**Question:** When was the x.com record created? Provide the answer in YYYY-MM-DD format.  
**Answer:** `[Run: whois x.com and find Creation Date]`

**Question:** When was the twitter.com record created? Provide the answer in YYYY-MM-DD format.  
**Answer:** `[Run: whois twitter.com and find Creation Date]`

---

## Task 4: HTTP(S) - Accessing the Web

HTTP (Hypertext Transfer Protocol) and HTTPS (HTTP Secure) define how web browsers communicate with web servers.

### HTTP Methods

**GET**
- Retrieves data from server
- Most common HTTP method
- Example: Loading a webpage

**POST**
- Submits data to server
- Example: Submitting a form, uploading a file

**PUT**
- Creates new resource or updates existing one
- Replaces entire resource

**DELETE**
- Removes specified resource from server
- Example: Deleting a file or database entry

### HTTP vs HTTPS

| Protocol | Port | Security |
|----------|------|----------|
| HTTP | 80 | Unencrypted |
| HTTPS | 443 | Encrypted (TLS/SSL) |

**Alternative ports:** 8080, 8443 (commonly used for development/testing)

### HTTP Request Structure

**Manual HTTP request via telnet:**
```bash
telnet MACHINE_IP 80
GET / HTTP/1.1
Host: example.com
[Press Enter twice]
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: text/html
Server: Apache/2.4.41
Last-Modified: Wed, 15 Jun 2022 10:30:00 GMT

<html>
<body>
<h1>Welcome</h1>
</body>
</html>
```

### HTTP Headers

**Request headers:**
- `Host:` Specifies the domain name
- `User-Agent:` Identifies the browser/client
- `Accept:` Content types the client can handle

**Response headers:**
- `Server:` Web server software and version
- `Content-Type:` Type of content being sent
- `Last-Modified:` When the resource was last changed

### Retrieving Specific Files

**Request specific file:**
```bash
telnet MACHINE_IP 80
GET /file.html HTTP/1.1
Host: anything
[Press Enter twice]
```

**Question:** Use telnet to access the file flag.html on MACHINE_IP. What is the hidden flag?  
**Answer:** `[Connect via telnet and retrieve flag.html]`

---

## Task 5: FTP - Transferring Files

FTP (File Transfer Protocol) is designed specifically for efficient file transfers between client and server.

### FTP Characteristics

**Port:** TCP 21 (control), separate connection for data transfer  
**Purpose:** Upload and download files  
**Efficiency:** Faster than HTTP for pure file transfers

### FTP Commands

**USER**
- Provides username for authentication
- Example: `USER anonymous`

**PASS**
- Provides password
- Example: `PASS password123`

**RETR (Retrieve)**
- Downloads file from server to client
- Example: `RETR document.pdf`

**STOR (Store)**
- Uploads file from client to server
- Example: `STOR upload.txt`

**LIST**
- Lists files in current directory
- Similar to `ls` command

### FTP Session Example

```bash
ftp MACHINE_IP
```

**Session:**
```
Connected to MACHINE_IP
220 (vsFTPd 3.0.5)
Name: anonymous
331 Please specify the password.
Password: 
230 Login successful.

ftp> ls
227 Entering Passive Mode
150 Here comes the directory listing.
-rw-r--r--    1 0        0            1480 Jun 27 08:03 coffee.txt
-rw-r--r--    1 0        0              14 Jun 27 08:04 flag.txt
-rw-r--r--    1 0        0            1595 Jun 27 08:05 tea.txt
226 Directory send OK.

ftp> type ascii
200 Switching to ASCII mode.

ftp> get coffee.txt
227 Entering Passive Mode
150 Opening BINARY mode data connection for coffee.txt (1480 bytes).
226 Transfer complete.
1480 bytes received

ftp> quit
221 Goodbye.
```

### Transfer Modes

**ASCII Mode:**
- For text files
- Converts line endings between systems
- Command: `type ascii`

**Binary Mode:**
- For non-text files (images, executables, archives)
- No conversion, byte-for-byte transfer
- Command: `type binary`

### Anonymous FTP

Many FTP servers allow anonymous access:
- Username: `anonymous`
- Password: (often blank or your email)

**Question:** Using the FTP client ftp on the AttackBox, access the FTP server at MACHINE_IP and retrieve flag.txt. What is the flag found?  
**Answer:** `[Connect via FTP and download flag.txt]`

---

## Task 6: SMTP - Sending Email

SMTP (Simple Mail Transfer Protocol) defines how email clients send messages to mail servers and how mail servers communicate with each other.

### SMTP Fundamentals

**Port:** TCP 25 (default)  
**Purpose:** Send outgoing email  
**Analogy:** Like going to the post office to mail a package

### SMTP Commands

**HELO / EHLO**
- Initiates SMTP session
- EHLO is Extended HELO with more features
- Example: `HELO client.thm`

**MAIL FROM**
- Specifies sender's email address
- Example: `MAIL FROM: <user@client.thm>`

**RCPT TO**
- Specifies recipient's email address
- Example: `RCPT TO: <admin@server.thm>`

**DATA**
- Indicates start of email message content
- Everything after this is message body

**. (Period)**
- Single period on its own line
- Indicates end of email message

**QUIT**
- Ends SMTP session

### Sending Email via Telnet

```bash
telnet MACHINE_IP 25
```

**Session:**
```
Connected to MACHINE_IP
220 example.thm ESMTP Exim 4.95 Ubuntu

HELO client.thm
250 example.thm Hello client.thm

MAIL FROM: <user@client.thm>
250 OK

RCPT TO: <admin@server.thm>
250 Accepted

DATA
354 Enter message, ending with "." on a line by itself

From: user@client.thm
To: admin@server.thm
Subject: Test Email

This is a test email sent via telnet!
.
250 OK id=1sMrpq-0001Ah-UT

QUIT
221 example.thm closing connection
```

### Email Headers

**Common headers:**
- `From:` Sender's email address
- `To:` Recipient's email address
- `Subject:` Email subject line
- `Date:` When email was sent
- `Message-ID:` Unique identifier for the email

**Question:** Which SMTP command indicates that the client will start the contents of the email message?  
**Answer:** `DATA`

**Question:** What does the email client send to indicate that the email message has been fully entered?  
**Answer:** `.`

---

## Task 7: POP3 - Receiving Email

POP3 (Post Office Protocol version 3) allows email clients to retrieve messages from a mail server.

### POP3 Fundamentals

**Port:** TCP 110  
**Purpose:** Download email from server to client  
**Analogy:** Checking your physical mailbox for letters

### POP3 vs IMAP

**POP3 behavior:**
- Downloads emails to local device
- Typically deletes from server after download
- Best for single-device access
- Minimizes server storage

**IMAP behavior (covered in next task):**
- Keeps emails synchronized on server
- Better for multi-device access
- Uses more server storage

### POP3 Commands

**USER**
- Identifies the username
- Example: `USER john`

**PASS**
- Provides password
- Example: `PASS password123`

**STAT**
- Requests number of messages and total size
- Response: `+OK 3 1264` (3 messages, 1264 bytes total)

**LIST**
- Lists all messages with their sizes
- Shows message number and size for each

**RETR**
- Retrieves specified message
- Example: `RETR 3` (retrieve message 3)

**DELE**
- Marks message for deletion
- Example: `DELE 2` (delete message 2)

**QUIT**
- Ends POP3 session and applies changes

### POP3 Session Example

```bash
telnet MACHINE_IP 110
```

**Session:**
```
Connected to MACHINE_IP
+OK Dovecot (Ubuntu) ready.

USER linda
+OK

PASS Pa$$123
+OK Logged in.

STAT
+OK 3 1264

LIST
+OK 3 messages:
1 407
2 412
3 445
.

RETR 3
+OK 445 octets
Return-path: <user@client.thm>
From: user@client.thm
To: admin@server.thm
Subject: Test Email

This is a test email!
.

QUIT
+OK Logging out.
```

### Login Credentials for Lab

- **Username:** linda
- **Password:** Pa$$123

**Question:** Looking at the traffic exchange, what is the name of the POP3 server running on the remote server?  
**Answer:** `[Connect and check server banner - should show software name like Dovecot]`

**Question:** Use telnet to connect to MACHINE_IP's POP3 server. What is the flag contained in the fourth message?  
**Answer:** `[Connect, login as linda, use RETR 4 to retrieve fourth message]`

---

## Task 8: IMAP - Synchronizing Email

IMAP (Internet Message Access Protocol) provides email synchronization across multiple devices, keeping messages on the server.

### IMAP Fundamentals

**Port:** TCP 143  
**Purpose:** Synchronized email access across multiple devices  
**Advantage:** Emails stay on server, accessible from anywhere

### IMAP vs POP3

| Feature | POP3 | IMAP |
|---------|------|------|
| Email location | Downloaded locally | Kept on server |
| Multi-device sync | No | Yes |
| Server storage | Minimal | Higher |
| Offline access | Yes (after download) | Limited |
| Folder management | No | Yes |

### IMAP Commands

**LOGIN**
- Authenticates user with username and password
- Example: `A LOGIN username password`

**SELECT**
- Selects mailbox folder to work with
- Example: `B SELECT inbox`

**FETCH**
- Retrieves message content
- Example: `C FETCH 3 body[]` (fetch message 3, full content)

**MOVE**
- Moves messages to another mailbox
- Example: `MOVE 1:5 Trash` (move messages 1-5 to Trash)

**COPY**
- Copies messages to another mailbox
- Example: `COPY 2 Archive`

**LOGOUT**
- Ends IMAP session
- Example: `D LOGOUT`

### IMAP Session Example

```bash
telnet MACHINE_IP 143
```

**Session:**
```
Connected to MACHINE_IP
* OK Dovecot (Ubuntu) ready.

A LOGIN strategos password
A OK Logged in

B SELECT inbox
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* 4 EXISTS
* 0 RECENT
B OK Select completed

C FETCH 3 body[]
* 3 FETCH (BODY[] {445}
Return-path: <user@client.thm>
From: user@client.thm
To: strategos@server.thm
Subject: Telnet email

Hello. I am using telnet to send you an email!
)
C OK Fetch completed

D LOGOUT
* BYE Logging out
D OK Logout completed
```

### IMAP Command Tags

Notice the letters (A, B, C, D) before each command:
- These are **command tags**
- Help match responses to requests
- Can be any alphanumeric string
- Server response starts with same tag

**Question:** What IMAP command retrieves the fourth email message?  
**Answer:** `FETCH 4 body[]` or `C FETCH 4 body[]`

---

## Task 9: Conclusion

This room covered fundamental application-layer protocols that power internet services we use daily.

### Protocol Summary Table

| Protocol | Transport | Default Port | Purpose |
|----------|-----------|--------------|---------|
| TELNET | TCP | 23 | Remote terminal access |
| DNS | UDP/TCP | 53 | Domain name resolution |
| HTTP | TCP | 80 | Web browsing (unencrypted) |
| HTTPS | TCP | 443 | Web browsing (encrypted) |
| FTP | TCP | 21 | File transfer |
| SMTP | TCP | 25 | Sending email |
| POP3 | TCP | 110 | Receiving email (download) |
| IMAP | TCP | 143 | Receiving email (sync) |

---

## Key Takeaways

### DNS Resolution

- Translates domain names to IP addresses
- A records for IPv4, AAAA for IPv6
- MX records point to mail servers
- CNAME creates domain aliases

### WHOIS Investigation

- Reveals domain registration information
- Shows creation, update, and expiration dates
- Privacy services can hide registrant details
- Useful for reconnaissance and research

### Web Communication

**HTTP methods:**
- GET retrieves resources
- POST submits data
- PUT creates/updates resources
- DELETE removes resources

**HTTPS adds encryption** to protect data in transit

### File Transfer

- FTP designed specifically for file operations
- More efficient than HTTP for transfers
- Supports both ASCII and binary modes
- Anonymous FTP common for public access

### Email Protocols

**Sending email:**
- SMTP handles outgoing mail
- Commands: HELO, MAIL FROM, RCPT TO, DATA

**Receiving email:**
- **POP3:** Downloads and deletes from server
- **IMAP:** Keeps synchronized across devices
- **IMAP** better for multi-device workflows

### Protocol Security

All protocols covered (except HTTPS) transmit data **unencrypted**:
- Passwords visible in network captures
- Email content readable
- FTP credentials exposed

This demonstrates the need for secure variants covered in the next room.

---

## Practical Application

### Real-World Scenarios

**Scenario 1: DNS Troubleshooting**
```bash
# Check if domain resolves
nslookup example.com

# Query specific DNS server
nslookup example.com 8.8.8.8

# Check MX records for email
nslookup -type=MX example.com
```

**Scenario 2: Testing Web Server**
```bash
# Check if web server responds
telnet webserver.com 80
GET / HTTP/1.1
Host: webserver.com

# Check specific file
GET /admin/login.php HTTP/1.1
Host: webserver.com
```

**Scenario 3: Email Server Verification**
```bash
# Test SMTP server connectivity
telnet mail.example.com 25
HELO test.com

# Verify email account exists
MAIL FROM: <test@test.com>
RCPT TO: <user@example.com>
# If "250 Accepted" - account exists
```

**Scenario 4: Domain Research**
```bash
# Find domain owner and age
whois suspicious-domain.com

# Check DNS records
nslookup -type=ANY suspicious-domain.com

# Check mail servers
nslookup -type=MX suspicious-domain.com
```

---

## Command Reference

| Command | Purpose | Example |
|---------|---------|---------|
| `nslookup <domain>` | DNS lookup | `nslookup google.com` |
| `whois <domain>` | Domain registration info | `whois example.com` |
| `telnet <ip> <port>` | Connect to service | `telnet 10.10.10.1 80` |
| `ftp <ip>` | Connect to FTP server | `ftp 192.168.1.100` |

### FTP Commands

| Command | Purpose |
|---------|---------|
| `ls` | List files |
| `get <file>` | Download file |
| `put <file>` | Upload file |
| `type ascii` | Switch to ASCII mode |
| `type binary` | Switch to binary mode |
| `quit` | Exit FTP |

---

## Next Steps

With core networking protocols mastered:
- Progress to **Networking Secure Protocols** to learn encryption
- Study **packet analysis** with Wireshark for protocol debugging
- Learn **SSL/TLS** to secure HTTP, FTP, SMTP, POP3, IMAP
- Practice **protocol fuzzing** for security testing
- Explore **network forensics** using protocol analysis

Understanding these protocols is crucial for:
- Network administration and troubleshooting
- Web application security testing
- Email security and anti-spam measures
- Incident response and network forensics
- Penetration testing and reconnaissance

---

**Room Completed:** ✓  
**Date:** 02/14/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
