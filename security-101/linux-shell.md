# TryHackMe - Linux Shell

**Room:** Linux Shell  
**Difficulty:** Easy  
**Focus:** Linux shell fundamentals, shell types (Bash, Fish, Zsh), shell scripting basics, automation

---

## Overview

This room introduces the Linux shell and its role in interacting with the operating system through the command-line interface (CLI). While GUIs are convenient, mastering the shell provides significant advantages in efficiency, resource usage, and automation—critical skills for cybersecurity professionals and system administrators.

**Learning Objectives:**
- Learn interaction with Linux shell
- Use basic shell commands
- Explore the types of Linux shells available
- Write shell scripts to automate tasks

---

## Task 1: Introduction to Linux Shells

### Why CLI Over GUI?

The shell provides several advantages over graphical interfaces:

**Efficiency & Control:** Execute tasks directly through commands rather than navigating through multiple menu options. Direct access to the operating system provides more power and control.

**Resource-Friendly:** CLI consumes minimal system resources compared to graphics-intensive GUIs, making it ideal for remote servers, older hardware, and resource-constrained environments.

**Automation:** Shell scripts enable automation of repetitive tasks, reducing manual effort and human error.

**Professional Preference:** Most Linux users in cybersecurity and system administration prefer CLI operations for their speed and precision.

The shell helps you "cook your own dish" in the operating system's "kitchen" rather than simply ordering from a menu through the GUI.

---

## Task 2: How To Interact With a Shell?

### Basic Shell Commands

**Check current directory:**
```bash
pwd
```
Output:
```
/home/user
```

**Change directory:**
```bash
cd Desktop
```

**List directory contents:**
```bash
ls
```
Output:
```
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

**Display file contents:**
```bash
cat filename.txt
```
Output:
```
this is a sample file
this is the second line of the file
```

**Search for patterns in files:**
```bash
grep THM dictionary.txt
```
Output:
```
The flag is THM
```

### Default Shell

Most Linux distributions use **Bash (Bourne Again Shell)** as their default shell. The exact default shell depends on your specific Linux distribution.

**Question:** What is the default shell in most Linux distributions?  
**Answer:** `bash`

**Question:** Which command utility is used to list down the contents of a directory?  
**Answer:** `ls`

**Question:** Which command utility can help you search for anything in a file?  
**Answer:** `grep`

---

## Task 3: Types of Linux Shells

### Checking Your Current Shell

**View current shell:**
```bash
echo $SHELL
```
Output:
```
/bin/bash
```

**List available shells:**
```bash
cat /etc/shells
```
Output:
```
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
/bin/zsh
/usr/bin/zsh
```

**Switch shells temporarily:**
```bash
zsh
```

**Change default shell permanently:**
```bash
chsh -s /usr/bin/zsh
```

### Shell Comparison

**Bourne Again Shell (Bash)**
- Default shell for most Linux distributions
- Widely used with extensive scripting capabilities
- Tab completion feature
- Command history (use arrow keys or `history` command)
- Backwards compatible with older shells (sh, ksh, csh)

**Friendly Interactive Shell (Fish)**
- User-friendly focus with simple syntax
- Auto spell correction for commands
- Syntax highlighting colors different parts of commands for readability
- Customizable command prompt with themes
- Tab completion, command history, and scripting support

**Z Shell (Zsh)**
- Modern shell combining features from multiple predecessors
- Advanced tab completion
- Auto spell correction
- Extensive customization options (may impact performance)
- Tab completion, command history, and scripting capabilities

**Shell Selection:** The best shell depends on your specific needs and preferences. Compare features and choose based on your tasks and workflow.

**Question:** Which shell comes with syntax highlighting as an out-of-the-box feature?  
**Answer:** `fish`

**Question:** Which shell does not have auto spell correction?  
**Answer:** `bash`

**Question:** Which command displays all the previously executed commands of the current session?  
**Answer:** `history`

---

## Task 4: Shell Scripting and Components

### What is Shell Scripting?

A shell script is a collection of commands combined into a single file. Instead of typing commands repeatedly for a repetitive task, you can execute the script once to run all commands automatically. This enables task automation and improves efficiency.

### Creating Your First Script

**Create script file:**
```bash
nano first_script.sh
```

**Every script starts with a shebang:**
```bash
#!/bin/bash
```

The shebang (`#!`) specifies the interpreter to use when executing the script. For bash scripts, use `#!/bin/bash`.

### Script Components

**Variables**

Variables store values that can be reused throughout your script:

```bash
#!/bin/bash
echo "Hey, what's your name?"
read name
echo "Welcome, $name"
```

**Make script executable:**
```bash
chmod +x first_script.sh
```

**Execute script:**
```bash
./first_script.sh
```

Note: The `./` prefix tells the shell to execute the file in the current directory. Without it, the shell searches the PATH environment variable and won't find your script.

**Loops**

Loops enable repetitive execution of code:

```bash
#!/bin/bash
for i in {1..10};
do
    echo $i
done
```

The `for` loop iterates through values 1 to 10, executing the code between `do` and `done` for each iteration.

**Conditional Statements**

Conditional statements execute code only when specific conditions are met:

```bash
#!/bin/bash
echo "Please enter your name first:"
read name
if [ "$name" = "Stewart" ]; then
    echo "Welcome Stewart! Here is the secret: THM_Script"
else
    echo "Sorry! You are not authorized to access the secret."
fi
```

The `if` statement compares the variable value with "Stewart". If it matches, it displays the secret; otherwise, it denies access. The `fi` keyword ends the conditional statement.

**Comments**

Comments document your code for future reference and collaboration:

