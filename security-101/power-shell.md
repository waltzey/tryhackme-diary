# TryHackMe - Windows PowerShell

**Room:** Windows PowerShell  
**Difficulty:** Easy  
**Focus:** PowerShell fundamentals, object-oriented shell concepts, cmdlets, piping, system administration

---

## Overview

PowerShell is Microsoft's cross-platform task automation solution built on the .NET framework. Unlike traditional text-based shells, PowerShell is object-oriented, meaning it passes structured data between commands rather than plain text. This fundamental difference makes PowerShell significantly more powerful for system administration, automation, and security operations.

**Learning Objectives:**
- Understand PowerShell's object-oriented architecture
- Master the Verb-Noun cmdlet structure
- Navigate the file system and manage files
- Use piping, filtering, and sorting for data manipulation
- Retrieve system and network information
- Monitor processes, services, and network connections

---

## Task 2: What is PowerShell?

### Definition and Core Concepts

From Microsoft's official documentation: "PowerShell is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework."

PowerShell combines:
- **Command-line interface** for interactive system management
- **Scripting language** built on .NET framework
- **Configuration management** capabilities

Initially Windows-exclusive, PowerShell became cross-platform in 2016 with PowerShell Core, now supporting Windows, macOS, and Linux.

### A Brief History

PowerShell emerged from a specific problem: Windows and Unix systems handled operations fundamentally differently. While Unix treated everything as text files, Windows relied on structured APIs and data. This made porting Unix tools to Windows impractical.

Jeffrey Snover, a Microsoft engineer, developed PowerShell using an **object-oriented approach** that leveraged the .NET framework. Released in 2006, PowerShell allowed administrators to manipulate structured data (objects) rather than parsing text output, enabling deeper system integration.

### The Object-Oriented Advantage

**Traditional shells (cmd.exe, bash):**
- Output plain text
- Require parsing and text manipulation
- Limited data structure preservation

**PowerShell:**
- Outputs objects with properties and methods
- Preserves data structure throughout the pipeline
- Enables complex data manipulation without parsing

**Example - Understanding Objects:**

Think of a car as an object:
- **Properties:** Color, Model, FuelLevel
- **Methods:** Drive(), HonkHorn(), Refuel()

In PowerShell, a file object has:
- **Properties:** Name, Length, CreationTime, Extension
- **Methods:** Delete(), CopyTo(), MoveTo()

This object-based approach allows you to directly access and manipulate specific properties without text parsing.

**Question:** What do we call the advanced approach used to develop PowerShell?  
**Answer:** `object-oriented`

---

## Task 3: PowerShell Basics

### Launching PowerShell

**From Windows GUI:**
- Start Menu: Search "powershell"
- Run Dialog: `Win + R`, type `powershell`
- File Explorer: Type `powershell` in address bar
- Task Manager: File → Run new task → `powershell`

**From Command Prompt:**
```cmd
powershell
```

After launching, you'll see the PS prompt:
```powershell
PS C:\Users\captain>
```

### Cmdlet Structure: Verb-Noun Convention

PowerShell commands are called **cmdlets** (command-lets). They follow a consistent Verb-Noun naming pattern:

```powershell
Get-Content      # Retrieves content
Set-Location     # Changes directory
Remove-Item      # Deletes files/folders
New-Item         # Creates files/folders
```

This structure makes cmdlets intuitive and discoverable.

### Essential Discovery Cmdlets

**List all available commands:**
```powershell
Get-Command
```

Output shows:
- CommandType (Cmdlet, Function, Alias)
- Name
- Version
- Source module

**Filter commands by type:**
```powershell
Get-Command -CommandType "Function"
Get-Command -CommandType "Cmdlet"
```

**Get help for any cmdlet:**
```powershell
Get-Help Get-Date
Get-Help Get-Date -Examples
Get-Help Get-Date -Detailed
Get-Help Get-Date -Full
```

**View command aliases:**
```powershell
Get-Alias
```

Common aliases:
- `dir` → `Get-ChildItem`
- `cd` → `Set-Location`
- `cat` → `Get-Content`
- `cls` → `Clear-Host`
- `echo` → `Write-Output`

### Extending PowerShell with Modules

**Search online repositories:**
```powershell
Find-Module -Name "PowerShell*"
```

**Install modules:**
```powershell
Install-Module -Name "ModuleName"
```

**Note:** These commands require internet connectivity and may prompt for repository trust.

### Practical Examples

**Question:** How would you retrieve a list of commands that start with the verb Remove?  
**Answer:** `Get-Command -Verb Remove`

**Question:** What cmdlet has its traditional counterpart echo as an alias?  
**Answer:** `Write-Output`

**Question:** What is the command to retrieve some example usage for the cmdlet New-LocalUser?  
**Answer:** `Get-Help New-LocalUser -Examples`

