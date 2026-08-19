# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Task
Level up your scripting — use loops, handle arguments, and deal with errors.

You will:
- Write **for** and **while** loops
- Use **command-line arguments** (`$1`, `$2`, `$#`, `$@`)
- Install packages via script
- Add basic **error handling**

---

## Expected Output
- A markdown file: `day-17-scripting.md`
- All scripts you write during the tasks

---

## Challenge Tasks

### Task 1: For Loop
1. Create `for_loop.sh` that:
   - Loops through a list of 5 fruits and prints each one

ubuntu@devops-ai-powered:~$ cat for_loop.sh
#!/bin/bash

for fruits in Mango, banana, apple, pears, grapes; do
	echo "$fruits"
done


2. Create `count.sh` that:
   - Prints numbers 1 to 10 using a for loop

ubuntu@devops-ai-powered:~$ cat count.sh
#!/bin/bash


for i in {1..10}; do
	echo "$i"
done

---

### Task 2: While Loop
1. Create `countdown.sh` that:
   - Takes a number from the user
   - Counts down to 0 using a while loop
   - Prints "Done!" at the end

#!/bin/bash

read -p "Enter the number: " NUM
while [ $NUM -ge 0 ]; do
	echo "$NUM"
	NUM=$((NUM - 1))
done

echo "Done"

---

### Task 3: Command-Line Arguments
1. Create `greet.sh` that:
   - Accepts a name as `$1`
   - Prints `Hello, <name>!`
   - If no argument is passed, prints "Usage: ./greet.sh <name>"

#!/bin/bash

if [ $# -eq 0 ]; then
	echo "Usage: ./greet2.sh <your name here>"
	exit 1
fi

echo "Hello, $1!"



2. Create `args_demo.sh` that:
   - Prints total number of arguments (`$#`)
   - Prints all arguments (`$@`)
   - Prints the script name (`$0`)

#!/bin/bash

echo "Total number of arguments: $#"
echo "All Arguments: $@"
echo "Script name: $0"

---

### Task 4: Install Packages via Script
1. Create `install_packages.sh` that:
   - Defines a list of packages: `nginx`, `curl`, `wget`
   - Loops through the list
   - Checks if each package is installed (use `dpkg -s` or `rpm -q`)
   - Installs it if missing, skips if already present
   - Prints status for each package

> Run as root: `sudo -i` or `sudo su`

ubuntu@devops-ai-powered:~$ cat install_packages.sh 
#!/bin/bash

packages=("nginx" "curl" "wget")

for package in "${packages[@]}"; do
	if dpkg -s "$package" &> /dev/null; then
		echo "$package already installed"
	else 
		echo "$package is not installed, installing...."
		sudo apt-get install -y $package
	fi
done

---

### Task 5: Error Handling
1. Create `safe_script.sh` that:
   - Uses `set -e` at the top (exit on error)
   - Tries to create a directory `/tmp/devops-test`
   - Tries to navigate into it
   - Creates a file inside
   - Uses `||` operator to print an error if any step fails

#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"
cd /tmp/devops-test || { echo "failed to enter directory"; exit 1; }
touch testfile.txt || { echo "failed to create a file"; exit 1; }

echo "All Task completed successfully"



Example:
```bash
mkdir /tmp/devops-test || echo "Directory already exists"
```

2. Modify your `install_packages.sh` to check if the script is being run as root — exit with a message if not.

---

## Hints
- For loop: `for item in list; do ... done`
- While loop: `while [ condition ]; do ... done`
- Arguments: `$1` first arg, `$#` count, `$@` all args
- Check root: `if [ "$EUID" -ne 0 ]; then echo "Run as root"; exit 1; fi`
- Check package: `dpkg -s <pkg> &> /dev/null && echo "installed"`

---