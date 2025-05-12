
| **Data Registers** | **Pointer Registers** |
| ------------------ | --------------------- |
| `rax`              | `rbp`                 |
| `rbx`              | `rsp`                 |
| `rcx`              | `rip`                 |
| `rdx`              |                       |
| `r8`               |                       |
| `r9`               |                       |
| `r10`              |                       |
## Data Registers 
Usually used for storing instructions/syscall arguments 
## Primary Data Registers 
- rax
- rbx 
- rcx 
- rdx 
## Source and Destination Operands 
- rdi 
- rsi 
## Secondary Data Registers 
Used when all previous registers above are in use 
- r8
- r9
- r10

# Pointer Registers 
Used to store specific important address pointers 
## rbp 
base stack pointer - points to the beginning of the stack 
## rsp 
current stack pointer - points to current location within the stack (top of the stack)
## rip 
instruction pointer - holds the address of the next instruction

# Sub-registers 
![[Pasted image 20250112130243.png]]


The following are the names of the sub-registers for all of the essential registers in an x86_64 architecture:

| Description                     | 64-bit Register | 32-bit Register | 16-bit Register | 8-bit Register |
| ------------------------------- | --------------- | --------------- | --------------- | -------------- |
| **Data/Arguments Registers**    |                 |                 |                 |                |
| Syscall Number/Return value     | `rax`           | `eax`           | `ax`            | `al`           |
| Callee Saved                    | `rbx`           | `ebx`           | `bx`            | `bl`           |
| 1st arg - Destination operand   | `rdi`           | `edi`           | `di`            | `dil`          |
| 2nd arg - Source operand        | `rsi`           | `esi`           | `si`            | `sil`          |
| 3rd arg                         | `rdx`           | `edx`           | `dx`            | `dl`           |
| 4th arg - Loop counter          | `rcx`           | `ecx`           | `cx`            | `cl`           |
| 5th arg                         | `r8`            | `r8d`           | `r8w`           | `r8b`          |
| 6th arg                         | `r9`            | `r9d`           | `r9w`           | `r9b`          |
| **Pointer Registers**           |                 |                 |                 |                |
| Base Stack Pointer              | `rbp`           | `ebp`           | `bp`            | `bpl`          |
| Current/Top Stack Pointer       | `rsp`           | `esp`           | `sp`            | `spl`          |
| Instruction Pointer 'call only' | `rip`           | `eip`           | `ip`            | `ipl`          |
