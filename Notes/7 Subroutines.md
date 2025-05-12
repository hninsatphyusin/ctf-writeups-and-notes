# Procedures
subroutine that does not return anything 

## Call
`call` instruction pushes the current next instruction pointer `rip` to the stack and then jumps to the address of the procedure? 

once the procedure is executed, we should end it with a `ret` instruction to return to the point we were at before jumping to the procedure. The `ret` procedure `pops` the address at the top of the stack into rip, so the program's next instruction is restored to what it was before jumping to the procedure
```nasm
global  _start

section .data
    message db "Fibonacci Sequence:", 0x0a

section .text
_start:
    call printMessage   ; print intro message
    call initFib        ; set initial Fib values
    call loopFib        ; calculate Fib numbers
    call Exit           ; Exit the program

printMessage:
    mov rax, 1      ; rax: syscall number 1
    mov rdi, 1      ; rdi: fd 1 for stdout
    mov rsi,message ; rsi: pointer to message
    mov rdx, 20     ; rdx: print length of 20 bytes
    syscall         ; call write syscall to the intro message
    ret

initFib:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    ret

loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    cmp rbx, 10		; do rbx - 10
    js loopFib		; jump if result is <0
    ret

Exit:
    mov rax, 60
    mov rdi, 0
    syscall
```

# Functions 
## Function calling convention 
Before calling a function:
1. `Save Registers` on the stack (`Caller Saved`)
2. Pass `Function Arguments` (like syscalls)
3. Fix `Stack Alignment`
4. Get Function's `Return Value` (in `rax`)
Writing a function 
Points to consider:
1. Saving `Callee Saved` registers (`rbx` and `rbp`)
2. Get arguments from registers
3. Align the Stack
4. Return value in `rax`

## Saving registers 
```nasm
printFib:
    push rax        ; push registers to stack
    push rbx
    ; function call
    pop rbx         ; restore registers from stack
    pop rax
    ret
```

## Stack Alignment 
We should have 16 bytes (or a multiple of 16) on top of the stack before making a call 
```nasm
    sub rsp, 16
    call function
    add rsp, 16
```
We can count the number of (unpoped) push instructions and (unreturned) call instructions in order to find how many 8-bytes have been pushed to the stack. Then we can caculate how many bytes to subtract to be a multiple of 16..
