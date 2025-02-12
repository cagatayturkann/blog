---
title: 'Basic Linux Commands - Part 1'
summary: In this article series, we will explore the most commonly used Linux commands that every developer should know. This first part covers 20 essential commands that will help you navigate and manage your Linux system effectively.
date: 2025-02-08T18:19:00+03:00
cardimage: '/en/blog/linux/basic-linux-commands/images/cover.png'
featureimage: 'en/blog/linux/basic-linux-commands/images/cover.png'
featured: true
categories: ['Linux']
tags: ['linux', 'commands', 'terminal']
translationKey: "basic-linux-commands"
authors:
  - Çağatay: /images/author.jpeg
---

Hello everyone! In this article series, I will explain the most commonly used Linux commands that every developer should know. These commands are essential for effectively managing your Linux system and improving your productivity in the terminal.

## Why Should We Learn Linux Commands?

Linux commands are fundamental tools that allow us to interact with our operating system through the terminal. Understanding these commands is crucial because:

1. They provide more control over the system
2. They're often faster than using a graphical interface
3. Many servers run on Linux and don't have graphical interfaces
4. They're essential for automation and scripting
5. They're used extensively in DevOps and system administration

### Most Used Linux Commands

Let's explore the 20 most commonly used Linux commands:

1. **ls (List)**
   - Lists files and directories in the current directory
   - Common options:
     - `ls -l`: Long format listing
     - `ls -a`: Show hidden files
     - `ls -h`: Human-readable file sizes

```bash
user@linux:~$ ls -la
total 32
drwxr-xr-x  2 user user 4096 Feb  8 10:00 .
drwxr-xr-x 20 user user 4096 Feb  8 10:00 ..
-rw-r--r--  1 user user  220 Feb  8 10:00 .bash_profile
-rw-r--r--  1 user user 3526 Feb  8 10:00 .bashrc
drwxr-xr-x  2 user user 4096 Feb  8 10:00 Documents
```

2. **cd (Change Directory)**
   - Changes your current directory
   - Usage examples:
     - `cd /path/to/directory`: Go to specific directory
     - `cd ..`: Go up one directory
     - `cd ~`: Go to home directory

```bash
user@linux:~$ pwd
/home/user
user@linux:~$ cd Documents
user@linux:~/Documents$ cd ..
user@linux:~$ cd ~
```

3. **pwd (Print Working Directory)**
   - Shows your current directory path
   - Useful when you need to confirm your location in the file system

```bash
user@linux:~$ pwd
/home/user/Documents/projects
```

4. **mkdir (Make Directory)**
   - Creates new directories
   - Options:
     - `mkdir -p`: Creates parent directories if they don't exist

```bash
user@linux:~$ mkdir -p projects/new-project
user@linux:~$ ls -l projects/
total 4
drwxr-xr-x 2 user user 4096 Feb 8 10:00 new-project
```

5. **rm (Remove)**
   - Deletes files and directories
   - Important options:
     - `rm -r`: Remove directories recursively
     - `rm -f`: Force removal without confirmation

```bash
user@linux:~$ ls
file1.txt file2.txt test_dir
user@linux:~$ rm file1.txt
user@linux:~$ rm -r test_dir
user@linux:~$ ls
file2.txt
```

6. **cp (Copy)**
   - Copies files and directories
   - Common usage:
     - `cp file1 file2`: Copy file1 to file2
     - `cp -r dir1 dir2`: Copy directory recursively

```bash
user@linux:~$ cp file1.txt backup.txt
user@linux:~$ cp -r projects/ projects_backup/
user@linux:~$ ls
backup.txt file1.txt projects projects_backup
```

7. **mv (Move)**
   - Moves or renames files and directories
   - Examples:
     - `mv old.txt new.txt`: Rename file
     - `mv file /path/to/dir`: Move file to directory

```bash
user@linux:~$ ls
old.txt documents/
user@linux:~$ mv old.txt new.txt
user@linux:~$ mv new.txt documents/
user@linux:~$ ls documents/
new.txt
```

8. **cat (Concatenate)**
   - Displays file contents
   - Also used to concatenate files

```bash
user@linux:~$ cat file.txt
This is the content of file.txt
user@linux:~$ cat file1.txt file2.txt > combined.txt
user@linux:~$ cat combined.txt
Content from file1
Content from file2
```

9. **grep (Global Regular Expression Print)**
   - Searches for patterns in files
   - Useful options:
     - `grep -i`: Case-insensitive search
     - `grep -r`: Recursive search

```bash
user@linux:~$ grep -r "TODO" .
./src/app.js:// TODO: Implement error handling
./docs/readme.md:TODO: Update documentation
user@linux:~$ grep -i "error" log.txt
Error: Connection failed
error: unable to connect
ERROR: System failure
```

10. **chmod (Change Mode)**
    - Changes file permissions
    - Format: `chmod [options] mode file`

