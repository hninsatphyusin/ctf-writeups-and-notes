# Unary Instructions 

| Instruction | Description    | Example    | Explanation                        |
| ----------- | -------------- | ---------- | ---------------------------------- |
| `inc`       | Increment by 1 | `inc rax`  | `rax++` or `rax += 1` -> `rax = 2` |
| `dec`       | Decrement by 1 | `dec rax`  | `rax--` or `rax -= 1` -> `rax = 0` |

# Binary Instructions 
Assume both rax and rbx start as 1 

| Instruction | Description                      | Example         | Explanation          |
| ----------- | -------------------------------- | --------------- | -------------------- |
| `add`       | Add both operands                | `add rax, rbx`  | `rax = 1 + 1` -> `2` |
| `sub`       | Subtract Source from Destination | `sub rax, rbx`  | `rax = 1 - 1` -> `0` |
| `imul`      | Multiply both operands           | `imul rax, rbx` | `rax = 1 * 1` -> `1` |

Bitwise Instruction 
Assume that rax = 1 and rbx = 2
  

| Instruction | Description                                                         | Example        | Explanation                           |
| ----------- | ------------------------------------------------------------------- | -------------- | ------------------------------------- |
| `not`       | Bitwise NOT (invert all bits, 0->1 and 1->0)                        | `not rax`      | `NOT 00000001` -> `11111110`          |
| `and`       | Bitwise AND (if both bits are 1 -> 1, if bits are different -> 0)   | `and rax, rbx` | `00000001 AND 00000010` -> `00000000` |
| `or`        | Bitwise OR (if either bit is 1 -> 1, if both are 0 -> 0)            | `or rax, rbx`  | `00000001 OR 00000010` -> `00000011`  |
| `xor`       | Bitwise XOR (if bits are the same -> 0, if bits are different -> 1) | `xor rax, rbx` | `00000001 XOR 00000010` -> `00000011` |
`xor`-ing a number by itself returns 0, more efficient way of making a register 0 

