# Day 16 – Shell Scripting Basics

## Task
Start your shell scripting journey — learn the fundamentals every script needs.

You will:
- Understand **shebang** (`#!/bin/bash`) and why it matters
- Work with **variables**, **echo**, and **read**
- Write basic **if-else** conditions

---

## Expected Output
- A markdown file: `day-16-shell-scripting.md`
- All scripts you write during the tasks

---

## Challenge Tasks

### Task 1: Your First Script
1. Create a file `hello.sh`
2. Add the shebang line `#!/bin/bash` at the top
3. Print `Hello, DevOps!` using `echo`
4. Make it executable and run it

```bash
chmod +x hello.sh
./hello.sh
```

**Document:** What happens if you remove the shebang line?

If you run it as ./hello.sh without a shebang, bash usually still executes it fine because your current shell (bash) ends up interpreting it by default on most systems.

---

### Task 2: Variables
1. Create `variables.sh` with:
   - A variable for your `NAME`
   - A variable for your `ROLE` (e.g., "DevOps Engineer")
   - Print: `Hello, I am <NAME> and I am a <ROLE>`
2. Try using single quotes vs double quotes — what's the difference?

#!/bin/bash

NAME="RUSHABH"
ROLE="DEVOPS ENGINEER"

echo "Hello , i am $NAME and Im a $ROLE"

WITH DOUBLE QOUTES: Hello , i am RUSHABH and Im a DEVOPS ENGINEER
W/O DOUBLE QUOTES: Hello , i am $NAME and Im a $ROLE


---

### Task 3: User Input with read
1. Create `greet.sh` that:
   - Asks the user for their name using `read`
   - Asks for their favourite tool
   - Prints: `Hello <name>, your favourite tool is <tool>`

#!/bin/bash

read -p "What is your name?: " NAME
read -p "What is your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"

---

### Task 4: If-Else Conditions
1. Create `check_number.sh` that:
   - Takes a number using `read`
   - Prints whether it is **positive**, **negative**, or **zero**

#!/bin/bash

read -p "Enter the number: " NUM

if [ $NUM -gt 0 ]; then
	echo "$NUM is positive"
elif [ $NUM -lt 0 ]; then
	echo "$NUM is negative"
else
        echo "$NUM is zero"
fi


2. Create `file_check.sh` that:
   - Asks for a filename
   - Checks if the file **exists** using `-f`
   - Prints appropriate message

#!/bin/bash

read -p "Enter file name: " FILE_NAME

if  [ -f $FILE_NAME  ]; then
	echo "$FILE_NAME exists"
else
	echo "$FILE_NAME Doesn't exists"
fi



WHEN FILE Is AVAILBLE:
ubuntu@devops-ai-powered:~$ ./file_check.sh
Enter file name: script.sh
script.sh exists

WHEN FILE DOES NOT EXISTS:

ubuntu@devops-ai-powered:~$ ./file_check.sh
Enter file name: name.txt
name.txt Doesn't exists


---

### Task 5: Combine It All
Create `server_check.sh` that:
1. Stores a service name in a variable (e.g., `nginx`, `sshd`)
2. Asks the user: "Do you want to check the status? (y/n)"
3. If `y` — runs `systemctl status <service>` and prints whether it's **active** or **not**
4. If `n` — prints "Skipped."


script:
#!/bin/bash

read -p "Enter the service name: " SERVICE
read -p "Do you want to check the status:  y/n: " ANSWER

if [ "$ANSWER" = "y" ]; then
	if systemctl is-active --quiet "$SERVICE"; then
		echo "$SERVICE is active"
	else 
		echo "$SERVICE is NOT active"
	fi

elif [ "$ANSWER" = "n" ]; then
	echo "Skipped"

else 
	echo "Either select 'y' OR 'n'"

fi


OUTPUT:

ubuntu@devops-ai-powered:~$ ./server_check.sh
Enter the service name: nginx
Do you want to check the status:  y/n: y
nginx is active
ubuntu@devops-ai-powered:~$ ./server_check.sh
Enter the service name: sshd
Do you want to check the status:  y/n: n
Skipped
ubuntu@devops-ai-powered:~$ ./server_check.sh
Enter the service name: sshd
Do you want to check the status:  y/n: y
sshd is active
ubuntu@devops-ai-powered:~$ ./server_check.sh
Enter the service name: apache2
Do you want to check the status:  y/n: y
apache2 is NOT active
ubuntu@devops-ai-powered:~$ 






---

## Hints
- Shebang: `#!/bin/bash` tells the system which interpreter to use
- Variables: `NAME="Shubham"` (no spaces around `=`)
- Read: `read -p "Enter name: " NAME`
- If syntax: `if [ condition ]; then ... elif ... else ... fi`
- File check: `if [ -f filename ]; then`

---


