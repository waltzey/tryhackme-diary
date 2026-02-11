# TryHackMe - Windows Command Line

**Room:** Windows Command Line  
**Difficulty:** Easy  
**Focus:** Windows CLI fundamentals, system information, network troubleshooting, file management, process management

---

## Overview

This room introduces the Windows Command Prompt (cmd.exe) and covers essential commands for system administration, network troubleshooting, file management, and process monitoring. While GUIs are intuitive, mastering the CLI provides significant advantages in speed, efficiency, automation, and remote management—critical skills for cybersecurity professionals.

**Learning Objectives:**
- Display basic system information
- Check and troubleshoot network configuration
- Manage files and folders
- Monitor and manage running processes

---

## Task 1: Introduction

### Why CLI Over GUI?

The command-line interface offers several advantages over graphical interfaces:

**Speed & Efficiency:** Execute tasks without leaving the keyboard. Repetitive actions become single commands instead of multiple mouse clicks.

**Lower Resource Usage:** CLIs consume minimal system resources compared to graphics-intensive GUIs, making them ideal for older hardware, cloud environments, or resource-constrained systems.

**Automation:** Creating batch files or scripts to automate repetitive tasks is straightforward with CLI commands.

**Remote Management:** SSH-based CLI access is essential for managing remote servers, routers, and IoT devices, especially over slow networks or limited-resource systems.

### Room Setup

Connect to the target machine via SSH:
```bash
ssh user@MACHINE_IP
# Password: Tryhackme123!
```

**Question:** What is the default command line interpreter in the Windows environment?  
**Answer:** `cmd.exe`

---

## Task 2: Basic System Information

### Understanding the PATH Environment Variable

Before executing commands, it's important to understand that Windows only executes commands found in the PATH environment variable.

**View all environment variables:**
```cmd
set
```

The `Path` variable shows directories where Windows searches for executables:
```
Path=C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;...
```

### Essential System Information Commands

**Check OS version:**
```cmd
ver
```
Output example:
```
Microsoft Windows [Version 10.0.17763.1821]
```

**Detailed system information:**
```cmd
systeminfo
```

This displays comprehensive information including:
- Host Name
- OS Name and Version
- OS Manufacturer and Configuration
- System Model and Type
- Processor details
- Memory (RAM) information
- Network adapter configuration
- Boot time and uptime

### Pro Tips

**Handling long output:**
```cmd
driverquery | more
```
The `more` command displays output page by page. Press `Space` to advance one page or `Ctrl+C` to exit.

**Essential utility commands:**
- `help` - Provides help information for specific commands
- `cls` - Clears the Command Prompt screen
- `command /?` - Displays help page for most commands

Getting the OS version & hostname through systeminfo while ssh:
<img width="734" height="484" alt="image" src="https://github.com/user-attachments/assets/d8a10bc8-992b-43b5-9cea-36e342d971d7" />

---

## Task 3: Network Troubleshooting

### Network Configuration Commands

**Basic network information:**
```cmd
ipconfig
```
Displays:
- IP address
- Subnet mask
- Default gateway

**Detailed network configuration:**
```cmd
ipconfig /all
```
Shows additional information:
- Physical (MAC) address
- DHCP status
- DNS servers
- DHCP lease information
- NetBIOS settings

**Common ipconfig options:**
- `ipconfig /release` - Release current IP address
- `ipconfig /renew` - Renew IP address from DHCP
- `ipconfig /flushdns` - Clear DNS resolver cache
- `ipconfig /displaydns` - Display DNS resolver cache contents

### Network Connectivity Testing

**Test connectivity to a host:**
```cmd
ping example.com
```
Output shows:
- IP address resolution
- Reply status (bytes, time, TTL)
- Packet statistics (sent, received, lost, loss percentage)
- Round trip time (minimum, maximum, average)

**Trace network route to destination:**
```cmd
tracert example.com
```
Shows each router (hop) along the path to the destination, useful for identifying network bottlenecks or connection failures.

