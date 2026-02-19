# TryHackMe - Wireshark: The Basics

**Room:** Wireshark: The Basics  
**Difficulty:** Easy  
**Focus:** Network packet analysis, protocol dissection, packet filtering, traffic inspection

---

## Overview

Wireshark is the industry-standard open-source network packet analyzer. This room introduces fundamental packet analysis skills including navigating the interface, understanding protocol layers, filtering traffic, and extracting meaningful information from captures.

**Learning Objectives:**
- Master Wireshark interface navigation and configuration
- Understand packet structure across OSI layers
- Apply display filters to isolate traffic of interest
- Extract files and follow network streams
- Analyze protocol details and identify anomalies

---

## Task 1: Introduction

### What is Wireshark?

**Wireshark** is a cross-platform network packet analyzer that captures and inspects network traffic in real-time or from saved capture files (PCAP format).

**Key capabilities:**
- Live traffic capture and analysis
- Deep packet inspection across all OSI layers
- Protocol decoding for hundreds of protocols
- Advanced filtering and search capabilities
- Traffic reconstruction and file extraction

### Use Cases

**Network Troubleshooting:**
- Identify network bottlenecks and congestion
- Diagnose connectivity issues
- Analyze performance problems

**Security Analysis:**
- Detect rogue hosts and suspicious traffic
- Investigate security incidents
- Analyze malware communication patterns
- Identify data exfiltration attempts

**Protocol Analysis:**
- Learn protocol behavior and structure
- Verify proper protocol implementation
- Debug application network issues

### Important Limitations

**Wireshark is NOT:**
- An Intrusion Detection System (IDS)
- An Intrusion Prevention System (IPS)
- A packet modifier or injector

**Wireshark only reads and displays packets** - detection of anomalies requires analyst knowledge and investigation skills.

### Lab Files

**http1.pcapng:** Used to simulate screenshots and follow along  
**Exercise.pcapng:** Used to answer room questions

**Question:** Which file is used to simulate the screenshots?  
**Answer:** `http1.pcapng`

**Question:** Which file is used to answer the questions?  
**Answer:** `Exercise.pcapng`

---

## Task 2: Tool Overview

### Wireshark GUI Components

The Wireshark interface consists of five main sections:

**1. Toolbar**
- Packet sniffing controls
- Processing functions (filtering, sorting, summarizing)
- Export and merge capabilities

**2. Display Filter Bar**
- Main query and filtering section
- Apply filters to narrow down visible packets
- Syntax highlighting for valid/invalid filters

**3. Recent Files**
- Quick access to previously opened captures
- Double-click to reopen files

**4. Capture Filter and Interfaces**
- Network interface selection for live capture
- Capture filter configuration
- Interface examples: `lo` (loopback), `eth0`, `ens33`

**5. Status Bar**
- Tool status and profile information
- Packet count statistics
- Current capture/analysis information

### Loading PCAP Files

**Three methods to load captures:**

1. **File Menu:** File → Open
2. **Drag and Drop:** Drag PCAP file into Wireshark window
3. **Double-Click:** Double-click PCAP file (if Wireshark is default handler)

### Packet Display Panes

Once a capture is loaded, three panes display packet information:

**Packet List Pane (Top)**
- Summary of each packet
- Source and destination addresses
- Protocol information
- Packet length and info column
- Click to select packet for detailed inspection

**Packet Details Pane (Middle)**
- Hierarchical protocol breakdown
- Expandable protocol layers
- Field-by-field analysis
- Clicking fields highlights corresponding bytes

**Packet Bytes Pane (Bottom)**
- Hexadecimal representation
- ASCII decoded text
- Highlights selected field from details pane

### Packet Coloring

Wireshark uses color coding to quickly identify protocol types and conditions:

**Default Color Scheme:**
- **Light Purple:** TCP traffic
- **Light Blue:** UDP traffic
- **Black:** Packets with errors
- **Light Green:** HTTP traffic
- **Yellow:** Routing protocols
- **Dark Gray:** TCP packets with issues

**Two types of coloring rules:**

**Temporary Rules:**
- Active only during current session
- Created via right-click menu
- Lost when closing Wireshark

**Permanent Rules:**
- Saved in profile preferences
- Available in future sessions
- Configured via View → Coloring Rules

