![](../Assets/Screenshot%202025-04-01%20at%202.12.31%20PM.png)
In the main function, it calls mmap to allocate a memory region of size 0x9e and the ptr to the memory region is assigned to rax. 

In the first for loop, it traverses through the memory region. At the end of every iteration, the random number is stored at the address rax + i. 

The second loop doesn't really matter because for every iteration it js generates a random number that isn't used or stored. 

At the end, seems like it runs rax() and its returns 1, it puts some success string. 

Running the binary tho, its outputs `What is the flag?` and asks for our input. 
So how does it actually display the output and read the input? Lets inspect this in gdb dynamically to find out. 

### Note about srand and rand
While the code seems like it is generating random numbers, it is actually not really random as the seed is the same. As such the numbers will generated thereafter will always be the same. 

## Dynamic Analysis 
Because it is a dynamically linked and stripped binary, it is harder to debug it in gdb. So we have to find the address of main first. 

In gdb (not gef), type in 
`break __libc_start_main` and then run the binary. 
It will stop at a breakpoint where it will give this: 
```
Breakpoint 1, __libc_start_main_impl (main=0x555555555169, argc=1, argv=0x7fffffffe328, init=0x0, fini=0x0, 

    rtld_fini=0x7ffff7fca380 <_dl_fini>, stack_end=0x7fffffffe318) at ../csu/libc-start.c:242

warning: 242 ../csu/libc-start.c: No such file or directory
```
Now in the arguments for `__libc_start_main_impl` the first argument is the main function's addresss. 

Now with the main function address clear, we can open up the binary again in gef and then set a breakpoint at main. 

```
**gef➤**  x/20i $rip

=> 0x555555555169: push   rbp

   0x55555555516a: mov    rbp,rsp

   0x55555555516d: sub    rsp,0x20

   0x555555555171: mov    r9d,0x0 //arg 6

   0x555555555177: mov    r8d,0xffffffff //arg 5

   0x55555555517d: mov    ecx,0x21 //arg 4

   0x555555555182: mov    edx,0x7 //arg 3

   0x555555555187: mov    esi,0x9e //arg 2

   0x55555555518c: mov    edi,0x0 //arg 1

   0x555555555191: call   0x555555555040 <mmap@plt> //output of mmap is stored in rax
   
   0x555555555196: mov    QWORD PTR [rbp-0x10],rax 

.
.
.
```

At we can then set another breakpoint at the line mmap to find out where the memory region starts by inspecting the `rax`  value.

```
**gef➤**  x/x $rax
0x7ffff7fbc000: 0x00000000
```

We can see that rax is 0x7ffff7fbc000. Inspecting the region which is of size 0x9e, its all empty. 

```
 x/50x $rax

0x7ffff7fbc000: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc010: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc020: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc030: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc040: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc050: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc060: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc070: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc080: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc090: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc0a0: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc0b0: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc0c0: 0x00000000 0x00000000 0x00000000 0x00000000

```

Now looking back at the main function, we can set the breakpoint right before `rax` is called to see what the memory addresses have been populated. 

```
   0x555555555208: mov    rax,QWORD PTR [rbp-0x10]

   0x55555555520c: mov    QWORD PTR [rbp-0x8],rax

   0x555555555210: mov    rax,QWORD PTR [rbp-0x8]

   0x555555555214: call   rax

   0x555555555216: cmp    eax,0x1

   0x555555555219: jne    0x55555555522c
```

Now if we inspect the same memory region, 
```
x/158x 0x7ffff7fbc000
```

Now we can see the memory region is now populated with some data
```
0x7ffff7fbc000: 0xe5894855 0x01213e68 0x24348101 0x01010101

0x7ffff7fbc010: 0x6874b848 0x6c662065 0x48506761 0x616857b8

0x7ffff7fbc020: 0x73692074 0x016a5020 0x5f016a58 0x485a126a

0x7ffff7fbc030: 0x050fe689 0x00ec8148 0x49000001 0xc031e489

0x7ffff7fbc040: 0xd231ff31 0x894c01b6 0x48050fe6 0x327ec085

0x7ffff7fbc050: 0x4c581a6a 0x0148e189 0xfe3181c8 0x48beefca

0x7ffff7fbc060: 0x4804c183 0xf172c139 0x48e7894c 0x0012358d

0x7ffff7fbc070: 0xc7480000 0x00001ac1 0xa6f3fc00 0x0fc0940f

0x7ffff7fbc080: 0xc3c9c0b6 0xc5ad9eb6 0xd5dffa92 0xc7dca8a1

0x7ffff7fbc090: 0xe18ba4ce 0xe1dca28a 0xd29dfa89 0x0000b79a

0x7ffff7fbc0a0: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc0b0: 0x00000000 0x00000000 0x00000000 0x00000000

0x7ffff7fbc0c0: 0x00000000 0x00000000
```

