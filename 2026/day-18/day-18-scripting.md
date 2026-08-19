# Day 18 – Shell Scripting: Functions & intermediate Concepts

## Task
Write cleaner, reusable scripts — learn functions, strict mode, and real-world patterns.

You will:
- Write and call **functions**
- Use **`set -euo pipefail`** for safer scripts
- Work with **return values** and **local variables**
- Build an intermediate script

---

## Expected Output
- A markdown file: `day-18-scripting.md`
- All scripts you write during the tasks

---

## Challenge Tasks

### Task 1: Basic Functions
1. Create `functions.sh` with:
   - A function `greet` that takes a name as argument and prints `Hello, <name>!`
   - A function `add` that takes two numbers and prints their sum
   - Call both functions from the script

 #!/bin/bash

#!/bin/bash

greet() {
    echo "Hello, $1!"
}

add() {
    local sum=$(( $1 + $2 ))
    echo "Sum: $sum"
}

greet "Rushabh"
add 5 10  

---

### Task 2: Functions with Return Values
1. Create `disk_check.sh` with:
   - A function `check_disk` that checks disk usage of `/` using `df -h`
   - A function `check_memory` that checks free memory using `free -h`
   - A main section that calls both and prints the results

#!/bin/bash

check_disk(){
	echo "--Disk Usage--"
	df -h /
}

check_mem(){
	echo "--Memory Usage--"
	free -h
}

check_disk
check_mem




---

### Task 3: Strict Mode — `set -euo pipefail`
1. Create `strict_demo.sh` with `set -euo pipefail` at the top
2. Try using an **undefined variable** — what happens with `set -u`?
3. Try a command that **fails** — what happens with `set -e`?
4. Try a **piped command** where one part fails — what happens with `set -o pipefail`?





What does each flag do?
- `set -e` → exits the script immediately if any command returns a non-zero (failure) exit code, instead of silently continuing to the next line.
- `set -u` → treats any reference to an undefined variable as an error and exits, instead of silently substituting an empty string.
- `set -o pipefail` → makes a pipeline (cmd1 | cmd2) report failure if any command in the chain fails, not just the last one, without this, a failure early in a pipe can be silently hidden by a later command succeeding.

---

### Task 4: Local Variables
1. Create `local_demo.sh` with:
   - A function that uses `local` keyword for variables
   - Show that `local` variables don't leak outside the function
   - Compare with a function that uses regular variables

#!/bin/bash

use_local() {
    local MY_VAR="I'm local"
    echo "Inside function: $MY_VAR"
}

use_global() {
    MY_VAR="I'm global"
    echo "Inside function: $MY_VAR"
}

echo "--- Testing local ---"
use_local
echo "Outside function: $MY_VAR"   # should be empty/unset

echo "--- Testing global ---"
use_global
echo "Outside function: $MY_VAR"   # should now show "I'm global"

---

### Task 5: Build a Script — System Info Reporter
Create `system_info.sh` that uses functions for everything:
1. A function to print **hostname and OS info**
2. A function to print **uptime**
3. A function to print **disk usage** (top 5 by size)
4. A function to print **memory usage**
5. A function to print **top 5 CPU-consuming processes**
6. A `main` function that calls all of the above with section headers
7. Use `set -euo pipefail` at the top

Output should look clean and readable.

#!/bin/bash

set -euo pipefail


hostnameOS(){
	echo "--HOSTNAME AND OS--"
	echo "Hostname: $(hostname)" 
	echo "OS: $(cat /etc/os-release)" | grep "PRETTY_NAME"
	echo
}

uptimecheck(){
	echo "--UPTIME--"
	echo "Uptime: $(uptime)"
	echo
}

diskusage(){
	echo "--Diskusage--"
	echo "Diskusage: $(du -h | sort -rh | head -n 5)"
	echo
}

memusage(){
	echo "--MEMORY USAGE--"
	echo "Memory Usage: $(free -h)"
	echo
}

topfiveprocesses(){
	echo "--TOP % CONSUMING PROCESSES"
	echo "Top 5 Processes: $(ps | head -n 6)"
	echo
}

main(){
	hostnameOS
	uptimecheck
	diskusage
	memusage
	topfiveprocesses
}	

main





---

## Hints
- Function syntax: `function_name() { ... }`
- Local vars: `local MY_VAR="value"`
- Strict mode: `set -euo pipefail` as first line after shebang
- Pass args to functions: `greet "Shubham"` → access as `$1` inside
- `$?` gives the exit code of last command

---

## Documentation