**Toggle coloring:** View → Colorize Packet List

### Traffic Sniffing (Live Capture)

**Capture controls:**
- **Blue shark button:** Start capture
- **Red square button:** Stop capture
- **Green shark button:** Restart capture

**Status bar shows:**
- Active capture interface
- Number of packets collected
- Capture duration

### Merging PCAP Files

Combine multiple capture files into one:

**Steps:**
1. File → Merge
2. Select second PCAP file
3. Wireshark shows total packet count
4. Click Open to merge
5. Save merged file before analysis

**Use case:** Combining captures from different time periods or network segments

### File Details

View capture file metadata and statistics:

**Access:** Statistics → Capture File Properties  
**OR:** Click PCAP icon in lower-left status bar

**Information displayed:**
- File hash (MD5, SHA256)
- Capture start/end time
- File comments
- Capture interface
- Packet statistics
- File size

**Question:** Use the "Exercise.pcapng" file. Read the "capture file comments". What is the flag?  
**Answer:** `[View Statistics → Capture File Properties → Comments section]`

**Question:** What is the total number of packets?  
**Answer:** `[Check Capture File Properties → Packets field]`

**Question:** What is the SHA256 hash value of the capture file?  
**Answer:** `[View Capture File Properties → Hash section]`

---

## Task 3: Packet Dissection

### Understanding Packet Dissection

**Packet dissection** is the process of decoding and analyzing packet contents by breaking them down into protocol layers and fields based on the OSI model.

**Wireshark supports:**
- Hundreds of protocols out-of-the-box
- Custom dissector development
- Protocol-specific field breakdown

### Packet Structure (OSI Layers)

Each packet contains 5-7 layers depending on the protocols involved:

**Layer 1: Frame/Packet (Physical)**
- Frame number and length
- Capture timestamp
- Time since previous packet
- Interface information

**Example fields:**
```
Frame 27: 314 bytes on wire
Arrival Time: Nov 15, 2023 10:30:45
Time since previous: 0.003 seconds
Interface: eth0
```

**Layer 2: Data Link (MAC)**
- Source MAC address
- Destination MAC address
- Ethernet type

**Example fields:**
```
Ethernet II
    Destination: 00:0c:29:4f:71:a3
    Source: 00:50:56:c0:00:08
    Type: IPv4 (0x0800)
```

**Layer 3: Network (IP)**
- Source IP address
- Destination IP address
- TTL (Time To Live)
- Protocol (TCP/UDP/ICMP)
- IP header checksum

**Example fields:**
```
Internet Protocol Version 4
    Source: 192.168.1.100
    Destination: 93.184.216.34
    TTL: 64
    Protocol: TCP (6)
```

**Layer 4: Transport (TCP/UDP)**
- Source port
- Destination port
- Sequence numbers
- Acknowledgment numbers
- TCP flags (SYN, ACK, FIN, etc.)
- Window size

**Example fields:**
```
Transmission Control Protocol
    Source Port: 54321
    Destination Port: 80
    Sequence number: 1
    Flags: 0x002 (SYN)
```

**Layer 5-7: Application Data**
- Protocol-specific headers (HTTP, DNS, FTP, etc.)
- Application payload
- File contents or data transfers

**Example fields:**
```
Hypertext Transfer Protocol
    GET /index.html HTTP/1.1
    Host: example.com
    User-Agent: Mozilla/5.0
```

### Packet Details Interaction

**Click on any field in Packet Details Pane:**
- Highlights corresponding bytes in Packet Bytes Pane
- Shows field value and structure
- Displays field filters for quick filtering

**Double-click packet:**
- Opens details in separate window
- Useful for side-by-side comparison

### Protocol Error Detection

Wireshark identifies protocol anomalies:
- Checksum errors
- Malformed packets
- TCP retransmissions
- Out-of-order segments
- Missing ACKs

**Question:** Use the "Exercise.pcapng" file. View packet number 38. Which markup language is used under the HTTP protocol?  
**Answer:** `[Expand HTTP layer in packet 38, look for Content-Type or payload format]`

**Question:** What is the arrival date of the packet? (Answer format: Month/Day/Year)  
**Answer:** `[Check Frame layer → Arrival Time field]`

**Question:** What is the TTL value?  
**Answer:** `[Check Internet Protocol layer → Time to live field]`

