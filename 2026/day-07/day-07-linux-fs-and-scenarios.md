# Day 07 – Linux File System Hierarchy & Scenario-Based Practice


#File System Hierarchy 

/ (root) — Top of the entire filesystem hierarchy, every other directory branches from here.

/home — Personal directories for each regular user (e.g. /home/ubuntu).

/root — Home directory for the root (superuser) account, separate from /home.

/etc — System-wide configuration files (e.g. sshd_config, hosts, fstab).

/var/log — Application and system log files, the first place to check when troubleshooting.

/tmp — Temporary files, cleared on reboot, safe scratch space for throwaway testing.

/bin — Essential command binaries needed for basic system operation (e.g. ls, cp, cat).

/usr/bin — Non-essential user command binaries, most installed software lands here.

/opt — Optional or third-party software packages, often self-contained (e.g. vendor apps).


Ran below commands:

ls -l <directory> : 

#ls -l /tmp
it  contained all the temporary files.
ubuntu@ip-172-31-30-33:~$ ls -l /tmp
total 0
drwx------ 2 root root 40 Aug  5 04:13 snap-private-tmp
drwx------ 3 root root 60 Aug  5 04:13 systemd-private-433585be3d504efa95069dd66b4b73d8-ModemManager.service-KZuyPE
drwx------ 3 root root 60 Aug  5 04:13 systemd-private-433585be3d504efa95069dd66b4b73d8-chrony.service-A4c4EQ


uls -l /var/log
total 1928
lrwxrwxrwx 1 root      root                39 Jun  4 05:29 README -> ../../usr/share/doc/systemd/README.logs
-rw-r--r-- 1 root      root              9211 Aug  1 14:43 alternatives.log
drwx------ 3 root      root              4096 Aug  1 14:37 amazon
-rw-r----- 1 root      adm                  0 Aug  1 14:37 apport.log
drwxr-xr-x 2 root      root              4096 Aug  4 17:50 apt
-rw-r----- 1 syslog    adm              42673 Aug  5 04:41 auth.log
-rw-rw---- 1 root      utmp                 0 Jun  4 05:40 btmp
drwxr-x--- 2 _chrony   _chrony           4096 Aug  1 14:37 chrony
-rw-r----- 1 root      adm               7509 Aug  5 04:13 cloud-init-output.log
-rw-r----- 1 syslog    adm             545771 Aug  5 04:13 cloud-init.log
drwxr-xr-x 2 root      root              4096 Apr 22 17:29 dist-upgrade
-rw-r----- 1 root      adm              50875 Aug  5 04:13 dmesg
-rw-r----- 1 root      adm              51112 Aug  4 17:12 dmesg.0
-rw-r----- 1 root      adm              15179 Aug  2 14:15 dmesg.1.gz
-rw-r----- 1 root      adm              15879 Aug  1 14:43 dmesg.2.gz
-rw-r----- 1 root      adm              15733 Aug  1 14:37 dmesg.3.gz
-rw-r--r-- 1 root      root             97823 Aug  4 17:51 dpkg.log
drwxr-sr-x+3 root      systemd-journal   4096 Aug  1 14:36 journal
-rw-r----- 1 syslog    adm             255613 Aug  5 04:15 kern.log
drwxr-xr-x 2 landscape landscape         4096 Aug  1 14:39 landscape
-rw-rw-r-- 1 root      utmp            292292 Aug  5 04:15 lastlog
drwxr-xr-x 2 root      adm               4096 Aug  4 18:02 nginx

#Handson Tasks:

Below command is finding the disk usage of entire /var/log directory , by hiding all error message , sorting in human numeric sort , and only showing last 5 lines.
ubuntu@ip-172-31-30-33:~$ du -sh /var/log/* 2>/dev/null | sort -h | tail -5
100K    /var/log/dpkg.log
252K    /var/log/kern.log
540K    /var/log/cloud-init.log
776K    /var/log/syslog
17M     /var/log/journal





#PART 2: Scenario Practice

buntu@ip-172-31-30-33:~$ systemctl ststuas nginx
Unknown command verb 'ststuas', did you mean 'status'?
ubuntu@ip-172-31-30-33:~$ systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Wed 2026-08-05 04:13:19 UTC; 1h 17min ago
 Invocation: 3e6e690758a14d0d9d20fd322b1fae67
       Docs: man:nginx(8)
    Process: 607 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 624 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 687 (nginx)
      Tasks: 3 (limit: 627)
     Memory: 4.7M (peak: 5.4M)
        CPU: 49ms
     CGroup: /system.slice/nginx.service
             ├─687 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             ├─688 "nginx: worker process"
             └─689 "nginx: worker process"


ubuntu@ip-172-31-30-33:~$ systemctl is-enabled nginx #to know if it will start automatically after reboot
enabled
ubuntu@ip-172-31-30-33:~$ 




###SCENARIOS:

**Scenario 1: Service Not Starting** 
```
A web application service called 'myapp' failed to start after a server reboot.
What commands would you run to diagnose the issue?
Write at least 4 commands in order.
```

**Hint:**
- First check: Is the service running or failed?
- Then check: What do the logs say?
- Finally check: Is it enabled to start on boot?

**Commands to explore:** `systemctl status myapp`, `systemctl is-enabled myapp`, `journalctl -u myapp -n 50`

**Resource:** Review Day 04 (Process and Services practice)

**Template for your answer:**
```
Step 1: [command]
Why: [one line explanation]

Step 2: [command]
Why: [one line explanation]

...
```

---

**Scenario 2: High CPU Usage** 
```
Your manager reports that the application server is slow.
You SSH into the server. What commands would you run to identify
which process is using high CPU?
```

**Hint:**
- Use a command that shows **live** CPU usage
- Look for processes sorted by CPU percentage
- Note the PID (Process ID) of the top process

**Commands to explore:** `top` (press 'q' to quit), `htop`, `ps aux --sort=-%cpu | head -10`

**Resource:** Review Day 05 (Troubleshooting Drill - CPU & Memory section)

---

**Scenario 3: Finding Service Logs** 
```
A developer asks: "Where are the logs for the 'docker' service?"
The service is managed by systemd.
What commands would you use?
```

**Hint:**
- systemd services → logs are in journald
- Command pattern: `journalctl -u <service-name>`
- Use -n flag to limit number of lines
- Use -f flag to follow logs in real-time (like tail -f)

**Commands to explore:**
```bash
# Check service status first
systemctl status ssh

# View last 50 lines of logs
journalctl -u ssh -n 50

# Follow logs in real-time
journalctl -u ssh -f
```