Now if we try to see the instructions here: 
```
**gef➤**  x/50i 0x7ffff7fbc000

   0x7ffff7fbc000: push   rbp

   0x7ffff7fbc001: mov    rbp,rsp

   0x7ffff7fbc004: push   0x101213e

   0x7ffff7fbc009: xor    DWORD PTR [rsp],0x1010101

   0x7ffff7fbc010: movabs rax,0x67616c6620656874

   0x7ffff7fbc01a: push   rax

   0x7ffff7fbc01b: movabs rax,0x2073692074616857

   0x7ffff7fbc025: push   rax

  .
  .
  .
```
In just the first few lines, u see some interesting hex numbers. Just converting those to ascii 

`0x67616c6620656874` -> `galf eht`
`0x2073692074616857` ->` si tahW`

Ah, so this is the string that is displayed when the binary is ran.

Now inspecting the next few lines u find that the syscall is a stdout syscall

```

   0x7ffff7fbc026: push   0x1

   0x7ffff7fbc028: pop    rax

   0x7ffff7fbc029: push   0x1

   0x7ffff7fbc02b: pop    rdi

   0x7ffff7fbc02c: push   0x12

   0x7ffff7fbc02e: pop    rdx

   0x7ffff7fbc02f: mov    rsi,rsp

   0x7ffff7fbc032: syscall
```

Then it calls syscalls stdin and stores in r12
```

   0x7ffff7fbc034: sub    rsp,0x100 //size of input it reads

   0x7ffff7fbc03b: mov    r12,rsp

   0x7ffff7fbc03e: xor    eax,eax

   0x7ffff7fbc040: xor    edi,edi

   0x7ffff7fbc042: xor    edx,edx

   0x7ffff7fbc044: mov    dh,0x1

   0x7ffff7fbc046: mov    rsi,r12

   0x7ffff7fbc049: syscall
```

The next few lines it decrypts the flag and then compares the input to the actual flag. Here we learn that the size of flag is 0x1a (26) and that every 4 bytes of the input is XOR-ed by 0xbeefcafe.

```

   0x7ffff7fbc04b: test   rax,rax

   0x7ffff7fbc04e: jle    0x7ffff7fbc082

   0x7ffff7fbc050: push   0x1a

   0x7ffff7fbc052: pop    rax

   0x7ffff7fbc053: mov    rcx,r12

   0x7ffff7fbc056: add    rax,rcx

   0x7ffff7fbc059: xor    DWORD PTR [rcx],0xbeefcafe

   0x7ffff7fbc05f: add    rcx,0x4

   0x7ffff7fbc063: cmp    rcx,rax

   0x7ffff7fbc066: jb     0x7ffff7fbc059

   0x7ffff7fbc068: mov    rdi,r12

   0x7ffff7fbc06b: lea    rsi,[rip+0x12]        # 0x7ffff7fbc084

   0x7ffff7fbc072: mov    rcx,0x1a

   0x7ffff7fbc079: cld

   0x7ffff7fbc07a: repz cmps BYTE PTR ds:[rsi],BYTE PTR es:[rdi]

   0x7ffff7fbc07c: sete   al

   0x7ffff7fbc07f: movzx  eax,al

   0x7ffff7fbc082: leave

   0x7ffff7fbc083: ret

.
.
.
```

At line `0x7ffff7fbc06b`, we can see the XOR-ed flag is stored at address `0x7ffff7fbc084`

Inspecting 26 bytes of it u see
```
0x7ffff7fbc084: 0xb6 0x9e 0xad 0xc5 0x92 0xfa 0xdf 0xd5

0x7ffff7fbc08c: 0xa1 0xa8 0xdc 0xc7 0xce 0xa4 0x8b 0xe1

0x7ffff7fbc094: 0x8a 0xa2 0xdc 0xe1 0x89 0xfa 0x9d 0xd2

0x7ffff7fbc09c: 0x9a 0xb7
```

Knowing that the first few characters is `HTB{` which is 0x48, 0x54, 0x42, 0x7B which in little endian is `0x7b425448`
Then XOR-ing it by `0xbeefcafe`, we get `0xc5ad9eb6` which in little endian is `0xb6ad9eb6` which is the first 4 bytes we see in above!

So `0x7b425448` ^ `0xbeefcafe` = `0xc5ad9eb6`. Since XOR is reversible  `0xc5ad9eb6` ^ `0xbeefcafe` = `0x7b425448`. 

Now we write a script to automate this: 
```python
encrypted = [ 0xb6, 0x9e, 0xad, 0xc5, 0x92, 0xfa, 0xdf, 0xd5, 0xa1, 0xa8, 0xdc, 0xc7, 0xce, 0xa4, 0x8b, 0xe1, 0x8a, 0xa2, 0xdc, 0xe1, 0x89, 0xfa, 0x9d, 0xd2, 0x9a, 0xb7 ] 

flag = "" 

xor_key = 0xbeefcafe 

for i in range(0, len(encrypted), 4): 
	chunk = encrypted[i:i+4] 
	if len(chunk) < 4: 
		chunk = chunk + [0] * (4 - len(chunk))
		value = int.from_bytes(bytes(chunk), byteorder='little')
		decrypted = value ^ xor_key 
		flag_bytes = decrypted.to_bytes(4, byteorder='little') 
		for b in flag_bytes[:min(4, len(encrypted) - i)]: 
			flag += chr(b) 
			
print(flag)
```

On running the script we get the flag: 
`HTB{l00k_b3y0nd_th3_w0rld}`

