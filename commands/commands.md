# Linux Commands Reference

---

## Basic Commands

### Navigation & File System

| Command | Description |
|---------|-------------|
| `pwd` | Print current working directory |
| `cd /path/to/dir` | Change to absolute path |
| `cd ..` | Go up one directory |
| `cd ~` | Go to home directory |
| `cd -` | Go back to previous directory |
| `ls` | List files in current directory |
| `ls -l` | Long listing (permissions, size, date) |
| `ls -a` | Show hidden files (dotfiles) |
| `ls -la` | Long listing including hidden files |
| `ls -ltr` | Sort by modification time, oldest first |
| `ls -lh` | Long listing with human-readable file sizes |
| `mkdir dirname` | Create a new directory |
| `mkdir -p a/b/c` | Create nested directories in one command |

```bash
# See where you are
pwd
# Output: /home/ubuntu

# Go to a specific path
cd /var/log

# Go up one level
cd ..

# Jump back to home
cd ~

# Go back to previous directory (toggle)
cd -

# List files with full details
ls -la

# List only .log files
ls *.log

# Create nested folder structure in one command
mkdir -p projects/devops/scripts
```

---

### Viewing Files

| Command | Description |
|---------|-------------|
| `cat file.txt` | Print entire file to terminal |
| `cat -n file.txt` | Print file with line numbers |
| `head file.txt` | Show first 10 lines |
| `head -n 20 file.txt` | Show first 20 lines |
| `tail file.txt` | Show last 10 lines |
| `tail -n 20 file.txt` | Show last 20 lines |
| `tail -f file.txt` | Live-follow file (stream new lines — great for logs) |
| `less file.txt` | Page through file (supports forward AND backward, `/` to search) |
| `more file.txt` | Page through file (forward only) |
| `zcat file.gz` | View contents of a gzip file without extracting |

```bash
# Print a file
cat /etc/os-release

# Print with line numbers
cat -n app.log

# See the first 5 lines of a file
head -n 5 /var/log/syslog

# See the last 20 lines of a log
tail -n 20 /var/log/nginx/error.log

# Watch a log file live as new entries appear
tail -f /var/log/app.log

# Page through a large file (press q to quit, /keyword to search)
less /var/log/syslog

# View a gzip compressed log without extracting it
zcat /var/log/syslog.1.gz
```

> **`less` vs `more`:** Always prefer `less` — it supports backward scrolling and search. Press `q` to quit, `/keyword` to search.

---

### Creating & Deleting Files

| Command | Description |
|---------|-------------|
| `touch file.txt` | Create an empty file (or update timestamp if exists) |
| `rm file.txt` | Remove a file (permanent — no trash) |
| `rm -i file.txt` | Remove with confirmation prompt |
| `rm -r dirname` | Remove a directory and all its contents recursively |
| `rm -rf dirname` | Force remove without prompts — **use with extreme caution** |
| `rmdir dirname` | Remove an empty directory only |

```bash
# Create a new empty file
touch notes.txt

# Create multiple files at once
touch file1.txt file2.txt file3.txt

# Delete a single file
rm old_report.txt

# Delete with a confirmation prompt (safer)
rm -i important.txt

# Delete an entire directory and everything inside it
rm -r old_project/

# Remove an empty directory
rmdir empty_folder/
```

> **Warning:** `rm -rf` is irreversible. Always double-check the path before running.

---

### Text & Output

| Command | Description |
|---------|-------------|
| `echo "text"` | Print text to terminal |
| `echo "text" > file.txt` | Write text to file (overwrites existing content) |
| `echo "text" >> file.txt` | Append text to file (does not overwrite) |
| `date` | Show current date and time |
| `clear` | Clear terminal screen (`Ctrl+L` also works) |

