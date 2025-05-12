![](Assets/Screenshot%202025-04-01%20at%2011.27.55%20AM.png)
Here the `main` function prints some text, asks for input and then runs `decrypt_message` on the input.
![](Assets/Screenshot%202025-04-01%20at%2011.01.38%20AM.png)
A `int64_t` variable named secret is declared. The string starting from `IUC..` is copied to the memory location of secret. 

After that it starts the loop 
```
int32_t var_3c = 0;
while (*(uint8_t*)(&secret + (int64_t)var_3c))
{
    *(uint8_t*)(&secret + (int64_t)var_3c) -= 1;
    var_3c += 1;
}
```
This loop will keep iterating along the length of the string as long as the index of the string it is at does not contain the null terminator of the string. 

Here for ith iteration, it accesses the `string[i]` character and then -1 its ascii value. 
That would mean `I` is converted to `H`, `U` to `T`, `C` to `B`...

Writing a python script for that: 
```
def decrypt(encrypted_text):
    decrypted = ""
    for char in encrypted_text:
        # Subtract 1 from the ASCII value of each character
        decrypted += chr(ord(char) - 1)
    return decrypted

encrypted = "IUC|t2nqm4`gm5h`5s2uin4u2d~"
decrypted = decrypt(encrypted)
print(f"Encrypted: {encrypted}")
print(f"Decrypted: {decrypted}")
```
Running it you get the flag: HTB{s1mpl3_fl4g_4r1thm3t1c}

## Alternative Solution
The faster solution without understanding the decryption algorithm that i actually used to solved in the ctf. 

In the `decrypt message` function after the `while` loop
![](Assets/Screenshot%202025-04-01%20at%2011.01.38%20AM%201.png)
The actual flag is actually decrypted and still stored at the same address as secret. 
Running this in gdb and breaking at the strcmp part will allow us to see the arguments (which is the decrypted secret)

Seeing the disassembled code 
```
0000128f    int64_t decrypt_message(char* input)

0000128f  55                 push    rbp {__saved_rbp}
00001290  4889e5             mov     rbp, rsp {__saved_rbp}
00001293  4883ec50           sub     rsp, 0x50
00001297  48897db8           mov     qword [rbp-0x48 {input_char}], rdi
0000129b  64488b0425280000…mov     rax, qword [fs:0x28]
000012a4  488945f8           mov     qword [rbp-0x8 {var_10}], rax
000012a8  31c0               xor     eax, eax  {0x0}
000012aa  48b84955437c7432…mov     rax, 0x716e32747c435549
000012b4  48ba6d3460676d35…mov     rdx, 0x6068356d6760346d
000012be  488945d0           mov     qword [rbp-0x30 {secret}], rax  {0x716e32747c435549}
000012c2  488955d8           mov     qword [rbp-0x28 {var_30}], rdx  {0x6068356d6760346d}
000012c6  48b86d3568603573…mov     rax, 0x753273356068356d
000012d0  48ba696e34753264…mov     rdx, 0x7e643275346e69
000012da  488945dc           mov     qword [rbp-0x24 {var_30+0x4}], rax  {0x753273356068356d}
000012de  488955e4           mov     qword [rbp-0x1c {var_24}], rdx  {0x7e643275346e69}
000012e2  c745cc00000000     mov     dword [rbp-0x34 {var_3c}], 0x0
000012e9  eb1c               jmp     0x1307
```
We can see at address `000012be`, secret is at `rbp-0x30`.

However, in order to get in running with gdb, you have to patch the binary to remove the anti-debugging stuff so that the main function looks like the one below. In binary ninja, you can just `ctrl` + `click` (mac) or `right click` (windows) on the line with `anti_debug()`, click on `Patch` and then `Convert to NOP`. Then `Save As` the file with the option `Save file contents only`
![](Assets/Screenshot%202025-04-01%20at%2010.59.59%20AM.png)
However, we can't just set a breakpoint at 0x012be to find the address at `rbp-0x30`because of PIE. Run the patched executable with gdb-gef first to allow the randomised address to load. 

Then `disass decrypt_message` 
```
Dump of assembler code for function decrypt_message:

   0x000055555555528f <+0>: push   rbp

   0x0000555555555290 <+1>: mov    rbp,rsp

   0x0000555555555293 <+4>: sub    rsp,0x50

 .
 .
 .

   0x0000555555555315 <+134>: lea    rdx,[rbp-0x30]

   0x0000555555555319 <+138>: mov    rax,QWORD PTR [rbp-0x48]

   0x000055555555531d <+142>: mov    rsi,rdx

   0x0000555555555320 <+145>: mov    rdi,rax

   0x0000555555555323 <+148>: call   0x555555555070 <strcmp@plt>

   0x0000555555555328 <+153>: test   eax,eax

.
.
.
```

Now set a breakpoint at the line `call   0x555555555070 <strcmp@plt>`, using `b *0x0000555555555323`. Rerun the program in gdb, input in arbitrary values for when prompt. 

When u stop at the strcmp, gef nicely shows u the arguments of strcmp and u can also see it in the stack. Alternatively, you can inspite the `rdi` and `rsi`
![](Assets/Screenshot%202025-04-01%20at%2011.58.08%20AM.png)