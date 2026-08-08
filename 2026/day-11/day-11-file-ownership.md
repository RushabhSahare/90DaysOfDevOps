Day-11


Task 1: Understanding Ownership

bash
ls -l
Format: -rw-r--r-- 1 owner group size date filename. The owner is the individual user who created/controls the file (gets the first permission triplet). The group is a set of users who share the second triplet of permissions, useful when multiple people need the same access without making everyone the owner.

Task 2: Basic chown Operations

bash
touch devops-file.txt
ls -l devops-file.txt

ubuntu@ip-172-31-30-33:~$ touch devops-file.txt
ubuntu@ip-172-31-30-33:~$ ls -l
total 16
-rw-rw-r-- 1 ubuntu ubuntu    0 Aug  7 04:39 devops-file.txt


sudo chown tokyo devops-file.txt
ls -l devops-file.txt

ubuntu@ip-172-31-30-33:~$ sudo chown tokyo devops-file.txt
ubuntu@ip-172-31-30-33:~$ ls -l
total 16
-rw-rw-r-- 1 tokyo  ubuntu    0 Aug  7 04:39 devops-file.txt


sudo chown berlin devops-file.txt
ls -l devops-file.txt
Each chown swaps just the owner column, group stays untouched.




### Task 3: Basic chgrp Operations (15 minutes)

1. Create file `team-notes.txt`
2. Check current group: `ls -l team-notes.txt`

ubuntu@ip-172-31-30-33:~$ sudo touch team-notes.txt
ubuntu@ip-172-31-30-33:~$ ls -la
total 68


3. Create group: `sudo groupadd heist-team`

ubuntu@ip-172-31-30-33:~$ sudo groupadd heist-name

4. Change file group to `heist-team`

ubuntu@ip-172-31-30-33:~$ ls -l team-notes.txt
-rw-r--r-- 1 root root 0 Aug  7 04:49 team-notes.txt
ubuntu@ip-172-31-30-33:~$ sudo chgrp heist-name team-notes.txt



5. Verify the change
ubuntu@ip-172-31-30-33:~$ ls -l team-notes.txt
-rw-r--r-- 1 root heist-name 0 Aug  7 04:49 team-notes.txt


---

### Task 4: Combined Owner & Group Change (15 minutes)

Using `chown` you can change both owner and group together:

1. Create file `project-config.yaml`

ubuntu@ip-172-31-30-33:~$ touch project-config.yaml

ubuntu@ip-172-31-30-33:~$ ls -l project-config.yaml
-rw-rw-r-- 1 ubuntu ubuntu 0 Aug  7 04:59 project-config.yaml

2. Change owner to `professor` AND group to `heist-team` (one command)

ubuntu@ip-172-31-30-33:~$ ls -l project-config.yaml
-rw-rw-r-- 1 professor heist-team 0 Aug  7 04:59 project-config.yaml


3. Create directory `app-logs/`

ubuntu@ip-172-31-30-33:~$ mkdir app-logs/

4. Change its owner to `berlin` and group to `heist-team`


ubuntu@ip-172-31-30-33:~$ ls -la app-logs/
total 8
drwxrwxr-x 2 ubuntu ubuntu 4096 Aug  7 05:05 .
drwxr-x--- 7 ubuntu ubuntu 4096 Aug  7 05:05 ..

ubuntu@ip-172-31-30-33:~$ sudo chown berlin:heist-team app-logs
ubuntu@ip-172-31-30-33:~$ ls -la app-logs/
total 8
drwxrwxr-x 2 berlin heist-team 4096 Aug  7 05:05 .
drwxr-x--- 7 ubuntu ubuntu     4096 Aug  7 05:05 ..


**Syntax:** `sudo chown owner:group filename`

---

### Task 5: Recursive Ownership (20 minutes)

1. Create directory structure:
   ```
   mkdir -p heist-project/vault

ubuntu@ip-172-31-30-33:~$ mkdir -p heist-project/vault

   mkdir -p heist-project/plans


   touch heist-project/vault/gold.txt
   touch heist-project/plans/strategy.conf
   ```


ubuntu@ip-172-31-30-33:~$ ls -f app-logs/
.  ..
ubuntu@ip-172-31-30-33:~$ ls -f heist-project/
.  ..  plans  vault






2. Create group `planners`: `sudo groupadd planners`

ubuntu@ip-172-31-30-33:~$ sudo groupadd planners

3. Change ownership of entire `heist-project/` directory:
   - Owner: `professor`
   - Group: `planners`
   - Use recursive flag (`-R`)

