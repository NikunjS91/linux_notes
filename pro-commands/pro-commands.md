# Pro Linux Commands

Advanced text processing and pattern matching tools — essential for log analysis, automation, and scripting.

---

## grep — Search for Patterns

**Global Regular Expression Print** — scans files line by line and prints lines that match a pattern.

### Syntax
```bash
grep [flags] "pattern" filename
```

### Common Flags

| Flag | Description |
|------|-------------|
| `-i` | Case-insensitive match |
| `-c` | Count matching lines (no output, just the number) |
| `-n` | Show line numbers alongside matches |
| `-v` | Invert — show lines that do NOT match |
| `-r` | Recursive search across all files in a directory |
| `-l` | List only filenames that contain a match |
| `-w` | Match whole words only (won't match substrings) |
| `-A 3` | Print 3 lines After each match (context) |
| `-B 3` | Print 3 lines Before each match |
| `-C 3` | Print 3 lines Before and After (context around match) |
| `-E` | Extended regex — enables `+`, `?`, `|`, `()` patterns |
| `--color` | Highlight matched text in color |

### Regex Special Characters

| Character | Meaning | Example |
|-----------|---------|---------|
| `.` | Any single character | `gr.p` matches `grep`, `grap` |
| `*` | Zero or more of preceding | `ab*c` matches `ac`, `abc`, `abbc` |
| `+` | One or more (needs `-E`) | `ab+c` matches `abc`, `abbc` |
| `?` | Zero or one (needs `-E`) | `colou?r` matches `color` or `colour` |
| `^` | Start of line | `^ERROR` — lines starting with ERROR |
| `$` | End of line | `done$` — lines ending with done |
| `[]` | Character class | `[aeiou]` matches any vowel |
| `[^]` | Negated class | `[^0-9]` matches non-digit |
| `\|` | OR (needs `-E`) | `error\|warn` matches either |
| `\b` | Word boundary | `\berror\b` exact word match |

### Examples

```bash
# Basic search
grep "error" app.log

# Case-insensitive
grep -i "ERROR" app.log

# Count how many lines contain "warning"
grep -c "warning" app.log

# Show line numbers
grep -n "failed" app.log

# Invert — show lines WITHOUT "debug"
grep -v "debug" app.log

# Recursive search across all .log files
grep -r "timeout" /var/log/

# Only show filenames, not the matching lines
grep -rl "database" /etc/

# Show 2 lines of context around each match
grep -C 2 "CRITICAL" app.log

# Match whole word — won't match "errors" or "errored"
grep -w "error" app.log

# Combine with pipe — filter process list
ps aux | grep nginx

# Use extended regex — match "error" OR "fail"
grep -E "error|fail" app.log
```

### Real-World Use Case
```bash
# Monitor a live log and filter only errors
tail -f /var/log/app.log | grep -i "error"

# Count errors per type in a log file
grep -oE "(ERROR|WARN|INFO)" app.log | sort | uniq -c | sort -rn
```

---

## awk — Structured Data Processing

A full column-oriented text processing language. Think of it as spreadsheet logic from the terminal.

- Works best with **structured files** (CSV, TSV, space-separated)
- Can filter rows, compute values, and reformat output
- Fields are referenced as `$1`, `$2`, `$3`... (`$0` = entire line)

### Syntax
```bash
awk 'pattern { action }' filename
```

### Special Variables

| Variable | Meaning |
|----------|---------|
| `$0` | Entire line |
| `$1`, `$2`, `$N` | Field N (split by delimiter) |
| `NR` | Current line number |
| `NF` | Number of fields in current line |
| `FS` | Field separator (default: whitespace) |
| `OFS` | Output field separator |

### Examples

```bash
# Print the second column of every line
awk '{print $2}' data.txt

# Print lines where 3rd column is greater than 100
awk '$3 > 100' data.txt

# Use comma as the field separator (CSV)
awk -F',' '{print $1, $3}' data.csv

# Print specific columns with a condition
awk -F',' '$1 == "ERROR" {print $2, $3}' app.log

# Print line number alongside the line
awk '{print NR, $0}' file.txt

# Calculate sum of column 2
awk '{sum += $2} END {print "Total:", sum}' data.txt

# Print lines 5 to 10 only
awk 'NR>=5 && NR<=10' file.txt

# Print last field of every line
awk '{print $NF}' file.txt

# Replace a field and reprint
awk '{$2 = "REPLACED"; print}' file.txt

# Count occurrences of each value in column 1
awk '{count[$1]++} END {for (k in count) print k, count[k]}' file.txt
```

### BEGIN and END Blocks
```bash
# BEGIN runs before any line is processed
# END runs after all lines are processed
awk 'BEGIN {print "Start"} {print $1} END {print "Done"}' file.txt
```

### awk Comparison & Logical Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equal to | `$1 == "ERROR"` |
| `!=` | Not equal | `$2 != "200"` |
| `>` | Greater than | `$3 > 100` |
| `<` | Less than | `$3 < 50` |
| `>=` | Greater or equal | `$4 >= 500` |
| `<=` | Less or equal | `$4 <= 10` |
| `&&` | Logical AND | `$1 == "GET" && $2 > 200` |
| `\|\|` | Logical OR | `$1 == "GET" \|\| $1 == "POST"` |
| `!` | Logical NOT | `!($1 == "DEBUG")` |
| `~` | Regex match | `$2 ~ /error/` |
| `!~` | Regex not match | `$2 !~ /debug/` |

---

## sed — Stream Editor

Processes text line by line and applies transformations — substitutions, deletions, insertions. Works on both structured and unstructured files.

### Syntax
```bash
sed 'expression' filename
```

> Add `-i` flag to edit the file **in-place** (modifies the actual file).

### sed Flags

| Flag | Description |
|------|-------------|
| `-i` | Edit file in-place (overwrites original) |
| `-i.bak` | In-place edit with a `.bak` backup created |
| `-n` | Suppress default output (use with `p` to print selectively) |
| `-e` | Chain multiple expressions: `sed -e 's/a/b/' -e 's/c/d/'` |
| `-r` / `-E` | Use extended regular expressions |

### Substitution (Most Common)

```bash
# Replace first occurrence per line
sed 's/old/new/' file.txt

# Replace ALL occurrences per line (g = global flag)
sed 's/old/new/g' file.txt

# Case-insensitive substitution
sed 's/old/new/gi' file.txt

# Edit file in-place
sed -i 's/old/new/g' file.txt

# In-place with backup (creates file.txt.bak)
sed -i.bak 's/old/new/g' file.txt
```

### Deletion

```bash
# Delete lines matching a pattern
sed '/error/d' file.txt

# Delete line 5
sed '5d' file.txt

# Delete lines 3 to 7
sed '3,7d' file.txt

# Delete empty/blank lines
sed '/^$/d' file.txt
```

### Printing Specific Lines

```bash
# Print only line 3 (-n suppresses default output)
sed -n '3p' file.txt

# Print lines 5 to 10
sed -n '5,10p' file.txt

# Print lines matching a pattern
sed -n '/error/p' file.txt
```

### Insertion & Appending

```bash
# Insert a line BEFORE line 3
sed '3i\This is inserted before line 3' file.txt

# Append a line AFTER line 3
sed '3a\This is appended after line 3' file.txt
```

### Real-World Use Cases

```bash
# Remove all comment lines (starting with #) from a config file
sed '/^#/d' config.conf

# Strip leading whitespace from all lines
sed 's/^[[:space:]]*//' file.txt

# Replace a specific config value in-place
sed -i 's/^PORT=.*/PORT=8080/' .env

# Remove blank lines and comment lines together
sed '/^#\|^$/d' config.conf
```

---

## Combining All Three: Pipelines

These tools become most powerful when chained together:

```bash
# Find all ERROR lines, extract the 3rd field (timestamp), count occurrences
grep "ERROR" app.log | awk '{print $3}' | sort | uniq -c | sort -rn

# Find lines matching a pattern, replace a value, save to new file
grep "user=" access.log | sed 's/user=/USER:/g' > cleaned.log

# Count unique IP addresses in an nginx access log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10
```

---

## Quick Reference

| Tool | Works With | Processes | Best For |
|------|-----------|-----------|----------|
| `grep` | Any text | Filters lines | Finding patterns, searching logs |
| `awk` | Structured (CSV/TSV) | Columns/fields | Data extraction, calculations |
| `sed` | Any text | Substitution/deletion | Find-replace, config editing |