```bash
# Print to terminal
echo "Hello, World!"

# Write to a file (creates it if it doesn't exist, overwrites if it does)
echo "server=production" > config.txt

# Append a new line to an existing file
echo "port=8080" >> config.txt

# Show today's date and time
date

# Custom date format
date "+%Y-%m-%d %H:%M:%S"
# Output: 2026-09-15 10:30:00

# Clear the screen
clear
```

---

### Redirection Operators

| Operator | Meaning | Behaviour |
|----------|---------|-----------|
| `>` | Redirect stdout to file | Overwrites the file if it exists |
| `>>` | Append stdout to file | Creates file if missing, appends if exists |
| `2>` | Redirect stderr to file | Captures error messages only |
| `2>&1` | Merge stderr into stdout | Both streams go to the same place |
| `&>` | Redirect both stdout and stderr | Shorthand for `> file 2>&1` |
| `<` | Read stdin from file | Command reads input from a file |
| `\|` | Pipe | Sends stdout of one command as stdin to the next |

```bash
# Save output of ls to a file
ls -la > file_list.txt

# Append more output without overwriting
ls /var/log >> file_list.txt

# Save only error messages
ls /nonexistent 2> errors.txt

# Save both output and errors to the same file
ls /var/log /nonexistent &> all_output.txt

# Use a file as input to a command
sort < unsorted.txt

# Pipe: send output of one command into another
ps aux | grep nginx

# Chain multiple pipes
cat /var/log/auth.log | grep "Failed" | wc -l
```

---

## File Operations

| Command | Description |
|---------|-------------|
| `cp source dest` | Copy file to destination |
| `cp -r src_dir dest_dir` | Copy entire directory recursively |
| `cp -p source dest` | Copy while preserving permissions and timestamps |
| `mv source dest` | Move file (or rename if same directory) |
| `wc file.txt` | Count lines, words, and bytes |
| `wc -l file.txt` | Count lines only |
| `wc -w file.txt` | Count words only |

```bash
# Copy a file to another location
cp app.log /backup/app.log

# Copy and keep original permissions and timestamps
cp -p config.conf /backup/config.conf

# Copy an entire directory
cp -r /var/www/html /backup/html_backup

# Move (rename) a file
mv old_name.txt new_name.txt

# Move a file to a different directory
mv report.txt /home/ubuntu/reports/

# Count lines, words, and bytes in a file
wc access.log
# Output: 1024 8192 65536 access.log (lines / words / bytes)

# Count only lines (useful for log analysis)
wc -l /var/log/syslog

# Count words in a document
wc -w essay.txt
```

---

### Links

| Command | Description |
|---------|-------------|
| `ln -s /path/to/file softlink` | Create a symbolic (soft) link — points to path |
| `ln /path/to/file hardlink` | Create a hard link — points to inode directly |

```bash
# Create a soft link (like a shortcut)
ln -s /var/www/html/index.html ~/my_index_link.html

# Create a soft link to a directory
ln -s /var/log/nginx ~/nginx_logs

# Create a hard link (shares the same file data)
ln /etc/hosts /backup/hosts_hardlink

# Verify the link
ls -la ~/my_index_link.html
# Output: lrwxrwxrwx ... my_index_link.html -> /var/www/html/index.html
```

> **Soft link** breaks if the original is deleted. **Hard link** survives deletion of the original file.

---

### Cut & Extract

| Command | Description |
|---------|-------------|
| `cut -b 1 file.txt` | Extract 1st byte/character from each line |
| `cut -b 1-4 file.txt` | Extract characters 1 to 4 from each line |
| `cut -d',' -f2 file.csv` | Extract 2nd field using comma as delimiter |

```bash
# Sample file: data.csv
# name,age,city
# Alice,30,Mumbai
# Bob,25,Delhi

# Extract only the 2nd column (age)
cut -d',' -f2 data.csv
# Output: age / 30 / 25

# Extract columns 1 and 3
cut -d',' -f1,3 data.csv
# Output: name,city / Alice,Mumbai / Bob,Delhi

# Extract first 5 characters from each line
cut -b 1-5 file.txt

# Extract using tab as delimiter (TSV)
cut -d$'\t' -f1 data.tsv
```

