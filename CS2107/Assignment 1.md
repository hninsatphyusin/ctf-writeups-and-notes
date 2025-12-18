## E.1 Huuuuuge RSA
Dump the challenge parameters into dcode.fr
```
Challenge Parameters:
n = 134483489259357587193062297294916557281511310492658874099915118851106176841242629412489233879805646303697723631290376922707848149746292340948693757862129548348543497180869087021892384409416014298517539741882667796027036147608670091266898895218841621334695007993716583675755016319734116593324017785790343763209
e = 65537
c = 102687950593631611439795983752527792474757459249112359566891231885071300563957355572372854325640742460221240091756824050729982284987694623185002468120085229505133037450786304818981737812221292836925419605029408917407642417732585756645359207207411677906338212716279539545603373179299650287710970218685927463998
```

![](../Assets/Screenshot%202025-09-12%20at%201.31.19%20PM.png)

## E.3 Plaintext attack
Use `zip.info` to get more information about the zip file
```
zipinfo - v sus_package.zip                    
```

```
compression method:                             none (stored)
file security status:                           encrypted
.
.
.
The central-directory extra field contains:
  - A subfield with ID 0x000a (PKWARE Win32) and 32 data bytes.  The first
    20 are:   00 00 00 00 01 00 18 00 68 75 6b 8e 7a 20 dc 01 00 00 00 00.
```


![](../Assets/Screenshot%202025-09-12%20at%201.46.02%20PM.png)

Using bkcrack
```
bkcrack/bkcrack -C sus_package.zip -c "Assignment 1.pdf" -p Assignment\ 1.pdf 
```
result:
```
bkcrack 1.8.0 - 2025-08-18
[13:21:31] Z reduction using 82137 bytes of known plaintext
18.6 % (15292 / 82137)
[13:21:32] Attack on 123 Z values at index 67342
Keys: 7e827b05 98ea3b23 33a5bfc8
95.9 % (118 / 123)
Found a solution. Stopping.
You may resume the attack with the option: --continue-attack 118
[13:21:32] Keys
7e827b05 98ea3b23 33a5bfc8
```

Use the keys to crack:
```
bkcrack/bkcrack -C sus_package.zip -k 7e827b05 98ea3b23 33a5bfc8 -D d-package.zip
bkcrack 1.8.0 - 2025-08-18
[13:22:33] Writing decrypted archive d-package.zip
100.0 % (2 / 2)
```

## E.2 Password attack
```
office2john encrypted_flag.docx > ef.hash
hashcat -m 9600 -a 0 hash /usr/share/wordlists/rockyou.txt
```

password found: cs2107711923
open the file normally, type in password, get the flag, yay!

## M.1 Rolling Thunder!
![](../Assets/Screenshot%202025-09-12%20at%202.48.16%20PM.png)
Known Portions: PNG Signature (8 Bytes) + IHDR length (4 Bytes) + IHDR Header Chunk (4 Bytes) = 16 Bytes 
```
KNOWN = [0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A, 0x00, 0x00, 0x00, 0x0D, 0x49, 0x48, 0x44, 0x52]
KEY = [0 for i in range(16)]

def decrypt_flag():
    with open("flag.png.enc", "rb") as f:
        enc_data = bytearray(f.read())
    for i in range(16):
        KEY[i] = enc_data[i] ^ KNOWN[i]


decrypt_flag()
print(KEY)
```
running it, we get this 16 bytes of key:
`[101, 108, 101, 112, 104, 97, 110, 116, 101, 108, 101, 112, 104, 97, 110, 116]`

Its 2 times of `101, 108, 101, 112, 104, 97, 110, 116` so this is the key

then decrypting is just xor-ing it again
```
KEY = [101, 108, 101, 112, 104, 97, 110, 116]


def decrypt_flag():
    with open("flag.png.enc", "rb") as f:
        enc_data = bytearray(f.read())
    for i in range(len(enc_data)):
        enc_data[i] ^= KEY[i % len(KEY)]
    with open("flag.png", "wb") as f:
        f.write(enc_data)

decrypt_flag()
```

