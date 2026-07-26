#Day-01 Linux Fundamental:

#Linux Architecture:

A - Application
S - Shell
K - Kernel

#How does a linux start:
Power-on
BIOS
BootLoader
Processes

The systemd is a PID1 - it starts other processes (d stands for deamon)

#Types of Hypervisor:
Type 1: Onprem, Baremetal
Type 2: Oracle Virtualbox

#Created ec2 instance on AWS
tried below commands

#Basic commands:
Ping
echo //to print//
dig //tells ip of hosted website//
alias //kind of nickname//
free -h //how much MEM is available//
df -h //Disk file//
top //gives processes//
sudo apt-get update && sudo apt-get upgrade
sudo install nginx
sudo systemctl start nginx
sudo systemctl status nginx

Nginx:
we installed nginx
We created the index file in /var/www/html in nginx with simple html code
opened port 80 on ec2 instance
tried to accces nginx server. 