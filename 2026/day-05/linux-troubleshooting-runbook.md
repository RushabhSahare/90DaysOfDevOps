**Environment basics 

uname -a
Linux ip-172-31-30-33 7.0.0-1006-aws #6-Ubuntu SMP PREEMPT Tue May 26 12:04:34 UTC 2026 x86_64 GNU/Linux

Observation: AWS-tuned kernel (-aws flavour), 64-bit, running the AWS-optimised build rather than generic Ubuntu kernel.
cat /etc/os-release
PRETTY_NAME="Ubuntu 26.04 LTS"
VERSION_CODENAME=resolute

Observation: Ubuntu 26.04 LTS, current supported release, no urgency to upgrade.

**Filesystem Sanity

mkdir /tmp/runbook-demo && cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -l /tmp/runbook-demo
total 4
-rw-r--r-- 1 ubuntu ubuntu 221 Aug  4 18:08 hosts-copy

Observation: write access to /tmp confirmed, file copy succeeded with normal permissions, no filesystem issues.
ls -la (home directory)
drwxr-x--- 5 ubuntu ubuntu 4096 Aug  4 17:38 .
-rw------- 1 ubuntu ubuntu 1562 Aug  4 17:49 .bash_history
-rwxrwxrwx 1 ubuntu ubuntu    0 Aug  1 15:04 hello.txt

Observation: ownership and permissions look normal, except hello.txt is world-writable (777), worth tightening if it's not intentional.


**Snapshot: CPU & Memory
ps -o pid,pcpu,pmem,comm -p 7221 (shell process)
    PID %CPU %MEM COMMAND
   7221  0.0  0.6 bash

Observation: shell itself is idle, negligible resource use, nothing unusual.
free -h
               total        used        free      shared  buff/cache   available
Mem:           908Mi       330Mi       210Mi       2.7Mi       498Mi       578Mi
Swap:             0B          0B          0B

Observation: small instance (~908Mi RAM, likely t3.micro or similar), ~63% of memory used/cached but 578Mi still available, no swap configured, no memory pressure right now.

**Snapshot: Disk & IO
df -h
/dev/root   19G  2.8G   16G  16% /
/dev/nvme0n1p13  989M  163M  760M  18% /boot

Observation: root filesystem at 16% usage, plenty of headroom, no disk-full risk currently.
du -sh /var/log
du: cannot read directory '/var/log/private': Permission denied
du: cannot read directory '/var/log/chrony': Permission denied
du: cannot read directory '/var/log/amazon': Permission denied
18M     /var/log

Observation: logs total only 18M (small, healthy), permission-denied errors are expected without sudo, not a real problem, just re-run with sudo for a fully accurate figure.


Snapshot: Network
ss -tulpn
tcp   LISTEN   0   4096   0.0.0.0:22    0.0.0.0:*
tcp   LISTEN   0   511    0.0.0.0:80    0.0.0.0:*

Observation: SSH (22) and a web service (80) are both listening on all interfaces, confirms sshd is up and bound correctly, port 80 suggests a web app is also running on this box.
ping -c 4 8.8.8.8
4 packets transmitted, 4 received, 0% packet loss
rtt min/avg/max/mdev = 0.843/0.875/0.914/0.025 ms

Observation: 0% packet loss, sub-millisecond latency, outbound network connectivity is healthy.


**Logs Reviewed
journalctl -u ssh -n 50
Aug 04 14:14:58 sshd[668]: Server listening on 0.0.0.0 port 22.
Aug 02 14:57:07 systemd[1]: ssh.service: Consumed 2.130s CPU time over 42min 7.101s wall clock time, 15.1M memory peak.

Observation: clean start/stop cycles tied to instance reboots (visible Boot markers), no crash loops, low CPU/memory footprint for the service itself.
sudo tail -n 50 /var/log/auth.log
2026-08-04T17:40:02 sshd-session[1893]: userauth_pubkey: signature algorithm ssh-rsa not in PubkeyAcceptedAlgorithms [preauth]
2026-08-04T17:40:03 sshd-session[1893]: AuthorizedKeysCommand ... failed, status 22
2026-08-04T17:40:12 sshd-session[1893]: Connection closed by authenticating user root 139.19.117.130 port 35736 [preauth]

Observation: a failed login attempt as root from external IP 139.19.117.130, rejected because ssh-rsa isn't in the accepted algorithms list. This looks like an automated scan/bot rather than a real user, worth keeping an eye on but not currently a successful breach.


**Quick Findings
1-System is healthy overall: low memory pressure, plenty of disk space, clean network connectivity, no crash loops on ssh.service.
2-sshd and a web service (port 80) are both up and correctly listening.
3-One security-relevant event: a rejected root login attempt from an unfamiliar external IP (139.19.117.130) using an unsupported key algorithm, blocked automatically, no compromise indicated.
4-Minor hygiene item: hello.txt in home directory has 777 permissions, unrelated to SSH but worth tightening.


**If This Worsens (Next Steps)
1-Tighten SSH exposure: if repeated root login attempts continue from external IPs, restrict inbound port 22 to known IP ranges via the EC2 security group, and confirm PermitRootLogin no is set in sshd_config.
2-Increase log verbosity / monitoring: enable fail2ban or CloudWatch alerting on repeated auth.log failures so scans like this surface automatically instead of requiring manual tail.
3-Deeper live diagnosis if CPU/memory spikes appear: run top/htop live during a suspected incident, and if sshd itself misbehaves, capture sudo strace -p <sshd-PID> for a short window to see what it's stuck on.
