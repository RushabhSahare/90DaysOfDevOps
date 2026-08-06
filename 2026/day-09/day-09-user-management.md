## Expected Output
- A markdown file: `day-09-user-management.md`
- Screenshots of command outputs
- List of commands used

---

## Challenge Tasks

### Task 1: Create Users (20 minutes)

Create three users with home directories and passwords:
- `tokyo`
- `berlin`
- `professor`


sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor

sudo passwd tokyo
sudo passwd berlin
sudo passwd professor

NOTE: -m creates the home directory. passwd sets/prompts for a password interactively for each user.

Verify:**Verify:** Check `/etc/passwd` and `/home/` directory

cat /etc/passwd | grep -E "tokyo|berlin|professor" # E stand for extended regex.
ls -l /home/

Output:
cat /etc/passwd | grep -E "tokyo|berlin|professor"
tokyo:x:1002:1002::/home/tokyo:/bin/sh
berlin:x:1003:1003::/home/berlin:/bin/sh
professor:x:1004:1004::/home/professor:/bin/sh








---

### Task 2: Create Groups (10 minutes)

Create two groups:
- `developers`
- `admins`

**Verify:** Check `/etc/group`
output:

cat /etc/group | grep -E "developers|admins"
developers:x:1005:
admins:x:1006:

---

### Task 3: Assign to Groups (15 minutes)

Assign users:
- `tokyo` → `developers`
- `berlin` → `developers` + `admins` (both groups)
- `professor` → `admins`

sudo usermod -aG developers tokyo
sudo usermod -aG developers,admins berlin
sudo usermod -aG admins professor

NOTE:-aG appends to a supplementary group without removing existing ones, always use -a with -G, dropping it wipes out a user's other group memberships.

**Verify:** Use appropriate command to check group membership

Output:

ubuntu@ip-172-31-30-33:~$ cat /etc/group | grep -E "developers|admins"
developers:x:1005:tokyo,berlin
admins:x:1006:berlin,professor

ubuntu@ip-172-31-30-33:~$ cat /etc/passwd | grep -E "tokyo|berlin|professor"
tokyo:x:1002:1002::/home/tokyo:/bin/sh
berlin:x:1003:1003::/home/berlin:/bin/sh
professor:x:1004:1004::/home/professor:/bin/sh


---

### Task 4: Shared Directory (20 minutes)

1. Create directory: `/opt/dev-project`
2. Set group owner to `developers`
3. Set permissions to `775` (rwxrwxr-x)
4. Test by creating files as `tokyo` and `berlin`

**Verify:** Check permissions and test file creation

output:
#created the directory; and we can see the ownership is root.

ubuntu@ip-172-31-30-33:~$ ls -ld /opt/dev-project
drwxr-xr-x 2 root root 4096 Aug  6 05:12 /opt/dev-project


ubuntu@ip-172-31-30-33:~$ sudo chgrp developers /opt/dev-project

#Now the owner is developers:
ubuntu@ip-172-31-30-33:~$ ls -ld /opt/dev-project
drwxr-xr-x 2 root developers 4096 Aug  6 05:12 /opt/dev-project

#we chhanged the permission to 775
ubuntu@ip-172-31-30-33:~$ sudo chmod 775 /opt/dev-project
ubuntu@ip-172-31-30-33:~$ ls -ld /opt/dev-project
drwxrwxr-x 2 root developers 4096 Aug  6 05:12 /opt/dev-project


#tokyo and berlin able to create files:

ubuntu@ip-172-31-30-33:~$ sudo -u tokyo touch /opt/dev-project/tokyo-file.txt
ubuntu@ip-172-31-30-33:~$ ls -ls /opt/dev-project
total 0
0 -rw-r--r-- 1 tokyo tokyo 0 Aug  6 05:23 tokyo-file.txt
ubuntu@ip-172-31-30-33:~$ sudo -u berlin touch /opt/dev-project/berlin-file.txt
ubuntu@ip-172-31-30-33:~$ ls -ls /opt/dev-project
total 0
0 -rw-r--r-- 1 berlin berlin 0 Aug  6 05:24 berlin-file.txt
0 -rw-r--r-- 1 tokyo  tokyo  0 Aug  6 05:23 tokyo-file.txt
ubuntu@ip-172-31-30-33:~$ ls -ld /opt/dev-project
drwxrwxr-x 2 root developers 4096 Aug  6 05:24 /opt/dev-project

---

### Task 5: Team Workspace (20 minutes)

1. Create user `nairobi` with home directory

ubuntu@ip-172-31-30-33:~$ sudo useradd -m nairobi

2. Create group `project-team`
ubuntu@ip-172-31-30-33:~$ sudo groupadd project-team

3. Add `nairobi` and `tokyo` to `project-team`
project-team:x:1008:
ubuntu@ip-172-31-30-33:~$ sudo usermod -aG project-team nairobi
ubuntu@ip-172-31-30-33:~$ sudo usermod -aG project-team tokyo

4. Create `/opt/team-workspace` directory

ubuntu@ip-172-31-30-33:~$ sudo mkdir /opt/team-workspace
ubuntu@ip-172-31-30-33:~$ ls -la


5. Set group to `project-team`, permissions to `775`

ubuntu@ip-172-31-30-33:~$ ls -ld /opt/team-workspace/
drwxr-xr-x 2 root root 4096 Aug  6 05:33 /opt/team-workspace/
ubuntu@ip-172-31-30-33:~$ sudo chgrp project-team /opt/team-workspace/
ubuntu@ip-172-31-30-33:~$ ls -ld /opt/team-workspace/
drwxr-xr-x 2 root project-team 4096 Aug  6 05:33 /opt/team-workspace/

ubuntu@ip-172-31-30-33:~$ ls -ld /opt/team-workspace/
drwxr-xr-x 2 root project-team 4096 Aug  6 05:33 /opt/team-workspace/

ubuntu@ip-172-31-30-33:~$ sudo chmod 775 /opt/team-workspace/
ubuntu@ip-172-31-30-33:~$ ls -ld /opt/team-workspace/
drwxrwxr-x 2 root project-team 4096 Aug  6 05:33 /opt/team-workspace/


6. Test by creating file as `nairobi`

ubuntu@ip-172-31-30-33:~$ sudo -u nairobi touch /opt/team-workspace/nairobi-file.txt

ubuntu@ip-172-31-30-33:~$ ls -ld /opt/team-workspace/
drwxrwxr-x 2 root project-team 4096 Aug  6 05:40 /opt/team-workspace/
---