### DNS and Connection Monitoring

**DNS lookup:**
```cmd
nslookup example.com
```
Returns the IP address(es) for a given domain using your default DNS server.

**Use specific DNS server:**
```cmd
nslookup example.com 1.1.1.1
```
Queries Cloudflare's 1.1.1.1 DNS server instead of the default.

**Monitor active network connections:**
```cmd
netstat
```
Shows established connections with protocol, local address, foreign address, and state.

**Advanced netstat usage:**
```cmd
netstat -abon
```
Options breakdown:
- `-a` - Display all connections and listening ports
- `-b` - Show executable associated with each connection
- `-o` - Display process ID (PID)
- `-n` - Use numerical form for addresses and ports

Example output:
```
Proto  Local Address          Foreign Address        State           PID
TCP    0.0.0.0:22             0.0.0.0:0              LISTENING       2116
[sshd.exe]
TCP    10.10.230.237:22       10.11.81.126:53486     ESTABLISHED     2116
[sshd.exe]
```

**Security Note:** `netstat` is crucial for incident response and identifying suspicious network activity. Look for:
- Unexpected listening ports
- Connections to suspicious IP addresses
- Unknown processes with network activity


Using netstat -abon to find services under X ports:
<img width="736" height="485" alt="image" src="https://github.com/user-attachments/assets/19c4e2e2-f81e-465e-88fc-4d7ccff51217" />

---

## Task 4: File and Disk Management

### Directory Navigation

**Show current directory:**
```cmd
cd
```

**List directory contents:**
```cmd
dir
```

**Common dir options:**
- `dir /a` - Show hidden and system files
- `dir /s` - Display files in current directory and all subdirectories
- `dir /o:n` - Sort by name
- `dir /o:d` - Sort by date

**Visual directory tree:**
```cmd
tree
```
Displays a hierarchical tree structure of directories and subdirectories.

**Change directory:**
```cmd
cd target_directory
```

**Navigate up one level:**
```cmd
cd ..
```

**Navigate to root directory:**
```cmd
cd \
```

### Directory Management

**Create directory:**
```cmd
mkdir directory_name
```

**Remove empty directory:**
```cmd
rmdir directory_name
```

**Remove directory and all contents:**
```cmd
rmdir /s directory_name
```

### File Operations

**Display file contents:**
```cmd
type filename.txt
```

**Display long files page by page:**
```cmd
more filename.txt
```

**Copy files:**
```cmd
copy source_file destination_file
```
Example:
```cmd
copy test.txt test2.txt
```

**Move files:**
```cmd
move source_file destination_directory
```
Example:
```cmd
move test2.txt ..
```

**Delete files:**
```cmd
del filename.txt
# or
erase filename.txt
```

### Wildcard Operations

Use the asterisk `*` wildcard to match multiple files:

```cmd
copy *.txt C:\backup\
```
Copies all .txt files to the backup directory.

```cmd
del *.log
```
Deletes all files with .log extension.

**Security Consideration:** Always verify wildcard commands before execution, especially with delete operations, to avoid unintended data loss.

---

## Task 5: Task and Process Management

### Viewing Running Processes

**List all running processes:**
```cmd
tasklist
```

Output shows:
- Image Name (executable)
- Process ID (PID)
- Session Name
- Session Number
- Memory Usage

### Filtering Process Output

**Filter by image name:**
```cmd
tasklist /FI "imagename eq sshd.exe"
```

**Common filters:**
- `tasklist /FI "status eq running"` - Only running processes
- `tasklist /FI "memusage gt 100000"` - Processes using >100MB RAM
- `tasklist /FI "username eq system"` - Processes running as SYSTEM

**View all filter options:**
```cmd
tasklist /?
```

### Terminating Processes

**Kill process by PID:**
```cmd
taskkill /PID 4567
```

**Kill process by image name:**
```cmd
taskkill /IM notepad.exe
```

