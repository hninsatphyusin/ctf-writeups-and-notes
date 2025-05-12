  

| Instruction | Description                                                                                  | Example            | Explanation          |
| ----------- | -------------------------------------------------------------------------------------------- | ------------------ | -------------------- |
| `mov`       | Move data or load immediate data (is actually a copy and does not affect the source operand) | `mov rax, 1`       | rax = 1              |
| `lea`       | Load an address pointing to the value                                                        | `lea rax, [rsp+5]` | rax = rsp + 5        |
| `xchg`      | Swap data between two registers or addresses                                                 | `xchg rax, rbx`    | rax = rbx, rbx = rax |
When to use `lea` instead of `mov` ? 
If you want to load a pointer with an offset, we should use `lea`. For example, 
```
lea rax, [rsp+10]
```
offset here is: `[rsp+10]`

---

