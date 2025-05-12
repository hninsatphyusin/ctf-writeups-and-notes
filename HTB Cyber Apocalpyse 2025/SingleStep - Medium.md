I actually did not finish this challenge but here is as much as i understood

```
00008fe0    int32_t main(int32_t argc, char** argv, char** envp)
00008fe0    {
00008fe0        char* rcx;
00008ff7        int64_t rdx;
00008ff7        int64_t rsi;
00008ff7        int64_t rdi_1;
00008ff7        rcx = setbuf(stdout, nullptr);
00009001        sub_43e0(rdi_1, rsi, rdx, rcx);
0000900c        return 0;
00008fe0    }
```
The Pseudo-C code for main calls sub_43e0

Inspecting sub_43e0 we get 
```
000043e0    int64_t sub_43e0(int64_t, int64_t, int64_t, char* arg4)

000043e0    {
000043e0        data_43ec = 0xfa1ee1f9;
000043ec        bool c;
000043ec        bool p;
000043ec        bool a;
000043ec        bool z;
000043ec        bool s;
000043ec        bool d;
000043ec        bool o;
000043ec        *(uint8_t*)arg4 = *(uint8_t*)arg4 - arg4;
000043ee        /* undefined */
000043e0    }
```

Which is all gibberish, nth substantial, but if we see the disassembly 
```
000043e0    int64_t sub_43e0(int64_t, int64_t, int64_t, char* arg4)

000043e0  9c                 pushfq   {var_8}
000043e1  813501000000e1e8…xor     dword [rel data_43ec], 0xaeee8e1  {0xfa1ee1f9}
000043eb  9d                 popfq    {var_8}
000043ec  1809               sbb     byte [rcx], cl
000043ee  f0                 ??
```

We see a very suspicious xor instruction that xors the instructions that is 2 instructions down from itself. 

To run this dynamically linked, stripped binary, we find the main function's address
![[Screenshot 2025-04-01 at 4.24.46 PM.png]]
From here we find that the main address is `0x55555555cfe0`. 

Run the binary in gef and execute the binary once. Then set a breakpoint at  `0x55555555cfe0`

```
**gef➤**  x/15i 0x55555555cfe0

   0x55555555cfe0: endbr64

   0x55555555cfe4: push   rbp

   0x55555555cfe5: mov    rbp,rsp

   0x55555555cfe8:

    mov    rax,QWORD PTR [rip+0x16029]        # 0x555555573018 <stdout>

   0x55555555cfef: mov    esi,0x0

   0x55555555cff4: mov    rdi,rax

   0x55555555cff7: call   0x555555555190 <setbuf@plt>

   0x55555555cffc: mov    eax,0x0

   0x55555555d001: call   0x5555555583e0

   0x55555555d006: mov    eax,0x0

   0x55555555d00b: pop    rbp

   0x55555555d00c: 
```

We know `sub_43e0` is at `0x5555555583e0`.

Disassembling at `0x5555555583e0`
```
   0x5555555583e0: pushf

   0x5555555583e1: xor    DWORD PTR [rip+0x1],0xaeee8e1        # 0x5555555583ec

   0x5555555583eb: popf

   0x5555555583ec: adc    ah,bh

   0x5555555583ee: lock lock pushf

   0x5555555583f1:

    xor    DWORD PTR [rip+0xfffffffffffffff1],0xaeee8e1        # 0x5555555583ec

   0x5555555583fb: popf

.
.
.
```
We see not only is there an xor instruction that decrypts the next instruction, we have another xor instruction below that re-encrypts the next instruction. `0xfffffffffffffff1` by twos complement is actually a negative number that results in it going back to `0x5555555583eb`

Walking through the binary in gdb, we find many many of these xor pairs. So we need to write a python script to decrypt this for us. 

## Decryption
We can decrypt the instructions and then replace the xor instructions with nop so that they do not do anything. 

But first we need to identify the start and end of the .text section
```
**gef➤**  info files

Symbols from "/home/hnin/htb2025/singlestep".

Local exec file:

`/home/hnin/htb2025/singlestep', file type elf64-x86-64.

Entry point: 0x1260

0x0000000000000318 - 0x0000000000000334 is .interp

0x0000000000000338 - 0x0000000000000368 is .note.gnu.property

0x0000000000000368 - 0x000000000000038c is .note.gnu.build-id

0x000000000000038c - 0x00000000000003ac is .note.ABI-tag

0x00000000000003b0 - 0x00000000000003d8 is .gnu.hash

0x00000000000003d8 - 0x0000000000000618 is .dynsym

0x0000000000000618 - 0x000000000000072c is .dynstr

0x000000000000072c - 0x000000000000075c is .gnu.version

0x0000000000000760 - 0x00000000000007b0 is .gnu.version_r

0x00000000000007b0 - 0x00000000000008b8 is .rela.dyn

0x00000000000008b8 - 0x0000000000000a50 is .rela.plt

0x0000000000001000 - 0x000000000000101b is .init

0x0000000000001020 - 0x0000000000001140 is .plt

0x0000000000001140 - 0x0000000000001150 is .plt.got

0x0000000000001150 - 0x0000000000001260 is .plt.sec

0x0000000000001260 - 0x000000000000900d is .text

.
.
.
```
Here we know .text starts at 0x1260 and ends at 0x900d. 

We already know that the offset of main from the start of .text section is 0x00008fe0 through static analysis in binary ninja. 

First we can try to identify xor functions in 0x1260 and 0x900d

I first tried to write a python script for this. 
```
from pwn import *
from capstone import *