open flag.png to get the flag

## M.2 Secret Message

"I intercepted this secret message but it's encrypted using a monoalphabetic substitution cipher but I don't know the key so can you help me find a way to decrypt it" ahh challenge :D

Frequency Analysis: https://alexandernwilson.com/gallery/frequency_analysis.html

![](../Assets/Screenshot%202025-09-12%20at%203.41.59%20PM.png)

Only alphabet have changed, numbers and special characters have not. Easily to identify the encrypted flag
`JY2107{QO4SDY1Y_GRTCV3OE1D_QM56J}`
C -> J 
S -> Y

![](../Assets/Screenshot%202025-09-12%20at%203.45.33%20PM.png)
E -> T
T -> E
A -> Q
O -> X/P/O

`(e>t>a>X>P>O>s>K...);`
O -> X
I -> P
N -> O

there is many everywhere "tKe", and its most likely `the` so K is h
H -> K

Its looking more like words so its easier to guess:
`GReCVencies` = frequencies
F -> G 
R -> R 
Q -> C 
U -> V 

`Hersion` = Version
V -> H

`reviseZ` = revised
D -> Z

`aNNears often toFether` = appears often together
P -> N 
G -> F

`engSish` = english
L -> S

`roughlD` = roughly 
Y -> D

`Bessage` = Message
M -> B

`readaMle` = readable
B -> M

at this point the flag can be seen
![](../Assets/Screenshot%202025-09-12%20at%203.43.17%20PM.png)

## H.1 Lazy Programmer
```
nc cs2107-ctfd-i.comp.nus.edu.sg 5001
Server connected.
Fri Sep 12 12:02:14 2025

Welcome!
A random admin hash has been generated for you, and you should be able to see it on your hardware encryption device.
Please enter the admin hash handed to you.
Input Hash:
yo mom
Wrong hash! A report has been generated and sent to our admins.
```

Hash Generation 
```
srand(time(NULL));
int random = rand();
char key[(int)((ceil(log10(random))+2)*sizeof(char))];
sprintf(key, "%d", random);
unsigned char result[16];
md5String(key, result);
char md5string[33];
for(int i = 0; i < 16; ++i)
    sprintf(&md5string[i*2], "%02x", (unsigned int)result[i]);
```

`srand()` -> initialises the rand() function with a seed which is the time
Because we know the time, we know the seed and then we can calculate the hash generated now 

`char key[(int)((ceil(log10(random))+2)*sizeof(char))];` -> allocates a character array to store the string representation of the integer random.

`log10(random)` -> estimates the number of digits in random 
`ceil` -> rounds up 
`+2` -> addes space for the null terminator and possible a negative sign

`sprintf(key, "%d", random);` -> converts random to a string and then store it in key.

`md5String(key, result);` -> calculate the md5 hash from the key (string) and store it in result

```
char md5string[33];
for(int i = 0; i < 16; ++i)
    sprintf(&md5string[i*2], "%02x", (unsigned int)result[i]);
```
Storing the hexadecimal notation of result into md5string

The program then compares input and md5string. 

We can extract the time of server that is printed out, then use that to seed srand, and then generate a "random" number and hash with md5 then that will be the key.

```
from ctypes import CDLL
from pwn import *
import time
from datetime import datetime
import hashlib

#r = process("./chall")
r = remote("cs2107-ctfd-i.comp.nus.edu.sg", 5001)

r.recvline() #server connected
serverTime = r.recvline().decode().strip() #server time

#conver to datetime object
dt = datetime.strptime(serverTime, "%a %b %d %H:%M:%S %Y")

#convert to time_t
timestamp = int(time.mktime(dt.timetuple()))
print(timestamp)

print(r.recvuntil(b'Input Hash:\n'))

libc = CDLL("libc.so.6")
libc.srand(timestamp)
random = hashlib.md5(str(libc.rand()).encode()).hexdigest()
print(random)
r.sendline(random)
print(r.recvall())
```





