# Day 12 – Breather & Revision (Days 01–11)


## What to Review (pick at least one per section)
- **Mindset & plan:** revisit your Day 01 learning plan—are your goals still right? any tweaks?  
 Still the right goal? (yes/no + why) 
 -> Yes. Studying continuously and trying mainting consistency. 

 Any tweak for the next stretch? (e.g. more time on troubleshooting, less on new commands)
 ->Try to explore, review documents about concepts.

- **Processes & services:** rerun 2 commands from Day 04/05 (e.g., `ps`, `systemctl status`, `journalctl -u <service>`); jot what you observed today.  

ubuntu@ip-172-31-30-33:~$ ps
    PID TTY          TIME CMD
   1410 pts/0    00:00:00 bash
   1539 pts/0    00:00:00 ps

ubuntu@ip-172-31-30-33:~$ journalctl -u nginx

-- Boot 1d75fe103c914a769adc28127a86c8eb --
Aug 07 04:38:26 ip-172-31-30-33 systemd[1]: Starting nginx.service - A high performance web server and a reverse proxy server...
Aug 07 04:38:27 ip-172-31-30-33 systemd[1]: Started nginx.service - A high performance web server and a reverse proxy server.

- **File skills:** practice 3 quick ops from Days 06–11 (e.g., `echo >>`, `chmod`, `chown`, `ls -l`, `cp`, `mkdir`).  
ubuntu@ip-172-31-30-33:~$ echo "This is a breather/revision" > day12breather.txt
ubuntu@ip-172-31-30-33:~$ ls -la
total 88
drwxr-x--- 9 ubuntu    ubuntu     4096 Aug  8 09:44 .
drwxr-xr-x 7 root      root       4096 Aug  6 05:28 ..
-rw------- 1 ubuntu    ubuntu     9829 Aug  7 06:01 .bash_history
-rw-r--r-- 1 ubuntu    ubuntu      220 Feb 13 12:16 .bash_logout
-rw-r--r-- 1 ubuntu    ubuntu     3771 Feb 13 12:16 .bashrc
drwx------ 2 ubuntu    ubuntu     4096 Aug  1 14:39 .cache
drwx------ 4 ubuntu    ubuntu     4096 Aug  5 05:42 .config
-rw------- 1 ubuntu    ubuntu       20 Aug  8 09:41 .lesshst
-rw-r--r-- 1 ubuntu    ubuntu      807 Feb 13 12:16 .profile
drwx------ 2 ubuntu    ubuntu     4096 Aug  1 14:37 .ssh
-rw------- 1 ubuntu    ubuntu     6463 Aug  6 05:56 .viminfo
drwxrwxr-x 2 berlin    heist-team 4096 Aug  7 05:05 app-logs
drwxrwxr-x 2 ubuntu    ubuntu     4096 Aug  7 05:43 bank-heist
-rw-rw-r-- 1 ubuntu    ubuntu       28 Aug  8 09:44 day12breather.txt
-rw-rw-r-- 1 berlin    ubuntu        0 Aug  7 04:39 devops-file.txt
-r--r--r-- 1 ubuntu    ubuntu        0 Aug  6 05:46 devops.txt
drwxrwxr-x 4 professor planners   4096 Aug  7 05:11 heist-project
-rwxrwxrwx 1 ubuntu    ubuntu        0 Aug  1 15:04 hello.txt
-rw-rw-r-- 1 ubuntu    ubuntu     1911 Aug  5 06:09 nginxaccesslog.txt
-rw-rw-r-- 1 ubuntu    ubuntu        0 Aug  5 06:08 nginxerrorlog.txt
-rw-r----- 1 ubuntu    ubuntu       18 Aug  6 05:47 notes.txt
drwxr-xr-x 2 ubuntu    ubuntu     4096 Aug  6 05:56 project
-rw-rw-r-- 1 professor heist-team    0 Aug  7 04:59 project-config.yaml
-rwxrwxr-x 1 ubuntu    ubuntu       20 Aug  6 05:56 script.sh
-rw-r--r-- 1 root      heist-name    0 Aug  7 04:49 team-notes.txt
ubuntu@ip-172-31-30-33:~$ cat day12breather.txt 
This is a breather/revision


In below command, we will try to see the content of file 'day12breather.txt', being berlin.

ubuntu@ip-172-31-30-33:~$ sudo -u berlin cat day12breather.txt
cat: day12breather.txt: Permission denied

But error, this is expected. Because /home/ubuntu has User as Ubuntu and Group as Ubuntu. This means only Ubuntu has rwx to the files inside /home/ubuntu (see below)