```bash
user@linux:~$ ls -l script.sh
-rw-r--r-- 1 user user 256 Feb 8 10:00 script.sh
user@linux:~$ chmod +x script.sh
user@linux:~$ ls -l script.sh
-rwxr-xr-x 1 user user 256 Feb 8 10:00 script.sh
```

11. **sudo (Superuser Do)**
    - Executes commands with superuser privileges
    - Important for system administration tasks

```bash
user@linux:~$ apt update
E: Could not open lock file - open (13: Permission denied)
user@linux:~$ sudo apt update
[sudo] password for user: 
Reading package lists... Done
Building dependency tree... Done
```

12. **top**
    - Shows running processes and system resources
    - Interactive process viewer

```bash
user@linux:~$ top
top - 10:00:00 up 2 days, 3:45, 1 user, load average: 0.52, 0.58, 0.59
Tasks: 180 total,   1 running, 179 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.9 us,  3.1 sy,  0.0 ni, 90.6 id,  0.4 wa,  0.0 hi,  0.0 si
MiB Mem :  7861.1 total,   2457.2 free,   3245.5 used,   2158.4 buff/cache
```

13. **ps (Process Status)**
    - Displays running processes
    - Common options:
      - `ps aux`: Show all processes

```bash
user@linux:~$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user      2345  0.0  0.1 169512  3252 pts/0    Ss   09:30   0:00 bash
user      2789  0.0  0.2 170284  6432 pts/0    R+   10:00   0:00 ps aux
```

14. **df (Disk Free)**
    - Shows disk space usage
    - Useful options:
      - `df -h`: Human-readable sizes

```bash
user@linux:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       234G   67G  156G  31% /
tmpfs           3.9G     0  3.9G   0% /tmp
/dev/sda2       100G   45G   55G  45% /home
```

15. **du (Disk Usage)**
    - Shows directory space usage
    - Common usage:
      - `du -sh *`: Size of current directory contents

```bash
user@linux:~$ du -sh *
156M    Documents
1.2G    Downloads
42M     Pictures
890M    projects
```

16. **tar**
    - Archives files and directories
    - Common operations:
      - `tar -czf`: Create archive
      - `tar -xzf`: Extract archive

```bash
user@linux:~$ tar -czf archive.tar.gz Documents/
user@linux:~$ ls -lh archive.tar.gz
-rw-r--r-- 1 user user 145M Feb 8 10:00 archive.tar.gz
user@linux:~$ tar -xzf archive.tar.gz
```

17. **find**
    - Searches for files in directory hierarchy
    - Examples:
      - `find . -name "*.txt"`: Find all .txt files

```bash
user@linux:~$ find . -name "*.txt"
./documents/notes.txt
./projects/readme.txt
./backup/old.txt
user@linux:~$ find . -type d -name "test"
./projects/test
./src/test
```

18. **wget**
    - Downloads files from the internet
    - Useful options:
      - `wget -c`: Continue interrupted download

```bash
user@linux:~$ wget https://example.com/file.zip
--2025-02-08 10:00:00--  https://example.com/file.zip
Resolving example.com... 93.184.216.34
Connecting to example.com... connected.
HTTP request sent, awaiting response... 200 OK
Length: 52890112 (50M) [application/zip]
Saving to: 'file.zip'
```

19. **systemctl**
    - Controls the systemd system and service manager
    - Common uses:
      - `systemctl start/stop/restart/status service`

```bash
user@linux:~$ sudo systemctl status nginx
● nginx.service - A high performance web server and reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2025-02-08 09:30:12 UTC; 30min ago
```

20. **history**
    - Shows command history
    - Useful features:
      - `!n`: Execute command number n
      - `!!`: Execute last command

```bash
user@linux:~$ history
  1  pwd
  2  cd Documents
  3  ls -la
  4  mkdir projects
  5  cd projects
user@linux:~$ !3
ls -la
total 32
drwxr-xr-x 2 user user 4096 Feb 8 10:00 .
drwxr-xr-x 5 user user 4096 Feb 8 10:00 ..
```

### Best Practices

1. **Always Use Tab Completion**
   - Saves time and prevents typos
   - Shows available options

2. **Check Commands Before Execution**
   - Use `--help` or `man` command
   - Be extra careful with `rm` and `sudo`

3. **Use Command History**
   - Press up arrow to browse previous commands
   - Use `Ctrl+R` for reverse search

4. **Create Aliases**
   - Save commonly used commands as aliases
   - Add them to your `.bashrc` or `.zshrc`

### Conclusion

These 20 commands form the foundation of Linux command-line usage. Understanding and mastering them will significantly improve your productivity when working with Linux systems. In the next part of this series, we'll explore more advanced commands and techniques for system administration and automation.

Remember that practice is key to becoming proficient with these commands. Try to use them regularly in your daily work, and don't be afraid to experiment in a safe environment.

Stay tuned for Part 2 where we'll dive into more advanced Linux commands!