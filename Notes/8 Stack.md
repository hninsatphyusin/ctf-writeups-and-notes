The top of the stack is referred to by the Top Stack Pointer `rsp`, while the bottom is referred to by the Base Stack Pointer `rbp`.

|**Instruction**|**Description**|**Example**|
|---|---|---|
|`push`|Copies the specified register/address to the top of the stack|`push rax`|
|`pop`|Moves the item at the top of the stack to the specified register/address|`pop rax`|

# Usage with Functions/Syscalls 
Before calling a function/syscall, we will push data from registers into the stack and then restore them after the function/syscall 
```nasm
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    push rax        ; push registers to stack
    push rbx
    ; call function
    pop rbx         ; restore registers from stack
    pop rax
```

