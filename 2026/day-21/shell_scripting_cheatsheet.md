# Shell Scripting Cheat Sheet

## Quick Reference Table

| Topic             | Key Syntax                                   | Example |
|-------------------|----------------------------------------------|---------|
| Variable          | `VAR="value"`                                | `NAME="DevOps"` |
| Argument          | `$1`, `$2`                                   | `./script.sh arg1` |
| If                | `if [ condition ]; then`                     | `if [ -f file ]; then` |
| For loop          | `for i in list; do`                          | `for i in 1 2 3; do` |
| While loop        | `while [ condition ]; do`                    | `while [ "$n" -gt 0 ]; do` |
| Function          | `name() { ... }`                             | `greet() { echo "Hi"; }` |
| Grep              | `grep pattern file`                          | `grep -i "error" log.txt` |
| Awk               | `awk '{print $1}' file`                      | `awk -F: '{print $1}' /etc/passwd` |
| Sed               | `sed 's/old/new/g' file`                     | `sed -i 's/foo/bar/g' config.txt` |

---

## 1. Basics

**Shebang** — `#!/bin/bash` at the top of a script tells the OS which interpreter to run it with. Without it, behaviour depends on how the script is invoked, which can silently break under cron, `sudo -u`, or a different shell.
```bash
#!/bin/bash
```

**Running a script:**
```bash
chmod +x script.sh   # make it executable
./script.sh          # run it (needs execute permission)
bash script.sh        # run it without needing execute permission
```

**Comments:**
```bash
# this is a full-line comment
echo "Hello"   # this is an inline comment
```

**Variables** — no spaces around `=`. Double quotes allow variable expansion, single quotes don't.
```bash
NAME="DevOps"
echo "$NAME"    # prints: DevOps
echo '$NAME'    # prints literally: $NAME
```

**Reading user input:**
```bash
read -p "Enter your name: " NAME
```

**Command-line arguments:**
```bash
# $0 = script name, $1/$2 = args, $# = arg count, $@ = all args, $? = last exit code
echo "Script: $0, First arg: $1, Total args: $#"
```

---

## 2. Operators and Conditionals

**String comparisons:**
```bash
[ "$a" = "$b" ]     # equal
[ "$a" != "$b" ]    # not equal
[ -z "$a" ]         # true if empty
[ -n "$a" ]         # true if NOT empty
```

**Integer comparisons:**
```bash
[ "$a" -eq "$b" ]   # equal
[ "$a" -ne "$b" ]   # not equal
[ "$a" -lt "$b" ]   # less than
[ "$a" -gt "$b" ]   # greater than
[ "$a" -le "$b" ]   # less than or equal
[ "$a" -ge "$b" ]   # greater than or equal
```

**File test operators:**
```bash
[ -f file ]   # is a regular file
[ -d dir ]    # is a directory
[ -e path ]   # exists (file or dir)
[ -r file ]   # readable
[ -w file ]   # writable
[ -x file ]   # executable
[ -s file ]   # exists and is NOT empty
```

**if / elif / else:**
```bash
if [ "$num" -gt 0 ]; then
    echo "positive"
elif [ "$num" -lt 0 ]; then
    echo "negative"
else
    echo "zero"
fi
```

**Logical operators:**
```bash
[ -f file ] && echo "exists"          # AND — run right side only if left succeeds
[ -f file ] || echo "missing"         # OR  — run right side only if left fails
if [ ! -f file ]; then echo "no"; fi  # NOT — negates the condition
```

**Case statements:**
```bash
case "$1" in
    start) echo "Starting..." ;;
    stop)  echo "Stopping..." ;;
    *)     echo "Usage: $0 {start|stop}" ;;
esac
```

---

## 3. Loops

**For loop (list-based):**
```bash
for fruit in apple banana mango; do
    echo "$fruit"
done
```

**For loop (C-style):**
```bash
for ((i=1; i<=5; i++)); do
    echo "$i"
done
```

**While loop:**
```bash
n=5
while [ "$n" -gt 0 ]; do
    echo "$n"
    n=$((n - 1))
done
```

**Until loop** — opposite of while, runs until the condition becomes true:
```bash
n=1
until [ "$n" -gt 5 ]; do
    echo "$n"
    n=$((n + 1))
done
```

**Loop control:**
```bash
for i in 1 2 3 4 5; do
    [ "$i" -eq 3 ] && continue   # skip this iteration
    [ "$i" -eq 5 ] && break      # exit the loop entirely
    echo "$i"
done
```

**Looping over files:**
```bash
for file in *.log; do
    echo "Processing $file"
done
```

**Looping over command output** (safer than `for` for multi-word lines):
```bash
grep "ERROR" file.log | while read -r line; do
    echo "$line"
done
```

---

## 4. Functions

**Defining and calling:**
```bash
greet() {
    echo "Hello, $1!"
}
greet "DevOps"   # prints: Hello, DevOps!
```