---

### Sorting & Comparing

| Command | Description |
|---------|-------------|
| `sort file.txt` | Sort lines alphabetically (ascending) |
| `sort -r file.txt` | Sort in reverse (descending) |
| `sort -n file.txt` | Sort numerically |
| `sort -u file.txt` | Sort and remove duplicate lines |
| `diff file1.txt file2.txt` | Show line-by-line differences between two files |
| `diff -y file1.txt file2.txt` | Side-by-side comparison |

```bash
# Sort a list of names alphabetically
sort names.txt

# Sort numbers correctly (not lexicographically)
sort -n numbers.txt

# Sort in reverse order
sort -r names.txt

# Remove duplicates while sorting
sort -u ip_list.txt

# Sort by 2nd column using comma as delimiter
sort -t',' -k2 data.csv

# Compare two config files
diff old_config.conf new_config.conf
# Lines starting with < are from file1, > from file2

# Side-by-side comparison
diff -y v1.txt v2.txt

# Ignore whitespace differences
diff -w file1.txt file2.txt
```

---

### Tee

```bash
echo "hello" | tee hello.txt
```
Prints output to terminal **and** writes to file simultaneously. Useful in pipelines.

```bash
# Append instead of overwrite
echo "hello" | tee -a hello.txt
```

| Flag | Description |
|------|-------------|
| (none) | Write to file and print to terminal (overwrites) |
| `-a` | Append to file instead of overwriting |
| `file1 file2` | Write to multiple files at once |

```bash
# Run a command and save output while still seeing it on screen
df -h | tee disk_report.txt

# Append to a running log while watching output
ping -c 5 google.com | tee -a ping_log.txt

# Write the same output to two different files
ls -la | tee file_list.txt backup_list.txt
```

---

## Text Editor: vi / vim

```bash
vi filename.txt
```

| Mode / Key | Action |
|------------|--------|
| `i` | Enter insert mode (start typing) |
| `Esc` | Exit insert mode, return to normal mode |
| `:w` | Save file |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:q!` | Quit without saving (force) |
| `dd` | Delete current line |
| `yy` | Copy (yank) current line |
| `p` | Paste below current line |
| `/keyword` | Search for keyword |
| `n` | Jump to next search match |
| `G` | Go to last line |
| `gg` | Go to first line |

```bash
# Open an existing file
vi /etc/nginx/nginx.conf

# Create and open a new file
vi new_script.sh

# Open at a specific line number
vi +42 app.log

# Typical editing workflow:
# 1. Open file:     vi config.txt
# 2. Press i        (enter insert mode)
# 3. Make edits
# 4. Press Esc      (exit insert mode)
# 5. Type :wq       (save and quit)
```

---

## SSH Login

SSH (Secure Shell) provides encrypted remote access over **port 22** using key-based authentication.

```bash
ssh -i "linux-for-devops-key.pem" ubuntu@ec2-3-138-188-2.us-east-2.compute.amazonaws.com
```

| Part | Meaning |
|------|---------|
| `-i "key.pem"` | Path to your private key file |
| `ubuntu` | Username on the remote machine |
| `@` | Separator between user and host (no spaces) |
| `ec2-...amazonaws.com` | Public DNS of the remote server |

**Common SSH flags:**

| Flag | Description |
|------|-------------|
| `-i keyfile` | Specify private key |
| `-p 2222` | Connect on a non-default port |
| `-L 8080:localhost:80` | Local port forwarding (tunnel) |
| `-v` | Verbose output (useful for debugging connection issues) |

```bash
# Basic SSH login with a key file
ssh -i "mykey.pem" ubuntu@54.123.45.67

# Connect on a non-standard port
ssh -p 2222 ubuntu@54.123.45.67

# Fix permission error before connecting
chmod 400 mykey.pem
ssh -i mykey.pem ubuntu@54.123.45.67