**Force termination:**
```cmd
taskkill /F /PID 4567
```

**Security Application:** These commands are essential for incident response when dealing with malicious processes. Always:
1. Document the PID and image name before termination
2. Preserve evidence when possible
3. Understand that some malware may respawn if parent processes remain active

---

## Task 6: Conclusion

### Additional System Commands

While not covered in detail in this room, these commands are valuable for system administration:

- `chkdsk` - Checks file system and disk volumes for errors and bad sectors
- `driverquery` - Displays a list of installed device drivers  
- `sfc /scannow` - Scans system files for corruption and attempts repair

### Key Takeaways

**Essential command patterns:**
- Use `command /?` to display help for any command
- Pipe long output through `more` to view page by page: `command | more`
- Combine options for powerful results: `netstat -abon`, `dir /a /s`

**CLI advantages recap:**
- **Speed:** Direct command execution is faster than GUI navigation
- **Scripting:** Automate repetitive tasks with batch files
- **Remote Access:** Manage systems via SSH without GUI overhead
- **Resource Efficiency:** Lower memory and CPU usage
- **Precision:** Exact control over operations with parameters and filters

### Security+ Relevance

These Windows CLI commands align with CompTIA Security+ objectives:

- **Network troubleshooting:** `ipconfig`, `ping`, `tracert`, `nslookup`, `netstat`
- **System monitoring:** `systeminfo`, `tasklist`, `netstat -abon`
- **Incident response:** Identifying processes, network connections, and system information
- **Host security:** Understanding listening ports, active connections, and running processes

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Investigating Suspicious Network Activity**
```cmd
netstat -abon | findstr "ESTABLISHED"
tasklist /FI "PID eq [suspicious_PID]"
```

**Scenario 2: Quick System Health Check**
```cmd
systeminfo | findstr /C:"OS Name" /C:"OS Version" /C:"System Boot Time"
tasklist | findstr "svchost"
```

**Scenario 3: Network Troubleshooting**
```cmd
ipconfig /all
ping 8.8.8.8
tracert google.com
nslookup google.com
```

### Command Reference Quick Sheet

| Category | Command | Purpose |
|----------|---------|---------|
| **System Info** | `ver` | OS version |
| | `systeminfo` | Detailed system information |
| | `set` | Environment variables |
| **Networking** | `ipconfig` | Network configuration |
| | `ipconfig /all` | Detailed network info |
| | `ping [host]` | Test connectivity |
| | `tracert [host]` | Trace route to host |
| | `nslookup [host]` | DNS lookup |
| | `netstat -abon` | Network connections & processes |
| **Files/Dirs** | `cd` | Show/change directory |
| | `dir` | List directory contents |
| | `tree` | Directory tree structure |
| | `mkdir [name]` | Create directory |
| | `rmdir [name]` | Remove directory |
| | `copy [src] [dst]` | Copy files |
| | `move [src] [dst]` | Move files |
| | `del [file]` | Delete files |
| | `type [file]` | Display file contents |
| **Processes** | `tasklist` | List running processes |
| | `tasklist /FI [filter]` | Filter process list |
| | `taskkill /PID [pid]` | Terminate process by PID |
| | `taskkill /IM [name]` | Terminate process by name |
| **Utilities** | `help` | Command help |
| | `[cmd] /?` | Command-specific help |
| | `cls` | Clear screen |
| | `more` | Page through output |

---

## Next Steps

With a solid foundation in Windows Command Line:
- Progress to **Windows PowerShell** for more advanced scripting capabilities
- Explore **Windows Event Viewer** for log analysis
- Practice **Active Directory** commands for domain management
- Learn **Windows Registry** manipulation via CLI

The command line is a fundamental skill for system administration, penetration testing, and incident response. Regular practice with these commands builds muscle memory and increases efficiency in real-world security operations.

---

**Room Completed:** ✓  
**Date:** 02/11/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