**Arguments inside functions** — `$1`, `$2` refer to the function's own arguments, not the script's:
```bash
add() {
    echo "Sum: $(($1 + $2))"
}
add 5 10   # prints: Sum: 15
```

**Return vs echo** — `return` only gives an exit code (0-255), `echo` is how you actually send data back:
```bash
check_even() {
    if [ $(( $1 % 2 )) -eq 0 ]; then
        return 0   # success/true
    else
        return 1   # failure/false
    fi
}
check_even 4 && echo "even"
```

**Local variables** — scoped to the function only, won't leak into the rest of the script:
```bash
my_func() {
    local temp="only visible in here"
    echo "$temp"
}
```

---

## 5. Text Processing Commands

**grep** — search for patterns:
```bash
grep -i "error" file.txt     # case-insensitive
grep -r "TODO" ./src         # recursive through directories
grep -c "ERROR" file.txt     # count matching lines
grep -n "CRITICAL" file.txt  # show line numbers
grep -v "INFO" file.txt      # invert match (show non-matching lines)
grep -E "ERROR|WARN" file.txt # extended regex, supports |
```

**awk** — column/field processing:
```bash
awk '{print $1}' file.txt              # print first column
awk -F: '{print $1}' /etc/passwd       # use : as field separator
awk '/error/ {print}' file.txt         # print lines matching pattern
awk 'BEGIN{print "start"} {print} END{print "done"}' file.txt
```

**sed** — stream editing:
```bash
sed 's/old/new/' file.txt        # replace first match per line
sed 's/old/new/g' file.txt       # replace ALL matches per line
sed '2d' file.txt                # delete line 2
sed -i 's/foo/bar/g' file.txt    # edit the file in place
```

**cut** — extract columns by delimiter:
```bash
cut -d: -f1 /etc/passwd    # -d sets delimiter, -f selects field number
```

**sort:**
```bash
sort file.txt        # alphabetical
sort -n file.txt      # numerical
sort -r file.txt      # reverse order
sort -u file.txt      # sort and remove duplicates
```

**uniq** — works best on already-sorted input:
```bash
sort file.txt | uniq       # remove adjacent duplicate lines
sort file.txt | uniq -c    # count occurrences of each line
```

**tr** — translate or delete characters:
```bash
echo "hello" | tr 'a-z' 'A-Z'    # lowercase to uppercase
echo "h e l l o" | tr -d ' '     # delete all spaces
```

**wc** — counting:
```bash
wc -l file.txt   # line count
wc -w file.txt   # word count
wc -c file.txt   # byte count
```

**head / tail:**
```bash
head -n 5 file.txt   # first 5 lines
tail -n 5 file.txt   # last 5 lines
tail -f file.txt     # follow the file live as it grows
```

---

## 6. Useful Patterns and One-Liners

```bash
# Find and delete files older than 30 days
find /path -name "*.log" -mtime +30 -exec rm {} \;

# Count total lines across all .log files
cat *.log | wc -l

# Replace a string across multiple files
sed -i 's/oldtext/newtext/g' *.txt

# Check if a service is running
systemctl is-active --quiet nginx && echo "running" || echo "not running"

# Monitor disk usage and alert if above 80%
df -h / | awk 'NR==2 {if ($5+0 > 80) print "Disk usage high: " $5}'

# Tail a log and filter for errors in real time
tail -f app.log | grep --line-buffered "ERROR"

# Find top 5 largest files in a directory
du -ah /path | sort -rh | head -5

# Count occurrences of each unique error message
grep "ERROR" file.log | sort | uniq -c | sort -rn | head -5
```

---

## 7. Error Handling and Debugging

**Exit codes:**
```bash
some_command
echo $?        # 0 = success, non-zero = failure
exit 0         # explicitly exit successfully
exit 1         # explicitly exit with failure
```

**set -e** — exits the script immediately if any command fails:
```bash
set -e
```

**set -u** — treats referencing an undefined variable as an error instead of silently substituting empty string:
```bash
set -u
```

**set -o pipefail** — makes a pipeline fail if ANY command in it fails, not just the last one:
```bash
set -o pipefail
```

**Combined strict mode** (the standard safe-script header):
```bash
set -euo pipefail
```

**set -x** — debug mode, prints every command before running it:
```bash
set -x
# ... commands ...
set +x   # turn debug mode back off
```

**trap** — run a command automatically on exit (great for cleanup):
```bash
trap 'echo "Script ended, cleaning up..."; rm -f /tmp/tempfile' EXIT
```

---

## What I Learned
1. `set -e`, `-u`, and `-o pipefail` each catch a different category of silent failure, together they're the difference between a script that fails loudly and one that fails quietly and keeps going.
2. `sort | uniq -c | sort -rn` is a pattern I now reach for constantly, it's the standard way to rank "most common" anything from log data.
3. Naming a function the same as a real command (like `uptime`) causes infinite recursion, since bash checks functions before `$PATH`. Small naming choices have real consequences.

