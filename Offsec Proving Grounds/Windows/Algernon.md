Level: Easy 

Open Ports: 
```
PORT      STATE  SERVICE       REASON          VERSION
21/tcp    open   ftp           syn-ack ttl 125 Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-29-20  09:31PM       <DIR>          ImapRetrieval
| 12-16-25  10:49PM       <DIR>          Logs
| 04-29-20  09:31PM       <DIR>          PopRetrieval
|_04-29-20  09:32PM       <DIR>          Spool
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp    open   http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows
135/tcp   open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open   netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open   microsoft-ds? syn-ack ttl 125
5040/tcp  open   unknown       syn-ack ttl 125
7680/tcp  closed pando-pub     reset ttl 125
9998/tcp  open   http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-title: Site doesn't have a title (text/html; charset=utf-8).
|_Requested resource was /interface/root
| uptime-agent-info: HTTP/1.1 400 Bad Request\x0D
| Content-Type: text/html; charset=us-ascii\x0D
| Server: Microsoft-HTTPAPI/2.0\x0D
| Date: Wed, 17 Dec 2025 06:55:52 GMT\x0D
| Connection: close\x0D
| Content-Length: 326\x0D
| \x0D
| <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN""http://www.w3.org/TR/html4/strict.dtd">\x0D
| <HTML><HEAD><TITLE>Bad Request</TITLE>\x0D
| <META HTTP-EQUIV="Content-Type" Content="text/html; charset=us-ascii"></HEAD>\x0D
| <BODY><h2>Bad Request - Invalid Verb</h2>\x0D
| <hr><p>HTTP Error 400. The request verb is invalid.</p>\x0D
|_</BODY></HTML>\x0D
|_http-favicon: Unknown favicon MD5: 9D7294CAAB5C2DF4CD916F53653714D5
17001/tcp open   remoting      syn-ack ttl 125 MS .NET Remoting services
49664/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 10907/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 54631/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 11675/udp): CLEAN (Timeout)
|   Check 4 (port 49029/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-12-17T06:55:53
|_  start_date: N/A
|_clock-skew: 0s
```


smb vulnerability scan: not vulnerable
```
nmap -sV -sC --open -T4  -p 139,445 --script=vuln --script-args=unsafe=1 192.168.240.65
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-17 14:58 +08
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).

```

Port 9998 -> login page for smarter mail
http://192.168.240.65:9998/interface/root#/login
![](../../Assets/Screenshot%202025-12-17%20at%203.00.20%20PM.png)

check source code
![](../../Assets/Screenshot%202025-12-17%20at%203.27.23%20PM.png)
find product version: 100.0.6919 


search that on exploit db 
https://www.exploit-db.com/exploits/49216

run that with modified LHOST and LPORt, catch the reverse shell
