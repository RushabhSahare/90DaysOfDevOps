## Challenge Tasks

### Task 1: DNS – How Names Become IPs
1. Explain in 3–4 lines: what happens when you type `google.com` in a browser?
->browser first checks its own cache, then asks the OS resolver, which checks /etc/resolv.conf's configured DNS server. That server either has the answer cached or walks the DNS hierarchy (root → .com TLD server → Google's authoritative nameserver) to find the IP. Once resolved, the browser opens a TCP connection to that IP on port 443 and sends the actual HTTP request.

2. What are these record types? Write one line each:
   - `A`, `AAAA`, `CNAME`, `MX`, `NS`
A – maps a domain name to an IPv4 address
AAAA – maps a domain name to an IPv6 address
CNAME – aliases one domain name to another domain name (not an IP directly)
MX – specifies which mail servers handle email for the domain
NS – specifies which nameservers are authoritative for the domain


3. Run: `dig google.com` — identify the A record and TTL from the output
dig g00gle.com

; <<>> DiG 9.20.18-1ubuntu2.1-Ubuntu <<>> g00gle.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32785
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;g00gle.com.                    IN      A

;; AUTHORITY SECTION:
g00gle.com.             300     IN      SOA     ns1.googledomains.com. cloud-dns-hostmaster.google.com. 1 21600 3600 259200 300

;; Query time: 30 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Sun Aug 09 10:04:12 UTC 2026
;; MSG SIZE  rcvd: 121

---

### Task 2: IP Addressing
1. What is an IPv4 address? How is it structured? (e.g., `192.168.1.10`)
A 32-bit address written as 4 decimal numbers (0–255) separated by dots, e.g. 192.168.1.10. Each number is called an "octet" (8 bits), so 4 octets = 32 bits total.


2. Difference between **public** and **private** IPs — give one example of each
Public vs private, with examples:

Public IP – globally unique, routable on the internet directly. Example: 142.250.183.14 (a Google server).

Private IP – only valid within a local/internal network, not routable on the public internet. Example: 172.31.30.33 (your EC2 instance's internal IP).



3. What are the private IP ranges?
   - `10.x.x.x`, `172.16.x.x – 172.31.x.x`, `192.168.x.x`
class A: 10.0.0.0 – 10.255.255.255
class B : 172.16.0.0 – 172.31.255.255
Class C: 192.168.0.0 – 192.168.255.255

4. Run: `ip addr show` — identify which of your IPs are private

---

### Task 3: CIDR & Subnetting
1. What does `/24` mean in `192.168.1.0/24`?
2. How many usable hosts in a `/24`? A `/16`? A `/28`?
3. Explain in your own words: why do we subnet?
4. Quick exercise — fill in:

| CIDR | Subnet Mask |           |Total IPs     | Usable Hosts |
|------|-------------|-----------|--------------|
| /24  | 255.255.255.0           |256           | 254         |
| /16  | 255.255.0.0             |65536         | 65534       |
| /28  | 255.255.255.240         |16            | 14          |

---

### Task 4: Ports – The Doors to Services
1. What is a port? Why do we need them?
2. Document these common ports:

| Port | Service |
|------|---------|
| 22   | ssh       |
| 80   | http      |
| 443  | https      |
| 53   | dns      |
| 3306 | sql      |
| 6379 | redis     |
| 27017| mangoDB    |

3. Run `ss -tulpn` — match at least 2 listening ports to their services

tcp            LISTEN          0               4096                         127.0.0.53%lo:53                           0.0.0.0:*                              
tcp            LISTEN          0               511                                0.0.0.0:80                           0.0.0.0:*  

---

### Task 5: Putting It Together
Answer in 2–3 lines each:
- You run `curl http://myapp.com:8080` — what networking concepts from today are involved?
- Your app can't reach a database at `10.0.1.50:3306` — what would you check first?

---

## Documentation

Create `day-15-networking-concepts.md` with:
- Your answers to each task
- Command outputs from `dig` and `ss`
- The filled CIDR table
- What you learned (3 key points)