**Question:** What is the TCP payload size?  
**Answer:** `[Check TCP layer → TCP Segment Len or Payload size]`

---

## Task 4: Packet Navigation

### Packet Numbering

Wireshark assigns unique sequential numbers to each packet:
- Helps track position in large captures
- Enables referencing specific packets
- Facilitates collaboration and documentation

### Go To Packet

**Multiple navigation methods:**

**Direct packet number:**
- Go → Go to Packet (Ctrl+G)
- Enter packet number
- Jump directly to that packet

**Navigation buttons:**
- Go → Next Packet (Down arrow)
- Go → Previous Packet (Up arrow)
- Go → First Packet (Home)
- Go → Last Packet (End)

**Conversation tracking:**
- Go → Next Packet in Conversation
- Go → Previous Packet in Conversation
- Follows specific TCP/UDP streams

### Finding Packets

Search for specific content within packets:

**Access:** Edit → Find Packet (Ctrl+F)

**Search types:**

**1. Display Filter**
- Uses Wireshark filter syntax
- Example: `http.request.method == "POST"`

**2. Hex Value**
- Search for hexadecimal patterns
- Example: `47 45 54` (ASCII "GET")

**3. String**
- Plain text search
- Case insensitive by default
- Can enable case sensitivity

**4. Regular Expression (Regex)**
- Advanced pattern matching
- Example: `pass(word|wd)?`

**Search scope options:**
- Packet List
- Packet Details
- Packet Bytes

**Important:** Choose correct pane to search - information in Packet Details won't be found if searching in Packet List.

### Marking Packets

Highlight packets of interest for further investigation:

**How to mark:**
- Edit → Mark Packet (Ctrl+M)
- Right-click → Mark/Unmark Packet

**Marked packet properties:**
- Displayed in black color
- Ignores original protocol coloring
- Temporary (lost when closing file)
- Useful for tracking suspicious packets

**Use cases:**
- Flag packets for deeper analysis
- Mark malicious traffic patterns
- Identify key events in timeline

### Packet Comments

Add notes to specific packets:

**How to add comments:**
- Edit → Packet Comment
- Right-click → Packet Comment

**Comment properties:**
- Persistent (saved in PCAP file)
- Survive file close/reopen
- Useful for collaboration
- Document findings and observations

**Use cases:**
- Document suspicious activity
- Leave notes for other analysts
- Track investigation progress
- Explain packet significance

### Exporting Packets

Extract subset of packets for focused analysis:

**Export options:**

**Export Specified Packets:**
- File → Export Specified Packets
- Choose packet range
- Save as new PCAP file

**Export Selected Packet:**
- Right-click → Export Selected Packet Bytes
- Save single packet data

**Use cases:**
- Share suspicious packets with team
- Reduce file size for analysis
- Focus on specific time period
- Remove irrelevant traffic

### Export Objects (Files)

Extract files transferred over the network:

**Supported protocols:**
- HTTP
- SMB
- TFTP
- DICOM
- IMF (email attachments)

**How to export:**
- File → Export Objects → [Protocol]
- Select files from list
- Save to disk

**Example:**
```
File → Export Objects → HTTP
[List shows transferred files]
Select file → Save
```

**Use cases:**
- Extract malware samples
- Recover documents from traffic
- Analyze file transfers
- Investigate data exfiltration

### Time Display Format

Adjust how timestamps are displayed:

**Access:** View → Time Display Format

**Common formats:**

**Seconds Since Beginning of Capture (default):**
- `0.000000` for first packet
- Relative to capture start

**Date and Time of Day (UTC):**
- `2023-11-15 14:30:45.123456`
- Absolute timestamps
- **Most useful for analysis**

**Time of Day:**
- Shows only time, no date

**Seconds Since Previous Packet:**
- Shows time delta
- Useful for latency analysis

### Expert Info

Wireshark's built-in anomaly detection:

**Access:** Analyze → Expert Information  
**OR:** Click colored circle in lower-left status bar

**Severity Levels:**

| Severity | Color | Meaning |
|----------|-------|---------|
| Chat | Blue | Normal workflow information |
| Note | Cyan | Notable events (e.g., error codes) |
| Warn | Yellow | Warnings (unusual behavior) |
| Error | Red | Problems (malformed packets) |