```bash
#!/bin/bash
# Asking the user to enter a value.
echo "Please enter your name first:"
# Storing the user input value in a variable.
read name
# Checking if the name the user entered is equal to our required name.
if [ "$name" = "Stewart" ]; then
    # If it equals the required name, the following line will be displayed.
    echo "Welcome Stewart! Here is the secret: THM_Script"
else
    echo "Sorry! You are not authorized to access the secret."
fi
```

Comments start with `#` and are ignored during script execution. They improve code readability without affecting functionality.

**Question:** What is the shebang used in a Bash script?  
**Answer:** `#!/bin/bash`

**Question:** Which command gives executable permissions to a script?  
**Answer:** `chmod +x`

**Question:** Which scripting functionality helps us configure iterative tasks?  
**Answer:** `loops`

---

## Task 5: The Locker Script

### Practical Application

**Requirement:** Create a script that verifies user credentials before granting locker access.

**Required credentials:**
- Username: John
- Company name: Tryhackme
- PIN: 7385

**Complete script:**
```bash
#!/bin/bash
# Defining the variables
username=""
companyname=""
pin=""

# Defining the loop
for i in {1..3}; do
    # Defining the conditional statements
    if [ "$i" -eq 1 ]; then
        echo "Enter your Username:"
        read username
    elif [ "$i" -eq 2 ]; then
        echo "Enter your Company name:"
        read companyname
    else
        echo "Enter your PIN:"
        read pin
    fi
done

# Checking if the user entered the correct details
if [ "$username" = "John" ] && [ "$companyname" = "Tryhackme" ] && [ "$pin" = "7385" ]; then
    echo "Authentication Successful. You can now access your locker, John."
else
    echo "Authentication Denied!!"
fi
```

**Script execution example:**
```bash
./locker_script.sh
Enter your Username:
John
Enter your Company name:
Tryhackme
Enter your PIN:
1349
Authentication Denied!!
```

**Question:** What would be the correct PIN to authenticate in the locker script?  
**Answer:** `7385`

---

## Task 6: Practical Exercise

### Lab Setup

A script is located in `/home/user` on the attached Ubuntu machine. This script searches for a specific keyword in all `.log` files within a specified directory.

**Become root user:**
```bash
sudo su
# Password: (credentials from Task 2)
```

### Script Configuration

**Required modifications:**
- Flag: `thm-flag01-script`
- Directory: `/var/log`

**Hint:** Look for empty double quotes `""` inside the script file and fill them with the appropriate values. Ensure no spaces exist between the quotes.

**Execute the configured script:**
```bash
./script_name.sh
```

**Question:** Which file has the keyword?  
**Answer:** `[Execute script to reveal]`

**Question:** Where is the cat sleeping?  
**Answer:** `[Execute script to reveal]`

---

## Task 7: Conclusion

### Key Takeaways

**Shell fundamentals:**
- Shells provide efficient, resource-friendly interaction with Linux systems
- Different shells offer unique features suited to specific needs
- Command history and tab completion improve workflow efficiency

**Shell scripting essentials:**
- Scripts automate repetitive tasks and reduce human error
- Variables, loops, and conditionals are fundamental building blocks
- Comments improve code maintainability and collaboration
- Proper permissions (`chmod +x`) are required for script execution

**Essential commands recap:**

| Command | Purpose |
|---------|---------|
| `pwd` | Display current working directory |
| `cd [dir]` | Change directory |
| `ls` | List directory contents |
| `cat [file]` | Display file contents |
| `grep [pattern] [file]` | Search for patterns in files |
| `echo $SHELL` | Show current shell |
| `cat /etc/shells` | List available shells |
| `history` | Display command history |
| `chmod +x [script]` | Make script executable |
| `./[script]` | Execute script in current directory |

### Security+ Relevance

These Linux shell concepts align with CompTIA Security+ objectives:

- **CLI Proficiency:** Essential for incident response and system administration
- **Log Analysis:** `grep` is fundamental for investigating security events in log files
- **Automation:** Shell scripting reduces manual effort in repetitive security tasks
- **Privilege Management:** Understanding `sudo su` for proper privilege escalation
- **System Administration:** Managing files, directories, and processes via CLI

---

## Practical Application

### Real-World Scenarios

**Scenario 1: Log Analysis for Security Investigation**
```bash
grep "Failed password" /var/log/auth.log
grep "root" /var/log/auth.log | grep "Failed"
```

**Scenario 2: Automated Backup Script**
```bash
#!/bin/bash
backup_dir="/backup/$(date +%Y%m%d)"
mkdir -p $backup_dir
cp -r /important/data/* $backup_dir/
echo "Backup completed: $backup_dir"
```

**Scenario 3: System Monitoring Script**
```bash
#!/bin/bash
echo "System Health Check - $(date)"
echo "Disk Usage:"
df -h
echo "Memory Usage:"
free -h
echo "Top Processes:"
ps aux --sort=-%mem | head -5
```

---

## Next Steps

With a solid foundation in Linux shells and scripting:
- Explore advanced bash scripting techniques
- Practice `grep` with regular expressions for complex pattern matching
- Learn scripting for security automation (log parsing, vulnerability scanning)
- Study bash one-liners for penetration testing
- Complete TryHackMe's Linux PrivEsc and Bash Scripting rooms

The shell is a fundamental skill for system administration, penetration testing, and incident response. Regular practice with these commands and scripting techniques builds efficiency in real-world security operations.

---

**Room Completed:** ✓  
**Date:** 02/12/2026  
**Author:** waltzey  
**GitHub:** https://github.com/waltzey/tryhackme-writeups