---

## Task 4: Navigating the File System and Working with Files

### Directory Navigation

**View current directory:**
```powershell
Get-Location
# or use alias
pwd
```

**List directory contents:**
```powershell
Get-ChildItem
# or use aliases
dir
ls
```

**Useful Get-ChildItem options:**
```powershell
Get-ChildItem -Force              # Show hidden files
Get-ChildItem -Recurse            # Include subdirectories
Get-ChildItem -Path C:\Windows    # List specific path
```

**Change directory:**
```powershell
Set-Location -Path "C:\Users"
# or use alias
cd Documents
```

**Navigate up one level:**
```powershell
cd ..
```

**Navigate to root:**
```powershell
cd \
```

### Directory Management

**Create directory:**
```powershell
New-Item -Path ".\backup" -ItemType "Directory"
# or use alias
mkdir backup
```

**Remove empty directory:**
```powershell
Remove-Item -Path ".\backup"
# or use alias
rmdir backup
```

**Remove directory with contents:**
```powershell
Remove-Item -Path ".\backup" -Recurse -Force
```

### File Operations

**Create new file:**
```powershell
New-Item -Path ".\notes.txt" -ItemType "File"
```

**View file contents:**
```powershell
Get-Content -Path ".\notes.txt"
# or use alias
cat notes.txt
type notes.txt
```

**Copy files:**
```powershell
Copy-Item -Path ".\source.txt" -Destination ".\backup\source.txt"
# or use alias
copy source.txt backup\
```

**Move files:**
```powershell
Move-Item -Path ".\file.txt" -Destination "C:\Archive\"
# or use alias
move file.txt C:\Archive\
```

**Delete files:**
```powershell
Remove-Item -Path ".\file.txt"
# or use aliases
del file.txt
rm file.txt
```

### Wildcard Operations

Use `*` to match multiple files:

```powershell
Copy-Item -Path "*.log" -Destination "C:\Logs\"
Remove-Item -Path "*.tmp"
Get-ChildItem -Path "*.txt"
```

### Practical Examples

**Question:** What cmdlet can you use instead of the traditional Windows command type?  
**Answer:** `Get-Content`

**Question:** What PowerShell command would you use to display the content of the "C:\Users" directory?  
**Answer:** `Get-ChildItem -Path C:\Users`

---

## Task 5: Piping, Filtering, and Sorting Data

### Understanding PowerShell Piping

Piping (`|`) passes objects from one cmdlet to another. Unlike traditional shells that pass text, PowerShell passes complete objects with all their properties and methods.

**Basic piping example:**
```powershell
Get-ChildItem | Sort-Object Length
```

Here, `Get-ChildItem` outputs file objects, and `Sort-Object` sorts them by their `Length` property.

### Sorting with Sort-Object

**Sort by property:**
```powershell
Get-ChildItem | Sort-Object Name
Get-ChildItem | Sort-Object Length
Get-ChildItem | Sort-Object LastWriteTime
```

**Sort in descending order:**
```powershell
Get-ChildItem | Sort-Object Length -Descending
```

### Filtering with Where-Object

Filter objects based on conditions:

**Basic filtering:**
```powershell
Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"
```

**Comparison operators:**
- `-eq` : Equal to
- `-ne` : Not equal to
- `-gt` : Greater than
- `-ge` : Greater than or equal to
- `-lt` : Less than
- `-le` : Less than or equal to
- `-like` : Pattern matching (supports wildcards)
- `-match` : Regex pattern matching

**Examples:**
```powershell
# Files larger than 1KB
Get-ChildItem | Where-Object Length -gt 1024

# Files NOT .txt
Get-ChildItem | Where-Object Extension -ne ".txt"

# Files matching pattern
Get-ChildItem | Where-Object Name -like "ship*"
```

### Selecting Properties with Select-Object

Display specific properties or limit results:

**Select specific properties:**
```powershell
Get-ChildItem | Select-Object Name, Length, LastWriteTime
```

**Limit number of results:**
```powershell
Get-ChildItem | Select-Object -First 5
Get-ChildItem | Select-Object -Last 3
```

**Skip items:**
```powershell
Get-ChildItem | Select-Object -Skip 2
```

### Searching Text with Select-String

Search for text patterns within files (similar to `grep` or `findstr`):

**Basic search:**
```powershell
Select-String -Path ".\log.txt" -Pattern "error"
```

**Search multiple files:**
```powershell
Select-String -Path "*.log" -Pattern "failed"
```

**Case-sensitive search:**
```powershell
Select-String -Path ".\file.txt" -Pattern "Error" -CaseSensitive
```

**Regex support:**
```powershell
Select-String -Path ".\data.txt" -Pattern "\d{3}-\d{3}-\d{4}"
```

### Complex Pipeline Example

Find the largest file in a directory:

