

## Quick Concepts (write 1–2 bullets each)
- OSI layers (L1–L7) vs TCP/IP stack (Link, Internet, Transport, Application)
1-OSI has 7 layers (Physical, Data Link, Network, Transport, Session, Presentation, Application).
2-TCP/IP has 4 practical layers: 
->Application (everything else, HTTP, DNS, SSH). Real networking is built and taught around TCP/IP, OSI is mostly used as a shared vocabulary for talking about "which layer" something breaks at.
->Transport (TCP/UDP), 
->Internet (network, this is where IP lives), 
->Link (physical + data link), 



- Where **IP**, **TCP/UDP**, **HTTP/HTTPS**, **DNS** sit in the stack
**IP** : Network (Layer 3)
**TCP/UDP** : Transport (Layer 4)
**Http/Https** : Application (Layer 7)
**DNS** : Application layer protocol, but uses UDP at the transport layer. (occasionally TCP for large responses) 
- One real example: “`curl https://example.com` = App layer over TCP over IP”
Application layer (HTTP/S) → Transport layer (TCP, port 443) → Internet layer (IP routes the packets) → Link layer (your NIC/EC2 network interface actually sends the bits).
---

## Hands-on Checklist (run these; add 1–2 line observations)
- **Identity:** `hostname -I` (or `ip addr show`) — note your IP.
ubuntu@ip-172-31-30-33:~$ hostname
ip-172-31-30-33
ubuntu@ip-172-31-30-33:~$ hostname -I #This give the IP Address
172.31.30.33

- **Reachability:** `ping <target>` — mention latency and packet loss.
ubuntu@ip-172-31-30-33:~$ ping -c 7 google.com
PING google.com (192.179.18.100) 56(84) bytes of data.
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=1 ttl=106 time=2.05 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=2 ttl=106 time=2.06 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=3 ttl=106 time=2.05 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=4 ttl=106 time=2.04 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=5 ttl=106 time=2.02 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=6 ttl=106 time=2.05 ms
64 bytes from ry-in-f100.1e100.net (192.179.18.100): icmp_seq=7 ttl=106 time=2.05 ms

- **Path:** `traceroute <target>` (or `tracepath`) — note any long hops/timeouts.
traceroute google.com
traceroute to google.com (192.179.18.113), 30 hops max, 60 byte packets
 1  240.64.108.131 (240.64.108.131)  1.101 ms 240.64.108.128 (240.64.108.128)  0.732 ms 240.64.108.131 (240.64.108.131)  1.054 ms
 2  100.100.36.106 (100.100.36.106)  0.798 ms 100.100.36.108 (100.100.36.108)  0.809 ms 100.100.34.98 (100.100.34.98)  1.133 ms
 3  * * *
 4  * * *
 5  142.251.53.2 (142.251.53.2)  1.936 ms 142.251.77.60 (142.251.77.60)  1.017 ms 142.251.53.2 (142.251.53.2)  1.814 ms
 6  192.178.243.4 (192.178.243.4)  1.329 ms 192.178.243.2 (192.178.243.2)  1.908 ms 192.178.242.24 (192.178.242.24)  2.665 ms
 7  142.251.49.194 (142.251.49.194)  2.873 ms 142.251.49.187 (142.251.49.187)  2.208 ms 142.251.49.160 (142.251.49.160)  2.398 ms
 8  142.250.59.235 (142.250.59.235)  2.538 ms 142.251.238.39 (142.251.238.39)  4.436 ms 142.251.236.105 (142.251.236.105)  2.044 ms
 9  209.85.253.3 (209.85.253.3)  3.809 ms 192.178.123.37 (192.178.123.37)  3.793 ms 192.178.123.237 (192.178.123.237)  3.779 ms
10  192.178.126.153 (192.178.126.153)  1.781 ms 108.170.229.145 (108.170.229.145)  1.804 ms 192.178.126.161 (192.178.126.161)  1.913 ms
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * ry-in-f113.1e100.net (192.179.18.113)  1.652 ms



- **Ports:** `ss -tulpn` (or `netstat -tulpn`) — list one listening service and its port.


- **Name resolution:** `dig <domain>` or `nslookup <domain>` — record the resolved IP.
buntu@ip-172-31-30-33:~$ dig google.com

; <<>> DiG 9.20.18-1ubuntu2.1-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32449
;; flags: qr rd ra; QUERY: 1, ANSWER: 6, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             39      IN      A       172.253.115.101
google.com.             39      IN      A       172.253.115.100
google.com.             39      IN      A       172.253.115.139
google.com.             39      IN      A       172.253.115.138
google.com.             39      IN      A       172.253.115.113
google.com.             39      IN      A       172.253.115.102

