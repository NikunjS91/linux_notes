# Pro Linux Commands

Advanced text processing and pattern matching tools used heavily in log analysis and scripting.

---

## awk

A powerful text processing tool — almost like a mini programming language.

- Works with **structured files** (CSV and TSV — comma or tab separated)
- Lets you filter, tally, and view data based on conditions, parameters, and ranges

**Syntax:**
```bash
awk 'condition/action' filename.extension
```

**Examples:**
```bash
# Print the second column of a file
awk '{print $2}' data.csv

# Print lines where 3rd column is greater than 100
awk '$3 > 100' data.csv

# Print specific columns with a condition
awk -F',' '$1 == "ERROR" {print $2, $3}' app.logs
```

> All conditions and actions go inside the single quotes `' '`.

---

## sed

Stream editor — similar to `awk` but with different syntax and a broader scope.

- Works with **unstructured files** too (plain text)
- Processes input **line by line**
- Can be used for real-time streaming/live editing

**Syntax:**
```bash
sed 'expression' filename.extension
```

**Examples:**
```bash
# Replace first occurrence of "foo" with "bar" per line
sed 's/foo/bar/' file.txt

# Replace all occurrences
sed 's/foo/bar/g' file.txt

# Delete lines containing a pattern
sed '/error/d' file.txt
```

---

## grep

**Global Regular Expression Print** — search for patterns in files.

**Syntax:**
```bash
grep "pattern" filename
```

**Common Flags:**

| Flag | Description |
|------|-------------|
| (none) | Case-sensitive search |
| `-i` | Case-insensitive search |
| `-c` | Count the number of matching lines |
| `-n` | Show line numbers of matches |
| `-r` | Recursive search through directories |
| `-v` | Invert match (show lines that do NOT match) |

**Examples:**
```bash
# Search for "info" in app.logs
grep info app.logs

# Case-insensitive search
grep -i "error" app.logs

# Count matching lines
grep -c "warning" app.logs

# Recursive search in all files under a directory
grep -r "database" /var/log/
```
