globally available function written in C, provided by the Operating System Kernel 

Heres is the entire list: 
https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#x86_64-64_bit

common ones 
```shell-session
#define __NR_read 0
#define __NR_write 1
#define __NR_open 2
#define __NR_close 3
#define __NR_stat 4
#define __NR_fstat 5
```

System Function Arguments 
```
man -s 2 <syscall command name> 
```

# Syscall Calling Convention 
To call a syscall, we have to:
1. Save registers to stack
2. Set its syscall number in `rax`
3. Set its arguments in the registers
4. Use the syscall assembly instruction to call it

|Description|64-bit Register|8-bit Register|
|---|---|---|
|Syscall Number/Return value|`rax`|`al`|
|Callee Saved|`rbx`|`bl`|
|1st arg|`rdi`|`dil`|
|2nd arg|`rsi`|`sil`|
|3rd arg|`rdx`|`dl`|
|4th arg|`rcx`|`cl`|
|5th arg|`r8`|`r8b`|
|6th arg|`r9`|`r9b`|