ubuntu@ip-172-31-30-33:~$ ls -ld /home/ubuntu
drwxr-x--- 9 ubuntu ubuntu 4096 Aug  8 09:44 /home/ubuntu

we will make berlin the user of /home/ubuntu and see whether berlin can view the content of day12breather.txt

ubuntu@ip-172-31-30-33:~$ sudo chown berlin /home/ubuntu

ubuntu@ip-172-31-30-33:~$ ls -ld /home/ubuntu
drwxr-x--- 9 berlin ubuntu 4096 Aug  8 09:44 /home/ubuntu

we can see that berlin can view the file.

ubuntu@ip-172-31-30-33:~$ sudo -u berlin cat day12breather.txt
This is a breather/revision

Giving back ubuntu its rights :
ubuntu@ip-172-31-30-33:~$ sudo chown ubuntu /home/ubuntu
ubuntu@ip-172-31-30-33:~$ ls -ld /home/ubuntu
drwxr-x--- 9 ubuntu ubuntu 4096 Aug  8 09:44 /home/ubuntu



- **Cheat sheet refresh:** skim your Day 03 commands—highlight 5 you’d reach for first in an incident.  

1-ps aux #to list running process with user , PID, CPU and memory usage
2-top #to view live process
3-htop #interactive way , colorised version
4-systemctl status <servicename> #to check status of systemd-managed service
5-ss -p #used to dump socket statistics. It allows showing information similar to netstat.  It can display more TCP and state information than other tools.

- **User/group sanity:** recreate one small scenario from Day 09 or Day 11 (create a user or change ownership) and verify with `id`/`ls -l`.

ubuntu@ip-172-31-30-33:~$ ls -ld /home/ubuntu/
drwxr-x--- 9 day12 ubuntu 4096 Aug  8 10:29 /home/ubuntu/
ubuntu@ip-172-31-30-33:~$ ls -l day12.txt
-rwxrw-r-x 1 day12 day12group 0 Aug  8 10:29 day12.txt

we can see that at dir and file level day12 user has rwx rights. however, day12 user cannot write in the day12.txt file.

ubuntu@ip-172-31-30-33:~$ sudo -u day12 echo "if you can read it, means day12 user has read access" > day12.txt
-bash: day12.txt: Permission denied
why getting permission denied?

We will use bash -c. why? bash -c tells bash to run the string that follows as a command, instead of trying to open it as a script file or start an interactive shell.

sudo -u day12 bash -c 'echo "if you can read it, means day12 user has read access" > day12.txt'

buntu@ip-172-31-30-33:~$ sudo -u day12 echo "if you can read it, means day12 user has read access" > day12.txt
-bash: day12.txt: Permission denied
ubuntu@ip-172-31-30-33:~$ sudo -u day12 bash -c 'echo "if you can read it, means day12 user has read access" > day12.txt'
ubuntu@ip-172-31-30-33:~$ sudo -u day12 cat day12.txt
if you can read it, means day12 user has read access



## Mini Self-Check (write short answers in `day-12-revision.md`)
1) Which 3 commands save you the most time right now, and why?  
->sudo chown <user>:<group> <filename> OR <directory>#in one go you can change the user and group of file
buntu@ip-172-31-30-33:~$ mkdir saturday
ubuntu@ip-172-31-30-33:~$ ls -ld saturday/
drwxrwxr-x 2 ubuntu ubuntu 4096 Aug  8 10:57 saturday/
ubuntu@ip-172-31-30-33:~$ sudo chown berlin:developers saturday/
ubuntu@ip-172-31-30-33:~$ ls -ld saturday/
drwxrwxr-x 2 berlin developers 4096 Aug  8 10:57 saturday/

->systemctl status <service>
#give the status of the service

->echo and redirection the text,content to a file.

importat learning: The rule: to access anything inside a directory, you need traversal (execute) permission on every directory in the path, not just the final file or the immediate parent. Permissions on the file itself, or on saturday/, don't matter at all if you get blocked earlier in the path.

2) How do you check if a service is healthy? List the exact 2–3 commands you’d run first.  
systemctl status <service> → confirms active/failed 
journalctl -u <service> -n 50 → recent errors 
s -tulpn → confirms it's actually listening on the expected port


3) How do you safely change ownership and permissions without breaking access? Give one example command.  
Check current state with ls -l first, change one thing at a time (sudo chown tokyo:developers file.txt), then re-verify with ls -l before moving on, rather than guessing and chaining multiple changes blind.

4) What will you focus on improving in the next 3 days?
More documentations and sources to go deep into concept and have questioning attitude. "WHY"?.