**Common information groups:**

- **Checksum:** Checksum validation errors
- **Comment:** Packet comments detected
- **Deprecated:** Obsolete protocol usage
- **Malformed:** Invalid packet structure
- **Sequence:** TCP sequence issues
- **Reassembly:** Packet reassembly problems

**Important:** Expert info provides suggestions, not definitive conclusions. False positives/negatives are possible.

**Question:** Use the "Exercise.pcapng" file. Search the "r4w" string in packet details. What is the name of artist 1?  
**Answer:** `[Edit → Find Packet → String → "r4w" → Search in Packet Details]`

**Question:** Go to packet 12 and read the packet comments. What is the answer?  
**Answer:** `[Go to packet 12 → Right-click → Packet Comment]`

**Question:** There is a ".txt" file inside the capture file. Find the file and read it; what is the alien's name?  
**Answer:** `[File → Export Objects → HTTP → Find .txt file → Save and open]`

**Question:** Look at the expert info section. What is the number of warnings?  
**Answer:** `[Analyze → Expert Information → Count yellow warnings]`

---

## Task 5: Packet Filtering

### Filter Types

**Capture Filters:**
- Applied BEFORE capturing
- Reduces captured data
- More efficient for live captures
- Uses BPF (Berkeley Packet Filter) syntax
- Cannot be changed after capture

**Display Filters:**
- Applied AFTER capturing
- Only affects what's displayed
- Can be changed anytime
- More flexible syntax
- **Focus of this task**

### Apply as Filter

Quick filtering by clicking packet fields:

**Steps:**
1. Click on field in Packet Details
2. Right-click → Apply as Filter
3. Choose filter type:
   - **Selected:** Show only this value
   - **Not Selected:** Hide this value
   - **... and Selected:** Add to existing filter (AND)
   - **... or Selected:** Add to existing filter (OR)

**Example:**
- Click on IP address `192.168.1.100`
- Apply as Filter → Selected
- Result: Shows only packets from/to that IP

### Conversation Filter

Filter all packets in a specific network conversation:

**Access:** Right-click → Conversation Filter

**Filters by:**
- IP addresses (source and destination)
- Port numbers (source and destination)
- Shows complete bidirectional communication

**Use case:** Track entire TCP session or UDP exchange

### Colorize Conversation

Highlight conversation packets without filtering:

**Access:** Right-click → Colorize Conversation

**Behavior:**
- Highlights related packets
- Doesn't hide other traffic
- Overrides standard color rules
- Temporary coloring

**Reset:** View → Colorize Conversation → Reset Colourization

### Prepare as Filter

Create filter without applying immediately:

**Access:** Right-click → Prepare as Filter

**Behavior:**
- Adds filter to display filter bar
- Doesn't execute until Enter pressed
- Allows combining multiple conditions

**Building complex filters:**
1. Prepare first condition
2. Right-click another field
3. Choose "... and Selected" or "... or Selected"
4. Press Enter to apply

### Apply as Column

Add custom columns to Packet List Pane:

**Steps:**
1. Click on field in Packet Details
2. Right-click → Apply as Column
3. New column appears in Packet List

**Use cases:**
- Track specific field across all packets
- Compare values quickly
- Identify patterns

**Manage columns:**
- Right-click column header to remove
- Drag columns to reorder
- Preference → Columns for advanced settings

### Follow Stream

Reconstruct and view application-level data:

**Access:** Right-click → Follow → TCP/UDP/HTTP Stream

**Color coding:**
- **Red:** Client to server
- **Blue:** Server to client

**Features:**
- Shows complete conversation
- Displays unencrypted data
- Can save stream to file
- Automatic filter applied

**Security use case:**
- Extract plaintext passwords
- View transmitted files
- Analyze malware C2 communication
- Investigate data exfiltration

**Important:** Following stream applies filter - click X button on filter bar to return to full capture.

### Basic Display Filter Syntax

**Filter by Protocol:**
```
http
dns
ftp
ssh
```

**Filter by Port:**
```
tcp.port == 80
udp.port == 53
tcp.port == 22 or tcp.port == 23
```

**Filter by IP Address:**
```
ip.addr == 192.168.1.100
ip.src == 10.0.0.5
ip.dst == 8.8.8.8
```