```powershell
Get-ChildItem | Sort-Object Length -Descending | Select-Object -First 1
```

Find all .txt files larger than 1KB:

```powershell
Get-ChildItem | Where-Object Extension -eq ".txt" | Where-Object Length -gt 1024
```

---

## Task 6: System and Network Information

### System Information Cmdlets

**Comprehensive system information:**
```powershell
Get-ComputerInfo
```

Returns detailed information including:
- OS version and edition
- Hardware specifications
- BIOS details
- Installation date
- System architecture
- Domain information

**List local user accounts:**
```powershell
Get-LocalUser
```

Displays:
- Username
- Enabled status
- Description
- Last logon time

**Filter users:**
```powershell
Get-LocalUser | Where-Object Enabled -eq $true
```

### Network Configuration

**Network interface configuration:**
```powershell
Get-NetIPConfiguration
```

Shows:
- Interface name and index
- IP addresses (IPv4 and IPv6)
- Default gateway
- DNS servers

**Detailed IP address information:**
```powershell
Get-NetIPAddress
```

Displays all IP addresses with:
- Address family (IPv4/IPv6)
- Interface details
- Address state
- Prefix length

**Filter for IPv4 only:**
```powershell
Get-NetIPAddress | Where-Object AddressFamily -eq "IPv4"
```

### Real-Time System Analysis

**View running processes:**
```powershell
Get-Process
```

Displays:
- Process ID (PID)
- Process name
- CPU usage
- Memory usage (PM, WS)
- Handle count

**Sort processes by memory usage:**
```powershell
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
```

**Get specific process:**
```powershell
Get-Process -Name "powershell"
```

**View system services:**
```powershell
Get-Service
```

Shows:
- Service name
- Display name
- Status (Running, Stopped)

**Filter running services:**
```powershell
Get-Service | Where-Object Status -eq "Running"
```

**Start/stop services:**
```powershell
Stop-Service -Name "ServiceName"
Start-Service -Name "ServiceName"
Restart-Service -Name "ServiceName"
```

### Network Connection Monitoring

**View active TCP connections:**
```powershell
Get-NetTCPConnection
```

Displays:
- Local address and port
- Remote address and port
- Connection state
- Owning process ID

**Filter established connections:**
```powershell
Get-NetTCPConnection | Where-Object State -eq "Established"
```

**Find connections by port:**
```powershell
Get-NetTCPConnection | Where-Object LocalPort -eq 80
```

**Identify process behind connection:**
```powershell
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess
```

Then use the OwningProcess (PID) with:
```powershell
Get-Process -Id <PID>
```

### File Integrity with Get-FileHash

Generate file hashes for integrity verification:

**Default SHA256 hash:**
```powershell
Get-FileHash -Path ".\file.txt"
```

**Specify algorithm:**
```powershell
Get-FileHash -Path ".\file.txt" -Algorithm MD5
Get-FileHash -Path ".\file.txt" -Algorithm SHA1
Get-FileHash -Path ".\file.txt" -Algorithm SHA256
Get-FileHash -Path ".\file.txt" -Algorithm SHA512
```

**Hash multiple files:**
```powershell
Get-ChildItem *.exe | Get-FileHash
```

### Alternate Data Streams (ADS)

View hidden data streams attached to files:

```powershell
Get-Item -Path ".\file.txt" -Stream *
```

Output shows:
- `:$DATA` - Default data stream (actual file content)
- Additional streams - ADS attached to the file

**Read ADS content:**
```powershell
Get-Content -Path ".\file.txt" -Stream "StreamName"
```

### Practical Applications

**Question:** Other than your current user and the default "Administrator" account, what other user is enabled on the target machine?  
*Navigate through local users to find enabled accounts.*

**Question:** What property retrieved by default by Get-NetTCPConnection contains information about the process that has started the connection?  
**Answer:** `OwningProcess`

---

## Task 7: Scripting and Remote Execution

### Introduction to PowerShell Scripting

PowerShell scripting automates repetitive tasks by executing a series of commands stored in `.ps1` files. Scripts can:
- Automate system administration tasks
- Process and analyze data
- Perform security assessments
- Manage configurations across multiple systems

### Remote Command Execution with Invoke-Command

**Execute command on remote computer:**
```powershell
Invoke-Command -ComputerName "Server01" -ScriptBlock { Get-Service }
```

**Execute script on remote computer:**
```powershell
Invoke-Command -FilePath "C:\Scripts\audit.ps1" -ComputerName "Server01"
```

**Execute with credentials:**
```powershell
Invoke-Command -ComputerName "Server01" -Credential "Domain\User" -ScriptBlock { Get-Process }
```

**Execute on multiple computers:**
```powershell
Invoke-Command -ComputerName "Server01", "Server02", "Server03" -ScriptBlock { Get-ComputerInfo }
```

