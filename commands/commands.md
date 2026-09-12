# Linux Commands Reference

## Basic Commands

| Command | Description |
|---------|-------------|
| `top` | See CPU utilization and running processes |
| `df -h` | Hard disk usage with sizes in GB |
| `free` | RAM usage in Linux |
| `free -h` | RAM usage in human-readable format |
| `cd` | Change directory |
| `ls` | List directory contents |
| `ls -a` | Detailed listing (including hidden files) |
| `ls -ltr` | List files sorted by time, reversed |
| `mkdir` | Make a directory |
| `pwd` | Print current directory location |
| `touch filename.ext` | Create a new file |
| `date` | Show current date |
| `clear` | Clear the terminal screen |
| `rm filename` | Remove a file |
| `rm -r foldername` | Remove a folder recursively |
| `rmdir` | Remove an empty directory |
| `cat filename.ext` | Show contents of a file |
| `echo "text"` | Print text to the terminal |
| `echo "hello" > demo.txt` | Redirect output of echo into a file |
| `zcat` | View contents of a zip file |
| `head file.ext` | Show top 10 lines of a file |
| `head -n 5 file.ext` | Show top 5 lines (`-n` = number) |
| `tail file.ext` | Show bottom 10 lines of a file |
| `tail -f file.txt` | Monitor file for new lines in real time (useful for logs) |
| `less file.ext` | View file page by page (forward/backward) |
| `more file.ext` | View file page by page (forward only) |

---

## Basic+ Commands

| Command | Description |
|---------|-------------|
| `cp source dest` | Copy file to destination |
| `mv source dest` | Move file to another location |
| `mv old-name new-name` | Rename a file or folder |
| `wc file.ext` | Count lines, words, and bytes in a file |
| `ln -s /path/to/file softlink` | Create a symbolic (soft) link |
| `ln /path/to/file hardlink` | Create a hard link |
| `cut -b 1 file.ext` | Return 1st character from each line (use range `1-4` for 4 chars) |
| `echo "hi" \| tee hello.txt` | Print to terminal AND write to file simultaneously |
| `sort file.ext` | Sort file contents in ascending order |
| `diff file1.txt file2.txt` | Show differences between two files |
| `vi file.ext` | Open file in vi editor (`i` = insert, `Esc` = exit insert, `:wq` = save & quit) |

---

## SSH Login

SSH (Secure Shell) runs on **port 22** and uses key-based authentication.

```bash
ssh -i "linux-for-devops-key.pem" ubuntu@ec2-3-138-188-2.us-east-2.compute.amazonaws.com
```

- `-i` — path to your private key file
- `ubuntu` — username on the remote server
- `@` — followed by the public DNS address (no spaces)

---

## Disk Usage

| Command | Description |
|---------|-------------|
| `df -h` | Show disk usage with sizes in GB (mount points and free space) |
| `du .` | Show disk usage of current directory (folder sizes in bytes) |

---

## Process Management

| Command | Description |
|---------|-------------|
| `ps` | Show important running processes |
| `top` | Show all processes with PID and resource usage |
| `fuser` | Show processes using a file or network socket (useful for NAS/shared network) |
| `kill -9 <PID>` | Force kill a process by PID |
| `free -h` | Show RAM memory usage in human-readable format |
| `vmstat -a` | Show virtual memory (RAM) statistics |

---

## Background & Logging

| Command | Description |
|---------|-------------|
| `nohup command &` | Run command in background; output saved to `nohup.out` |
| `nohup free -h` | Capture output of `free -h` to a file (appends on subsequent runs) |