def find_xor_instructions():
    binary_path = "singlestep"
    start = 0x1260
    end = 0x900d
    elf = ELF(binary_path)
    code = elf.read(start, end - start)
    md = Cs(CS_ARCH_X86, CS_MODE_64)
    for insn in md.disasm(code, start):
        if insn.mnemonic == "xor":
            print(f"0x{insn.address:x}: {insn.mnemonic} {insn.op_str}")

if __name__ == "__main__":
    find_xor_instructions()
```
The problem was it was terminating at 0x1821 and not moving on to find other xor instructions
```
0x1264: xor ebp, ebp
0x1273: xor r8d, r8d
0x1276: xor ecx, ecx
0x13dc: xor eax, eax
0x1821: xor dword ptr [rip + 1], 0xeac9f690
```
I figured it must be due to the corrupted instructions that made it hard for capstone to read them. So whenever there is an error, it just force it to move by one byte. 
```
from pwn import *

from capstone import *

def find_xor_instructions():
    binary_path = "singlestep"
    start = 0x1260
    end = 0x900d
    elf = ELF(binary_path)
    code = elf.read(start, end - start)
    md = Cs(CS_ARCH_X86, CS_MODE_64)
    md.detail = True 
    last_address = start
    index = 0
    while last_address < end and index < len(code):
        try:
            for insn in md.disasm(code[index:], last_address):
                last_address = insn.address + insn.size
                index += insn.size
                if insn.mnemonic == "xor":
                    print(f"0x{insn.address:x}: {insn.mnemonic} {insn.op_str}")
                if last_address >= end:
                    break
            else:
                # If no valid instruction is found, move forward byte by byte 
                index += 1
                last_address += 1
        except Exception as e: #if there is any error, move forward also
            print(f"Error at 0x{last_address:x}, skipping byte...")
            index += 1
            last_address += 1;
  

if __name__ == "__main__":
    find_xor_instructions()
```

Managed to get a lot of xors:
```
0x140: xor byte ptr [rsi], ch

0x148: xor byte ptr [rsi], ch

0x170: xor ch, ch

0x1e8: xor byte ptr [rax], al

0x1f0: xor byte ptr [rax], al

0x258: xor byte ptr [rax], al
.
.
.
0x1276: xor ecx, ecx

0x13dc: xor eax, eax

0x1821: xor dword ptr [rip + 1], 0xeac9f690

0x183d: xor byte ptr [rip + 1], 0x23

0x1847: xor byte ptr [rip - 9], 0x23

0x1850: xor word ptr [rip + 8], 0x4413

0x1859: xor byte ptr [rip + 3], 0xb1

0x1865: xor word ptr [rip - 0xd], 0x4413
.
.
.
0x8ef2: xor eax, 0xffffffed

0x8efa: xor dword ptr [rip + 1], 0x37616112

0x8f0a: xor dword ptr [rip - 0xf], 0x37616112

0x8f16: xor dword ptr [rip + 0x12], 0x49855b0a

0x8f20: xor dword ptr [rip + 0xc], 0x6df49f34

0x8f2a: xor byte ptr [rip + 9], 0xf2

0x8f3c: xor dword ptr [rip - 0x14], 0x49855b0a

0x8f46: xor dword ptr [rip - 0x1a], 0x6df49f34

0x8f50: xor byte ptr [rip - 0x1d], 0xf2

0x8f85: xor dword ptr [rip + 8], 0x8f474d96

0x8f8f: xor byte ptr [rip + 5], 0xfe

0x8f9d: xor dword ptr [rip - 0x10], 0x8f474d96

0x8fa7: xor byte ptr [rip - 0x13], 0xfe

0x8fb0: xor byte ptr [rip + 1], 7

0x8fba: xor byte ptr [rip - 9], 7
```

But then there is a lot of other xors that are not involved in the encryption and decryption so we need to make our filter more narrow to only find the xors with this format 
```
xor size ptr [rip + x], 0xhexnumber
```

```python
from pwn import *
import sys
from capstone import *

def process_xor_instr(xor_instr):
	addr = xor_instr.address
	str = xor_instr.op_str
	data = str.split(" ")
	print(data)
	if (len(data) != 6): #don't process this if its not according to format
		return
	#data will be something like 
	#['dword', 'ptr', '[rip', '+', '0xe],', '0x5a3c0bb7']
	offset = data[4].split('],')
	#key = p64(data[5])
	print(f"Target addr: {target}")
	
	if data[0] == 'qword':
		key = p64(data[5])
	else if data[0] == 'dword':
		key = p32(data[5])
	else if data[0] == 'word':
		key = p16(data[5])
	else if data[0] == 'byte':
		key = p8(data[5])
	
	
def find_xor_instructions():
	binary_path = "singlestep"
	start = 0x1260
	end = 0x900d
	elf = ELF(binary_path)
	code = elf.read(start, end - start)
	md = Cs(CS_ARCH_X86, CS_MODE_64)
	md.detail = True
	last_address = start
	index = 0
	while last_address < end and index < len(code):
		try:
			for insn in md.disasm(code[index:], last_address):
				last_address = insn.address + insn.size
				index += insn.size
				if insn.mnemonic == "xor":
					process_xor_instr(insn)
					print(f"0x{insn.address:x}: {insn.mnemonic} {insn.op_str}")
				if last_address >= end:
					break
			else:
			# If no valid instruction is found, move forward byte by byte
				index += 1
				last_address += 1
		except Exception as e: #if there is any error, move forward als
			print(e)
			print(f"Error at 0x{last_address:x}, skipping byte...")
			index += 1
			last_address += 1;
  

if __name__ == "__main__":
	find_xor_instructions()
```
but this is still buggy and the resulting binary from this doesn't run... 
