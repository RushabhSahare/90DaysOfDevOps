# Day 10 – File Permissions & File Operations Challenge




## Challenge Tasks

### Task 1: Create Files (10 minutes)

1. Create empty file `devops.txt` using `touch`
2. Create `notes.txt` with some content using `cat` or `echo`
3. Create `script.sh` using `vim` with content: `echo "Hello DevOps"`


ubuntu@ip-172-31-30-33:~$ touch devops.txt
ubuntu@ip-172-31-30-33:~$ echo "This is notes.txt" >notes.txt

ubuntu@ip-172-31-30-33:~$ vim script.sh
ubuntu@ip-172-31-30-33:~$ cat script.sh
o "Hello DevOps"



**Verify:** `ls -l` to see permissions

ubuntu@ip-172-31-30-33:~$ ls -l
total 8
-rw-rw-r-- 1 ubuntu ubuntu    0 Aug  6 05:46 devops.txt
-rwxrwxrwx 1 ubuntu ubuntu    0 Aug  1 15:04 hello.txt
-rw-rw-r-- 1 ubuntu ubuntu 1911 Aug  5 06:09 nginxaccesslog.txt
-rw-rw-r-- 1 ubuntu ubuntu    0 Aug  5 06:08 nginxerrorlog.txt
-rw-rw-r-- 1 ubuntu ubuntu   18 Aug  6 05:47 notes.txt


---

### Task 2: Read Files (10 minutes)

1. Read `notes.txt` using `cat`
2. View `script.sh` in vim read-only mode
3. Display first 5 lines of `/etc/passwd` using `head`
4. Display last 5 lines of `/etc/passwd` using `tail`

ubuntu@ip-172-31-30-33:~$ cat notes.txt 
This is notes.txt
ubuntu@ip-172-31-30-33:~$ vim -R script.sh
ubuntu@ip-172-31-30-33:~$ head -n 5 /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
ubuntu@ip-172-31-30-33:~$ tail -n 5 /etc/passwd
shubham:x:1001:1001::/home/shubham:/bin/sh
tokyo:x:1002:1002::/home/tokyo:/bin/sh
berlin:x:1003:1003::/home/berlin:/bin/sh
professor:x:1004:1004::/home/professor:/bin/sh
nairobi:x:1005:1007::/home/nairobi:/bin/sh



---

### Task 3: Understand Permissions (10 minutes)

Format: `rwxrwxrwx` (owner-group-others)
- `r` = read (4), `w` = write (2), `x` = execute (1)

Check your files: `ls -l devops.txt notes.txt script.sh`

ubuntu@ip-172-31-30-33:~$ ls -l devops.txt notes.txt script.sh
-rw-rw-r-- 1 ubuntu ubuntu  0 Aug  6 05:46 devops.txt
-rw-rw-r-- 1 ubuntu ubuntu 18 Aug  6 05:47 notes.txt
-rw-rw-r-- 1 ubuntu ubuntu 17 Aug  6 05:48 script.sh
ubuntu@ip-172-31-30-33:~$ 

Answer: What are current permissions? Who can read/write/execute?
in every file, user/owenr has RW, group has rw, other has only read

---

### Task 4: Modify Permissions (20 minutes)

1. Make `script.sh` executable → run it with `./script.sh`
2. Set `devops.txt` to read-only (remove write for all)
3. Set `notes.txt` to `640` (owner: rw, group: r, others: none)
4. Create directory `project/` with permissions `755`

**Verify:** `ls -l` after each change

ubuntu@ip-172-31-30-33:~$ ls -l notes.txt script.sh devops.txt 
-r--r--r-- 1 ubuntu ubuntu  0 Aug  6 05:46 devops.txt
-rw-r----- 1 ubuntu ubuntu 18 Aug  6 05:47 notes.txt
-rwxrwxr-x 1 ubuntu ubuntu 20 Aug  6 05:56 script.sh



---

### Task 5: Test Permissions (10 minutes)

1. Try writing to a read-only file - what happens?
2. Try executing a file without execute permission
3. Document the error messages

ubuntu@ip-172-31-30-33:~$ echo "trying to write" >> devops.txt
-bash: devops.txt: Permission denied