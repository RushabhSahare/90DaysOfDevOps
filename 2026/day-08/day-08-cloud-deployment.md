# Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

#In this I performed below:
- Launch a cloud instance (AWS EC2 or Utho)
- Connect via SSH
- Install Nginx
- Configure security groups for web access (port 80 by default for nginx)
- Extract and save logs to a file
- Verify your webpage is accessible from the internet


#List of commands I used:
cat /var/log/nginx/access.log
cat /var/log/nginx/error.log
sudo cat /var/log/nginx/access.log > nginxaccesslog.txt
cat nginxaccesslog.txt
ss -tulpn


What I learned:
INstall nginx, check its running and active, configure the port so that its accesisble oon port 80, configured a basic cafe store for nerds and made sure its accessible. Also, checked the logs of nginx's access.log and error.log


ubuntu@ip-172-31-30-33:~$ sudo cat /var/log/nginx/access.log > nginxaccesslog.txt
ubuntu@ip-172-31-30-33:~$ cat nginxaccesslog.txt
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /wp-content/plugins/hellopress/wp_filemanager.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /this_is_a_new_hello_world.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /adminner.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /100.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /database.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /w3llscc.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:51 +0000] "GET /asd67.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /aaa.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /byug.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /2.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /m.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /bxhqswrm.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /dex.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /lock360.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /mejkubyp.php HTTP/1.1" 404 162 "-" "-"
20.170.17.213 - - [05/Aug/2026:05:12:52 +0000] "GET /rcc.php?p= HTTP/1.1" 404 162 "-" "-"
223.185.37.222 - - [05/Aug/2026:05:58:00 +0000] "GET / HTTP/1.1" 200 3303 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/26.6 Safari/605.1.15"
223.185.37.222 - - [05/Aug/2026:05:58:00 +0000] "GET /favicon.ico HTTP/1.1" 404 134 "http://54.173.108.17/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/26.6 Safari/605.1.15"