# Run a single command remotely without opening a shell
ssh -i mykey.pem ubuntu@54.123.45.67 "df -h"

# Copy a file from remote to local (scp)
scp -i mykey.pem ubuntu@54.123.45.67:/var/log/app.log ./app.log

# Copy a file from local to remote
scp -i mykey.pem ./deploy.sh ubuntu@54.123.45.67:/home/ubuntu/
```

> **Key permissions:** Your `.pem` file must have restricted permissions or SSH will refuse it.
> Fix with: `chmod 400 linux-for-devops-key.pem`

---

## Disk Usage

| Command | Description |
|---------|-------------|
| `df -h` | Show all mounted filesystems with used/free space in GB |
| `df -hT` | Also show filesystem type (ext4, xfs, etc.) |
| `du .` | Show disk usage of current directory |
| `du -h dirname` | Human-readable size of a directory |
| `du -sh dirname` | Summary — total size of a directory only |
| `du -sh *` | Size of every item in current directory |

```bash
# Check how full all disks are
df -h
# Output shows: Filesystem  Size  Used  Avail  Use%  Mounted on

# Include filesystem type in output
df -hT

# Find the size of the /var/log directory
du -sh /var/log

# See the size of everything in the current directory
du -sh *

# Find the top 5 largest directories in /var
du -h /var/* | sort -rh | head -5

# Check free space on a specific mount
df -h /mnt/data
```

---

## Process Management

### Viewing Processes

| Command | Description |
|---------|-------------|
| `ps` | Show processes for current terminal session |
| `ps aux` | Show all running processes with full details |
| `ps aux \| grep nginx` | Filter processes by name |
| `top` | Real-time process viewer (CPU, memory, PID) |
| `htop` | Improved `top` with color and mouse support (needs install) |

```bash
# List all running processes with details
ps aux

# Find a specific process by name
ps aux | grep nginx

# Show process tree (parent-child relationships)
ps auxf

# Show only PID and command for all processes
ps -eo pid,comm

# Open real-time process monitor
top

# Sort top by memory usage (press M inside top)
# Sort top by CPU usage (press P inside top)
```

**top keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `q` | Quit |
| `k` | Kill a process (enter PID) |
| `M` | Sort by memory usage |
| `P` | Sort by CPU usage |
| `1` | Toggle per-CPU core stats |

---

### Killing Processes

| Command | Description |
|---------|-------------|
| `kill <PID>` | Send SIGTERM (graceful shutdown) |
| `kill -9 <PID>` | Send SIGKILL (force kill — immediate) |
| `killall nginx` | Kill all processes named "nginx" |
| `pkill -f "pattern"` | Kill processes matching a pattern |

```bash
# Find the PID of a process first
ps aux | grep apache2
# Output: ubuntu  1234  ...  apache2

# Gracefully stop it
kill 1234

# If it doesn't stop, force kill it
kill -9 1234

# Kill all processes named "python3"
killall python3

# Kill a process by matching part of the command
pkill -f "gunicorn app:application"

# Reload nginx config without restarting (SIGHUP)
kill -1 $(pgrep nginx)
```

> **Signal difference:** `kill` (SIGTERM) asks the process to clean up and stop. `kill -9` (SIGKILL) forces the OS to terminate it immediately — no cleanup.

### Common Kill Signals

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGTERM` | `15` | Graceful shutdown — process can clean up (default) |
| `SIGKILL` | `9` | Immediate force kill — no cleanup possible |
| `SIGHUP` | `1` | Reload config — used by daemons (e.g. nginx) |
| `SIGINT` | `2` | Interrupt — same as pressing `Ctrl+C` |
| `SIGSTOP` | `19` | Pause/suspend a process |
| `SIGCONT` | `18` | Resume a paused process |

---

### Memory & System Stats

| Command | Description |
|---------|-------------|
| `free -h` | Show RAM usage in human-readable format |
| `free -m` | Show RAM usage in MB |
| `vmstat` | Virtual memory, CPU, I/O stats |
| `vmstat -a` | Show active/inactive memory breakdown |
| `vmstat 2 5` | Refresh every 2 seconds, show 5 samples |
| `fuser /path/to/file` | Show which process is using a file |
| `fuser -k /path/to/file` | Kill the process using a file |

```bash
# Check available RAM
free -h
# Output: total / used / free / shared / buff/cache / available

# Check memory in MB
free -m

# One-shot virtual memory snapshot
vmstat

# Monitor memory/cpu every 2 seconds, 5 times
vmstat 2 5

# See which process has a file open
fuser /var/log/app.log

# Find and kill what's using a mounted drive (before unmounting)
fuser -km /mnt/data
```

---

## Background & Logging

| Command | Description |
|---------|-------------|
| `command &` | Run command in background |
| `nohup command &` | Run in background, immune to hangup — output goes to `nohup.out` |
| `nohup command > mylog.txt 2>&1 &` | Background with custom log file |
| `jobs` | List background jobs in current shell |
| `fg %1` | Bring background job #1 to foreground |
| `bg %1` | Resume stopped job in background |
| `Ctrl+Z` | Suspend a running foreground process |
| `Ctrl+C` | Kill a running foreground process |

```bash
# Run a script in the background
./backup.sh &

# Start a long job that survives SSH disconnects
nohup ./long_job.sh &

# Background job with a named log file
nohup python3 app.py > app.log 2>&1 &

# See what background jobs are running
jobs
# Output: [1]+  Running    nohup ./long_job.sh &

# Bring job #1 back to the foreground
fg %1

# Suspend a running process (then resume it in background)
# Press Ctrl+Z while it's running, then:
bg %1

# Check if your nohup job is still running
ps aux | grep long_job.sh
```

> **`nohup` use case:** Start a long-running script on a remote server via SSH — `nohup` ensures it keeps running even after you disconnect.

### Job Control Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Kill the foreground process (SIGINT) |
| `Ctrl+Z` | Suspend (pause) the foreground process |
| `Ctrl+D` | Send EOF — exits a shell or interactive program |
| `Ctrl+L` | Clear the terminal screen |
| `Ctrl+A` | Move cursor to beginning of line |
| `Ctrl+E` | Move cursor to end of line |
| `Ctrl+R` | Search command history interactively |

---

## File Permissions

```
-rwxr-xr--  1 user group  1234 Jan 1 12:00 file.txt
 ^^^  ^^^  ^^^
 owner group others
```

| Command | Description |
|---------|-------------|
| `chmod 755 file` | Owner: rwx, Group: r-x, Others: r-x |
| `chmod 644 file` | Owner: rw-, Group: r--, Others: r-- |
| `chmod +x file` | Add execute permission for everyone |
| `chmod -x file` | Remove execute permission |
| `chown user:group file` | Change file owner and group |
| `chown -R user:group dir` | Change ownership recursively |

**Permission numbers:**

| Number | Permission |
|--------|-----------|
| `7` | rwx (read + write + execute) |
| `6` | rw- (read + write) |
| `5` | r-x (read + execute) |
| `4` | r-- (read only) |
| `0` | --- (no permissions) |

```bash
# Make a script executable
chmod +x deploy.sh
./deploy.sh

# Set typical web file permissions (owner rw, everyone else r)
chmod 644 index.html

# Set typical directory permissions (owner rwx, others r+x)
chmod 755 /var/www/html

# Fix SSH key permissions (required or SSH will refuse it)
chmod 400 mykey.pem

# Change owner of a file
chown ubuntu:ubuntu /var/www/html/app.py

# Change owner of a whole directory and everything inside
chown -R ubuntu:www-data /var/www/html/

# Check current permissions
ls -l deploy.sh
# Output: -rwxr-xr-x 1 ubuntu ubuntu 512 Sep 15 10:00 deploy.sh
```