**Combine filters with operators:**
```
http and ip.addr == 192.168.1.100
tcp.port == 80 or tcp.port == 443
not arp
```

**Filter by packet content:**
```
http.request.method == "POST"
http.request.uri contains "login"
dns.qry.name contains "malware"
```

**Question:** Use the "Exercise.pcapng" file. Go to packet number 4. Right-click on the "Hypertext Transfer Protocol" and apply it as a filter. What is the filter query?  
**Answer:** `http`

**Question:** What is the number of displayed packets?  
**Answer:** `[Check status bar after filter applied]`

**Question:** Go to packet number 33790, follow the HTTP stream. What is the total number of artists?  
**Answer:** `[Right-click → Follow → HTTP Stream → Count artists in response]`

**Question:** What is the name of the second artist?  
**Answer:** `[Read through HTTP stream response data]`

---

## Task 6: Conclusion

This room covered Wireshark fundamentals essential for network analysis.

**Skills acquired:**
- Wireshark interface navigation
- Protocol dissection and OSI layer analysis
- Packet filtering and searching
- Stream following and file extraction
- Expert information interpretation

---

## Key Takeaways

### Wireshark Workflow

**1. Load Capture**
- Open PCAP file
- Review file properties
- Check packet count and time span

**2. Initial Analysis**
- Review Protocol Hierarchy (Statistics menu)
- Check Expert Info for anomalies
- Identify conversations (Statistics → Conversations)

**3. Apply Filters**
- Narrow down to traffic of interest
- Use display filters for specific protocols/IPs
- Follow streams for application data

**4. Deep Dive**
- Examine packet details layer by layer
- Export suspicious files
- Mark and comment on key packets

**5. Document Findings**
- Add packet comments
- Export relevant packets
- Create analysis report

### Common Analysis Scenarios

**Scenario 1: Investigating Suspicious HTTP Traffic**
```
1. Filter: http
2. Look for POST requests: http.request.method == "POST"
3. Follow stream to view submitted data
4. Check for credentials or sensitive data
5. Export any downloaded files
```

**Scenario 2: Identifying Malware C2**
```
1. Check Expert Info for unusual patterns
2. Look for repeated connections: tcp.stream
3. Filter by suspicious IPs
4. Follow TCP streams for C2 commands
5. Export malware samples if present
```

**Scenario 3: Finding Data Exfiltration**
```
1. Filter outbound traffic: ip.src == [internal_ip]
2. Look for large transfers: frame.len > 1000
3. Check DNS for tunneling: dns
4. Export objects to find stolen files
5. Follow streams to identify data
```

### Essential Filters Reference

| Purpose | Filter |
|---------|--------|
| HTTP traffic | `http` |
| HTTPS traffic | `tls` or `ssl` |
| DNS queries | `dns` |
| Specific IP | `ip.addr == 192.168.1.1` |
| Port 80 | `tcp.port == 80` |
| POST requests | `http.request.method == "POST"` |
| Failed connections | `tcp.flags.reset == 1` |
| Large packets | `frame.len > 1000` |
| Errors only | `expert.severity == error` |

---

## Practical Application

### Real-World Usage

**Network Troubleshooting:**
1. Capture traffic during problem period
2. Filter by affected protocol
3. Check Expert Info for errors
4. Analyze timing (time deltas)
5. Identify bottlenecks or failures

**Security Incident Response:**
1. Obtain PCAP from affected timeframe
2. Filter by IOCs (IPs, domains, ports)
3. Follow streams to reconstruct attacks
4. Export malicious files for analysis
5. Document timeline of events

**Malware Analysis:**
1. Capture malware network activity
2. Identify C2 servers (repeated connections)
3. Extract downloaded payloads
4. Analyze protocol usage
5. Create network signatures

---

## Next Steps

With Wireshark basics mastered:
- Progress to **Wireshark: Packet Operations** for advanced techniques
- Practice with **real-world PCAP datasets**
- Learn **advanced filtering** with complex expressions
- Study **specific protocols** in depth (HTTP, DNS, SMB)
- Explore **TLS decryption** with keys
- Master **statistical analysis** features

Wireshark is an essential tool for any network analyst, security professional, or systems administrator. The skills learned here form the foundation for advanced network forensics and security analysis.

---

**Room Completed:** ✓  
**Date:** 02/14/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