;; Query time: 1 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Sun Aug 09 09:41:20 UTC 2026
;; MSG SIZE  rcvd: 135



- **HTTP check:** `curl -I <http/https-url>` — note the HTTP status code.
ubuntu@ip-172-31-30-33:~$ curl -i https://google.com
HTTP/2 301 
location: https://www.google.com/
content-type: text/html; charset=UTF-8
content-security-policy-report-only: object-src 'none';base-uri 'self';script-src 'nonce-A6Mt-PCuRSSiSMUb-zSxZg' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
date: Sun, 09 Aug 2026 09:37:31 GMT
expires: Tue, 08 Sep 2026 09:37:31 GMT
cache-control: public, max-age=2592000
server: gws
content-length: 220
x-xss-protection: 0
x-frame-options: SAMEORIGIN
alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
ubuntu@ip-172-31-30-33:~$ curl -I https://google.com
HTTP/2 301 
location: https://www.google.com/
content-type: text/html; charset=UTF-8
content-security-policy-report-only: object-src 'none';base-uri 'self';script-src 'nonce-KH675c5ffT0tS-mKqmDgQg' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
date: Sun, 09 Aug 2026 09:38:00 GMT
expires: Tue, 08 Sep 2026 09:38:00 GMT
cache-control: public, max-age=2592000
server: gws
content-length: 220
x-xss-protection: 0
x-frame-options: SAMEORIGIN
alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000


- **Connections snapshot:** `netstat -an | head` — count ESTABLISHED vs LISTEN (rough).

Pick one target service/host (e.g., `google.com`, your lab server, or a local service) and stick to it for ping/traceroute/curl where possible.

---

## Mini Task: Port Probe & Interpret
1) Identify one listening port from `ss -tulpn` (e.g., SSH on 22 or a local web app).  


2) From the same machine, test it: `nc -zv localhost <port>` (or `curl -I http://localhost:<port>`).  
3) Write one line: is it reachable? If not, what’s the next check? (e.g., service status, firewall).

---

## Reflection (add to your markdown)
- Which command gives you the fastest signal when something is broken?

ping → fastest signal for "is the host even reachable" (Network layer)
ss -tulpn → fastest signal for "is the service actually listening" (Transport layer)
traceroute → slower, but tells you where along the path things break


- What layer (OSI/TCP-IP) would you inspect next if DNS fails? If HTTP 500 shows up?
DNS is an Application layer protocol, but it depends on Transport layer connectivity (usually UDP port 53) to actually reach a DNS server. So the check order is:

Application layer → is DNS resolution itself broken (wrong resolver, bad /etc/resolv.conf, try dig @8.8.8.8 domain.com to bypass your default resolver and see if a different server resolves it)

Transport layer → can you even reach the DNS server on port 53 (nc -zv 8.8.8.8 53)


Layer 7 (Application) is correct. And no, ping/traceroute are not useful here, here's why: a 500 error means your request already successfully travelled through every layer below Application, the network path was fine, TCP connected, the server received your request. The failure happened inside the application itself (backend code, a crashed dependency, a database connection failure, etc.). ping/traceroute only test reachability, they can't tell you anything about what the application did once it received the request. The right next move is checking the application's own logs (journalctl -u <app>, or the app's log file) rather than the network stack.


so basically,
The @8.8.8.8 tells dig "ignore /etc/resolv.conf, ask this specific server instead." Google's 8.8.8.8 is a public resolver that's almost never down, so it's a reliable reference point.

If @8.8.8.8 works but your default resolver doesn't: the problem is your resolver config (/etc/resolv.conf pointing somewhere broken), not your network path. Fix the config.

If even @8.8.8.8 fails: the problem isn't DNS config at all, it's that you can't reach any DNS server, which pushes you down to the Transport layer check.

Where the transport layer check fits in
bash
nc -zv 8.8.8.8 53
This isn't asking "can DNS resolve a name," it's asking "can I even open a connection to port 53 on that server at all." If this fails, DNS was never going to work regardless of configuration, because you can't reach any DNS server, likely a firewall or security group blocking outbound port 53.
So the real diagnostic ladder is:
Does my default resolver work? (dig google.com)
If not, does a known-good resolver work? (dig @8.8.8.8 google.com) → tells you if it's a config problem
If that also fails, can I even reach a DNS server on the network level? (nc -zv 8.8.8.8 53) → tells you if it's a connectivity/firewall problem





- Two follow-up checks you’d run in a real incident.
systemctl status <service> → is the service itself even running/healthy
journalctl -u <service> -n 50 (or the app's log file) → what does the service say happened