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

> **`less` vs `more`:** Always prefer `less` — it supports backward scrolling and search. Press `q` to quit, `/keyword` to search.

### Creating & Deleting Files

| Command | Description |
|---------|-------------|
| `touch file.txt` | Create an empty file (or update timestamp if exists) |
| `rm file.txt` | Remove a file (permanent — no trash) |
| `rm -i file.txt` | Remove with confirmation prompt |
| `rm -r dirname` | Remove a directory and all its contents recursively |
| `rm -rf dirname` | Force remove without prompts — **use with extreme caution** |
| `rmdir dirname` | Remove an empty directory only |

> **Warning:** `rm -rf` is irreversible. Always double-check the path before running.

### Text & Output

| Command | Description |
|---------|-------------|
| `echo "text"` | Print text to terminal |
| `echo "text" > file.txt` | Write text to file (overwrites existing content) |
| `echo "text" >> file.txt` | Append text to file (does not overwrite) |
| `date` | Show current date and time |
| `clear` | Clear terminal screen (`Ctrl+L` also works) |

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

### Links

| Command | Description |
|---------|-------------|
| `ln -s /path/to/file softlink` | Create a symbolic (soft) link — points to path |
| `ln /path/to/file hardlink` | Create a hard link — points to inode directly |

> **Soft link** breaks if original is deleted. **Hard link** survives deletion of the original file.

### Cut & Extract

| Command | Description |
|---------|-------------|
| `cut -b 1 file.txt` | Extract 1st byte/character from each line |
| `cut -b 1-4 file.txt` | Extract characters 1 to 4 from each line |
| `cut -d',' -f2 file.csv` | Extract 2nd field using comma as delimiter |

### Sorting & Comparing

| Command | Description |
|---------|-------------|
| `sort file.txt` | Sort lines alphabetically (ascending) |
| `sort -r file.txt` | Sort in reverse (descending) |
| `sort -n file.txt` | Sort numerically |
| `sort -u file.txt` | Sort and remove duplicate lines |
| `diff file1.txt file2.txt` | Show line-by-line differences between two files |
| `diff -y file1.txt file2.txt` | Side-by-side comparison |

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

**top keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `q` | Quit |
| `k` | Kill a process (enter PID) |
| `M` | Sort by memory usage |
| `P` | Sort by CPU usage |
| `1` | Toggle per-CPU core stats |

### Killing Processes

| Command | Description |
|---------|-------------|
| `kill <PID>` | Send SIGTERM (graceful shutdown) |
| `kill -9 <PID>` | Send SIGKILL (force kill — immediate) |
| `killall nginx` | Kill all processes named "nginx" |
| `pkill -f "pattern"` | Kill processes matching a pattern |

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
