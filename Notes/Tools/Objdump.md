Getting the text section 
```shell-session
objdump -M intel -d helloWorld
```

Show only assembly code without machine code/addresses 
```shell-session
objdump -M intel --no-show-raw-insn --no-addresses -d helloWorld
```

Dump strings in the data section 
```shell-session
objdump -sj .data helloWorld
```