### Real-World Use Cases

**Blue Team (Defensive):**
- Automate log analysis across multiple systems
- Detect anomalies in system configurations
- Extract indicators of compromise (IOCs)
- Automate incident response procedures
- Monitor system integrity
- Scan for malware signatures

**Red Team (Offensive):**
- System enumeration during penetration tests
- Execute payloads on target systems
- Lateral movement within networks
- Credential harvesting
- Obfuscated script execution to bypass defenses

**System Administration:**
- Automate security policy enforcement
- Manage configurations at scale
- Perform integrity checks
- Monitor system health
- Respond automatically to security events
- Remote system management

### Example Scripting Scenarios

**Automated user audit:**
```powershell
Get-LocalUser | Where-Object Enabled -eq $true | Select-Object Name, LastLogon | Export-Csv "audit.csv"
```

**Service monitoring:**
```powershell
Get-Service | Where-Object Status -ne "Running" | Export-Csv "stopped_services.csv"
```

**Network connection analysis:**
```powershell
Get-NetTCPConnection | Where-Object State -eq "Established" | Select-Object LocalAddress, RemoteAddress, OwningProcess
```

**Question:** What is the syntax to execute the command Get-Service on a remote computer named "RoyalFortune"? Assume you don't need to provide credentials to establish the connection.  
**Answer:** `Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }`

---

## Command Reference

### File System Navigation
| Cmdlet | Alias | Purpose |
|--------|-------|---------|
| `Get-Location` | `pwd`, `gl` | Show current directory |
| `Set-Location` | `cd`, `sl` | Change directory |
| `Get-ChildItem` | `dir`, `ls`, `gci` | List directory contents |
| `New-Item` | `mkdir`, `ni` | Create file/directory |
| `Remove-Item` | `del`, `rm`, `ri` | Delete file/directory |
| `Copy-Item` | `copy`, `cp`, `ci` | Copy file/directory |
| `Move-Item` | `move`, `mv`, `mi` | Move file/directory |
| `Get-Content` | `cat`, `type`, `gc` | Read file contents |

### Data Manipulation
| Cmdlet | Purpose |
|--------|---------|
| `Where-Object` | Filter objects by properties |
| `Select-Object` | Select properties or limit results |
| `Sort-Object` | Sort objects by properties |
| `Select-String` | Search text patterns in files |
| `Measure-Object` | Calculate statistics (count, sum, average) |

### System Information
| Cmdlet | Purpose |
|--------|---------|
| `Get-ComputerInfo` | Comprehensive system information |
| `Get-LocalUser` | List local user accounts |
| `Get-Process` | List running processes |
| `Get-Service` | List system services |
| `Get-FileHash` | Generate file hashes |

### Network Information
| Cmdlet | Purpose |
|--------|---------|
| `Get-NetIPConfiguration` | Network interface configuration |
| `Get-NetIPAddress` | Detailed IP address information |
| `Get-NetTCPConnection` | Active TCP connections |
| `Test-Connection` | Ping hosts (like `ping`) |
| `Resolve-DnsName` | DNS lookup (like `nslookup`) |

### Discovery & Help
| Cmdlet | Purpose |
|--------|---------|
| `Get-Command` | List available commands |
| `Get-Help` | Display cmdlet help |
| `Get-Alias` | List command aliases |
| `Get-Member` | View object properties/methods |

---

## Key Takeaways

### Object-Oriented Advantages
- PowerShell passes structured objects, not text
- Properties and methods preserved throughout pipeline
- No text parsing required
- Direct access to specific data elements

### Verb-Noun Convention
- Consistent, predictable naming
- Easy to discover new cmdlets
- Intuitive command structure

### Pipeline Power
- Chain multiple operations efficiently
- Filter, sort, and select in one line
- Objects maintain structure throughout

### Real-World Applications
- System administration and automation
- Security monitoring and analysis
- Incident response and forensics
- Penetration testing and red teaming
- Configuration management at scale

### Best Practices
- Use `Get-Help` liberally to learn cmdlets
- Leverage tab completion for cmdlet discovery
- Chain cmdlets with pipes for complex operations
- Test scripts in controlled environments first
- Use `-WhatIf` parameter to preview destructive operations
- Comment scripts for maintainability

---

## Next Steps

Continue building command-line proficiency:
- **Linux Command Line** - Master Unix/Linux shells
- **PowerShell Scripting** - Advanced automation techniques
- **Active Directory** - AD management with PowerShell
- **Windows Event Logs** - Log analysis and SIEM integration
- **Threat Hunting** - Use PowerShell for detection and response

PowerShell is an essential skill for modern IT professionals, system administrators, and security practitioners. Its deep Windows integration, cross-platform support, and object-oriented design make it indispensable for automation, security operations, and system management.

---

**Room Completed:** ✓  
**Date:** February 11, 2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
