Linux Commands Cheat Sheet – Day 03 (90 Days of DevOps)
A quick-reference toolkit for real troubleshooting: process management, file system, and networking.


1. Process Management


ps aux	List all running processes with user, PID, CPU and memory usage

top	Live view of processes, sorted by CPU usage by default

htop	Interactive, colourised version of top (install separately)

kill <PID>	Send a termination signal (SIGTERM) to a process

kill -9 <PID>	Force kill a process (SIGKILL), use as last resort

pkill <name>	Kill a process by name instead of PID

nice -n 10 <cmd>	Start a command with a lower scheduling priority

renice -n 5 -p <PID>	Change priority of an already running process

jobs	List background jobs in the current shell session

bg / fg	Resume a stopped job in the background/foreground

nohup <cmd> &	Run a command immune to hangups, in the background

systemctl status <service>	Check the status of a systemd-managed service

systemctl restart <service>	Restart a systemd-managed service

2. File System


ls -lah	List files with permissions, size and hidden files

cd <path>	Change the working directory

pwd	Print the current working directory

cp -r <src> <dest>	Copy files/folders recursively

mv <src> <dest>	Move or rename a file/folder

rm -rf <path>	Remove files/folders recursively and forcefully (use carefully)

mkdir -p <path>	Create a directory, including parent folders if missing

find / -name "<file>"	Search the filesystem for a file by name

df -h	Show disk space usage in human-readable form

du -sh <path>	Show total size of a folder/file, human-readable

chmod 755 <file>	Change file permissions (owner/group/others)

chown user:group <file>	Change file ownership

tail -f <file>	Continuously stream new lines added to a log file

cat <file>	Print the full contents of a file to the screen

grep -i "error" <file>	Search a file for a pattern, case-insensitive


3. Networking Troubleshooting


ping <host>	Test basic connectivity/reachability to a host

ip addr	Show network interfaces and assigned IP addresses

dig <domain>	Query DNS records for a domain

curl -I <url>	Fetch HTTP headers only, useful to check if a service responds

netstat -tulnp	List listening ports and the processes using them (or use ss)

ss -tulnp	Modern replacement for netstat, faster socket stats

traceroute <host>	Trace the network path/hops to a host

wget <url>	Download a file from a URL

nslookup <domain>	Basic DNS lookup for a domain

hostname -I	Show the machine's own IP address(es)