ubuntu@ip-172-31-30-33:~$ ls -la
total 76
drwxr-x--- 8 ubuntu    ubuntu     4096 Aug  7 05:11 .
drwxr-xr-x 7 root      root       4096 Aug  6 05:28 ..
-rw------- 1 ubuntu    ubuntu     7621 Aug  6 06:05 .bash_history
-rw-r--r-- 1 ubuntu    ubuntu      220 Feb 13 12:16 .bash_logout
-rw-r--r-- 1 ubuntu    ubuntu     3771 Feb 13 12:16 .bashrc
drwx------ 2 ubuntu    ubuntu     4096 Aug  1 14:39 .cache
drwx------ 4 ubuntu    ubuntu     4096 Aug  5 05:42 .config
-rw------- 1 ubuntu    ubuntu       20 Aug  5 05:54 .lesshst
-rw-r--r-- 1 ubuntu    ubuntu      807 Feb 13 12:16 .profile
drwx------ 2 ubuntu    ubuntu     4096 Aug  1 14:37 .ssh
-rw------- 1 ubuntu    ubuntu     6463 Aug  6 05:56 .viminfo
drwxrwxr-x 2 berlin    heist-team 4096 Aug  7 05:05 app-logs
-rw-rw-r-- 1 berlin    ubuntu        0 Aug  7 04:39 devops-file.txt
-r--r--r-- 1 ubuntu    ubuntu        0 Aug  6 05:46 devops.txt
drwxrwxr-x 4 ubuntu    ubuntu     4096 Aug  7 05:11 heist-project

ubuntu@ip-172-31-30-33:~$ sudo chown -R professor:planners heist-project/
ubuntu@ip-172-31-30-33:~$ ls -la




4. Verify all files and subdirectories changed: `ls -lR heist-project/`

ubuntu@ip-172-31-30-33:~$ ls -lR ./heist-project/
./heist-project/:
total 8
drwxrwxr-x 3 professor planners 4096 Aug  7 05:12 plans
drwxrwxr-x 3 professor planners 4096 Aug  7 05:12 vault

./heist-project/plans:
total 4
drwxrwxr-x 2 professor planners 4096 Aug  7 05:12 strategy.conf

./heist-project/plans/strategy.conf:
total 0

./heist-project/vault:
total 4
drwxrwxr-x 2 professor planners 4096 Aug  7 05:12 gold.txt

./heist-project/vault/gold.txt:
total 0




---

### Task 6: Practice Challenge (20 minutes)

1. Create users: `tokyo`, `berlin`, `nairobi` (if not already created)
2. Create groups: `vault-team`, `tech-team`
3. Create directory: `bank-heist/`
4. Create 3 files inside:
   ```
   touch bank-heist/access-codes.txt
   touch bank-heist/blueprints.pdf
   touch bank-heist/escape-plan.txt
   ```

5. Set different ownership:
   - `access-codes.txt` → owner: `tokyo`, group: `vault-team`

ubuntu@ip-172-31-30-33:~$ ls -lR bank-heist/access-codes.txt
-rw-rw-r-- 1 ubuntu ubuntu 0 Aug  7 05:42 bank-heist/access-codes.txt

ubuntu@ip-172-31-30-33:~$ sudo chown tokyo:vault-team bank-heist/access-codes.txt
ubuntu@ip-172-31-30-33:~$ ls -lR bank-heist/access-codes.txt
-rw-rw-r-- 1 tokyo vault-team 0 Aug  7 05:42 bank-heist/access-codes.txt



   - `blueprints.pdf` → owner: `berlin`, group: `tech-team`

ubuntu@ip-172-31-30-33:~$ sudo chown berlin:tech-team bank-heist/blueprints.pdf
ubuntu@ip-172-31-30-33:~$ ls -lR bank-heist/blueprints.pdf
-rw-rw-r-- 1 berlin tech-team 0 Aug  7 05:43 bank-heist/blueprints.pdf



   - `escape-plan.txt` → owner: `nairobi`, group: `vault-team`

ubuntu@ip-172-31-30-33:~$ sudo chown nairobi:vault-team bank-heist/escape-plan.txt 
ubuntu@ip-172-31-30-33:~$ ls -lR bank-heist/escape-plan.txt
-rw-rw-r-- 1 nairobi vault-team 0 Aug  7 05:43 bank-heist/escape-plan.txt





**Verify:** `ls -l bank-heist/`

Before making changes:
ubuntu@ip-172-31-30-33:~$ ls -l bank-heist/
total 0
-rw-rw-r-- 1 ubuntu ubuntu 0 Aug  7 05:42 access-codes.txt
-rw-rw-r-- 1 ubuntu ubuntu 0 Aug  7 05:43 blueprints.pdf
-rw-rw-r-- 1 ubuntu ubuntu 0 Aug  7 05:43 escape-plan.txt


After Making changes:

ubuntu@ip-172-31-30-33:~$ ls -l bank-heist/
total 0
-rw-rw-r-- 1 tokyo   vault-team 0 Aug  7 05:42 access-codes.txt
-rw-rw-r-- 1 berlin  tech-team  0 Aug  7 05:43 blueprints.pdf
-rw-rw-r-- 1 nairobi vault-team 0 Aug  7 05:43 escape-plan.txt



---

## Key Commands Reference

```bash
# View ownership
ls -l filename

# Change owner only
sudo chown newowner filename

# Change group only
sudo chgrp newgroup filename

# Change both owner and group
sudo chown owner:group filename

# Recursive change (directories)
sudo chown -R owner:group directory/

# Change only group with chown
sudo chown :groupname filename
```

---

## Hints

- Most `chown`/`chgrp` operations need `sudo`
- Use `-R` flag for recursive directory changes
- Always verify with `ls -l` after changes
- User must exist before using in `chown`
- Group must exist before using in `chgrp`/`chown`

---
