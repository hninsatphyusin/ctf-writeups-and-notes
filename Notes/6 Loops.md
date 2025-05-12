# Loop n times
Set `rcx` to n (`mov rcx, n`)
```nasm
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    mov rcx, 10
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    loop loopFib
```

# Unconditional Branching
`jmp`

```nasm
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    mov rcx, 10
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    jmp loopFib
```
Main difference btw `jmp` and `loop` is that for `jmp` it doesn't decrement the `rcx` value 

# Conditional Branching

| **Instruction** | **Condition** | **Description**                                    |
| --------------- | ------------- | -------------------------------------------------- |
| `jz`            | `D = 0`       | Destination `equal to Zero`                        |
| `jnz`           | `D != 0`      | Destination `Not equal to Zero`                    |
| `js`            | `D < 0`       | Destination `is Negative`                          |
| `jns`           | `D >= 0`      | Destination `is Not Negative` (i.e. 0 or positive) |
| `jg`            | `D > S`       | Destination `Greater than` Source                  |
| `jge`           | `D >= S`      | Destination `Greater than or Equal` Source         |
| `jl`            | `D < S`       | Destination `Less than` Source                     |
| `jle`           | `D <= S`      | Destination `Less than or Equal` Source            |

## RFLAGS register
  

|Bit(s)|0|1|2|3|4|5|6|7|8|9|10|11|12-13|14|15|16|17|18|19|20|21|22-63|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|**Label** (`1`/`0`)|`CF` (`CY`/`NC`)|`1`|`PF` (`PE`/`PO`)|`0`|`AF` (`AC`/`NA`)|`0`|`ZF` (`ZR`/`NZ`)|`SF` (`NC`/`PL`)|`TF`|`IF` (`EL`/`DI`)|`DF` (`DN`/`UP`)|`OF` (`OV`/`NV`)|`IOPL`|`NT`|`0`|`RF`|`VM`|`AC`|`VIF`|`VIP`|`ID`|`0`|
|**Description**|Carry Flag|_Reserved_|Parity Flag|_Reserved_|Auxiliary Carry Flag|_Reserved_|Zero Flag|Sign Flag|Trap Flag|Interrupt Flag|Direction Flag|Overflow Flag|I/O Privilege Level|Nested Task|_Reserved_|Resume Flag|Virtual-x86 Mode|Alignment Check / Access Control|Virtual Interrupt Flag|Virtual Interrupt Pending|Identification Flag|_Reserved_|

The flags we would mostly be interested in are:

- The Carry Flag `CF`: Indicates whether we have a float.
- The Parity Flag `PF`: Indicates whether a number is odd or even.
- The Zero Flag `ZF`: Indicates whether a number is zero.
- The Sign Flag `SF`: Indicates whether a register is negative.

If a `dec` instruction resulted in a `0`, then bit `#6`, the Zero Flag `ZF`, turns to `1`. Likewise, whenever the bit `#6` is `0`, it means that the Zero Flag is off. Similarly, if a division instruction results in a float number, then the Carry Flag `CF` bit is turned on, or if a `sub` instruction resulted in a negative value, then the Sign Flag `SF` is turned on, and so on.

## JNZ 
`loop` is actually `dec` and `jnz` combined
```nasm
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
    mov rcx, 10
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    dec rcx			; decrement rcx counter
    jnz loopFib		; jump to loopFib until rcx is 0
```

## CMP 
compares two operands, by subtracting the second operand from first operand and then sets the necessary flags in the RFLAGS register. As use `js` after that to jump if the result is negative 
```nasm
global  _start

section .text
_start:
    xor rax, rax    ; initialize rax to 0
    xor rbx, rbx    ; initialize rbx to 0
    inc rbx         ; increment rbx to 1
loopFib:
    add rax, rbx    ; get the next number
    xchg rax, rbx   ; swap values
    cmp rbx, 10		; do rbx - 10
    js loopFib		; jump if result is <0
```